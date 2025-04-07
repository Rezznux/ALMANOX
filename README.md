# Almanox Malware Technical Report

## Overview
### Almanox is a sophisticated and stealthy malware campaign discovered in early April 2025. It employs email-based social engineering, steganographic encoding, and advanced obfuscation techniques to bypass detection. This report provides a comprehensive forensic breakdown, including infection vectors, command-and-control (C2) mechanisms, and embedded payload behavior, supported with direct evidence.
---
## Key Findings

- The email contains **obfuscated payloads** hidden using **base64 encoding**.
- The email includes a **calendar invitation (.ics file)**, used to trick the user and potentially bypass security filters.
- The embedded executable is **XOR-encrypted** and requires decoding to become active.
- Command-like markers in the payload (e.g., `RRET(1)`, `BTY.8`, `DM1.9`) suggest internal communication codes for the malware.
---

## Infection Vector
### Email Origin & Headers
- **Spoofed Domain**: `olss.liverpool.sch.uk`
- **Sender IP Address**: `185.246.87.100` (France-based, not authorized to send mail on behalf of domain)
- **SMTP Trace**:
  - `Received: from aufersthehung.info (185.246.87.100)` → forged HELO address

### Authentication Failures
- **SPF**: Fail
- **DKIM**: None
- **DMARC**: None

### SMTP Marker Evidence
- Present in the header:
  ```
  RSET RPT(54).1824
  ```
  Hypothesis:
  - Used as a covert indicator for triggering payload parsing or stage transitions.

### Subject Line
- Encoded as:
  ```
  =?UTF-8?B?SGFycnkgS2FuZeKAmXMgaW52ZXN0bWVudCB0b29sIGZvciB1bmxpbWl0ZWQgd2VhbHRoIA==?=
  ```
  Decodes to: `Harry Kane’s investment tool for unlimited wealth`
  
### Email Characteristics
- Uses enticing subject lines ("Fire Safety Kit", "Harry Kane's tool for unlimited wealth")
- Contains HTML with embedded images and clickable links
- Multiple variations with different social engineering themes
- Spoofs legitimate sender addresses (e.g., Aviva)

### Structure
- Multi-part MIME format with nested boundaries
- Contains mathematical equations as text/plain content filler
- Heavy HTML obfuscation including excessive CSS and comment tags
- Multiple fake calendar attachments (.ics files)
- Uses Unicode emoji characters to bypass text-based detection

## Obfuscation Techniques

### HTML Content Obfuscation
- Repeated CSS blocks with `q{display:none}` to hide malicious elements
- Excessive whitespace in HTML attributes (especially in href tags)
- String substitution replacing apostrophes with random strings (e.g., "didn't" → "didnr2MgOAWDRi83uByt")
- Multiple HTML comment tags mimicking legitimate email template code

### Payload Concealment
- Primary payload hidden within a PNG image attachment
- Uses polyglot file format (valid PNG + hidden executable)
- PNG has legitimate header and renders as a normal image
- Malicious code appended after the IEND chunk
- PE executable starts at approximately offset 115,934

---

## Steganographic and Embedded Artifacts
### Repeating Mathematical Patterns (Evidence from plaintext body)
```
x^3 + y^3 = z^3 + 2
sum(n=1 to inf) [((-1)^n) * (n!)] / (n^n)
y''(x) - 3*x*y'(x) + 2*y(x) = e^(x^2)
int(0 to inf) e^(-x^2) * cos(a*x) dx
```
- Purpose: Likely used as visual noise or obfuscation of beaconing logic within the message.
- Repeated dozens of times in the plaintext segment to confuse signature-based scanners.

### HTML Payload
#### Snippet from email body:
```html
<a href="...&u=http://mZZdbibc.ltscybersecurity.com/rd/...">
```
- URL destination: `http://mZZdbibc.ltscybersecurity.com` (domain under attacker control).
- Possible use as:
  - Initial C2 beacon
  - Payload download stage

### Inline PNG Image
- Referenced as: `cid:Outlook-51883A05.png`
- Metadata:
  - Size: 13 KB
  - Modification date: `06 Nov 2024`
