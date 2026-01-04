---
title: "Intelligence Fundamentals: From Raw Data to Actionable Intelligence"
slug: "intelligence-fundamentals-part-0"
date: 2025-01-05
draft: false
series: "Intelligence Fundamentals"
series_order: 0
tags: ["intelligence", "OSINT", "SIGINT", "HUMINT", "threat-intelligence", "CTI", "fundamentals"]
categories: ["Intelligence"]
description: "A comprehensive deep-dive into the foundations of intelligence work. Understanding the data-to-wisdom pipeline, collection disciplines (INTs), the intelligence cycle, and how these concepts apply to modern cyber threat intelligence."
author: "burnedsignal"
toc: true
---

## TL;DR

Intelligence is not just data collection—it's the systematic transformation of raw information into actionable insights that drive decision-making. This foundational piece covers:

- The **DIKW Pyramid**: Data → Information → Knowledge → Wisdom progression
- **Intelligence Levels**: Strategic, Operational, and Tactical distinctions
- **Collection Disciplines (INTs)**: HUMINT, SIGINT, OSINT, GEOINT, MASINT, FININT, and more
- **Source Evaluation**: The Admiralty System (A-F, 1-6) for rating source reliability and information credibility
- **The Intelligence Cycle**: A continuous 6-phase process from requirements to feedback
- **Cognitive Biases**: Common analytical pitfalls and historical intelligence failures
- **Structured Analytical Techniques (SATs)**: ACH, Key Assumptions Check, Red Team, Devil's Advocacy
- **Attribution Confidence**: IC standards for expressing certainty (ICD 203)
- **Legal & Ethical Framework**: GDPR, CFAA, and ethical OSINT principles
- **Counterintelligence**: Denial, deception, and false flag awareness
- **Application to Cyber Threat Intelligence (CTI)**: How traditional intelligence concepts map to modern threat detection

---

## What is Intelligence?

Intelligence is frequently misunderstood. It is not merely the accumulation of facts, nor is it synonymous with data or information. Intelligence represents the **refined product** of a rigorous analytical process—the transformation of disparate data points into coherent, contextual understanding that enables informed decision-making.

> "Intelligence is information that has been collected, integrated, evaluated, analyzed, and interpreted."
> — Joint Publication 2-0, Joint Intelligence

The distinction matters. A sensor detecting network traffic produces **data**. When that data is parsed and correlated, it becomes **information**. When an analyst determines the traffic pattern matches known command-and-control behavior, it becomes **knowledge**. When leadership uses that knowledge to authorize defensive measures or attribute the activity to a specific threat actor, we approach **wisdom**.

---

## The DIKW Pyramid: From Noise to Insight

The Data-Information-Knowledge-Wisdom (DIKW) hierarchy provides a foundational framework for understanding how raw inputs transform into actionable intelligence.

![DIKW Pyramid](/images/intelligence-fundamentals/dikw-pyramid.svg)

### Data (What?)

Raw, unprocessed facts without context. In cyber operations, this includes:
- Packet captures
- Log entries
- File hashes
- IP addresses
- DNS queries

Data alone answers "What happened?" but provides no meaning.

**Example**: `192.168.1.105 connected to 45.33.32.156:443 at 03:42:17 UTC`

### Information (Who, When, Where?)

Data becomes information when organized, structured, and given context. Information answers basic interrogatives.

**Example**: "Workstation WS-105, assigned to user john.doe in the Finance department, established an encrypted connection to an IP address geolocated to St. Petersburg, Russia, during non-business hours."

### Knowledge (How? Why?)

Knowledge emerges from analyzing patterns, correlating multiple information sources, and applying expertise. It provides understanding of mechanisms and motivations.

**Example**: "This connection pattern matches known Cobalt Strike beacon behavior. The destination IP is associated with infrastructure previously attributed to APT29. The Finance department has access to sensitive M&A documentation. This likely represents initial access by a sophisticated threat actor conducting economic espionage."

### Wisdom (What Should We Do?)

Wisdom synthesizes knowledge with experience, ethical considerations, and strategic context to inform decisions. It enables foresight and optimal action selection.

**Example**: "Based on the attribution confidence level, potential business impact, and geopolitical context, we recommend: (1) immediate network isolation of affected systems, (2) engagement of incident response, (3) notification of legal counsel regarding potential nation-state involvement, and (4) coordination with sector ISAC partners who may face similar targeting."

