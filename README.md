# Vocallabs Product Feedback Report

**Prepared by:** Sahaj Malla  

> **Note:** A browser network HAR file was saved while testing the Voci voice agent and is available for reference alongside this report.

---

## Evaluation Approach

To prepare this report, I evaluated Vocallabs through multiple touchpoints:
* Website and landing page experience
* Enterprise signup flow
* Voci voice agent demo
* Browser Network and HAR analysis
* Developer console inspection
* Public API documentation review

The goal was not to identify isolated bugs, but to understand the product from the perspective of a prospective customer, technical evaluator, and enterprise buyer.

---

## Prioritisation Logic

I prioritized feedback using the following framework:
* **P0 — Activation Blocker:** Issues that prevent users from accessing or experiencing the product.
* **P1 — Trust & Conversion:** Issues that affect purchase decisions, onboarding confidence, or buyer perception.
* **P2 — Positioning & Differentiation:** Areas where product strengths exist but are not communicated effectively.
* **P3 — Long-Term Competitive Considerations:** Architectural or strategic observations that may become important as the company scales.

---

## Feedback 1 (P0): Enterprise Verification Flow Breaks During Activation

**Observed**  
While signing up for an enterprise account, the verification email contained a verification link that pointed to an internal hostname (`http://auth/...`) rather than a publicly accessible URL. Attempting to open the verification link resulted in a DNS resolution failure, making account verification impossible.

**Problem**  
This creates a hard failure at the activation stage of the funnel. The affected user has already discovered the product, decided to evaluate it, completed signup, verified their email inbox, and is now blocked from accessing the platform entirely. Unlike many onboarding issues that merely create friction, this prevents the user from reaching any product value. Because the issue occurs at the highest-intent moment in the journey, the conversion impact is likely disproportionate to the engineering effort required to fix it.

**Ship Instead**
* Ensure all outbound verification emails generate publicly routable URLs instead of internal service hostnames.
* Add automated verification-link testing as part of deployment validation.
* Add CI/CD checks that detect internal hostnames appearing in customer-facing emails.
* Monitor verification-link success rate as a core onboarding metric.

---

## Feedback 2 (P1): Security & Compliance Messaging Does Not Translate Into Buyer Confidence

**Observed**  
Vocallabs prominently communicates security and compliance standards throughout the website. However, the messaging is primarily presented in compliance-oriented language and certification terminology. As a prospective customer, I could see that standards were being followed, but it was difficult to understand what customer risks they mitigate, how they affect day-to-day operations, what measurable guarantees they provide, and how they compare against competing platforms.

**Problem**  
Most buyers evaluating AI voice infrastructure are not compliance specialists. They are founders, operations leaders, customer support heads, and business owners trying to answer practical questions:
* Is customer data safe?
* Can recordings be accessed by unauthorized parties?
* How reliable is the platform?
* What evidence exists beyond certification badges?

The current implementation demonstrates compliance but does not fully communicate trust.

**Ship Instead**  
Introduce a dedicated Trust Center that translates security standards into business outcomes.

| Security Standard | Customer Interpretation |
| :--- | :--- |
| **Encryption** | Customer recordings remain protected during storage and transit |
| **Access Controls** | Only authorized personnel can access call data |
| **Compliance Audits** | Security controls are independently validated |

Additionally include: uptime metrics, security architecture summaries, incident response commitments, data retention policies, and audit outcomes. This would turn compliance from a checkbox into a competitive advantage.

---

## Feedback 3 (P2): Product Capabilities Are More Advanced Than The Website Communicates

**Observed**  
After testing the Voci demo and reviewing browser activity and API documentation, I found evidence that the platform extends far beyond a traditional voice bot. Examples include real-time voice communication, knowledge ingestion capabilities, website crawling support, FAQ management, audit capabilities, telephony support, and conversation analytics.

**Problem**  
A first-time visitor may conclude that Vocallabs is simply an AI calling solution. However, the underlying platform appears much closer to an enterprise conversation infrastructure stack consisting of: Voice AI, Knowledge management, Analytics, Auditing, Workflow automation, and Telephony integration. This creates a positioning gap. The product appears more powerful than the story currently being told.

**Ship Instead**  
Introduce a dedicated Platform Architecture section that visually explains how the system operates. 

> **Voice Agent** &rarr; **Knowledge Layer** &rarr; **Analytics Layer** &rarr; **Audit & Quality Layer** &rarr; **Business Systems**

This would help prospective customers understand the broader platform vision and differentiate Vocallabs from voice-generation competitors.

---

## Feedback 4 (P2): Strong Observability Capabilities Are Hidden From Prospective Buyers

**Observed**  
One of the most interesting findings from the API documentation and network analysis was the apparent emphasis on observability and post-call intelligence. Endpoints such as `getCallConversation`, `getCallData`, `getCallTimeline`, `uploadAudits`, and `getAllAudits` suggest that Vocallabs is not only generating conversations but also investing in transcript analysis, call timelines, auditing workflows, quality assurance, and operational monitoring.

**Problem**  
These capabilities appear to be valuable differentiators but are not prominently communicated during product evaluation. As a result, prospects may compare Vocallabs against simple voice-agent providers rather than enterprise conversation platforms. The product risks being evaluated on voice quality alone when additional value clearly exists elsewhere in the stack.

**Ship Instead**  
Surface observability capabilities earlier in the customer journey. Potential additions include:
* Call analytics dashboard screenshots
* Quality assurance workflows
* Audit pipeline examples
* Conversation intelligence case studies
* Operational monitoring capabilities

Positioning these capabilities more prominently would strengthen differentiation and appeal to enterprise buyers focused on scale and reliability.

---

## Feedback 5 (P1): Homepage Performance Creates A Credibility Gap

**Observed**  
HAR analysis showed an overall page load time of approximately 4.3 seconds. The network waterfall suggests that the embedded YouTube player is loading significant resources during page initialization rather than after explicit user interaction.

**Problem**  
That's heavy for a landing page. The likely cause is the YouTube embed loading its full player JavaScript bundle on page initialization. For a company selling real-time voice AI, a slow landing page creates an ironic credibility gap. Technical buyers frequently evaluate infrastructure companies with browser developer tools open. A multi-second landing page load can unintentionally weaken confidence in a product whose core value proposition is responsiveness and real-time performance.

**Ship Instead**
* Lazy-load the YouTube player.
* Load video resources only after user interaction.
* Defer non-critical scripts.
* Optimize above-the-fold assets.

Reducing page weight would improve both conversion and perceived technical quality.

---

## Strategic Observation: WebSocket Today, WebRTC Tomorrow

While testing Voci, I observed that real-time communication is currently delivered through a dedicated WebSocket connection (`wss://call.vocallabs.ai/ws/?agent=...`). This appears to be a deliberate architectural choice.

**Tradeoff**  
WebSocket provides several advantages:
* Simpler implementation
* Easier debugging
* Better observability
* Predictable infrastructure
* Reliable operation behind restrictive corporate firewalls

However, WebRTC offers capabilities specifically designed for real-time media:
* Native packet-loss handling
* Built-in jitter buffers
* Adaptive media transport
* Peer-to-peer routing
* Lower end-to-end audio latency

For voice AI, where every 100ms of latency matters, these differences become increasingly visible under poor network conditions. At the current stage, WebSocket appears to be a pragmatic engineering decision that optimizes development velocity and operational simplicity. However, as Vocallabs expands into larger enterprise call-center deployments, the latency and media-quality advantages of WebRTC-native competitors may become a meaningful buying consideration. This is not an immediate concern, but it is a strategic architectural tradeoff worth monitoring as the product scales.