- Purpose:
  - Embedded tracking pixel
  - Possible covert channel via steganographic encoding

 ### Repeating Mathematical Patterns 
 ```
x^3 + y^3 = z^3 + 2
sum(n=1 to inf) [((-1)^n) * (n!)] / (n^n)
y''(x) - 3*x*y'(x) + 2*y(x) = e^(x^2)
int(0 to inf) e^(-x^2) * cos(a*x) dx
```
- Purpose: Likely used as visual noise or obfuscation of beaconing logic within the message.
- Repeated dozens of times in the plaintext segment to confuse signature-based scanners.

---

## .ics Calendar Attachment
### Metadata
- **Filename**: `Hot Ukrainian Women's Profiles .ics`
- **MIME Type**: `text/calendar`
- **Encoding**: base64 inline

### Decoded Content Analysis
- **Entropy-heavy Tokens**:
  ```
  ADdc152258f9c72fec5bD81DFfa2B2
  cA923b6A3ddBC6ACa3aBf1eDBa15a6d7B9FfB376
  ```
- **Observed 9-item Grouping Structure**:
```
<!--1FB3973944a1e-->

--_bc327e97-ab05-4a15-9d5b-30014897f10f_
Content-Disposition: attachment;
	filename="Hot Ukrainian Women's Profiles .ics"
Content-Type: text/calendar; name="Hot Ukrainian Women's Profiles .ics"
Content-Transfer-Encoding: base64
Content-Description: Hot Ukrainian Women's Profiles .ics

<p style="margin:0;margin-bottom:19px">1. hvow7</p>
<p style="margin:0;margin-bottom:19px">2. f8ppbm74</p>
<p style="margin:0;margin-bottom:19px">3. HCwGyKoBP</p>
<p style="margin:0;margin-bottom:19px">4. mpYktqtniwHPu34</p>
<p style="margin:0;margin-bottom:19px">5. IJdfrtGetcdRQxnGicss</p>
<p style="margin:0;margin-bottom:19px">6. hEFXUyLf5Gx2XAROhGm4atMM5y</p>
<p style="margin:0;margin-bottom:19px">7. IrazFN6koiGzyvT8keKG2PHi5Tr5Dd</p>
<p style="margin:0;margin-bottom:19px">8. Ke0xmOhJTS4o2LBjnFacuKa98HjdRTa2F2W</p>
<p style="margin:0;margin-bottom:19px">9. ELvRsvAzPz3oTY2CE0t9ZRF0OPpSSrPsWLNbEz</p>

<p style="margin:0;margin-bottom:19px">1. q82de</p>
<p style="margin:0;margin-bottom:19px">2. e87jix0s</p>
<p style="margin:0;margin-bottom:19px">3. HQzDHrwZh</p>
<p style="margin:0;margin-bottom:19px">4. XJRBXCugqvbLq5z</p>
<p style="margin:0;margin-bottom:19px">5. bBYrKIWbkuSWQOUYMKHg</p>
<p style="margin:0;margin-bottom:19px">6. uj1rMBz8lyGd8zCKeKZ15dyjVY</p>
<p style="margin:0;margin-bottom:19px">7. rQDoQYpHOYmyKwmduey1cGWIXlP3JF</p>
<p style="margin:0;margin-bottom:19px">8. NWosYu6j5NNfTAREW5ao3HzQUftEDw27piZ</p>
<p style="margin:0;margin-bottom:19px">9. MLoFvL3UmQWeYe2mQeao0VtzD2c30Fd6MyQ4RU</p>

<p style="margin:0;margin-bottom:19px">1. rhy0k</p>
<p style="margin:0;margin-bottom:19px">2. sx17uhfb</p>
<p style="margin:0;margin-bottom:19px">3. PWKfTcXu5</p>
<p style="margin:0;margin-bottom:19px">4. 0kAmTIaGemIY0Y0</p>
<p style="margin:0;margin-bottom:19px">5. ATAvTaqDJBCwFlzrNWZN</p>
<p style="margin:0;margin-bottom:19px">6. OpCT5BqRRVGgUEPUW4vxkuLUeW</p>
<p style="margin:0;margin-bottom:19px">7. b1SczoHLG24r7vvmT8RxxGduFS1KEz</p>
<p style="margin:0;margin-bottom:19px">8. PWUtQ5JwjVLoRVeiVgx3Pmu8Lcu4RA3CRL8</p>
<p style="margin:0;margin-bottom:19px">9. saax7jye78xTVKvJJqojEVo04jnVexHqLgsAk4</p>

<p style="margin:0;margin-bottom:19px">1. De31B</p>
<p style="margin:0;margin-bottom:19px">2. FecaAf78</p>
<p style="margin:0;margin-bottom:19px">3. E42aB21ed1</p>
<p style="margin:0;margin-bottom:19px">4. e3AcFbe0e5FB4Df</p>
<p style="margin:0;margin-bottom:19px">5. aF67e2BA7459F2c3861C</p>
<p style="margin:0;margin-bottom:19px">6. B04361D1C8f720dBBaEe2D54D</p>
<p style="margin:0;margin-bottom:19px">7. ADdc152258f9c72fec5bD81DFfa2B2</p>
<p style="margin:0;margin-bottom:19px">8. f6Cb3D7DfD9fb7B678b614FC1A284FFEFb1</p>
<p style="margin:0;margin-bottom:19px">9. cA923b6A3ddBC6ACa3aBf1eDBa15a6d7B9FfB376</p>
```