---

## The Four Qualities of Actionable Intelligence

Not all intelligence is created equal. Effective intelligence exhibits four critical characteristics:

| Quality | Definition | Counter-Example |
|---------|------------|-----------------|
| **Actionable** | Enables specific decisions or actions | "Bad actors exist on the internet" |
| **Timely** | Delivered when it can still influence outcomes | Attribution report arriving 6 months post-breach |
| **Relevant** | Addresses the consumer's specific requirements | Sending ICS/SCADA threat intel to a SaaS company |
| **Accurate** | Factually correct with appropriate confidence levels | Misattributing commodity malware to an APT |

The acronym **ATRA** (Actionable, Timely, Relevant, Accurate) provides a useful mnemonic for evaluating intelligence quality.

---

## Source Reliability and Information Credibility

Intelligence is only as good as its sources. The **Admiralty System** (also known as the NATO System) provides a standardized method for evaluating both the reliability of sources and the credibility of the information they provide.

### Source Reliability (A-F)

| Rating | Description | Interpretation |
|--------|-------------|----------------|
| **A** | Completely Reliable | No doubt about authenticity, trustworthiness, or competency; has a history of complete reliability |
| **B** | Usually Reliable | Minor doubt; has a history of valid information most of the time |
| **C** | Fairly Reliable | Doubt of authenticity or trustworthiness; has provided valid information in the past |
| **D** | Not Usually Reliable | Significant doubt; has provided valid information in the past |
| **E** | Unreliable | Lacking in authenticity, trustworthiness, or competency; history of invalid information |
| **F** | Cannot Be Judged | No basis for evaluating reliability; new or unknown source |

### Information Credibility (1-6)

| Rating | Description | Interpretation |
|--------|-------------|----------------|
| **1** | Confirmed | Confirmed by independent sources; logical in itself; consistent with other information |
| **2** | Probably True | Not confirmed; logical in itself; consistent with other information |
| **3** | Possibly True | Not confirmed; reasonably logical in itself; agrees with some other information |
| **4** | Doubtfully True | Not confirmed; possible but not logical; no other information on the subject |
| **5** | Improbable | Not confirmed; not logical in itself; contradicted by other information |
| **6** | Cannot Be Judged | No basis for evaluating validity |

### Combined Rating Example

An intelligence report might be rated **B2** meaning:
- The source is "Usually Reliable" (has provided good intel before)
- The information is "Probably True" (logical but not independently confirmed)

**Critical Practice**: Always include source ratings in intelligence products. A report stating "APT29 is targeting financial institutions" means nothing without knowing if this comes from an A1 source (highly confident) or an E5 source (essentially rumor).

> **CTI Application**: When consuming threat intelligence feeds, demand source transparency. Commercial feeds that don't provide confidence levels or source characterization should be treated with skepticism.

---

## Intelligence Levels: Strategic, Operational, Tactical

Intelligence requirements and products vary significantly based on the consumer's decision-making horizon.

![Intelligence Levels](/images/intelligence-fundamentals/intelligence-levels.svg)

**Critical Insight**: Organizations often over-invest in tactical intelligence (IOC feeds) while under-investing in strategic and operational intelligence. IOCs are inherently perishable—they represent the *artifacts* of an attack, not the *behavior*. Mature intelligence programs balance all three levels.

---

## Intelligence Collection Disciplines (The "INTs")

Intelligence collection is organized into specialized disciplines, each with distinct sources, methods, and analytical requirements. These disciplines are collectively referred to as "INTs."

### Primary Collection Disciplines

#### HUMINT (Human Intelligence)

**Definition**: Intelligence derived from human sources through interpersonal contact.

**Sources**:
- Informants and agents
- Diplomatic reporting
- Traveler debriefings
- Defectors
- Interrogations
- Elicitation (social engineering in cyber context)

**Characteristics**:
- Oldest intelligence discipline
- Provides intent and motivation (the "why")
- High value, high risk
- Difficult to scale
- Requires significant operational security

**Cyber Application**: Engaging with threat actors in underground forums, recruiting sources within criminal organizations, social engineering assessments.

#### SIGINT (Signals Intelligence)

**Definition**: Intelligence derived from the interception of signals, including communications and electronic emissions.

**Sub-disciplines**:

