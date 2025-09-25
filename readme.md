# F5 SQL Injection Protection iRules

This repository contains two F5 iRules designed to protect web applications from SQL injection attacks. The rules are written in TCL (Tool Command Language) and can be deployed on F5 BIG-IP devices.

## 📋 Overview

### Available iRules

1. **`adv-sqli-rule`** - Advanced SQL Injection Protection
   - Comprehensive protection against SQL injection and XSS attacks
   - Multiple detection layers and pattern matching
   - Extensive logging capabilities

2. **`med-sqli-rule`** - Medium SQL Injection Protection
   - Basic SQL injection protection
   - Lightweight and performance-optimized
   - Essential pattern detection

## 🚀 Quick Start

### Prerequisites
- F5 BIG-IP device (version 11.x or higher)
- Administrative access to F5 Configuration Utility
- Virtual Server configured for your web application

### Installation Steps

1. **Access F5 Configuration Utility**
   ```
   https://<your-f5-ip>/
   ```

2. **Navigate to iRules**
   ```
   Local Traffic > iRules > iRule List
   ```

3. **Create New iRule**
   - Click "Create"
   - Name your iRule (e.g., "sql_injection_protection")
   - Copy and paste the desired iRule code
   - Click "Finished"

4. **Apply to Virtual Server**
   ```
   Local Traffic > Virtual Servers > Virtual Server List
   ```
   - Select your virtual server
   - Go to "Resources" tab
   - Add the iRule under "iRules"

## 📊 Feature Comparison

| Feature | Advanced Rule | Medium Rule |
|---------|--------------|-------------|
| **File Size** | ~5.4 KB | ~1.4 KB |
| **Pattern Count** | 48+ patterns | 7 patterns |
| **URI Decoding** | ✅ Yes | ❌ No |
| **POST Data Inspection** | ✅ Yes (up to 1MB) | ✅ Basic |
| **Header Inspection** | ✅ All headers | ❌ No |
| **Cookie Inspection** | ✅ Yes | ❌ No |
| **XSS Protection** | ✅ Yes | ❌ No |
| **Debug Logging** | ✅ Configurable | ✅ Always on |
| **Response Action** | Drop connection | Reject (RST) |
| **Performance Impact** | Higher | Lower |

## 🔍 Detection Patterns

### Advanced Rule Coverage
- **SQL Keywords**: SELECT, INSERT, DELETE, DROP, UPDATE, UNION, EXEC, etc.
- **SQL Functions**: CAST(), CONVERT(), CONCAT(), CHAR(), etc.
- **Meta Characters**: Single quotes, comments (--), (/* */), hex encoding
- **XSS Patterns**: script tags, javascript:, event handlers
- **Encoded Attacks**: URL-encoded SQL injection attempts
- **Common Exploits**: 1=1, 1'='1, waitfor delay, sleep()

### Medium Rule Coverage
- **Basic SQL Injection**: union select, or 1=1, drop table
- **Function Calls**: exec(), cast()
- **Comment Injection**: ; --
- **Simple Boolean**: 'or 1=1

## ⚙️ Configuration

### Advanced Rule Configuration

```tcl
# Enable/Disable debug logging
set debug 1    # Set to 0 to disable logging
```

### Logging

Both rules log detected attacks to the local0 facility. View logs:
```bash
tail -f /var/log/ltm
```

Example log entries:
```
SQL Injection detected in URI from 10.0.0.100: /login?id=1' or '1'='1
SQLi detected in POST: username=admin&password=test' OR '1'='1
```

## 🎯 Use Case Recommendations

### When to Use Advanced Rule
- **Public-facing applications**: E-commerce, banking, healthcare
- **Compliance requirements**: PCI-DSS, HIPAA, SOC2
- **High-risk environments**: Applications handling sensitive data
- **Comprehensive logging needs**: Security audit requirements
- **Multi-vector protection**: Need protection beyond basic SQL injection

### When to Use Medium Rule
- **Internal applications**: Intranet sites, admin panels
- **Performance-critical**: High-traffic applications with resource constraints
- **Basic protection**: Applications with limited attack surface
- **Quick deployment**: Need immediate basic protection
- **Lower-risk environments**: Development or staging environments

## 🔧 Customization

### Adding Custom Patterns

For the advanced rule:
```tcl
# Add to sql_keywords list
set sql_keywords {
    # ... existing patterns ...
    "your_custom_pattern"
}
```

For the medium rule:
```tcl
# Add to sql_patterns list
set sql_patterns {
    # ... existing patterns ...
    {(?i)your_pattern_here}
}
```

### Modifying Response Actions

Advanced rule (change from drop to reject):
```tcl
# Replace: drop
# With:    reject
```

Medium rule (change from reject to drop):
```tcl
# Replace: reject
# With:    drop
```

## ⚠️ Important Considerations

### False Positives
- Both rules may block legitimate requests containing SQL-like patterns
- Test thoroughly in non-production environments
- Consider whitelisting specific URIs if needed

### Performance Impact
- **Advanced Rule**: ~5-10ms additional latency per request
- **Medium Rule**: ~1-3ms additional latency per request
- Monitor CPU usage on high-traffic sites

### Limitations
- Not a replacement for secure coding practices
- Should be used as part of defense-in-depth strategy
- Cannot protect against all SQL injection variants
- Does not protect against second-order SQL injection

## 🧪 Testing

### Test SQL Injection Detection
```bash
# Test basic SQL injection
curl "http://your-site.com/page?id=1' OR '1'='1"

# Test union select
curl "http://your-site.com/page?id=1 UNION SELECT * FROM users"

# Test encoded attack (advanced rule only)
curl "http://your-site.com/page?id=1%27%20OR%20%271%27%3D%271"

# Test POST data
curl -X POST -d "username=admin' OR '1'='1" http://your-site.com/login
```

### Verify Protection
1. Check F5 logs for detection messages
2. Confirm requests are blocked (connection drop or reset)
3. Verify legitimate traffic passes through

## 🔄 Updates and Maintenance

### Regular Updates Recommended
- Review logs monthly for false positives
- Update patterns based on new attack vectors
- Test after F5 system updates
- Document any customizations

### Version History
- Initial release: Basic SQL injection patterns
- Current version: Enhanced with XSS protection and encoding detection

## 📚 Additional Resources

- [F5 iRules Documentation](https://techdocs.f5.com/en-us/bigip-15-1-0/big-ip-local-traffic-management-irules.html)
- [OWASP SQL Injection Prevention](https://owasp.org/www-community/attacks/SQL_Injection)
- [F5 DevCentral Community](https://community.f5.com/)

## 🤝 Contributing

To contribute improvements:
1. Test thoroughly in a lab environment
2. Document new patterns and their purpose
3. Include performance impact analysis
4. Submit with clear description of changes

## 📄 License

These iRules are provided as-is for educational and protection purposes. Test thoroughly before production deployment.

## 🆘 Support

For issues and questions:
- Check F5 DevCentral forums
- Review F5 official documentation
- Consult with F5 support for production deployments

---

**Remember**: These iRules are one layer of defense. Always follow secure coding practices and perform regular security assessments.
