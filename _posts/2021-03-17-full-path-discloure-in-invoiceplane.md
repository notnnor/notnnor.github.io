---
layout: post
comments: true
tag: cve, research
category: research
title: Full Path Disclosure in InvoicePlane CRM
---

## Summary

InvoicePlane is one of the popular open-source CRM. During the search for a PHP based open-source CRM in Github, this comes mostly within first ten.

The latest version of InvoicePlane (v1.5.11) has several vulnerabilities. Without further wasting your time let’s dive into the details.

### Full Path Disclosure

In the file upload vulnerability the same system has, there was no direct way to identify the file uploaded path without reading the source code. If there is a custom installation, the upload path can be modified via the _UPLOADS_FOLDER_ constant in the *index.php*. But _/upload/show_files/_  allows the attacker to read the full path of the uploaded file.

- CWE-200: Exposure of Sensitive Information to an Unauthorized Actor

#### Proof of Concept

![invoiceplane path discloure request](/images/invoiceplane/path-discloure-request.png "Path Disclosure Request")

![invoiceplane path discloure response](/images/invoiceplane/path-discloure-response.png "Path Disclosure Response")