| Abbreviation | Name | Focus |
|--------------|------|-------|
| **COMINT** | Communications Intelligence | Voice, text, messaging intercepts |
| **ELINT** | Electronic Intelligence | Non-communication signals (radar, telemetry) |
| **FISINT** | Foreign Instrumentation Signals Intelligence | Weapons systems, space vehicle telemetry |

**Characteristics**:
- Technical, scalable collection
- Volume creates analytical challenges
- Encryption is a significant obstacle
- Provides communication patterns and content
- Legal frameworks vary significantly by jurisdiction

**Cyber Application**: Network traffic analysis, malware C2 protocol analysis, encrypted traffic metadata analysis, passive DNS collection.

#### OSINT (Open Source Intelligence)

**Definition**: Intelligence derived from publicly available sources.

**Sources**:
- Media (print, broadcast, online)
- Academic publications
- Government reports and filings
- Social media
- Commercial databases
- Technical documentation
- Conference proceedings
- Court records
- Patent filings

**Characteristics**:
- Accessible and cost-effective
- Volume requires sophisticated filtering
- Verification challenges
- Estimated to provide 60-80% of intelligence requirements
- Legal to collect, but ethical considerations remain

**Cyber Application**: Threat actor research, vulnerability intelligence, leaked credentials monitoring, brand protection, attack surface mapping.

#### GEOINT (Geospatial Intelligence)

**Definition**: Intelligence derived from the analysis of imagery and geospatial information.

**Sources**:
- Satellite imagery (optical, radar, multispectral)
- Aerial photography
- Mapping data
- Location-based services
- Geolocation metadata

**Characteristics**:
- Provides physical context
- Commercial availability has democratized access
- Temporal analysis reveals patterns
- Integration with other INTs enhances value

**Cyber Application**: Physical infrastructure mapping, data center identification, supply chain verification, attribution support (correlating physical and cyber activities).

#### MASINT (Measurement and Signature Intelligence)

**Definition**: Intelligence derived from the analysis of data obtained from sensing instruments for the purpose of identifying distinctive features associated with the source, emitter, or sender.

**Focus Areas**:
- Radar signatures
- Acoustic signatures
- Nuclear radiation
- Chemical/biological detection
- Spectral analysis
- Materials analysis

**Characteristics**:
- Highly technical
- Requires specialized sensors
- Provides unique identification capabilities
- Often complements other INTs

**Cyber Application**: Electromagnetic emanations analysis (TEMPEST), side-channel attacks, hardware fingerprinting.

### Emerging and Specialized Disciplines

| INT | Full Name | Focus |
|-----|-----------|-------|
| **FININT** | Financial Intelligence | Monetary transactions, sanctions evasion, money laundering |
| **SOCMINT** | Social Media Intelligence | Social platform analysis, influence operations |
| **TECHINT** | Technical Intelligence | Foreign weapons and equipment analysis |
| **CYBINT** | Cyber Intelligence | Threat actor capabilities, infrastructure, malware analysis |
| **MEDINT** | Medical Intelligence | Health-related intelligence, biological threats |

---

## The Intelligence Cycle

The intelligence cycle describes the process by which raw information is converted into finished intelligence and delivered to consumers. While various models exist (5, 6, or 7 phases depending on the source), the six-phase model is widely adopted:

![Intelligence Cycle](/images/intelligence-fundamentals/intelligence-cycle.svg)

### Phase 1: Requirements (Planning & Direction)

Intelligence begins with a question. Requirements define:
- **Priority Intelligence Requirements (PIRs)**: Critical questions leadership needs answered
- **Essential Elements of Information (EEIs)**: Specific data points needed to address PIRs
- **Collection emphasis**: Which INTs to prioritize

**Example PIR**: "What are the most likely threat actors to target our organization in the next 12 months, and what TTPs do they employ?"

### Phase 2: Collection

Systematic gathering of raw information using appropriate INTs. Effective collection:
- Aligns with defined requirements
- Employs multiple INTs for corroboration
- Documents sources and methods
- Maintains chain of custody

### Phase 3: Processing (Exploitation)

Raw collected material is converted into usable form:
- Translation
- Decryption
- Format conversion
- Data normalization
- Deduplication
- Initial validation

### Phase 4: Analysis (Production)

The intellectual core of intelligence work. Analysis involves:
- Correlation of multiple sources
- Pattern recognition
- Hypothesis development and testing
- Assessment of reliability and credibility
- Confidence level assignment
- Production of finished intelligence products

