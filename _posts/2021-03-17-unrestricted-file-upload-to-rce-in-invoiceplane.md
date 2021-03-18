---
layout: post
comments: true
tag: cve, research
category: research
title: Unrestricted Upload to RCE in InvoicePlane CRM
---

## Summary

InvoicePlane is one of the popular open-source CRM. During the search for a PHP based open-source CRM in Github, this comes mostly within first ten.

The latest version of InvoicePlane (v1.5.11) has several vulnerabilities. Without further wasting your time let’s dive into the details.

### CWE-434: Unrestricted Upload of File with Dangerous Type 

This vulnerability was reported in 2017 (CVE-2017-1000238) for an earlier version, which exists still in the latest as well. The application allows users to attach files for a quote and invoice. Although the application has some preventive measures to protect the uploaded files like disallowing direct access to the directory and file using *htaccess*, still it's not enough. With the default Apache installation, the application will work and during this process, there is no instruction or warning to given to the user about this. This allows the attacker to upload any file he/she wants and able to access this.

### RCE via Unrestricted File Upload

Since the application allows the attacker to upload any file type he/she wants. Through this attacker can achieve gain access to the server via RCE.

#### Proof of Concept

![invoiceplane rce request](/images/invoiceplane/rce-request.png "RCE Request")

![invoiceplane rce response](/images/invoiceplane/rce-response.png "RCE Response")

![invoiceplane rce proof](/images/invoiceplane/rce-poc.png "RCE POC")
