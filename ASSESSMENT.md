# OWASP crAPI | API Security Assessment

**Assessment:** October 2026  
**Environment:** Authorized local crAPI deployment (`http://localhost:8888`)  
**Tools:** Burp Suite Community, OWASP ZAP 2.17.0, Firefox, Docker

## Scope and method

I assessed authentication-related behavior, object and function authorization, input validation, and shop transaction logic. Using two controlled user accounts, I captured legitimate requests, changed one parameter or object identifier in Burp Repeater, and checked response contents or subsequent application state. ZAP supplemented manual testing with passive analysis and one limited active scan.

## Principal findings

| Finding | Evidence observed | Recommended control |
| --- | --- | --- |
| **F01. Vehicle location authorization (BOLA)** | Requests made with one user's token returned the other user's vehicle location data, in both test directions. | Authorize each vehicle object against the requesting user. |
| **F02. Order details authorization (BOLA)** | Cross-account order IDs returned another user's order details with HTTP 200. | Enforce ownership checks for every order retrieval. |
| **F03. Video deletion authorization** | A regular user's request to an `admin` video DELETE route returned success; a subsequent retrieval returned HTTP 404. The route name alone does not prove an administrator-only policy. | Enforce and test the intended deletion permissions server-side. |
| **F04. Negative quantity / shop credit** | An order with `quantity: -1` was accepted; repeated orders increased credit by $10, reaching $120, with negative entries visible in order history. | Reject nonpositive quantities and enforce server-side transaction rules. |

## Supporting observations

**Input handling:** The community pagination API accepted `limit=abc`, `limit=-1`, and `limit=0` with HTTP 200, without a demonstrated security impact. The comment API rejected numeric content and malformed JSON with HTTP 400.

**ZAP:** Passive review observed missing CSP and anti-clickjacking protection on `/shop`, a missing `X-Content-Type-Options` header, a chatbot cookie without an explicit SameSite attribute, server version disclosure, and wildcard CORS on login and token-verification responses. These are configuration observations, **not demonstrated exploits**. A targeted low-strength active scan of the product listing endpoint completed **344 requests**; some payloads were blocked by endpoint protection.

## Assessment boundaries

This was black-box testing of an intentionally vulnerable **local training application**, not a production penetration test. Only selected workflows and one ZAP active-scan endpoint were covered. The HTTP-only warning reflects the local setup; the JavaScript timestamp alert had no demonstrated sensitive-data impact. Findings rely on the captured requests, responses, and browser state; no source-code audit or remediation retest was performed.

**Evidence handling:** Keep original captures private. Publish only screenshots that substantiate the four principal findings or material configuration observations, after concealing tokens, credentials, cookies, personal identifiers, and unnecessary location details.


## Evidence References

The following screenshots document observed HTTP behavior and application responses. All published images have been sanitized.

### F01. Vehicle Location Authorization

[User 1 accessing User 2 vehicle](evidence/F01-vehicle-user1-access-user2.png) | [User 2 accessing User 1 vehicle](evidence/F01-vehicle-user2-access-user1.png)

### F02. Order Details Authorization

[Order 6 baseline](evidence/F02-order6-user1-baseline.png) | [Order 6 cross-account access](evidence/F02-order6-user2-cross-account.png)

[Order 7 baseline](evidence/F02-order7-user2-baseline.png) | [Order 7 cross-account access](evidence/F02-order7-user1-cross-account.png)

### F03. Video Deletion Authorization

[Video 53 accessible before deletion](evidence/F03-video53-before-GET-200.png) | [DELETE returned 200](evidence/F03-video53-DELETE-200.png) | [Subsequent GET returned 404](evidence/F03-video53-after-GET-404.png)

### F04. Shop Credit Business Logic

[Normal order, credit $80](evidence/F04-normal-order-credit-80.png) | [Negative quantity, credit $120](evidence/F04-negative-quantity-credit-120.png)

[Negative orders in history](evidence/F04-negative-orders-history.png) | [Persisted balance of $120](evidence/F04-persisted-balance-120.png)

### Authentication Testing

[Missing token, 401](evidence/AUTH-user1-missing-token-401.png) | [Modified token, 401](evidence/AUTH-user1-modified-token-401.png)

[Reported post-logout request, 200](evidence/AUTH-user1-post-logout-request-200.png) | [User 2 authenticated request, 200](evidence/AUTH-user2-valid-token-200.png)

### Input Validation

[Invalid pagination limit, 200](evidence/VAL-pagination-invalid-limit-200.png) | [Valid string comment, 200](evidence/VAL-comment-valid-string-200.png) | [Malformed JSON, 400](evidence/VAL-comment-malformed-json-400.png)

### OWASP ZAP Assessment

[Passive alerts overview](evidence/ZAP-passive-alerts-overview.png) | [Missing framing protection](evidence/ZAP-shop-clickjacking-header-missing.png)

[Cookie SameSite observation](evidence/ZAP-cookie-samesite-missing.png) | [Login CORS observation](evidence/ZAP-login-cors-wildcard.png) | [Token verification CORS observation](evidence/ZAP-auth-verify-cors-wildcard.png)

[Completed targeted active scan, 344 requests](evidence/ZAP-active-scan-344-requests.png)

Automated ZAP alerts are configuration observations and are not independently demonstrated exploits.