**Analytical Rigor**: Professional analysis employs structured analytical techniques (SATs) to mitigate cognitive biases and ensure defensible conclusions.

### Phase 5: Dissemination

Finished intelligence must reach consumers in appropriate formats and through secure channels:
- Written reports
- Briefings
- Alerts and notifications
- Machine-readable feeds (STIX/TAXII)
- Dashboards

### Phase 6: Feedback (Evaluation)

Consumers provide feedback on:
- Relevance to their needs
- Timeliness of delivery
- Actionability of recommendations
- Accuracy (over time)

This feedback refines future requirements, completing the cycle.

---

## Cognitive Biases in Intelligence Analysis

Even experienced analysts are susceptible to cognitive biases—systematic errors in thinking that can lead to flawed assessments. Recognizing these biases is the first step toward mitigating them.

### Common Analytical Biases

| Bias | Description | Example in CTI |
|------|-------------|----------------|
| **Confirmation Bias** | Seeking information that confirms existing beliefs while discounting contradictory evidence | Attributing an attack to a known APT without considering other actors who use similar TTPs |
| **Anchoring Bias** | Over-relying on the first piece of information encountered | Initial IOC from a vendor report shapes all subsequent analysis, even when new evidence suggests otherwise |
| **Mirror Imaging** | Assuming adversaries think and act as we would | Expecting nation-state actors to follow Western corporate security practices |
| **Groupthink** | Conforming to group consensus to avoid conflict | Entire team agrees on attribution without vigorous debate |
| **Availability Heuristic** | Overweighting recent or memorable events | Focusing on ransomware after high-profile attacks while neglecting persistent espionage threats |
| **Fundamental Attribution Error** | Attributing adversary actions to intent rather than circumstance | Assuming a targeted attack when the compromise was opportunistic |

### Historical Intelligence Failures

These biases have contributed to significant intelligence failures:

- **Pearl Harbor (1941)**: Mirror imaging—analysts couldn't conceive Japan would attack a superior naval power
- **Bay of Pigs (1961)**: Groupthink—dissenting voices were silenced in planning sessions
- **Iraqi WMD (2003)**: Confirmation bias—evidence was interpreted to support predetermined conclusions
- **9/11 (2001)**: Failure to connect dots due to organizational silos and availability heuristic

**Key Takeaway**: Structured analytical techniques exist specifically to counteract these biases. No analyst is immune—the goal is to create processes that surface and challenge assumptions.

---

## Structured Analytical Techniques (SATs)

Structured Analytical Techniques are formal methods designed to externalize thinking, challenge assumptions, and improve the rigor of intelligence analysis.

### Analysis of Competing Hypotheses (ACH)

Developed by Richards Heuer at CIA, ACH forces analysts to consider multiple explanations simultaneously.

**Process**:
1. Identify all reasonable hypotheses
2. List significant evidence and arguments
3. Create a matrix: evidence vs. hypotheses
4. Assess consistency of each piece of evidence with each hypothesis
5. Refine the matrix, adding evidence as needed
6. Draw tentative conclusions based on which hypotheses have the least inconsistent evidence
7. Analyze sensitivity—identify evidence that would change the conclusion
8. Report conclusions with identified milestones for future observation

**Critical Insight**: ACH focuses on *disconfirming* evidence rather than confirming evidence. A hypothesis with no inconsistent evidence is more likely correct than one with abundant supporting evidence but key inconsistencies.

### Key Assumptions Check

Systematically identifies and challenges the assumptions underlying an assessment.

**Questions to Ask**:
- How confident are we in each assumption?
- What would change if this assumption were wrong?
- Under what circumstances might this assumption fail?
- What evidence supports this assumption?

### Red Team Analysis

Adopting the adversary's perspective to identify vulnerabilities in our own analysis or defenses.

**Applications**:
- Challenging defensive plans from attacker viewpoint
- Validating threat assessments
- Testing incident response procedures
- Evaluating attribution conclusions

### Devil's Advocacy

Deliberately arguing against the prevailing assessment to test its robustness.

**Value**: Forces the team to articulate *why* the mainstream view is correct, strengthening the overall analysis or revealing weaknesses.

### Pre-Mortem Analysis

Imagining a future failure and working backward to identify causes.

**Process**: "Assume our assessment was wrong. What happened? What did we miss?"

---

## Attribution Confidence Levels

Attribution—determining who conducted an operation—is one of the most challenging aspects of cyber intelligence. The Intelligence Community uses standardized confidence levels to communicate certainty.

