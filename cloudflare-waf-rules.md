# Cloudflare WAF Rules for rblez terminal

## Custom WAF Rules

### 1. Rate Limiting Rules
```
Rule: Terminal API Rate Limit
- Path: /*
- Rate: 100 requests per minute
- Action: Challenge
- Description: Prevent abuse of terminal commands
```

### 2. SQL Injection Protection
```
Rule: Block SQL Injection Attempts
- Expression: (http.request.uri contains "SELECT" or "INSERT" or "DELETE" or "DROP" or "UNION") and (http.request.uri contains "'" or '"' or ";" or "--")
- Action: Block
- Description: Prevent SQL injection attempts
```

### 3. XSS Protection
```
Rule: Block XSS Attempts
- Expression: http.request.uri contains "<script" or "javascript:" or "onload=" or "onerror="
- Action: Block
- Description: Prevent cross-site scripting attacks
```

### 4. Bot Protection
```
Rule: Block Malicious Bots
- Expression: (cf.bot_management.score lt 30) and (not cf.bot_management.verified_bot)
- Action: Block
- Description: Block low-score bots
```

### 5. Geographic Rules (Optional)
```
Rule: Restrict High-Risk Countries
- Expression: ip.geoip.country in {"CN", "RU", "KP", "IR"}
- Action: Challenge
- Description: Additional protection from certain regions
```

## Page Rules Configuration

### 1. Cache Static Assets
```
Pattern: *.(css|js|png|jpg|jpeg|webp|ico|svg)
Settings:
- Cache Level: Cache Everything
- Edge Cache TTL: 1 month
- Browser Cache TTL: 4 hours
```

### 2. Security Headers
```
Pattern: rblez.com/*
Settings:
- Security Level: High
- SSL: Full (Strict)
- HSTS: Enable
- Automatic HTTPS Rewrites: On
```

## Transform Rules

### 1. Security Headers
```javascript
// Add security headers
response.headers.set("X-Frame-Options", "DENY");
response.headers.set("X-Content-Type-Options", "nosniff");
response.headers.set("X-XSS-Protection", "1; mode=block");
response.headers.set("Referrer-Policy", "strict-origin-when-cross-origin");
response.headers.set("Content-Security-Policy", "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; font-src 'self'; connect-src 'self';");
```

### 2. CORS Headers
```javascript
// Set CORS headers
response.headers.set("Access-Control-Allow-Origin", "https://rblez.com");
response.headers.set("Access-Control-Allow-Methods", "GET, POST, OPTIONS");
response.headers.set("Access-Control-Allow-Headers", "Content-Type");
```

## DNS Settings

### 1. DNS Records
```
Type: A
Name: @
Content: 192.0.2.1 (your server IP)
Proxy: Enabled (Orange cloud)

Type: CNAME
Name: www
Content: rblez.com
Proxy: Enabled (Orange cloud)
```

### 2. Additional Records
```
Type: TXT
Name: @
Content: "v=spf1 include:_spf.google.com ~all"

Type: TXT
Name: _dmarc
Content: "v=DMARC1; p=quarantine; rua=mailto:dmarc@rblez.com"
```

## SSL/TLS Configuration

### 1. SSL/TLS Settings
```
- SSL/TLS: Full (Strict)
- Minimum TLS Version: 1.2
- Opportunistic Encryption: On
- TLS 1.3: On
- HSTS: Enable with max-age=31536000
```

### 2. Certificate Management
```
- Always Use HTTPS: On
- Automatic HTTPS Rewrites: On
- Certificate Transparency Monitoring: On
```

## Firewall Rules

### 1. IP Blocking
```
Rule: Block Known Malicious IPs
- Expression: ip.src in {192.0.2.0/24, 203.0.113.0/24}
- Action: Block
- Description: Block known malicious IP ranges
```

### 2. User Agent Protection
```
Rule: Block Suspicious User Agents
- Expression: http.user_agent contains "bot" or "crawler" or "scanner" or "exploit"
- Action: Challenge
- Description: Challenge suspicious user agents
```

## Bot Fight Mode

### 1. Bot Fight Mode Settings
```
- Bot Fight Mode: On
- Super Bot Fight Mode: On
- Automatically Mitigate Bots: On
```

## DDoS Protection

### 1. DDoS Settings
```
- HTTP DDoS Mitigation: On
- Network DDoS Mitigation: On
- Advanced DDoS Protection: On
```

## Analytics and Monitoring

### 1. Analytics Settings
```
- Web Analytics: On
- Bot Analytics: On
- Security Events: On
- Rate Limiting Analytics: On
```

## Implementation Steps

1. **Set up Cloudflare account** and add your domain
2. **Update DNS servers** to point to Cloudflare
3. **Configure DNS records** with proxy enabled
4. **Set up SSL/TLS** with Full (Strict) mode
5. **Create WAF rules** using the expressions above
6. **Configure page rules** for caching and security
7. **Set up transform rules** for security headers
8. **Enable Bot Fight Mode** and DDoS protection
9. **Test configuration** and monitor analytics
10. **Fine-tune rules** based on traffic patterns

## Monitoring

Regularly check:
- Security Events dashboard
- Rate limiting logs
- Bot analytics
- Firewall events
- SSL/TLS analytics