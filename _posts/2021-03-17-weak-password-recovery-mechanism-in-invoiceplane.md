---
layout: post
comments: true
tag: cve, research
category: research
title: Weak Password Recovery Mechanism in InvoicePlane CRM
---

## Summary

InvoicePlane is one of the popular open-source CRM. During the search for a PHP based open-source CRM in Github, this comes mostly within first ten.

The latest version of InvoicePlane (v1.5.11) has a weak password recovery mechanism which could lead to authenticated remote code execution. Without further wasting your time let’s dive into the details.

- CWE-640: Weak Password Recovery Mechanism for Forgotten Password
- CWE-770: Allocation of Resources Without Limits or Throttling

The application allows users to reset their passwords by sending a reset link via email. As usual, the application allows the user to reset a new password by visiting the link. Here the things seem to be normal on the surface, but when we dive into source code things are different.

#### Proof of Concept

The password reset feature doesn't have any rate-limiting mechanism implemented to prevent brute force. When a user requests a password reset, the application generates a reset token stores it in the database. The problem is, during this token generation application concatenates the current time and email to generate an MD5 hash then uses this generated hash as a token. Since there is no rate limiting, if the attacker has the users' email address predicting this token becomes so easy. During the password generation, through the response date header attacker can figure out the time value as well.

![invoiceplane password reset](/images/invoiceplane/password-reset-token-code.png "Password Reset Code")

As seen on the above image, at line 172 application concatenates the current server time and user email to generate a token.

![invoiceplane password reset success](/images/invoiceplane/password-reset-token-success.png "Password Reset Success Message")

The above image shows the successful password change using the exploit.