### IC Confidence Standards (ICD 203)

| Confidence Level | Meaning |
|-----------------|---------|
| **High Confidence** | Based on high-quality information from multiple sources; well-established analytical judgments; few information gaps |
| **Moderate Confidence** | Based on credibly sourced and plausible information, but insufficient for higher confidence; OR based on a well-reasoned analytical judgment with some information gaps |
| **Low Confidence** | Based on questionable or implausible information; limited analytical judgment possible; significant information gaps |

### Probability Language

| Term | Approximate Probability |
|------|------------------------|
| Almost Certain | 95%+ |
| Highly Likely | 80-95% |
| Likely | 55-80% |
| Roughly Even Chance | 45-55% |
| Unlikely | 20-45% |
| Highly Unlikely | 5-20% |
| Remote | <5% |

### Why Cyber Attribution is Difficult

1. **Technical Attribution vs. Actor Attribution**: Tracing an attack to an IP address is not the same as identifying the human or organization responsible
2. **False Flags**: Sophisticated actors deliberately leave indicators pointing to other groups
3. **Shared Infrastructure**: Multiple actors may use the same bulletproof hosting, tools, or techniques
4. **Tool Proliferation**: Nation-state tools leak and are adopted by other actors (e.g., Eternal Blue)
5. **Contractor Ecosystem**: The line between state actors and criminal contractors is increasingly blurred

**Best Practice**: Always state confidence levels explicitly. "We assess with moderate confidence that APT29 conducted this operation" is more useful than "APT29 did it."

---

## Legal and Ethical Framework

Intelligence collection, even from open sources, operates within legal and ethical boundaries. Understanding these constraints is essential for professional practice.

### Legal Considerations

| Framework | Scope | Key Requirements |
|-----------|-------|------------------|
| **GDPR** (EU) | Personal data of EU residents | Lawful basis for processing; data minimization; right to erasure |
| **CFAA** (US) | Computer access | Prohibits unauthorized access; "exceeding authorized access" is broadly interpreted |
| **ECPA** (US) | Electronic communications | Restrictions on interception and stored communications access |
| **National Laws** | Varies by jurisdiction | Many countries have specific intelligence and cybersecurity laws |

### Ethical OSINT Principles

1. **Legality**: All collection must comply with applicable laws
2. **Necessity**: Collect only what is required for the intelligence requirement
3. **Proportionality**: Methods should be proportionate to the threat
4. **Accuracy**: Verify information before acting on it
5. **Source Protection**: Do not unnecessarily expose sources or methods
6. **Minimization**: Avoid collecting or retaining unnecessary personal data
7. **No Harm**: Collection should not cause harm to uninvolved parties

### Responsible Disclosure

When intelligence collection reveals vulnerabilities:
- Follow coordinated disclosure practices
- Notify affected parties when appropriate
- Consider public safety implications
- Document decisions and rationale

**CTI Application**: Commercial threat intelligence providers must balance information sharing with privacy regulations. Always consider the legal basis for collecting, processing, and sharing threat data.

---

## Counterintelligence Considerations

No intelligence discussion is complete without acknowledging counterintelligence (CI)—the discipline of protecting against adversary intelligence activities and deception.

### Denial and Deception (D&D)

Sophisticated adversaries actively work to:

- **Deny** intelligence by concealing activities, encrypting communications, and using operational security
- **Deceive** by planting false information, conducting false flag operations, and manipulating our collection

### False Flags in Cyber Operations

Threat actors routinely attempt to mislead attribution:

| Technique | Description |
|-----------|-------------|
| **Language Artifacts** | Inserting foreign language strings in code |
| **Timezone Manipulation** | Compiling malware during another region's working hours |
| **TTP Mimicry** | Deliberately using another group's known techniques |
| **Infrastructure Borrowing** | Using infrastructure associated with other actors |
| **Tool Reuse** | Using leaked tools from other groups |

**Example**: The Olympic Destroyer malware (2018) contained false flags pointing to North Korea, China, and Russia. Attribution required looking beyond surface indicators.

### CI Implications for CTI

- **Source Validation**: Adversaries may feed disinformation through trusted channels
- **Indicator Poisoning**: IOC feeds can be contaminated with false data
- **Perception Management**: Threat reports may be influenced by actors seeking to shape narratives
- **Collection Awareness**: Sophisticated actors know what we collect and adjust behavior accordingly