These are:

Possibly Base64/XOR encoded segments.

Each numeric/alphanumeric sequence may represent an index or offset into a later payload (or key for decoding).

- **Hypothesis:**
  
  - Steganographic command placeholders
  - May function as unique identifiers or flags for loader stages

---

## Payload Analysis

### Extraction Process
1. Embedded PNG image contains hidden data after valid image content
2. Data is encrypted with multiple XOR operations:
   - First key: 0x45 (decimal 69)
   - Second key: 0x99 (decimal 153)
   - Possible third key: 0x69 (decimal 105)
3. Contains GZIP-like compression (non-standard implementation)
4. Final payload is a Windows PE executable

### Embedded Data
- Structured data in consistent format: `[10 chars]*"""*[4 chars]*"""*[4 chars]*"""*[4 chars]*"""*[12 chars]`
- Multiple instances of similarly structured data
- Likely contains C2 server addresses, encryption keys, or configuration parameters

### Malware Capabilities
Based on strings and code fragments identified:
- C2 communication functionality with HTTP components ("CONFIG:", "User-Agent:", "Host:", "GET /")
- High entropy code section (7.99) indicating packed/obfuscated code
- Anti-debugging techniques (int 3 instructions)


## Indicators of Compromise (IOCs)
| Type | Value | Description |
|------|-------|-------------|
| Sender Domain | `olss.liverpool.sch.uk` | Forged identity |
| Sender IP | `185.246.87.100` | France-based untrusted IP |
| Redirect URL | `http://mZZdbibc.ltscybersecurity.com` | Likely C2 server |
| PNG CID | `Outlook-51883A05.png` | Embedded tracking or payload container |
| SMTP Marker | `RSET RPT(54).1824` | Covert signaling string |
| Attachment | `Hot Ukrainian Women's Profiles .ics` | Malicious calendar file |

---

## C2 Communication Analysis
### Primary Channels

1. **Calendar Entry Markers**:
   - Unique encoded tokens potentially used as command triggers or configuration data.
2. **SMTP Signal**:
   - `RSET RPT(54).1824` may designate stage 54, process ID 1824.

### C2 Hypothesis
- Stealthy trigger-and-response model
- Calendar content doubles as:
  - Timing-based trigger mechanism
  - Data exfiltration channel using pseudo-legitimate filetypes

---

## Anti-Analysis Features
- **Layered MIME Structure**: Multipart message with embedded signed and calendar content.
- **Obfuscation**:
  - Quoted-printable and base64 encoding of multiple sections
  - Deep nesting of HTML and calendar artifacts
- **Anti-signature Techniques**:
  - Mathematical decoys
  - Image-based payload concealment

---


## Conclusion
*Almanox* is an advanced persistent threat employing obfuscated email vectors, passive C2 triggers, and steganographic content to evade modern detection systems. Its reliance on polyglot formats (e.g., calendar files and inline HTML with PNGs) signals a tailored and modular approach to malware delivery.



---



