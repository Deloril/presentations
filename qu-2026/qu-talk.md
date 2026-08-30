---
marp: true
theme: lpearson-terminal
paginate: true
size: 16:9
---

<!--
Converted from QU-Talk.pptx into the lpearson-terminal theme.
The source is a reusable talk template, so the agenda, section, bullet,
comparison and diagram slides keep their placeholder text for you to fill
per talk. The title, whoami and contact slides use your real details.
The source had duplicate title/whoami template slides; collapsed to one each.
Render: marp qu-talk.md --theme ./lpearson-terminal.css -o qu-talk.pdf
-->

<!-- _class: lead -->
<!-- _paginate: false -->

> $ ./talk --start

# **Case Study: Intrusion Response**

## Living through the fun together

Luke Pearson · lpearson.co · QU 2026

---

<!-- _class: lead -->

> $ whoami

# Luke Pearson

## Incident Response · Digital Forensics · Expert Witness

- 8 years of incident response, 60+ intrusions worked
- GSE #358 · CCE · 20+ GIAC · Master of Cyber Security
- SANS instructor · PhD candidate

---

# Agenda

1. Intrusion Response Theory
2. Hired by The Secrets Group
3. An intrusion! Who could have seen this coming?!
4. Doing the fun technical stuff
5. Doing the ~~fun~~ business stuff
6. AI in intrusion response - today and tomorrow.
6. Wrapping up / QnA
7. Bonus / Time Dependent: Kerberoasting

---

# What's an Incident?

```console
> cat definition_of_incident.NIST_SP_800-61R2
a violation or imminent threat of violation of computer security policies, 
acceptable use policies, or standard security practices.

> cat definition_of_incident.MICROSOFT
a confirmed breach of security that leads to the accidental or unlawful destruction, 
loss, alteration, unauthorized disclosure of, or access to customer data or personal 
data while Microsoft processes it.

> cat definition_of_incident.BHP
any actual or potential accidental, unauthorised or unlawful destruction, loss, 
alteration, or unauthorised third-party access to or disclosure of Company Data.

```

---

# What's an Intrusion?

```console
> cat definition_of_INTRUSION

the interactive access to more than one computing system by an attacker, where the 
access to subsequent systems is dependent on the compromise of previous systems.

For example, two different systems being compromised by the same phishing campaign
would not meet this criteria, but the compromise of one system leading to lateral
movement to a second system would.

> # OH! It's the FUN stuff!

```

---

# The Secrets Group (TSG)

### TSG: **Trust us, not Luck**

Founded in Wellington in 2012 as a boutique source-code escrow agent, The Secrets Group has grown into a global custody business spanning 35 jurisdictions and around 6,800 people. From software escrow and key custody for banks and governments, to physical safe-deposit vaults, digital-asset custody, and secure storefronts on the high street, we hold what our clients cannot afford to lose, and hand it back only when it's needed.

**Our mission** is simple: be the safest place in the world to leave a secret. Everything we build, from the SSN platform to the vault door, exists to keep custody of sensitive material verifiable, recoverable, and private.

---

![bg fit](images/TSG_AMER_Network_Dark.jpeg)

---

# Mandiant Targeted Attack Lifecycle

![outline w:1000](images/mandiant_attack_lifecycle_dark.jpeg)

---
<!-- _class: two-up -->

# Incident Response Lifecycles

![outline w:600](images/NIST_IR_Dark.jpeg) ![outline w:600](images/SANS_PICERL_Dark.jpeg)

---

<!-- _class: img-right -->

# The Bleeding-Edge

![outline w:650](images/DAIR_Dark.jpeg) 

- Dynamic Approach to Incident Response (DAIR)
- Brings the SOC to the table
- Still doesn't mention Investigate, but Scope is closer

---
<!-- _class: img-right -->

# The Detection

![outline right w:600](images/initial-alert.png) 

- Detection fired for anomalous activity for the svc_filesvc account from FILE-3​
- Consistent short-lived network connections to 9.9.9.9.​
- WinRM and SMB connections to DC1​
- All activity came from the process associated with image C:\Windows\Temp\WDUpdate.exe​
- WDUpdate.exe is a child process of services.exe​

---

# Alert in Context

![outline w:1000](./images/alert-timeline.jpg)

---

<!-- _class: img-right -->

# Where are we?

![outline w:600](images/scope-1.png)

- I don't believe FILE-3 is the first compromised system, there's more to find on the left.
- Depending on how long ago this activity occurred, I don't believe that we've seen the end of the activity on the right.
- What else happened on FILE-3?!

---
<!-- _class: img-right -->

# Timeline: Left


![outline right w:600](images/tl-left-dark.png)

- Search for other systems connecting to 9.9.9.9
    - Find LPEARSON
- Investigate LPEARSON: 
    - Phished from mike@myodb.com
    - Bunch of internal recon
    - Invoke-Kerberoast
