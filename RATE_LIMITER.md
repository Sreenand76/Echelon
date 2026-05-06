# DDoS Protection - Rate Limiter Module

## Overview

The Rate Limiter module provides comprehensive DDoS attack protection using the **Token Bucket Algorithm**. It limits requests per IP address to prevent abuse and ensure fair resource distribution.

## Features

### 🛡️ Protection Mechanisms

- **Token Bucket Algorithm**: Efficient rate limiting with burst allowance
- **IP-based Limiting**: Track and limit requests per client IP
- **Configurable Thresholds**: Easy adjustment of rate limits
- **Real-time Monitoring**: Track active rate limits and statistics
- **Proxy Support**: Handles proxied requests (X-Forwarded-For, X-Real-IP)

### 📊 Monitoring & Analytics

- Live request statistics
- Block rate visualization
- Per-IP tracking
- Historical data collection
- DDoS simulation for testing

## Architecture

### Backend Components

#### 1. **RateLimiterService** (`RateLimiterService.java`)
- Core rate limiting logic
- Token bucket implementation
- Per-IP token management
- Statistics tracking

#### 2. **RateLimiterFilter** (`RateLimiterFilter.java`)
- Servlet filter for intercepting requests
- Automatic IP extraction
- Returns 429 (Too Many Requests) on limit exceeded
- Transparent request blocking

#### 3. **RateLimiterController** (`RateLimiterController.java`)
- REST endpoints for monitoring
- Status check endpoint
- Statistics endpoint
- DDoS simulation endpoint
- Admin reset functions

#### 4. **RateLimiterConfig** (`RateLimiterConfig.java`)
- Spring configuration
- Bean definitions
- Property management

### Frontend Components

#### RateLimiter.jsx
- Real-time status dashboard
- Statistics visualization
- DDoS simulation interface
- Attack result analysis
- System control panel

## API Endpoints

### Status & Monitoring

```
GET /api/ratelimiter/status
- Returns current rate limit status for requesting IP
- Response:
  {
    "clientIp": "192.168.1.1",
    "remainingTokens": 45.5,
    "isAllowed": true,
    "message": "Request allowed"
  }
```

```
GET /api/ratelimiter/stats
- Returns system-wide statistics
- Response:
  {
    "totalTrackedIPs": 150,
    "tokensPerMinute": 60,
    "blockedIPs": 12,
    "allowedIPs": 138,
    "blockRate": 8.0
  }
```

### Testing & Management

```
POST /api/ratelimiter/simulate-ddos?requests=100
- Simulate DDoS attack with specified number of requests
- Returns attack impact analysis
```

```
POST /api/ratelimiter/reset/{ip}
- Reset rate limit for specific IP (Admin)
```

```
POST /api/ratelimiter/reset-all
- Reset all rate limits (Admin)
```

## Configuration

### application-ratelimiter.properties

```properties
# Number of requests allowed per minute per IP
ratelimiter.tokens-per-minute=60

# Enable/Disable rate limiter
ratelimiter.enabled=true

# Logging level
logging.level.com.securescape.ratelimiter=INFO
```

### Common Configurations

```properties
# Very Strict (10 requests/minute)
ratelimiter.tokens-per-minute=10

# Moderate (60 requests/minute) - Default
ratelimiter.tokens-per-minute=60

# Lenient (300 requests/minute)
ratelimiter.tokens-per-minute=300

# Very Lenient (1000 requests/minute)
ratelimiter.tokens-per-minute=1000
```

## How Token Bucket Works

1. **Initialization**: Each IP gets a bucket with full tokens (e.g., 60)
2. **Request Processing**:
   - Check if tokens available
   - If YES: Consume 1 token, allow request
   - If NO: Reject request (429 Too Many Requests)
3. **Refill**: Tokens automatically refill at constant rate (1 per second for 60/min)
4. **Burst Handling**: Allows burst of requests up to bucket capacity

## Usage Examples

### Basic Setup

```bash
# Start backend with rate limiter
cd backend
mvn spring-boot:run -Dspring.profiles.active=ratelimiter
```

### Check Rate Limit Status

```bash
curl http://localhost:5000/api/ratelimiter/status
```

### Simulate DDoS Attack

```bash
curl -X POST http://localhost:5000/api/ratelimiter/simulate-ddos?requests=500
```

### Reset All Rate Limits

```bash
curl -X POST http://localhost:5000/api/ratelimiter/reset-all
```

## Testing Scenarios

### 1. Normal Traffic
- 50 requests with 60 req/min limit
- Result: All allowed ✅

### 2. Light Attack
- 150 requests with 60 req/min limit
- Result: ~60 allowed, 90 blocked

### 3. Medium Attack
- 500 requests with 60 req/min limit
- Result: ~60 allowed, 440 blocked

### 4. Massive Attack
- 1000+ requests with 60 req/min limit
- Result: ~60 allowed, 940+ blocked

## Educational Benefits

✅ **Learn DDoS Prevention**: Understand how to protect against DDoS attacks
✅ **Algorithm Study**: Study token bucket algorithm implementation
✅ **Monitoring Skills**: Learn to track and analyze request patterns
✅ **Testing Practice**: Hands-on testing of rate limiting mechanisms
✅ **Real-world Application**: See production-like security measures

## Security Considerations

### Advantages
- Simple and efficient
- Allows controlled bursts
- Fair resource allocation
- Easy to implement

### Limitations
- Requires tracking per IP
- Memory overhead with many IPs
- Can be circumvented with distributed attacks
- Needs complementary measures (WAF, DDoS service)

### Best Practices
- Combine with other security measures
- Adjust limits based on use case
- Monitor and log all blocks
- Use in conjunction with WAF
- Implement feedback mechanisms

## Integration with Other Features

- **XSS Protection**: Rate limit sensitive endpoints
- **CSRF Protection**: Limit form submission endpoints
- **Session Management**: Protect session endpoints
- **API Endpoints**: Universal request limiting

## Future Enhancements

- [ ] User-based rate limiting (authenticated users)
- [ ] Endpoint-specific limits
- [ ] Geographic IP blocking
- [ ] Machine learning-based threat detection
- [ ] Redis-based distributed rate limiting
- [ ] Whitelist/blacklist management
- [ ] Advanced analytics dashboard

## Security Notes

⚠️ **Important**:
- Rate limiting alone is not complete DDoS protection
- Should be combined with other security measures (WAF, CDN, etc.)
- Monitor and adjust limits regularly
- Log all rate limit violations
- Never store sensitive data in rate limiter
- Test thoroughly before production deployment

## Dependencies

- Spring Boot 3.x
- Jakarta Servlet API
- Recharts (for frontend visualization)
- Axios (for API calls)

## File Structure

```
SecureScape/
├── backend/
│   └── src/main/java/com/securescape/ratelimiter/
│       ├── RateLimiterService.java
│       ├── config/
│       │   └── RateLimiterConfig.java
│       ├── filter/
│       │   └── RateLimiterFilter.java
│       └── controller/
│           └── RateLimiterController.java
├── frontend/
│   └── src/pages/
│       └── RateLimiter.jsx
└── docs/
    └── RATE_LIMITER.md
```

## References

- [Token Bucket Algorithm](https://en.wikipedia.org/wiki/Token_bucket)
- [OWASP DDoS Protection](https://owasp.org/www-community/attacks/Denial_of_Service)
- [HTTP 429 Status Code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429)
- [Rate Limiting Best Practices](https://cloud.google.com/architecture/rate-limiting-strategies-techniques)
