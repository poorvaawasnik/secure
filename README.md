# Week 9 Secure E-commerce Platform

📊 Sample Output:
🔐 SECURE E-COMMERCE PLATFORM
=============================

🚀 SECURITY SYSTEM INITIALIZED
------------------------------
✅ JWT Configuration: HS512 with 512-bit secret
✅ Token Expiry: Access=15min, Refresh=7days
✅ Password Policy: BCrypt (strength: 12)
✅ CORS: Configured for 3 origins
✅ Rate Limiting: Enabled for login and API
✅ Multi-tenancy: Database routing enabled
✅ OAuth2: Google, GitHub configured
✅ Audit Logging: Security events captured

🔑 AUTHENTICATION FLOW:
------------------------
# User Login
POST /api/auth/login
{
  "email": "user@tenant1.com",
  "password": "SecurePass123!"
}

Response:
{
  "accessToken": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9...",
  "tokenType": "Bearer",
  "expiresIn": 900,
  "user": {
    "id": 1,
    "email": "user@tenant1.com",
    "roles": ["ROLE_CUSTOMER"],
    "tenantId": "tenant1"
  }
}

# Token Payload (Decoded):
{
  "sub": "user@tenant1.com",
  "auth": "ROLE_CUSTOMER",
  "tenantId": "tenant1",
  "type": "access",
  "iat": 1706193000,
  "exp": 1706193900
}

🏢 MULTI-TENANT ARCHITECTURE:
-----------------------------
Tenant Context: tenant1
Database Connection: jdbc:postgresql://localhost:5432/tenant1_db

# Tenant Database Schema:
tenant1_db
├── users (tenant-specific)
├── products (tenant-specific)
├── orders (tenant-specific)
└── payments (tenant-specific)

shared_db
├── tenants (shared)
└── audit_logs (shared)

👥 ROLE-BASED ACCESS CONTROL:
------------------------------
User: john@tenant1.com (ROLE_CUSTOMER)
Allowed Endpoints:
- GET /api/products (tenant1 only)
- POST /api/orders (tenant1 only)
- GET /api/orders/{id} (own orders only)

User: admin@tenant1.com (ROLE_ADMIN)
Allowed Endpoints:
- ALL /api/products (tenant1 only)
- ALL /api/orders (tenant1 only)
- GET /api/users (tenant1 only)
- POST /api/tenants (global admin only)

User: superadmin@system.com (ROLE_SUPER_ADMIN)
Allowed Endpoints:
- ALL endpoints across all tenants

🔒 METHOD-LEVEL SECURITY:
-------------------------
@Service
public class ProductService {
    
    // Only tenant admin can create products
    @PreAuthorize("hasRole('ADMIN') or " +
                  "(hasRole('VENDOR') and " +
                  "@tenantSecurityService.isCurrentUserTenantAdmin(#tenantId))")
    public Product createProduct(Product product, Long tenantId) {
        // Implementation
    }
    
    // Users can only access products from their tenant
    @PreAuthorize("hasRole('ADMIN') or " +
                  "@tenantSecurityService.hasAccessToProduct(#productId)")
    public Product getProduct(Long productId) {
        // Implementation
    }
}

🌐 OAUTH2 SOCIAL LOGIN:
------------------------
# Google OAuth2 Flow:
1. User clicks "Login with Google"
2. Redirect to: https://accounts.google.com/o/oauth2/auth?...
3. User authenticates with Google
4. Redirect back to: /oauth2/callback/google
5. System creates/updates local user
6. Generate JWT tokens
7. Redirect to dashboard

# GitHub OAuth2 Flow:
1. User clicks "Login with GitHub"
2. Redirect to: https://github.com/login/oauth/authorize?...
3. User authorizes application
4. Redirect back to: /oauth2/callback/github
5. Process similar to Google

📊 SECURITY AUDIT LOGS:
------------------------
# Sample Audit Events:
2024-01-25 10:30:15 | INFO  | AUTH_SUCCESS | user@tenant1.com | tenant1 | 192.168.1.100
2024-01-25 10:31:22 | INFO  | PRODUCT_CREATED | admin@tenant1.com | tenant1 | Product ID: 123
2024-01-25 10:32:45 | WARN  | RATE_LIMIT_EXCEEDED | user@tenant2.com | tenant2 | 192.168.1.101
2024-01-25 10:33:10 | ERROR | AUTH_FAILED | hacker@example.com | unknown | 10.0.0.1
2024-01-25 10:34:30 | INFO  | PASSWORD_RESET_REQUEST | user@tenant1.com | tenant1 | Token: abc123

