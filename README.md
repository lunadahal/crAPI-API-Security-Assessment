# OWASP crAPI API Security Assessment

## Overview
Security assessment of OWASP crAPI conducted in a local, authorized testing environment to evaluate API security controls and identify observable weaknesses.

## Assessment Environment
- Target: OWASP crAPI
- Tools: Burp Suite Community Edition, OWASP ZAP
- Environment: Local Docker deployment

## Assessment Coverage
- Authentication and JWT handling
- Broken Object Level Authorization (BOLA)
- Broken Function Level Authorization (BFLA)
- Input validation
- Business logic
- HTTP security configuration

## Methodology
Manual API request interception, modification, and response analysis using Burp Suite, supplemented by OWASP ZAP scanning.

## Documentation

**[Technical Security Assessment](ASSESSMENT.md)**

Detailed findings, observed HTTP behavior, recommended controls, assessment boundaries, and references to supporting evidence.

**[Security Testing Evidence](evidence/)**

26 sanitized screenshots documenting authentication, authorization, business logic, input validation, and OWASP ZAP results.
