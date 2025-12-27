# Advisory: Multiple SQL Injection Vulnerabilities in online-shopping-system-php v1.0

## Overview

This advisory covers **five distinct SQL Injection vulnerabilities** discovered in the *online-shopping-system-php v1.0* application hosted on GitHub:

Repository: https://github.com/indieka900/online-shopping-system-php

The vulnerabilities allow remote attackers to execute time-based blind SQL injection due to improper input handling and lack of prepared statements. These issues affect both admin and public interfaces and can lead to data exfiltration, unauthorized actions, and potential code execution.

Assigned CVE IDs:
- **CVE-2025-61320**
- **CVE-2025-61321**
- **CVE-2025-61322**
- **CVE-2025-61323**
- **CVE-2025-61324**

---

## Affected Version

- **online-shopping-system-php v1.0**

---

## Vulnerability Details

### CVE-2025-61320 — SQL Injection via `user_id` (manageuser.php)

**Description:**  
In `admin/admin/manageuser.php`, the `user_id` parameter is concatenated directly into SQL without sanitization or parameterization. Remote attackers can leverage time-based blind SQL injection for data enumeration and unauthorized admin actions.

**Affected Component:**  
```
admin/admin/manageuser.php
Parameter: user_id (GET)
```

**PoC (example):**
```
GET /admin/admin/manageuser.php?user_id=1 AND IF(SUBSTR((SELECT database()),1,1)='o',SLEEP(5),0) HTTP/1.1
```

---

### CVE-2025-61321 — SQL Injection via `product_id` (edit_product.php)

**Description:**  
The `product_id` GET parameter in `admin/admin/edit_product.php` is directly embedded in SQL, allowing time-based blind SQL injection. Exploitation can extract sensitive database data.

**Affected Component:**  
```
admin/admin/edit_product.php
Parameter: product_id (GET)
```

**PoC (example):**
```
GET /admin/admin/edit_product.php?product_id=1 IF(SLEEP(5),1,0)
```

---

### CVE-2025-61322 — SQL Injection via `product_id` (products_list.php)

**Description:**  
The `product_id` parameter used in delete actions on the admin products listing page is concatenated into the SQL DELETE command without validation, enabling SQL injection.

**Affected Component:**  
```
admin/admin/products_list.php
Parameter: product_id (GET)
```

**PoC (example):**
```
GET /admin/admin/products_list.php?action=delete&product_id=1 OR IF(SLEEP(5),1,0)
```

---

### CVE-2025-61323 — SQL Injection via `country` (addsuppliers.php)

**Description:**  
In `admin/admin/addsuppliers.php`, unvalidated `country` input in a multipart POST request triggers blind SQL injection via constructed payloads.

**Affected Component:**  
```
admin/admin/addsuppliers.php
Parameter: country (POST multipart/form-data)
```

**PoC (example):**
```
POST /admin/admin/addsuppliers.php
Content-Type: multipart/form-data
country: USA' AND IF(SLEEP(5),1,0)--
```

---

### CVE-2025-61324 — SQL Injection via `city` (addsuppliers.php)

**Description:**  
Similar to CVE-2025-61323, the `city` field in the supplier creation form is vulnerable to SQL injection through blind timing techniques.

**Affected Component:**  
```
admin/admin/addsuppliers.php
Parameter: city (POST multipart/form-data)
```

**PoC (example):**
```
POST /admin/admin/addsuppliers.php
Content-Type: multipart/form-data
city: NYC' AND IF(SLEEP(5),1,0)--
```

---

## Impact

Successful exploitation may result in:

- Remote data extraction from the database
- Unauthorized data changes
- Unauthorized admin actions
- Exposure of sensitive user/product information

---

## Fix & Mitigation

1. Replace all dynamic SQL with **prepared statements** (PDO/MySQLi).
2. Validate and sanitize all user input; enforce strict typing for IDs.
3. Restrict admin panel access.
4. Deploy Web Application Firewall (WAF) protections.
5. Conduct thorough testing after patching.

---

## References

- OWASP SQL Injection: https://owasp.org/www-community/attacks/SQL_Injection

---

## Discoverer

**Rakesh Kumar Raushan**

---

## Contact

For more details or questions, contact:  
📧 your-email@example.com
