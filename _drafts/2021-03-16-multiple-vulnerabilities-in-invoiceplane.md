---
layout: post
comments: true
tag: cve, research
category: research
title: Multiple Vulnerabilities in InvoicePlane CRM
---

## Summary

InvoicePlane is one of the popular open-source CRM. During the search for a PHP based open-source CRM in Github, this comes mostly within first ten.

The latest version of InvoicePlane (v1.5.11) has a weak password recovery mechanism which could lead to authenticated remote code execution. Without further wasting your time let’s dive into the details.

## CWE-640: Weak Password Recovery Mechanism for Forgotten Password and CWE-770: Allocation of Resources Without Limits or Throttling

The application allows users to reset their passwords by sending a reset link via email. As usual, the application allows the user to reset a new password by visiting the link. Here the things seem to be normal on the surface, but when we dive into source code things are different.

#### Proof of Concept

The password reset feature doesn't have any rate-limiting mechanism implemented to prevent brute force. When a user requests a password reset, the application generates a reset token stores it in the database. The problem is, during this token generation application concatenates the current time and email to generate an MD5 hash then uses this generated hash as a token. Since there is no rate limiting, if the attacker has the users' email address predicting this token becomes so easy. During the password generation, through the response date header attacker can figure out the time value as well.

![invoiceplane password reset](/images/invoiceplane/password-reset-token-code.png "Password Reset Code")

As seen on the above image, at line 172 application concatenates the current server time and user email to generate a token.

![invoiceplane password reset success](/images/invoiceplane/password-reset-token-success.png "Password Reset Success Message")

The above image shows the successful change using the exploit.

## CWE-434: Unrestricted Upload of File with Dangerous Type 

This vulnerability was reported in 2017 (CVE-2017-1000238) for an earlier version, which exists still in the latest as well. The application allows users to attach files for a quote and invoice. Although the application has some preventive measures to protect the uploaded files like disallowing direct access to the directory and file using *htaccess*, still it's not enough. With the default Apache installation, the application will work and during this process, there is no instruction or warning to given to the user about this. This allows the attacker to upload any file he/she wants and able to access this.

### RCE via Unrestricted File Upload

Since the application allows the attacker to upload any file type he/she wants. Through this attacker can achieve gain access to the server via RCE.

#### Proof of Concept

![invoiceplane rce request](/images/invoiceplane/rce-request.png "RCE Request")

![invoiceplane rce response](/images/invoiceplane/rce-response.png "RCE Response")

![invoiceplane rce proof](/images/invoiceplane/rce-poc.png "RCE POC")

### Full Path Disclosure

In the file upload vulnerability, there was no direct way to identify the file uploaded path without reading the source code. If there is a custom installation, the upload path can be modified via the _UPLOADS_FOLDER_ constant in the *index.php*. But _/upload/show_files/_  allows the attacker to read the full path of the uploaded file.

#### Proof of Concept

![invoiceplane path discloure request](/images/invoiceplane/path-discloure-request.png "Path Disclosure Request")

![invoiceplane path discloure response](/images/invoiceplane/path-discloure-response.png "Path Disclosure Response")

The developer has implemented some preventive measures to avoid exploitation of this vulnerability like *htaccess* based directory access restriction and users can download the file via an authenticated URL instead of direct download. But none of these will work if the server is not configured properly. During the testing, it seems the default installation of Apache will not help with the implemented protection mechanism.

> .htaccess based mitigation will work only with the supported webserver.

#### CWE-552: Files or Directories Accessible to External Parties

As mentioned above, if the webserver is not configured properly, this allows unauthenticated directory listing and file download. Allowing an attacker to directory traversal and download files suppose to be private without authentication.

![invoiceplane directory listing](/images/invoiceplane/directory-listing.png "Directory Listing")