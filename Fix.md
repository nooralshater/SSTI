# Exercise 00 — Fixing Server-Side Template Injection (SSTI)

## 1. Vulnerability Overview

The application is vulnerable to Server-Side Template Injection (SSTI) because it inserts user-controlled input into a template string and then evaluates that string using Jinja2.

The vulnerable code is:

```python
user_input = request.form.get('input')
template = f'Hello, {{ {user_input} }}!'
result = render_template_string(template)
```

The problem is that user input becomes part of the template source code. As a result, Jinja2 may evaluate expressions supplied by the user instead of treating them as ordinary text.

## 2. Recommended Fix

The main solution is to separate the template source code from user input. Use a fixed template and pass the submitted value as a variable.

### Secure implementation

```python
@app.route('/render', methods=['POST'])
def render():
    user_input = request.form.get('input', '')

    result = render_template_string(
        'Hello, {{ user_input }}!',
        user_input=user_input
    )

    return render_template_string(index(), result=result)
```

In this implementation, the template is defined by the application, and the user's input is passed as data.

For example, if a user submits `{{ 7 * 7 }}`, the application should display the expression as text instead of evaluating it to `49`.

## 3. Additional Security Measures

- **Use fixed templates:** Never concatenate untrusted user input into template source code.
- **Use template files:** Prefer Flask's `render_template()` with predefined templates stored in a `templates/` directory.
- **Apply least privilege:** Run the application with only the permissions it requires.
- **Restrict template context:** Avoid exposing unnecessary objects or functions that could provide access to sensitive operations.
- **Protect sensitive files:** Limit access to application secrets and system files.
- **Secure the container:** Use appropriate Docker isolation and avoid running the application as root.
- **Keep dependencies updated:** Apply security updates to Flask, Jinja2, and related packages.

## 4. Testing the Fix

After applying the change, restart the training application and test the input field with an arithmetic template expression.

Expected behavior: the submitted expression is displayed as plain text and is not evaluated by the server.

Also verify that ordinary input, such as `Hello Noor`, continues to work correctly.

## 5. Conclusion

The root cause of SSTI is the interpretation of untrusted input as template source code. The primary fix is to use a predefined template and pass user input as data. Additional security controls, including least privilege and container isolation, help reduce the impact of other vulnerabilities but do not replace fixing the underlying issue.

## References

- OWASP: https://owasp.org/
- PortSwigger Web Security Academy — Server-side template injection: https://portswigger.net/web-security/server-side-template-injection
- Flask Documentation — Templates: https://flask.palletsprojects.com/en/stable/templating/
