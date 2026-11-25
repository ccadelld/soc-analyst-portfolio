# ParrotPost: Phishing Email and Attachment Analysis

**Platform:** TryHackMe (guided, sandboxed lab) | [Room link](https://tryhackme.com/room/parrotpost)

*Flags and answers are not published here. This write-up covers my method and reasoning.*

## Summary
I investigated a suspicious email and its HTML attachment to determine whether it was a credential-theft attempt, following the evidence from the email headers, through obfuscated page code, to the network behaviour of the fake login form.

## Scenario
A senior executive received an email that appeared to come from ParrotPost, a legitimate email tool, asking them to log in to resolve an account problem. My task was to decide whether it was genuine and support that decision with evidence.

## What I did
- **Email headers:** Analysed the raw email in Sublime Text, reviewing the sender, `Received` routing headers (indicating apparent origin via the email spoofing service `emkei.lv`, while keeping in mind that Received headers require careful interpretation), a typosquatted Reply-To domain (`postparr0t.thm`), an absence of successful SPF, DKIM and DMARC authentication results, and custom `X-` headers to assess where it really came from and spot inconsistencies.
- **Sending IP:** Looked up the sending IP's location, treating it as context for triage and not as proof of who sent it.
- **Attachment:** Inspected the `.htm` attachment as source code and found encoded content to analyse.
- **Deobfuscation:** Decoded layers of obfuscation in CyberChef (Base64 and HTML entities), and recognised how `atob()`, `unescape()` and `document.write()` conceal and render content.
- **Code analysis:** Beautified the JavaScript (and reviewed the minified CSS), then traced the form's submission flow to an external PHP endpoint (`cred-capture.php`), and the redirect behaviour (using `window.location.href` to forward victims to the real site).
- **Network behaviour:** Used Firefox and the Developer Tools Network panel to inspect the GET submission request, its parameters and the response.
- **Impact:** Confirmed credential submission directly through the lab's credentials log, proving the page actively captured entered data rather than merely pointing to a suspicious endpoint.

## Verdict
**Malicious: a credential-harvesting phishing email.** The sender could not be trusted, the attachment hid its real content, and the page sent entered credentials to an attacker-controlled destination.

## Response I would take as a Tier 1 analyst
Quarantine the phishing email across affected mailboxes, block the malicious sender address and confirmed malicious domains, and consider IP blocking only where justified by threat intelligence and organisational policy to avoid collateral disruption on shared infrastructure. Determine whether the user submitted credentials, initiating password resets and session revocation where necessary. Search for related indicators across email and security telemetry, document all findings in the case ticket, and escalate any suspected account compromise. (Where appropriate, external abuse reporting to the domain registrar or hosting provider can also be carried out by the relevant team.)

## Tools and skills
**Tools:** Sublime Text, CyberChef, Firefox, Browser Developer Tools, IP-geolocation lookup.


**Skills:** Email-header analysis, SPF/DKIM/DMARC verification, phishing-indicator identification, attachment inspection, Base64 and HTML-entity decoding, JavaScript and CSS deobfuscation, tracing form submission and redirects, HTTP request and response inspection.
