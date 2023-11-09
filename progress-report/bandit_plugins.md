# Identifying the important plugins in Bandit
Bandit is used as a command-line tool, and you can specify which plugins to use when running it. Here's the syntax of how to use Bandit with specific plugins using the command line.
bandit -r /path/to/your/python/code -s <plugin_name>

Bandit also supports third-party plugins. This allows users to extend the functionality of Bandit to check for additional types of vulnerabilities. Some popular third-party plugins for Bandit include:
•	bandit-blacklists - This plugin allows users to blacklist specific modules, functions, and classes. This can be useful for preventing the use of known insecure libraries or frameworks.
•	bandit-security-headers - This plugin checks for the presence of important security headers in HTTP responses. These headers can help to protect against a variety of attacks, such as cross-site scripting and clickjacking.
•	bandit-vulnerabilities - This plugin checks for known vulnerabilities in Python code. It uses a database of known vulnerabilities to identify potential security risks.

Third-party plugins can be installed from the Python Package Index (PyPI) using the pip command. For example, to install the bandit-blacklists plugin, you would run the following command:
pip install bandit-blacklists

Once a third-party plugin is installed, it can be enabled by adding it to the plugins configuration option in the Bandit configuration file.
By using the built-in and third-party plugins for Bandit, you can help to identify and fix security vulnerabilities in your Python code.

**B201: Test for use of flask app with debug set to true (app_debug.py)**
Enabling debug mode when running Flask applications activates the Werkzeug debugger, which, in turn, enables a functionality allowing for the execution of arbitrary code. The documentation for both Flask [1]_ and Werkzeug [2]_ strongly advises against enabling debug mode on production systems.
![1.png](images%2F1.png)
In the code above, we enable debug mode using app.debug = True. We also have a route that contains an unsafe use of eval, which is not recommended in real-world applications.

**B101 : Test for use of assert (file – asserts.py)** 
in Bandit looks for the use of the assert keyword in Python code. The assert statements are typically used for debugging. __debug__ and AssertionError are related to built-in variables and their behavior changes when code optimization is requested. Using assert can cause problems because it's removed when the code is optimized, potentially removing important security checks. It's better to use meaningful errors or AssertionError instead. You can configure Bandit to skip this check when necessary, especially in test cases.
![2.png](images%2F2.png)
In the code above, the assert statement is used to check if b is not zero before performing division. This is considered a poor practice because, as mentioned in the error message, assertions are removed when Python code is optimized, and the check for division by zero would be eliminated.
A better approach to handle this situation would be to raise an AssertionError or use explicit error handling, like so:
![3.png](images%2F3.png)
In the improved code, we raise a ZeroDivisionError when attempting to divide by zero, ensuring that the error is appropriately handled even in optimized code.

**B501 : Test for missing certificate validation (file-- crypto_request_no_cert_validation.py)**
checks for missing certificate validation, which is crucial for securing applications. TLS ensures the identity of parties in a connection by presenting trusted certificates. When using HTTPS, certificates are automatically validated, which is the expected behavior. Disabling certificate validation results in a HIGH severity error from Bandit.
![4.png](images%2F4.png)
In this code, we're using the requests library in Python to make an HTTPS request to "https://example.com". However, there is no certificate validation being performed. This means that if the server's SSL certificate cannot be trusted, the code will accept the response without verifying the server's identity, which is a security risk.

**B610 : Potential SQL injection on extra function (file -- django_sql_injection.py)**
is a Bandit plugin that checks for SQL injection vulnerabilities in Django projects where the extra parameter is used. It's used to identify potential security risks when untrusted data is incorporated into custom SQL expressions within Django queries, which can lead to SQL injection if not handled properly. To mitigate this risk, ensure data is sanitized and use parameterized queries to prevent SQL injection in Django applications. 
![5.png](images%2F5.png)
In the code above, we have a potential SQL injection vulnerability because the user input is directly interpolated into the SQL query string without proper sanitization or parameterization. This is dangerous because if an attacker provides malicious input, they could manipulate or even damage the database.

**B703: Potential XSS on mark_safe function (file -- django_xss.py)**
 error in Bandit warns of a security risk when using the mark_safe function in Django incorrectly. mark_safe is used to prevent HTML escaping in templates, but if untrusted data is marked as safe, it can lead to Cross-Site Scripting (XSS) vulnerabilities. To fix this, only use mark_safe with trusted content and ensure that untrusted data is properly sanitized and validated.
![6.png](images%2F6.png)
the code above, mark safe is applied to untrusted data without proper validation or sanitation. If user input contains malicious JavaScript, it would execute as a part of the page, leading to a potential XSS vulnerability.

**B102: Test for the use of exec (file -- exec.py)**
error in Bandit warns against using Python's exec because it can execute arbitrary code, posing a security risk. It's used for dynamic code execution but should be used cautiously, especially with untrusted or user-generated code, to prevent code injection vulnerabilities.
![7.png](images%2F7.png)
This can be extremely risky, especially if untrusted users can input code, as it can execute malicious or unintended commands. To avoid this risk, you should avoid using exec with untrusted or unsanitized inputs and explore safer alternatives for achieving the desired functionality.

**B507: Test for missing host key validation (file --ssh_no_host_key_verification.py)**
assesses the importance of host key validation in SSH connections, which is a fundamental aspect of security. SSH ensures the identity of the parties involved by employing trusted host keys during the connection setup. When working with Paramiko methods, host key verification is enabled by default. If you disable host key verification, Bandit will issue a HIGH severity warning.
![8.png](images%2F8.png)
In the code above, the set_missing_host_key_policy method is used with paramiko.AutoAddPolicy(), which disables host key verification. This is not recommended because it allows connections to SSH servers without verifying their host keys, which is a significant security risk.

**B105: Test for use of hard-coded password strings (file -- general_hardcoded_password.py)**
it is designed to identify the use of hard-coded password strings, which significantly raises the risk of password guessing attacks. This plugin scans for string literals under specific conditions, such as when they are assigned to variables, dictionary keys, class attributes, or used in comparisons that resemble password-related scenarios. 
![9.png](images%2F9.png)
In the code above, a hard-coded password string "mysecretpassword" is assigned to the variable password. This is a security risk because hard-coded passwords make it easy for attackers to discover the password and gain unauthorized access. The code then compares a user's input password with the hard-coded password, which is typically a poor practice.

**B505: Test for weak cryptographic key use (file -- weak_cryptographic_key.py)**
As computational power continues to advance, it becomes easier to break ciphers with shorter key lengths. To bolster security, it's advisable to use RSA and DSA key lengths of 2048 bits or more, as key lengths of 1024 bits and below are now considered vulnerable. For EC (Elliptic Curve) keys, a recommended minimum key length of 224 bits is suggested, as key lengths of 160 bits and below are no longer deemed secure. This plugin conducts checks to identify keys that fall short of these recommended thresholds. It issues a high severity error for keys below the lower threshold and a medium severity error for keys below the higher threshold. 
![10.png](images%2F10.png)
In the code above, cryptographic keys are generated with key lengths of 1024 bits, which are considered weak and insecure in today's computing environment. To address the "B505" error, it's important to use cryptographic keys with stronger key lengths (e.g., 2048 bits or more for RSA and DSA, 224 bits or more for EC) to enhance the security of your cryptographic operations. The exact code that triggers this error may vary depending on your specific use case and where you're using cryptographic keys.