- Search for bad use of kerb creds
- Find PRINT-1:
    - Service and malicious binary created, no execution?
    - No other findings

---
<!-- _class: img-right -->

# Timeline: Right

![outline right w:600](images/tl-right-dark.png)


- Investigate DC-1:
    - Group policy created targeting CTX-13 that runs a command
- Investigate CTX-13:
    - Command downloads and runs ctxhealth.exe from 2.2.2.2
    - ctxhealth.exe is a C2 implant that talks to 3.3.3.3
    - ctxhealth exploits a citrix gateway to steal credentials
    - MWILLIAMS credentials used to access and change contract data for Meridian, Inc

---

<!-- _class: timeline -->

# Attack Timeline

#### Day 1

- `12:24:18` Phishing binary delivered (mchen &rarr; lpearson)
- `12:30:31` Runs myodb_client_hotfix.exe (Sliver implant)
- `12:33:37` C2 out to Proxy Alpha 9.9.9.9
- `12:40:44` Persistence on wks-lpearson
- `12:47:33` Credential discovery
- `12:53:19` Invoke-Kerberoast
- `19:22:22` Lateral move to FILE-3 (svc_filesvc)
- `19:23:22` Sliver persistence on FILE-3
- `19:38:16` Sleeper planted on PRINT-1 (6.6.6.6)
- `19:39:22` Sliver beacon from FILE-3

#### Day 2

- `06:17:23` LDAP recon from FILE-3
- `06:28:45` Target picked: CTX-13
- `06:38:54` Port scan of CTX-13
- `06:45:12` Failed move to CTX-13
- `07:02:33` WinRM to DC-1 (svc_filesvc)
- `07:15:48` Malicious GPO: scheduled task
- `07:23:41` GPO fires, Sliver via Proxy Bravo 2.2.2.2
- `07:31:18` Host recon on CTX-13

#### Day 3

- `09:15:22` Citrix tool via certutil (3.3.3.3)
- `09:28:47` CitrixBleed exploited from CTX-13
- `09:35:17` Published-desktop breakout
- `09:41:33` VAULT-3 via hijacked session
- `13:52:08` Data staged on CTX-13
- `14:30:00` Exfil over C2 (Proxy Bravo)

#### Day 4

- `02:17:44` Three Meridian files tampered on VAULT-3

#### Day 34

- `03:05:05` PRINT-1 restart (planned maintenance)
- `03:12:20` Sleeper wakes via Proxy Charlie

---

# Documenting the Investigation

![outline w:1100](images/ioc-tracker.png)

Crowdstrike Incident Response Tracker: https://www.crowdstrike.com/en-us/blog/crowdstrike-releases-digital-forensics-and-incident-response-tracker/

---

# Alternate Entry Points

- Phishing email being sent to lpearson
- Use of Sliver, either time
- Invoke-Kerberoast
- Service accounts for lateral movement
- Group Policy Object creation by service account
- CTX workstations communicating with internet resources
- Meridian reach out and say their data has been changed

---
<!-- _class: img-right -->

# Informing the Business

![outline w:600](images/frustrated-stakeholders.png)

- Security Leadership
- Business Leadership
- The Board
- Legal
    - Regulators
    - Litigation
    - Customer Notification
- Public Relations
- Law Enforcement

---

## In Incident Response, AI is Excellent at

  - Explaining a single artefact fast: decode an obfuscated PowerShell blob, tell you what a LOLBin or registry key does, draft the detection query.
  - First-pass triage on one host: "here's what looks odd, here's what to pull next." A tireless junior analyst.
  - Writing it up: turning scratch notes and a raw timeline into a clean report, exec summary or client update.

---

<!-- _class: img-right -->

## But Terrible at

![alt text](images/ai-novelty.png)

  - Scoping the whole intrusion: holding 40 hosts in its head, correlating across them, and knowing it hasn't found the edge yet. It declares victory early.
  - Catching the novel: it pattern-matches what's been seen before, so an advanced adversary doing something new is exactly what it misses.
  - Being accountable: it hallucinates with confidence, can't testify, and can't own a call. Someone has to sign their name to "this is what happened."

## Ultimately 
- Leadership want **people** to hold accountable

---
<!-- _class: end -->
<!-- _paginate: false -->

Thank you.

Further Reading: https://dynamicincidentresponse.com/

# Questions?

`hello@lpearson.co` · `lpearson.co` · `thisinsecureworld.com/p/qu-2026-intrusion-response/`

---
<!-- _class: two-up -->

![outline left w:700](images/kerberos-fair-analogy.png)
![outline right w:800](images/kerberos-flow-diagram.png)

---

<!-- _class: two-up -->

![alt text](images/kerberoast-fair-cracking.png)
![outline left w:700](images/kerberoasting-attack-flow.png)

---