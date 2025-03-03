# Cursor Rules for Secure Coding

## Overview

This document outlines security-focused rules for implementation in a code editor cursor system. These rules automatically detect and highlight potential security vulnerabilities as developers write code, providing real-time guidance on secure coding practices.

## Table of Contents

1. [Input Validation](#1-input-validation)
2. [Output Encoding](#2-output-encoding)
3. [Authentication & Password Management](#3-authentication--password-management)
4. [Session Management](#4-session-management)
5. [Access Control](#5-access-control)
6. [Cryptographic Practices](#6-cryptographic-practices)
7. [Error Handling & Logging](#7-error-handling--logging)
8. [Memory Management](#8-memory-management)
9. [General Coding Practices](#9-general-coding-practices)
10. [File Management](#10-file-management)
11. [API Security](#11-api-security)
12. [Container & Infrastructure Security](#12-container--infrastructure-security)
13. [Dependency Management](#13-dependency-management)
14. [Framework-Specific Rules](#14-framework-specific-rules)

---

## 1. Input Validation

### 1.1 Whitelist Validation
- **Rule**: Enforce validation against whitelists of allowed characters, types, and lengths
- **Severity**: High
- **Detection**: Flag unvalidated inputs in sensitive contexts (queries, commands, etc.)
- **Suggestion**: Recommend parameterized queries or input sanitization libraries
- **Example**:
  ```
  🚫 query = "SELECT * FROM users WHERE name = '" + userName + "'";
  ✅ query = "SELECT * FROM users WHERE name = ?"; 
      preparedStatement.setString(1, userName);
  ```

### 1.2 Type & Length Validation
- **Rule**: Verify input data types and lengths match expected values
- **Severity**: Medium
- **Detection**: Flag implicit type conversions or missing length checks
- **Suggestion**: Suggest explicit type checking and length validation
- **Example**:
  ```
  🚫 userId = request.getParameter("id");
  ✅ userId = validateId(request.getParameter("id"));
  ```

### 1.3 Centralized Validation
- **Rule**: Ensure validation logic is centralized, not duplicated
- **Severity**: Medium
- **Detection**: Identify multiple implementations of similar validation logic
- **Suggestion**: Recommend refactoring to centralized validation functions
- **Example**:
  ```
  🚫 // Validation scattered across multiple files/functions
  ✅ // Using centralized InputValidator.validate() 
  ```

---

## 2. Output Encoding

### 2.1 Contextual Encoding
- **Rule**: Apply context-appropriate encoding for all output
- **Severity**: High
- **Detection**: Flag unencoded data in HTML, JavaScript, CSS, or URL contexts
- **Suggestion**: Recommend appropriate encoding function for the context
- **Example**:
  ```
  🚫 element.innerHTML = userInput;
  ✅ element.textContent = userInput; // or HTML encoding for innerHTML
  ```

### 2.2 Output Sanitization
- **Rule**: Sanitize all untrusted data before rendering
- **Severity**: High
- **Detection**: Flag direct insertion of user data into output
- **Suggestion**: Suggest sanitization libraries or encoding functions
- **Example**:
  ```
  🚫 document.write("<script>" + userInput + "</script>");
  ✅ document.write("<script>" + sanitizeJsString(userInput) + "</script>");
  ```

---

## 3. Authentication & Password Management

### 3.1 Secure Password Storage
- **Rule**: Use strong, adaptive hashing algorithms with salts
- **Severity**: Critical
- **Detection**: Flag weak hashing (MD5, SHA1) or missing salts
- **Suggestion**: Recommend bcrypt, Argon2, or PBKDF2
- **Example**:
  ```
  🚫 hashedPassword = md5(password);
  ✅ hashedPassword = bcrypt.hash(password, saltRounds);
  ```

### 3.2 Multi-Factor Authentication
- **Rule**: Implement MFA for sensitive operations
- **Severity**: High
- **Detection**: Flag authentication flows without MFA options
- **Suggestion**: Suggest adding MFA through TOTP or other methods
- **Example**:
  ```
  🚫 function login(username, password) { ... }
  ✅ function login(username, password, mfaToken) { ... }
  ```

### 3.3 Brute Force Protection
- **Rule**: Implement account lockout or rate limiting
- **Severity**: High
- **Detection**: Flag authentication without failure limits
- **Suggestion**: Suggest rate limiting or temporary lockout mechanisms
- **Example**:
  ```
  🚫 // No limit on authentication attempts
  ✅ if (failedAttempts > MAX_ATTEMPTS) { implementTemporaryLock(); }
  ```

---

## 4. Session Management

### 4.1 Secure Session IDs
- **Rule**: Generate unpredictable session identifiers
- **Severity**: High
- **Detection**: Flag weak session ID generation algorithms
- **Suggestion**: Recommend cryptographically secure random functions
- **Example**:
  ```
  🚫 sessionId = userId + timestamp;
  ✅ sessionId = generateSecureRandom(32);
  ```

### 4.2 Session Timeout
- **Rule**: Implement appropriate session expiration
- **Severity**: Medium
- **Detection**: Flag missing session timeout or excessive durations
- **Suggestion**: Recommend 15-30 minute idle timeouts for sensitive applications
- **Example**:
  ```
  🚫 session.setTimeout(-1); // Infinite timeout
  ✅ session.setTimeout(1800); // 30 minutes
  ```

### 4.3 Secure Cookie Attributes
- **Rule**: Set appropriate security attributes on cookies
- **Severity**: High
- **Detection**: Flag cookies without Secure, HttpOnly, or SameSite attributes
- **Suggestion**: Recommend adding missing security attributes
- **Example**:
  ```
  🚫 setCookie("session", sessionId);
  ✅ setCookie("session", sessionId, {httpOnly: true, secure: true, sameSite: "strict"});
  ```

---

## 5. Access Control

### 5.1 Least Privilege Principle
- **Rule**: Enforce minimum necessary permissions
- **Severity**: High
- **Detection**: Flag over-privileged operations or roles
- **Suggestion**: Recommend more granular permission checks
- **Example**:
  ```
  🚫 if (user.isAuthenticated()) { // Allow any authenticated user
  ✅ if (user.hasPermission("document:edit")) { // Specific permission check
  ```

### 5.2 Server-Side Verification
- **Rule**: Verify all access controls on the server
- **Severity**: Critical
- **Detection**: Flag client-only access controls
- **Suggestion**: Recommend adding server-side verification
- **Example**:
  ```
  🚫 // Client-side check only
      if (isAdmin) { showAdminPanel(); }
  ✅ // Server verifies before sending sensitive data
      if (await verifyPermissionOnServer()) { showAdminPanel(); }
  ```

### 5.3 Insecure Direct Object References
- **Rule**: Prevent unauthorized access to resources via IDs
- **Severity**: High
- **Detection**: Flag direct use of user-supplied IDs without access checks
- **Suggestion**: Recommend access control checks for all resource access
- **Example**:
  ```
  🚫 document = db.getDocument(request.params.docId);
  ✅ if (userCanAccess(user, request.params.docId)) {
        document = db.getDocument(request.params.docId);
      }
  ```

---

## 6. Cryptographic Practices

### 6.1 Strong Algorithms
- **Rule**: Use modern, strong cryptographic algorithms
- **Severity**: Critical
- **Detection**: Flag weak or deprecated algorithms (DES, RC4, etc.)
- **Suggestion**: Recommend AES-256, ChaCha20, RSA-2048+, or ECC
- **Example**:
  ```
  🚫 cipher = Crypto.createCipher('des', key);
  ✅ cipher = Crypto.createCipheriv('aes-256-gcm', key, iv);
  ```

### 6.2 Secure Random Generation
- **Rule**: Use cryptographically secure random number generators
- **Severity**: High
- **Detection**: Flag insecure random functions (Math.random(), rand())
- **Suggestion**: Recommend crypto.getRandomValues() or similar
- **Example**:
  ```
  🚫 randomValue = Math.random();
  ✅ randomValue = crypto.getRandomValues(new Uint8Array(32));
  ```

### 6.3 Key Management
- **Rule**: Properly store and rotate cryptographic keys
- **Severity**: Critical
- **Detection**: Flag hardcoded keys or insecure storage
- **Suggestion**: Recommend key management systems or environment variables
- **Example**:
  ```
  🚫 const ENCRYPTION_KEY = "ABC123"; // Hardcoded
  ✅ const ENCRYPTION_KEY = process.env.ENCRYPTION_KEY;
  ```

---

## 7. Error Handling & Logging

### 7.1 Generic Error Messages
- **Rule**: Return non-specific error messages to users
- **Severity**: Medium
- **Detection**: Flag detailed error exposure to clients
- **Suggestion**: Recommend generic messages with internal logging
- **Example**:
  ```
  🚫 return res.status(500).send(`Database error: ${err.message}`);
  ✅ logger.error(`Database error: ${err.message}`);
      return res.status(500).send("An internal error occurred");
  ```

### 7.2 Sensitive Data Logging
- **Rule**: Never log sensitive information
- **Severity**: High
- **Detection**: Flag logging of passwords, tokens, PII, etc.
- **Suggestion**: Recommend redacting sensitive fields
- **Example**:
  ```
  🚫 logger.info(`User login: ${username}, password: ${password}`);
  ✅ logger.info(`User login: ${username}`);
  ```

### 7.3 Proper Exception Handling
- **Rule**: Catch and handle all exceptions appropriately
- **Severity**: Medium
- **Detection**: Flag unhandled exceptions or overly broad catches
- **Suggestion**: Recommend specific exception handling
- **Example**:
  ```
  🚫 try { ... } catch (e) { } // Silent catch
  ✅ try { ... } catch (DatabaseError e) { ... } catch (AuthError e) { ... }
  ```

---

## 8. Memory Management

### 8.1 Buffer Overflow Prevention
- **Rule**: Validate buffer sizes and avoid unsafe functions
- **Severity**: Critical
- **Detection**: Flag unsafe buffer operations (strcpy, gets, etc.)
- **Suggestion**: Recommend safe alternatives with bounds checking
- **Example**:
  ```
  🚫 strcpy(dest, src);
  ✅ strncpy(dest, src, destSize - 1);
      dest[destSize - 1] = '\0';
  ```

### 8.2 Memory Cleanup
- **Rule**: Properly free allocated memory
- **Severity**: High
- **Detection**: Flag memory allocations without corresponding deallocations
- **Suggestion**: Recommend using RAII or smart pointers where appropriate
- **Example**:
  ```
  🚫 char* buffer = (char*)malloc(100); // No free()
  ✅ char* buffer = (char*)malloc(100);
      // ... use buffer ...
      free(buffer);
      buffer = NULL;
  ```

### 8.3 Integer Overflow Checks
- **Rule**: Validate arithmetic operations to prevent overflows
- **Severity**: High
- **Detection**: Flag unchecked integer operations
- **Suggestion**: Recommend safe integer handling with bounds checks
- **Example**:
  ```
  🚫 size_t newSize = count * elementSize; // Might overflow
  ✅ if (elementSize && count > SIZE_MAX / elementSize) {
        // Handle overflow error
      }
      size_t newSize = count * elementSize;
  ```

---

## 9. General Coding Practices

### 9.1 Avoid Dangerous Functions
- **Rule**: Avoid known dangerous functions
- **Severity**: High
- **Detection**: Flag use of functions with known security issues
- **Suggestion**: Recommend safer alternatives
- **Example**:
  ```
  🚫 system(userInput);
  ✅ // Use safer APIs or properly sanitize input
  ```

### 9.2 Initialization
- **Rule**: Initialize all variables before use
- **Severity**: Medium
- **Detection**: Flag uninitialized variables
- **Suggestion**: Recommend explicit initialization
- **Example**:
  ```
  🚫 int id; // Used without initialization
  ✅ int id = 0; // Explicit initialization
  ```

### 9.3 Code Injection Prevention
- **Rule**: Prevent all forms of code injection
- **Severity**: Critical
- **Detection**: Flag dynamic code execution with user input
- **Suggestion**: Recommend safer alternatives to eval() and similar functions
- **Example**:
  ```
  🚫 eval(userInput);
  ✅ // Use safer alternatives like JSON.parse() or validated templates
  ```

---

## 10. File Management

### 10.1 File Upload Validation
- **Rule**: Validate file types, sizes, and contents
- **Severity**: High
- **Detection**: Flag file uploads without proper validation
- **Suggestion**: Recommend content type verification and size limits
- **Example**:
  ```
  🚫 file.save(uploadPath + fileName);
  ✅ if (validateFileType(file) && file.size < MAX_SIZE) {
        file.save(uploadPath + sanitizedFileName);
      }
  ```

### 10.2 Safe Storage Location
- **Rule**: Store uploaded files outside the web root
- **Severity**: Medium
- **Detection**: Flag file storage in web-accessible directories
- **Suggestion**: Recommend storage in protected directories
- **Example**:
  ```
  🚫 const path = "./public/uploads/" + filename;
  ✅ const path = "../protected_storage/" + filename;
  ```

### 10.3 Path Traversal Prevention
- **Rule**: Prevent path traversal attacks
- **Severity**: High
- **Detection**: Flag unsanitized file paths
- **Suggestion**: Recommend path normalization and validation
- **Example**:
  ```
  🚫 readFile(userSuppliedPath);
  ✅ const safePath = path.normalize(userSuppliedPath).replace(/^(\.\.[\/\\])+/, '');
      readFile(safePath);
  ```

---

## 11. API Security

### 11.1 Rate Limiting
- **Rule**: Implement rate limiting for API endpoints
- **Severity**: Medium
- **Detection**: Flag endpoints without rate limiting
- **Suggestion**: Recommend rate limiting middleware
- **Example**:
  ```
  🚫 app.post('/api/login', loginHandler);
  ✅ app.post('/api/login', rateLimiter, loginHandler);
  ```

### 11.2 JWT Security
- **Rule**: Properly secure and validate JWTs
- **Severity**: High
- **Detection**: Flag insecure JWT practices
- **Suggestion**: Recommend proper signing, expiration, and validation
- **Example**:
  ```
  🚫 jwt.sign({ userId }, 'secret');
  ✅ jwt.sign({ userId, exp: Math.floor(Date.now()/1000) + 3600 }, process.env.JWT_SECRET);
  ```

### 11.3 CORS Configuration
- **Rule**: Implement secure CORS policies
- **Severity**: Medium
- **Detection**: Flag overly permissive CORS settings
- **Suggestion**: Recommend specific origin restrictions
- **Example**:
  ```
  🚫 app.use(cors({ origin: '*' }));
  ✅ app.use(cors({ origin: 'https://example.com', credentials: true }));
  ```

---

## 12. Container & Infrastructure Security

### 12.1 Secure Docker Configuration
- **Rule**: Follow Docker security best practices
- **Severity**: High
- **Detection**: Flag insecure Docker configurations
- **Suggestion**: Recommend principle of least privilege
- **Example**:
  ```
  🚫 FROM ubuntu
      RUN apt-get update && apt-get install -y ...
      USER root
  ✅ FROM ubuntu
      RUN apt-get update && apt-get install -y ... && rm -rf /var/lib/apt/lists/*
      USER non-root-user
  ```

### 12.2 Secret Management
- **Rule**: Secure handling of secrets in infrastructure
- **Severity**: Critical
- **Detection**: Flag hardcoded secrets in infrastructure code
- **Suggestion**: Recommend secret management solutions
- **Example**:
  ```
  🚫 password: "admin123" # Hardcoded in YAML
  ✅ password: ${SECRET_MANAGER:db_password} # Reference to secret manager
  ```

### 12.3 Infrastructure as Code Validation
- **Rule**: Validate IaC templates for security issues
- **Severity**: High
- **Detection**: Flag insecure cloud resource configurations
- **Suggestion**: Recommend secure defaults and least privilege
- **Example**:
  ```
  🚫 "Effect": "Allow", "Action": "*", "Resource": "*"
  ✅ "Effect": "Allow", "Action": ["s3:GetObject"], "Resource": ["arn:aws:s3:::mybucket/*"]
  ```

---

## 13. Dependency Management

### 13.1 Vulnerable Dependencies
- **Rule**: Avoid dependencies with known vulnerabilities
- **Severity**: High
- **Detection**: Flag imports of packages with CVEs
- **Suggestion**: Recommend updating to secure versions
- **Example**:
  ```
  🚫 "dependencies": { "log4j": "2.14.0" }
  ✅ "dependencies": { "log4j": "2.17.1" }
  ```

### 13.2 Minimal Dependencies
- **Rule**: Minimize third-party dependencies
- **Severity**: Low
- **Detection**: Flag excessive dependencies
- **Suggestion**: Recommend evaluating necessity of each dependency
- **Example**:
  ```
  🚫 // Importing a large library for a simple function
  ✅ // Using built-in functionality or smaller package
  ```

### 13.3 Lock Files
- **Rule**: Use dependency lock files
- **Severity**: Medium
- **Detection**: Flag missing lock files
- **Suggestion**: Recommend generating and committing lock files
- **Example**:
  ```
  🚫 // No package-lock.json or yarn.lock
  ✅ // Including package-lock.json or yarn.lock in the repository
  ```

---

## 14. Framework-Specific Rules

### 14.1 React XSS Prevention
- **Rule**: Prevent XSS in React applications
- **Severity**: High
- **Detection**: Flag use of dangerouslySetInnerHTML without sanitization
- **Suggestion**: Recommend using React's escaping mechanisms
- **Example**:
  ```
  🚫 <div dangerouslySetInnerHTML={{ __html: userInput }} />
  ✅ <div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userInput) }} />
  ```

### 14.2 SQL ORM Safety
- **Rule**: Use ORMs safely to prevent SQL injection
- **Severity**: High
- **Detection**: Flag raw queries with user input
- **Suggestion**: Recommend parameterized ORM methods
- **Example**:
  ```
  🚫 const users = await User.query(`SELECT * FROM users WHERE name LIKE '%${input}%'`);
  ✅ const users = await User.findAll({ where: { name: { [Op.like]: `%${input}%` } } });
  ```

### 14.3 Express Security Headers
- **Rule**: Set security headers in Express applications
- **Severity**: Medium
- **Detection**: Flag missing security headers
- **Suggestion**: Recommend using Helmet.js or similar
- **Example**:
  ```
  🚫 const app = express();
      app.use(/* no security headers */);
  ✅ const app = express();
      app.use(helmet());
  ```

---

## Implementation Levels

Rules can be implemented at different levels of strictness:

1. **Info**: Informational messages about best practices
2. **Warning**: Potential issues that should be reviewed
3. **Error**: Critical issues that should be fixed
4. **Fatal**: Issues that block code submission

## Rule Configuration

```json
{
  "ruleSets": {
    "default": ["input-validation", "output-encoding", "auth", "crypto"],
    "enterprise": ["all"],
    "hipaa": ["auth", "crypto", "logging", "access-control"],
    "pci-dss": ["input-validation", "output-encoding", "auth", "crypto", "logging"]
  },
  "severityOverrides": {
    "api-rate-limiting": "high",
    "jwt-expiration": "critical"
  }
}
```

## Integration with Security Tools

The rules system should integrate with:

- Static Analysis Security Testing (SAST) tools
- Software Composition Analysis (SCA) tools
- CVE databases
- OWASP Top 10 and SANS Top 25 references

## Machine Learning Enhancement

These rules can be augmented with machine learning to:

- Detect patterns not covered by explicit rules
- Reduce false positives based on developer feedback
- Identify project-specific security patterns
- Suggest context-aware improvements
