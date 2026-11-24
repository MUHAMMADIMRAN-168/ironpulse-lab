\# Web Application Mapping and OSINT Reconnaissance (`web\_recon.md`)



\## 1. Burp Site Map and Request Log



| Endpoint / Path | HTTP Method | Status Code | Notes / Purpose |

| :--- | :--- | :--- | :--- |

| `/dvwa/login.php` | GET / POST | 200 / 302 | Login page and authentication |

| `/dvwa/index.php` | GET | 200 | Main dashboard after login |

| `/dvwa/security.php` | GET / POST | 200 | Security level configuration |

| `/dvwa/vulnerabilities/sqli/` | GET | 200 | SQL Injection module |

| `/dvwa/vulnerabilities/sqli\_blind/` | GET | 200 | Blind SQL Injection module |

| `/dvwa/vulnerabilities/exec/` | GET / POST | 200 | OS Command Injection module |

| `/dvwa/vulnerabilities/xss\_r/` | GET | 200 | Reflected XSS module |

| `/dvwa/vulnerabilities/xss\_s/` | GET / POST | 200 | Stored XSS module |

| `/dvwa/vulnerabilities/fi/` | GET | 200 | File Inclusion module |

| `/dvwa/vulnerabilities/upload/` | GET / POST | 200 | File Upload module |

| `/dvwa/vulnerabilities/brute/` | GET | 200 | Brute Force module |

| `/dvwa/vulnerabilities/csrf/` | GET | 200 | CSRF module |

| `/dvwa/phpinfo.php` | GET | 200 | PHP configuration and environment info |



\## 2. OSINT Summary



\- \*\*`robots.txt` \& `sitemap.xml`:\*\* Checked `/dvwa/robots.txt` and `/dvwa/sitemap.xml` during passive discovery; recorded whether each returned `200 OK` or `404 Not Found`.

\- \*\*Exposed Informational Paths:\*\* Identified `/dvwa/phpinfo.php` (exposes PHP version and server details) and setup/config paths (`/dvwa/setup.php`) in the lab environment.

\- \*\*Real-World Web App Context:\*\* In a production web application (such as a bakery ordering site), mapping public routes, login/order endpoints, and `robots.txt` or sitemap files helps identify exposed administrative or internal paths without sending intrusive payloads.



\## 3. Input Parameters and Injection Points



| Endpoint | Method | Parameter(s) | Potential Injection / Test Area |

| :--- | :--- | :--- | :--- |

| `/dvwa/login.php` | POST | `username`, `password`, `user\_token` | Authentication form inputs |

| `/dvwa/vulnerabilities/brute/` | GET | `username`, `password`, `Login` | Login parameter handling |

| `/dvwa/vulnerabilities/exec/` | POST | `ip`, `Submit` | Command execution input |

| `/dvwa/vulnerabilities/sqli/` | GET | `id`, `Submit` | Query string parameter (`id`) |

| `/dvwa/vulnerabilities/sqli\_blind/` | GET | `id`, `Submit` | Blind query string parameter (`id`) |

| `/dvwa/vulnerabilities/xss\_r/` | GET | `name` | Reflected query parameter (`name`) |

| `/dvwa/vulnerabilities/xss\_s/` | POST | `txtName`, `mtxMessage`, `btnSign` | Stored form fields (`txtName`, `mtxMessage`) |

| `/dvwa/vulnerabilities/fi/` | GET | `page` | File path parameter (`page`) |

| `/dvwa/vulnerabilities/upload/` | POST | `uploaded`, `Upload` | Multipart file upload field (`uploaded`) |

| `/dvwa/vulnerabilities/csrf/` | GET | `password\_new`, `password\_conf`, `Change` | State-changing password parameters |

