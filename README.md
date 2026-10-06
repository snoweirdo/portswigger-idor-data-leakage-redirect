# PortSwigger: IDOR Data Leakage in Redirect

A writeup for the PortSwigger Web Security Academy Apprentice lab:

**User ID controlled by request parameter with data leakage in redirect**

## Overview

This lab demonstrates an access-control vulnerability where the application exposes sensitive account information in the body of a redirect response.

The application uses a user-controlled `id` parameter to identify which account to display. By changing the parameter from the authenticated user to `carlos`, it is possible to retrieve Carlos's API key.

## Vulnerability

- Vulnerability type: Insecure Direct Object Reference (IDOR)
- Security impact: Horizontal privilege escalation
- Additional issue: Sensitive data leakage in a redirect response
- Tool used: Burp Suite

## Credentials

```text
Username: wiener
Password: peter
```

## TL;DR

1. Log in as `wiener`.
2. Open **My account**.
3. Capture the request in Burp Suite.
4. Send it to Burp Repeater.
5. Change the `id` parameter to `carlos`.
6. Send the modified request.
7. Inspect the redirect response body.
8. Extract Carlos's API key.
9. Submit the API key to solve the lab.

## Example Request

```http
GET /my-account?id=carlos HTTP/2
Host: <lab-id>.web-security-academy.net
Cookie: session=<wiener-session>
```

## Key Takeaway

A redirect does not prevent sensitive-data exposure if the confidential information is already included in the response body. Applications must perform server-side authorization checks before retrieving account data and ensure that redirect responses do not contain sensitive information.

## Remediation

- Enforce server-side authorization for every account request.
- Never trust user-controlled parameters to determine access permissions.
- Do not include sensitive data in redirect response bodies.
- Return `403 Forbidden` or `404 Not Found` when access is unauthorized.
- Monitor requests attempting to access other users' accounts.

## Disclaimer

This writeup is for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.