🚨 RATE LIMITING METRICS:
-------------------------
Endpoint: /api/auth/login
Limit: 5 requests per 5 minutes per IP

Current Usage:
- IP: 192.168.1.100 → 2/5 requests (Reset in: 3:12)
- IP: 192.168.1.101 → 5/5 requests (BLOCKED - Reset in: 1:45)
- IP: 10.0.0.1 → 15/5 requests (PERMANENTLY BLOCKED)

Response Headers:
X-Rate-Limit-Limit: 5
X-Rate-Limit-Remaining: 3
X-Rate-Limit-Reset: 1706193900

🔍 SECURITY HEADERS:
--------------------
HTTP Response Headers:
• Strict-Transport-Security: max-age=31536000; includeSubDomains
• X-Content-Type-Options: nosniff
• X-Frame-Options: DENY
• X-XSS-Protection: 1; mode=block
• Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'
• Referrer-Policy: strict-origin-when-cross-origin
• Permissions-Policy: geolocation=(), microphone=(), camera=()

🧪 SECURITY TEST RESULTS:
-------------------------
# OWASP ZAP Scan Results:
✅ SQL Injection Tests: 0 vulnerabilities
✅ XSS Tests: 0 vulnerabilities
✅ CSRF Tests: Protected by stateless JWT
✅ Authentication Tests: All secure
✅ Authorization Tests: RBAC working correctly

# Dependency Vulnerability Scan:
Total Dependencies: 45
Vulnerabilities: 0 (Critical), 0 (High), 2 (Medium), 3 (Low)
Patched: All medium and low vulnerabilities

# Penetration Test Results:
Security Score: 98/100
Areas for Improvement:
1. Implement 2FA for admin accounts
2. Add security question for password reset
3. Regular security awareness training for users
📤 Submission Requirements:
GitHub Structure:

week9-secure-ecommerce/
│── src/main/java/com/ecommerce/
│ ├── security/
│ │ ├── config/
│ │ │ ├── SecurityConfig.java
│ │ │ ├── JwtConfig.java
│ │ │ └── OAuth2Config.java
│ │ ├── jwt/
│ │ │ ├── JwtTokenProvider.java
│ │ │ ├── JwtAuthenticationFilter.java
│ │ │ └── TokenBlacklistService.java
│ │ ├── oauth2/
│ │ │ ├── CustomOAuth2UserService.java
│ │ │ └── OAuth2AuthenticationSuccessHandler.java
│ │ ├── multitenancy/
│ │ │ ├── TenantContext.java
│ │ │ ├── TenantAwareRoutingDataSource.java
│ │ │ └── TenantFilter.java
│ │ └── audit/
│ │ ├── SecurityAuditAspect.java
│ │ └── SecurityEvent.java
│ ├── model/
│ │ ├── entity/
│ │ │ ├── User.java
│ │ │ ├── Tenant.java
│ │ │ └── Role.java
│ │ ├── enums/
│ │ │ ├── UserStatus.java
│ │ │ └── Permission.java
│ │ └── dto/
│ │ ├── LoginRequest.java
│ │ ├── JwtResponse.java
│ │ └── UserProfile.java
│ ├── controller/
│ │ ├── AuthController.java
│ │ ├── UserController.java
│ │ └── TenantController.java
│ ├── service/
│ │ ├── UserService.java
│ │ ├── TenantService.java
│ │ └── AuditService.java
│ └── EcommerceApplication.java
│── src/main/resources/
│ ├── application.yml
│ ├── oauth2-credentials.yml
│ └── db/migration/
│── src/test/java/com/ecommerce/
│ ├── security/
│ │ ├── SecurityIntegrationTest.java
│ │ └── JwtTokenTest.java
│ └── controller/
│ └── AuthControllerTest.java
│── docker-compose.yml
│── security-test-report.md
│── README.md
└── .gitignore
