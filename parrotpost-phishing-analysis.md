# ParrotPost: Phishing Email and Attachment Analysis

**Platform:** TryHackMe (guided, sandboxed lab) | [Room link](https://tryhackme.com/room/parrotpost)

*Flags and answers are not published here. This write-up covers my method and reasoning.*

## Summary
I investigated a suspicious email and its HTML attachment to determine whether it was a credential-theft attempt, following the evidence from the email headers, through obfuscated page code, to the network behaviour of the fake login form.

## Scenario
A user received an email that appeared to come from ParrotPost, a legitimate email tool, asking them to log in to resolve an account problem. I had to decide whether it was genuine and support that decision with evidence.

## What I did
- **Email headers:** Analysed the raw email in Sublime Text, reviewing the sender, `Received` routing headers, `Reply-To` and custom `X-` headers to assess where it really came from and spot inconsistencies.
- **Sending IP:** Looked up the sending IP's location, treating it as context for triage and not as proof of who sent it.
- **Attachment:** Inspected the `.htm` attachment as source code and found encoded content to analyse.
- **Deobfuscation:** Decoded layers of obfuscation in CyberChef (Base64 and HTML entities), and recognised how `atob()`, `unescape()` and `document.write()` conceal and render content.
- **Code analysis:** Beautified the JavaScript and CSS, then traced the form's submission flow, the destination of the captured data and the redirect behaviour.
- **Network behaviour:** Used Firefox and the Developer Tools Network panel to inspect the submission request, its parameters and the response.
- **Impact:** Followed the lab evidence through to the credentials log, linking the page's behaviour to the captured data.

## Verdict
**Malicious: a credential-harvesting phishing email.** The sender could not be trusted, the attachment hid its real content, and the page sent entered credentials to an attacker-controlled destination.

## Response I would take as a Tier 1 analyst
Quarantine the email from all affected mailboxes, block the sender, domain and IP, check whether the user entered credentials and reset them if so, record the indicators in the case ticket, and escalate if there is any compromise. Report the malicious domain to the registrar and the IP to the ISP or hosting provider.

## Tools and skills
**Tools:** Sublime Text, CyberChef, Firefox, Browser Developer Tools, IP-geolocation lookup.
**Skills:** email-header analysis, phishing-indicator identification, attachment inspection, Base64 and HTML-entity decoding, JavaScript and CSS deobfuscation, tracing form submission and redirects, HTTP request and response inspection.
