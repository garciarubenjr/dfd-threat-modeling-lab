# dfd-threat-modeling-lab
Security-focused Data Flow Diagram and STRIDE threat modeling lab using OWASP Threat Dragon.
# DFD Threat Modeling Lab

This repository documents a hands-on **Data Flow Diagram (DFD)** and **STRIDE threat modeling** project using **OWASP Threat Dragon**.

The goal of this project is to demonstrate how threat modeling supports penetration testing, application security review, secure design analysis, and professional security documentation.

---

## Project Overview

This project focuses on **QRScope**, a browser-based QR code inspection utility that helps users decode QR codes from uploaded images, screenshots, or camera input before deciding whether to open the result.

QRScope is designed to help users inspect QR code content safely before taking action.

---

## Tools Used

- OWASP Threat Dragon v2.6.2
- STRIDE threat modeling
- Data Flow Diagrams
- Markdown documentation
- GitHub

---

## QRScope Architecture Notes

QRScope is modeled as a browser-based utility.

Important architecture details:

- QRScope has **no backend API**
- QRScope does **not expose an API**
- QRScope does **not use a database**
- Uploaded QR images are **not persistently stored**
- Decoded QR contents are **not persistently stored**
- QR decoding occurs in the browser
- Links are not opened automatically
- Users must take deliberate action before leaving QRScope
- External destination websites are outside QRScope control

---

## Threat Modeling Questions

This project follows the four-question threat modeling structure:

1. **What are we working on?**  
   Understand QRScope’s architecture, data flows, trust boundaries, and user interactions.

2. **What can go wrong?**  
   Identify threats using STRIDE categories such as Spoofing, Tampering, Information Disclosure, and Denial of Service.

3. **What are we going to do about it?**  
   Document practical mitigations such as input validation, safe output handling, no automatic link opening, and no persistent storage.

4. **Did we do a good enough job?**  
   Review the DFD, validate the threat list, and document lessons learned.

---

## Data Flow Diagram

The QRScope Level 1 DFD shows how user-provided QR images, screenshots, and camera input move through the browser-based application.

<img width="760" height="1000" alt="QRScope Level 1 DFD" src="https://github.com/user-attachments/assets/9e62d467-e575-42f8-adff-e36b6ac9e3f6" />

---

## DFD Scope

### In Scope

- QR image upload
- Screenshot upload
- Camera-based QR scanning
- Browser-based QR decoding
- Decoded content display
- Safety message / defanged output
- Temporary browser state
- User decision before opening an external destination

### Out of Scope

- Security of external destination websites
- Third-party website behavior
- Backend API security
- Server-side database security
- Persistent storage security
- Third-party API integrations

---

## DFD Elements

### External Entities

- User Browser / Mobile
- External Destination URL outside QRScope control

### Processes

- Image Upload Handler
- Camera Scan Handler
- QR Decode Logic
- Decode Result Display
- Safety Message / Defanged Output

### Data Store

- Browser Memory / Temporary State

### Trust Boundary

- Browser / QRScope App

---

## Data Flows

| Source | Destination | Data Flow |
|---|---|---|
| User Browser / Mobile | Image Upload Handler | Uploads QR image or screenshot |
| User Browser / Mobile | Camera Scan Handler | Grants camera access / scans QR code |
| Image Upload Handler | QR Decode Logic | Image data |
| Camera Scan Handler | QR Decode Logic | Camera frame data |
| QR Decode Logic | Decode Result Display | Decoded QR content |
| Decode Result Display | Safety Message / Defanged Output | Plain-text result / warning |
| Safety Message / Defanged Output | Browser Memory / Temporary State | Temporary browser state |
| QRScope / User Action | External Destination URL | Optional manual navigation |

---

## STRIDE Threat Analysis Summary

| # | Threat | STRIDE Category | Severity | Score | Status |
|---|---|---|---|---:|---|
| 1 | Oversized or malformed image causes browser performance issue | Denial of Service | Medium | 6 | Mitigated |
| 2 | Malicious QR content rendered unsafely | Tampering | High | 8 | Mitigated |
| 3 | User trusts a spoofed QR destination | Spoofing | High | 8 | Mitigated |
| 4 | Camera scan continues after successful decode | Denial of Service | Medium | 6 | Mitigated |
| 5 | Sensitive screenshot data exposed through persistence | Information Disclosure | Medium | 7 | Mitigated |

---

## Key Threats and Mitigations

### Threat 1: Oversized or Malformed Image Causes Browser Performance Issue

**Category:** Denial of Service  
**Severity:** Medium  
**Score:** 6  
**Status:** Mitigated  

A user may upload an oversized, malformed, or unsupported QR image that causes excessive browser resource usage, decoder errors, slow performance, or a failed scan.

**Mitigation:**  
Enforce file size limits, allow only supported image types, validate file MIME/type before processing, handle decoder errors safely, prevent repeated decode loops, and display a clear error message when an image cannot be processed.

---

### Threat 2: Malicious QR Content Rendered Unsafely

**Category:** Tampering  
**Severity:** High  
**Score:** 8  
**Status:** Mitigated  

A QR code may contain crafted text, HTML-like content, JavaScript-like payloads, or misleading URL formatting intended to manipulate how decoded content is displayed to the user.

**Mitigation:**  
Render decoded QR content as plain text only. Escape all user-controlled output, avoid unsafe HTML rendering, do not use `dangerouslySetInnerHTML`, do not auto-open decoded links, and require deliberate user action before navigation.

---

### Threat 3: User Trusts a Spoofed QR Destination

**Category:** Spoofing  
**Severity:** High  
**Score:** 8  
**Status:** Mitigated  

A QR code may point to a spoofed or lookalike destination that imitates a trusted website, brand, login page, payment page, or university service.

**Mitigation:**  
Display the decoded QR content before navigation, show the full destination clearly, avoid automatic link opening, defang suspicious URLs when appropriate, and require deliberate user action before leaving QRScope.

---

### Threat 4: Camera Scan Continues After Successful Decode

**Category:** Denial of Service  
**Severity:** Medium  
**Score:** 6  
**Status:** Mitigated  

The camera scanner may continue processing frames after a QR code has already been decoded. This could cause repeated decode attempts, unnecessary browser resource usage, repeated result updates, or continued camera access longer than needed.

**Mitigation:**  
Stop scanning after a successful decode, release or pause camera processing when appropriate, and require the user to manually start another scan.

---

### Threat 5: Sensitive Screenshot Data Exposed Through Persistence

**Category:** Information Disclosure  
**Severity:** Medium  
**Score:** 7  
**Status:** Mitigated  

Uploaded QR screenshots may contain sensitive information outside the QR code, such as messages, account details, names, emails, location data, or other private screen content.

**Mitigation:**  
Process uploaded QR images in the browser, avoid persistent storage, do not save uploaded images or decoded QR contents, clear temporary browser state when the user scans another QR code, and clearly disclose the no-storage behavior to users.


