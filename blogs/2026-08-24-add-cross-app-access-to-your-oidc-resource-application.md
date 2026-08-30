---
title: "Add Cross App Access to Your OIDC Resource Application"
url: "https://developer.okta.com/blog/2026/08/24/xaa-oidc-resource"
date: "2026-08-24"
feed_url: "https://developer.okta.com/feed.xml"
---
If you currently federate enterprise customers using OpenID Connect (OIDC) and want to allow applications to access your API on behalf of those users, this Cross App Access (XAA) guide is for you. The Identity Assertion Authorization Grant specification , the basis of XAA, was designed with OIDC in mind. Your authorization server already trusts the customer’s IdP for single sign-on (SSO), and XAA reuses that same trust for API access.
