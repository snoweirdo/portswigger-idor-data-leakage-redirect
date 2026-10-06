# Lab Report: User ID Controlled by Request Parameter with Data Leakage in Redirect

## Lab Overview

This PortSwigger Web Security Academy Apprentice lab demonstrated an access-control vulnerability where sensitive information was leaked in the body of a redirect response.

The objective was to obtain the API key belonging to the user `carlos` and submit it as the lab solution.

## Credentials

```text
Username: wiener
Password: peter
```

## Tools Used

- Burp Suite
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy browser

## Exploitation Steps

1. Signed in to the application using the provided credentials:

   ```text
   wiener:peter
   ```

2. Navigated to the **My account** page.

3. Captured the request used to load the account page in Burp Proxy.

4. Sent the captured request to Burp Repeater.

5. Identified the `id` parameter in the request. This parameter controlled which user account the application attempted to display.

6. Changed the value of the `id` parameter from the authenticated user to:

   ```text
   carlos
   ```

7. Sent the modified request from Burp Repeater.

8. The application returned a redirect response instead of displaying the account page normally.

9. Inspected the response body in Burp Repeater.

10. Found Carlos's API key in the body of the redirect response.

11. Submitted the API key in the lab solution field.

12. The lab was successfully solved.

## Modified Request

```http
GET /my-account?id=carlos HTTP/2
Host: <lab-id>.web-security-academy.net
Cookie: session=<wiener-session>
```

The important change was replacing the original user identifier with `carlos`. The authenticated session remained associated with the `wiener` account.

## Result

The server returned a redirect response, but the response body still contained sensitive information belonging to Carlos.

This demonstrated that the application attempted to redirect the request but failed to remove confidential account data from the response body.

## Vulnerability Identified

The application contained an **Insecure Direct Object Reference (IDOR)** vulnerability.

It trusted the user-controlled `id` parameter without verifying whether the authenticated user was authorized to access the requested account.

The application also leaked sensitive information in the body of a redirect response. Therefore, even though the browser was redirected, the API key could still be viewed by inspecting the original response in Burp Repeater.

## Impact

An authenticated user could access another user's private account information, including:

- API keys
- Account details
- Personal information
- Internal identifiers
- Other sensitive data

In a real-world application, an exposed API key could allow:

- Unauthorized API access
- Data theft
- Account compromise
- Unauthorized actions
- Further privilege escalation

## Recommended Remediation

- Perform server-side authorization checks before retrieving account data.
- Do not trust user-controlled parameters such as `id`.
- Verify that the authenticated user owns or has permission to access the requested account.
- Do not include sensitive information in redirect response bodies.
- Return `403 Forbidden` or `404 Not Found` when access is unauthorized.
- Use consistent access-control checks across all response types, including redirects.
- Monitor and investigate repeated attempts to access accounts belonging to other users.

## Key Takeaway

Redirecting a user does not make sensitive data secure if that data is already present in the response body.

Applications must validate authorization before retrieving account information and must ensure that denied responses, including redirects, contain no confidential data.

## Disclaimer

This report was created for educational purposes and applies only to the authorized PortSwigger Web Security Academy lab environment.