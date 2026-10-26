# Exercise 00 — Server-Side Template Injection (SSTI)

## 1. Vulnerability Overview

Server-Side Template Injection (SSTI) occurs when an application incorporates untrusted user input into a server-side template as template source code instead of treating it as ordinary data.

In this exercise, the application uses Flask with the Jinja2 template engine. The vulnerable implementation constructs a template using user-controlled input and passes it to `render_template_string()`.

## 2. Root Cause

The application retrieves input from the HTTP POST request and inserts it into a template string:

```python
user_input = request.form.get('input')
template = f'Hello, {{ {user_input} }}!'
result = render_template_string(template)
```

Because user input becomes part of the template source, the template engine may evaluate expressions supplied by the user.

## 3. Detection and Impact

Arithmetic expressions were used to verify whether user input was evaluated by the template engine. The successful evaluation indicated a potential SSTI vulnerability.

Further testing in the provided Docker lab demonstrated file disclosure and server-side command execution.

The observed impact included reading `/etc/passwd` and executing a command within the application container. These results demonstrate that the vulnerability can have a critical impact depending on the application's permissions and environment.

## 4. OWASP Classification

The vulnerability is Server-Side Template Injection (SSTI). It is associated with injection risks and can lead to server-side code execution when the template engine and application context expose dangerous capabilities.

## 5. Potential Exploitation Scenarios

### Scenario 1: Sensitive File Disclosure

An attacker may exploit SSTI to read files accessible to the application process. Depending on file permissions, this could expose system account information, application configuration, or secrets.

### Scenario 2: Server-Side Command Execution

If the template context exposes functionality that allows operating-system command execution, an attacker may execute commands with the privileges of the application process. This can compromise application data and services.

## 6. Conclusion

The root cause is the use of untrusted input as template source code. User input should be passed as data to a fixed template, and dangerous capabilities should not be exposed to the template context.

## References

- OWASP: https://owasp.org/
- PortSwigger Web Security Academy — Server-side template injection: https://portswigger.net/web-security/server-side-template-injection