**Key Takeaway**: Maintain healthy skepticism. The absence of evidence is not evidence of absence, and the presence of evidence may be deliberately planted.

---

## Application to Cyber Threat Intelligence (CTI)

The traditional intelligence framework maps directly to cyber threat intelligence:

| Traditional Concept | CTI Application |
|--------------------|-----------------|
| PIRs | "What ransomware groups target our sector?" |
| HUMINT | Dark web forum engagement, insider threat programs |
| SIGINT | Network traffic analysis, C2 protocol research |
| OSINT | Threat actor blogs, paste sites, code repositories |
| GEOINT | Infrastructure geolocation, data residency compliance |
| Intelligence Cycle | CTI program operations |

### The Pyramid of Pain

David Bianco's Pyramid of Pain illustrates the relationship between indicator types and the cost to adversaries when those indicators are denied:

![Pyramid of Pain](/images/intelligence-fundamentals/pyramid-of-pain.svg)

**Key Insight**: Organizations that focus detection on Tactics, Techniques, and Procedures (TTPs) rather than atomic indicators create significantly higher costs for adversaries. This aligns with the MITRE ATT&CK framework's behavior-based approach.

---

## Case Study: From Data to Intelligence

Let's trace how raw data becomes actionable intelligence:

### The Data

```
timestamp: 2025-01-05T03:42:17Z
src_ip: 192.168.1.105
dst_ip: 45.33.32.156
dst_port: 443
bytes_out: 2847
bytes_in: 156892
```

### The Information

- Source: Workstation WS-105 (Finance, user: john.doe)
- Destination: IP geolocated to St. Petersburg, Russia
- Provider: Bulletproof hosting provider (documented abuse history)
- Time: 03:42 UTC (non-business hours for US East Coast)
- Traffic pattern: Small request, large response (possible exfiltration or beacon check-in)

### The Knowledge

After analysis and correlation:
- Destination IP appears in threat intelligence feeds associated with APT29 infrastructure
- Traffic pattern matches known Cobalt Strike malleable C2 profile
- john.doe received a spearphishing email with weaponized document 6 hours prior
- Similar activity observed at 3 other organizations in same sector (ISAC reporting)
- Finance department contains sensitive M&A documentation

### The Wisdom

**Assessment**: High confidence initial access by APT29 or affiliated actor. Economic espionage targeting M&A intelligence is consistent with historical targeting patterns.

**Recommendations**:
1. Initiate incident response procedures
2. Preserve forensic evidence
3. Engage legal counsel (nation-state attribution implications)
4. Coordinate with sector ISAC
5. Brief executive leadership on potential business impact
6. Prepare regulatory notification materials

---

## Looking Ahead

This foundational piece establishes the conceptual framework for intelligence operations. Subsequent articles in this series will explore each collection discipline in depth:

- **Part 1**: OSINT Deep Dive — Sources, Tools, and Tradecraft
- **Part 2**: SIGINT Fundamentals — From RF to Packet
- **Part 3**: HUMINT in Cyber Operations — Social Engineering and Beyond
- **Part 4**: GEOINT for Cyber — Physical-Digital Convergence
- **Part 5**: Building a CTI Program — Operationalizing Intelligence
- **Part 6**: Advanced Analytical Techniques — Deep Dive into SATs and Bias Mitigation

---

## References and Further Reading

### Official Publications
- Joint Publication 2-0: Joint Intelligence (US DoD)
- Intelligence Community Directive 203: Analytic Standards (Confidence Levels)
- NIST SP 800-150: Guide to Cyber Threat Information Sharing
- NATO STANAG 2022: Intelligence Reports

### Academic Sources
- Lowenthal, M. (2019). *Intelligence: From Secrets to Policy*
- Heuer, R. (1999). *Psychology of Intelligence Analysis*
- Clark, R. (2019). *Intelligence Analysis: A Target-Centric Approach*

### CTI-Specific Resources
- MITRE ATT&CK Framework: https://attack.mitre.org
- Bianco, D. (2013). *The Pyramid of Pain*: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html
- STIX/TAXII Standards: https://oasis-open.github.io/cti-documentation/

---

*This article is part of the Intelligence Fundamentals series. The series aims to bridge traditional intelligence tradecraft with modern cyber threat intelligence operations.*

*Have questions or feedback? Reach out via the [About](/en/about/) page.*
