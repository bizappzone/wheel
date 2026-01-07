# Implementation Brief

## 1. Executive Summary

**URL:** https://bigbrainplan.com/projects/LfaKyxkUbgsvBZntXQBo/product-definition/marketing/executiveSummary
**Contains:** High-level project overview, goals, and business context

The Learning Wheel seizes a $15–20B global K–12 digital content market growing at 8–12% CAGR through 2028, targeting a $450M–$1B SAM among 3–5 million digitally fluent STEM, ESL, and special education teachers who spend $100–300 annually on subscriptions amid chronic time scarcity and demand for peer-validated resources. We differentiate in the untapped reciprocal/curated quadrant—beyond TpT's transactional noise and Twinkl's one-way curation—by positioning the credit economy as a professional meritocracy that rewards educator contributions with compounding access and recognition. Branded around the message "Your expertise deserves to grow in value," this strategy is timely amid subscription normalization, AI threats to static content, and shifts toward community-driven quality, enabling viral growth through peer invitations and organic inventory buildup.

In a $160–180B global digital education market, The Learning Wheel addresses a critical gap for self-directed educators frustrated by low-trust sharing sites and rigid publisher platforms, focusing on high-agency segments like STEM and ESL teachers who represent a 3–5 million-user SAM with $450M–$1B subscription potential. Teacher-initiated spending is normalized at $100–300/year, fueled by workload pressures and curriculum evolution, creating fertile ground for platforms blending content with community and reciprocity.

Competitively, we occupy the white space of reciprocal/curated resources on the transactional vs. reciprocal and unvetted vs. curated matrix, outmaneuvering TpT's commercialized search fatigue, Twinkl's uniform content, and OER's chaos via a credit economy that incentivizes quality uploads and peer referrals for viral supply-demand balance. This exploits incumbent weaknesses like transactional friction while mitigating cold-start risks through targeted niche marketing. The brand strategy cements this as a "professional ecosystem," with core messaging "Your expertise deserves to grow in value" across pillars of peer-validated quality, compounding contributions, professional recognition, and fair exchange—delivered in a professionally respectful voice with teal-led visuals evoking trust and collaboration. Launch now to capitalize on subscription maturity, AI-driven needs for human-verified pedagogy, and inflation-pressured classroom budgets: prioritize high-value segments for rapid content density, leverage referrals for 30–35M TAM expansion, and scale via transparent proof points proving ROI like "three resources cover your subscription," driving retention, advocacy, and market disruption.

## 2. Product Definition

**URL:** https://bigbrainplan.com/projects/LfaKyxkUbgsvBZntXQBo/product-definition/product/productSummary
**Contains:** Product Manager perspective including functional requirements, user flows, and product scope

The Learning Wheel is a reciprocal educational ecosystem that monetizes teacher expertise through a credit-based economy rather than direct transactions, utilizing a sophisticated content management and subscription engine. It solves the liquidity crisis in educational resources by allowing educators to trade intellectual capital for platform access, supported by a serverless, scalable technology architecture.

### Product Overview

This platform envisions a closed-loop professional economy where high-quality content contribution acts as the currency for platform access. It differentiates itself through a rigor-first approach to taxonomy and a unique algorithmic credit system that balances supply and demand dynamically.


### Product Vision
The Learning Wheel is designed to be the premier reciprocal ecosystem for professional educators, fundamentally shifting the paradigm from a transactional marketplace to a meritocratic economy. The core vision is to operationalize the intellectual capital of teachers—specifically in high-demand verticals like STEM, Special Education, and ESL—allowing them to subsidize their professional costs through high-quality contributions. Unlike static repositories or peer-to-peer sales platforms, The Learning Wheel functions as a dynamic exchange where the value of a user is defined by the utility they provide to the network. The platform's success hinges on three interconnected pillars: a rigorous **Content Management System** that prioritizes taxonomy and discoverability, a **Subscription Management Engine** that enforces strict access controls, and a proprietary **Credit Economy Engine** that dynamically values content based on algorithmic demand factors.

### Strategic Objectives
The primary strategic objective is to build a self-sustaining library of high-efficacy teaching resources where quality control is crowd-sourced yet institutionally verified. By implementing a 'contribution-as-currency' model, the product aims to solve the 'cold start' problem of content marketplaces; teachers are incentivized not just to consume, but to produce and curate. The system promotes long-term retention by locking accumulated value (credits) into the subscription lifecycle—credits are not cash-out assets but rather tools for sustaining access or expanding the network through peer invitations. This creates a viral loop of acquisition and retention driven purely by professional utility and economic necessity.

### Key Differentiators
*   **The Reciprocal Credit Economy:** Unlike competitors where users pay cash per download, The Learning Wheel utilizes a variable credit award system. This system rewards creators based on the *performance* and *demand* of their content (e.g., seasonal spikes, scarcity in STEM topics), aligning individual incentives with platform health.
*   **Rigorous Taxonomy & Quality Assurance:** Moving beyond simple file tagging, the platform enforces a hierarchical taxonomy (Grade > Subject > Discipline > Difficulty). This structure supports the 'professional tool' positioning, ensuring that search results are pedagogically relevant rather than just keyword matches.
*   **Algorithmic Fairness:** The demand calculation algorithm prevents credit inflation and ensures sustainability. By weighing factors like 'content popularity' against 'seasonal demand patterns,' the system ensures that the economy remains balanced, preventing a surplus of low-value content from gaming the system.

## 3. Technical Stack

- **Frontend:** Next.js
- **UI Library:** Tailwind CSS
- **Mobile:** React Native
- **Backend:** firebase functions
- **Database:** Firestore
- **Deployment:** GCP

## 4. Marketing & Branding

**URL:** https://bigbrainplan.com/projects/LfaKyxkUbgsvBZntXQBo/product-definition/marketing/marketingBranding
**Contains:** Brand identity, voice, UX principles, messaging, and visual design guidelines

The Learning Wheel brand positions the platform as a professional ecosystem for educators who value contribution, recognition, and reciprocity over transactional resource exchanges. The brand identity communicates trustworthiness, educator empowerment, and intellectual respect through a warm-yet-credible visual system and messaging that emphasizes collaborative growth. This branding framework establishes The Learning Wheel as the credible alternative between low-quality sharing sites and corporate publisher platforms.

### Messaging Framework

The core brand message—"Your expertise deserves to grow in value"—anchors four supporting pillars: Quality Through Peers, Contribution That Compounds, Professional Recognition, and Fair Economic Exchange. Messages are tailored by educator segment while maintaining consistent emphasis on reciprocity, respect, and sustainable professional value.


**Core Message & Supporting Pillars:**

**Core Brand Message:** "Your expertise deserves to grow in value."

**Primary Message Pillars:**

1. **Quality You Can Trust** - Peer-validated resources mean you spend time teaching, not vetting materials.
   - *Proof points:* Multi-stage review process, community flagging, demand-based quality signals, transparent ratings from practicing educators
   - *Audience variation (Elementary):* "Resources reviewed by teachers who understand your classroom reality"
   - *Audience variation (STEM/ESL):* "Specialized content validated by subject-matter experts, not generalists"

2. **Contribution That Compounds** - Your work continues earning value as long as colleagues find it useful.
   - *Proof points:* Algorithmic demand multipliers, seasonal relevance tracking, ongoing credit accumulation, performance dashboards showing real-time impact
   - *Audience variation (High contributors):* "Your three best resources could cover your annual subscription—and keep working for you year after year"

3. **Recognition That Matters** - Build professional visibility and reputation within a community of serious educators.
   - *Proof points:* Creator profiles, peer endorsements, content performance metrics, invitation privileges for proven contributors
   - *Audience variation (International/ESL):* "Your multilingual expertise gains recognition across borders"

4. **Fair Economic Exchange** - Transparent credit system rewards what teachers actually need, not arbitrary pricing.
   - *Proof points:* Published credit calculation methodology, demand-based multipliers for STEM/multilingual content, no hidden algorithms, fixed redemption bundles prevent devaluation

**Message Hierarchy:** Primary messaging emphasizes reciprocity and professional respect ("your expertise deserves to grow"). Secondary messaging focuses on practical benefits (time savings, quality assurance, cost offset). Tertiary messaging addresses specific use cases (STEM resources, multilingual content, seasonal materials) and technical credibility (peer review process, algorithmic transparency).

### Brand Voice and Tone

The Learning Wheel brand voice is Professionally Respectful, Transparently Intelligent, Collaboratively Confident, Warmly Credible, and Educator-Centric. The tone balances intellectual respect with approachability—speaking as a knowledgeable peer rather than corporate vendor or cheerleading motivator.


**Brand Personality Characteristics:**

1. **Professionally Respectful** - Acknowledges teacher expertise and time constraints; never condescending or simplistic
2. **Transparently Intelligent** - Explains the "why" behind systems (credit algorithms, quality processes) without jargon or obfuscation
3. **Collaboratively Confident** - Believes in collective teacher wisdom while providing clear guidance and structure
4. **Warmly Credible** - Approachable and human, but substantive and evidence-based
5. **Educator-Centric** - Every communication prioritizes teacher needs, not platform promotion

**Voice Attributes & Tone Variations:**

The core voice is **collegial expertise**—imagine a respected department chair or instructional coach who combines deep knowledge with genuine respect for colleagues' professionalism. Language should be clear and direct without being simplistic, substantive without being academic, and warm without being overly casual.

**Tone by Context:**
- **Marketing/Acquisition:** Confident and value-focused. "Here's how this solves your actual problem" rather than hype or urgency tactics. Example: "Your STEM resources earn 1.5x base credits because we know they require specialized expertise—and teachers need them."
- **Onboarding/Educational:** Patient and thorough. Explain systems clearly with rationale. Example: "Credits accumulate as colleagues use your resources—here's exactly how the demand algorithm works and why it matters for sustainability."
- **Support/Problem-Solving:** Empathetic and solution-oriented. Acknowledge frustration, provide clear paths forward. Example: "I understand the credit calculation seems complex—let me walk you through your specific case."
- **Community/Social:** Celebratory and inclusive. Highlight member contributions and collective achievements. Example: "This week, Maria's bilingual math unit reached 50 classrooms across three countries—exactly the cross-border collaboration we're building together."

**What to Say / What to Avoid:**

**DO:**
- Use "you" and "your" to emphasize teacher agency and ownership
- Explain rationale behind platform decisions ("We use peer review because...")
- Acknowledge real challenges ("We know finding quality STEM resources is frustrating")
- Celebrate specific member contributions with permission
- Use precise language ("peer-validated" not "curated," "credit economy" not "rewards program")

**DON'T:**
- Use corporate jargon ("leverage," "synergy," "ecosystem" overuse)
- Employ artificial urgency ("Limited time!" "Don't miss out!")
- Condescend or oversimplify ("Teaching made easy!" "Effortless lesson planning!")
- Make unsubstantiated claims ("Best resources" without criteria)
- Use educational buzzwords without substance ("21st-century learning," "student-centered" as filler)
- Treat teachers as consumers rather than professionals

**Creative Direction:** Visual and written content should feel like professional development done right—substantive, respectful, and genuinely useful. Think "Harvard Business Review for educators" rather than "Pinterest meets TpT." Photography and examples should show real classroom complexity, not stock-photo perfection. Testimonials should emphasize professional growth and intellectual satisfaction, not just convenience.

### Visual Brand Identity

The Learning Wheel visual identity combines intellectual credibility with approachable warmth through a sophisticated teal primary color, clean modern typography pairing (Outfit for headings, Inter for body), and photography that shows real educators in authentic teaching moments. The design system emphasizes clarity, professionalism, and collaborative energy while avoiding both corporate coldness and elementary-school cheerfulness.


**Color Palette System:**

The primary brand color—**Deep Teal (#0D7C7C)**—conveys trustworthiness, intellectual depth, and growth, positioning between corporate blue (too cold) and educational primary colors (too elementary). This sophisticated teal suggests both professionalism and organic collaboration, resonating with educators who see themselves as serious professionals. The secondary palette of **Warm Coral (#E76F51)** and **Sage Green (#6A994E)** adds energy and natural growth associations without sacrificing credibility. Accent colors provide functional clarity: **Vibrant Amber (#F4A261)** for calls-to-action demands attention while maintaining warmth, and **Deep Plum (#5A3A5E)** offers visual weight for important information. The neutral palette ensures readability and professional polish across all applications.

**Typography Hierarchy:**

The pairing of **Outfit** (headings) and **Inter** (body) balances contemporary professionalism with excellent readability. Outfit's geometric warmth provides distinctive personality in headings without sacrificing seriousness, while Inter's proven web performance and extensive weight range ensure clarity across all devices and contexts. This combination avoids both the coldness of pure geometric sans-serifs and the informality of rounded typefaces, positioning the brand as modern and credible. The typography system supports complex information hierarchy (content taxonomies, credit calculations) while remaining approachable for quick scanning.

**Visual Design System:**

Photography should depict real educators in authentic teaching moments—showing focused collaboration, genuine student engagement, and the intellectual work of teaching. Avoid stock-photo perfection; embrace real classroom complexity and diversity of teaching contexts (urban, rural, international, various grade levels). Images should show teachers as professionals engaged in intellectually demanding work, not just facilitators or cheerleaders. Illustration style, when used for explaining systems (credit algorithms, quality processes), should be clean and diagrammatic—think infographic clarity rather than decorative elements. Iconography follows a **rounded line style** (2px stroke weight) that balances friendliness with precision, using the primary teal for active states and neutral grays for inactive. Graphic elements include subtle circular motifs (echoing "wheel" and suggesting cycles of contribution/benefit) and flowing connector lines that suggest network effects and collaborative relationships. Layout principles emphasize generous white space, clear information hierarchy, and modular grid systems (12-column for desktop, 4-column for mobile) that accommodate complex content structures without visual clutter.

**Design Applications:**

Logo usage maintains minimum clear space of 1.5x the wheel icon height on all sides, with primary horizontal lockup for most applications and stacked vertical for square formats. Marketing collateral (landing pages, email campaigns, PDF guides) uses the full color palette with teal-dominant headers, coral accents for key statistics or testimonials, and abundant white space to convey professionalism. Digital/web applications prioritize readability and functional clarity: teal for primary navigation and key actions, amber for CTAs, neutral grays for content areas, with sage green reserved for success states and positive metrics. Social media templates use bold teal backgrounds with white or amber typography for announcements, photo-dominant layouts with subtle teal overlays for member spotlights, and infographic formats with the full palette for explaining platform features or sharing impact statistics.


#### Color Palette


**Primary Color**: Deep Teal

- Hex: #0D7C7C

- RGB: rgb(13, 124, 124)

- Usage: Primary brand color for headers, navigation, key CTAs, and brand presence. Use for logo, primary buttons, section headers, and anywhere strong brand association is needed.

- Psychology: Teal combines the trustworthiness and professionalism of blue with the growth and renewal associations of green. This specific deep teal conveys intellectual credibility, collaborative energy, and sustainable value—positioning between cold corporate blue and elementary-education primary colors. It suggests both expertise and approachability.


**Secondary Colors**:

1. Warm Coral: #E76F51 - Secondary accent for highlighting key statistics, testimonial callouts, and adding warmth to teal-dominant layouts. Use sparingly for visual interest and to draw attention to important supporting information.

2. Sage Green: #6A994E - Tertiary brand color for success states, positive metrics (credits earned, resources downloaded), and growth-oriented messaging. Reinforces natural, organic collaboration themes.


**Accent Colors**:

1. Vibrant Amber: #F4A261 - Primary CTA color for action buttons (Subscribe, Upload Resource, Claim Credits). High contrast against teal creates clear visual hierarchy. Use for elements requiring immediate attention or action.

2. Deep Plum: #5A3A5E - Accent for premium features, advanced analytics, or important notifications. Adds depth and sophistication. Use for badges, premium tier indicators, or critical alerts.


**Neutral Colors**:

1. Charcoal: #2D3748 - Primary text color for body copy and headings. Softer than pure black for reduced eye strain while maintaining strong readability (WCAG AAA compliant on white).

2. Slate Gray: #718096 - Secondary text for metadata, captions, helper text, and de-emphasized content. Use for timestamps, content tags, and supplementary information.

3. Light Gray: #E2E8F0 - Borders, dividers, and subtle background differentiation. Use for card borders, input field outlines, and separating content sections.

4. Off-White: #F7FAFC - Alternate background color for sections, cards, and content areas. Provides subtle visual separation from pure white (#FFFFFF) main backgrounds while maintaining brightness.



#### Typography


**Heading Font**: Outfit

- Weights: 400, 500, 600, 700

- Usage: All headings (H1-H6), navigation labels, button text, and prominent UI labels. Use weight 700 for H1/H2, 600 for H3/H4, 500 for H5/H6 and navigation. Outfit's geometric warmth provides contemporary professionalism without coldness.

- Fallback: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif


**Body Font**: Inter

- Weights: 400, 500, 600

- Line Height: 1.6

- Usage: All body text, form inputs, table content, and extended reading. Use weight 400 for body copy, 500 for emphasis, 600 for strong emphasis or labels. Inter's extensive character set and optimized rendering ensure clarity across all devices and languages (critical for multilingual content).

- Fallback: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif


**Accent Font**: JetBrains Mono

- Usage: Code snippets, credit amounts, technical specifications, and data displays where monospace clarity is beneficial. Use sparingly for functional differentiation, not decoration. Weight 400 only.



#### Visual Design System


**Photography**: Authentic documentary-style photography showing real educators in genuine teaching moments—planning collaboratively, engaging with students, reviewing materials, or working thoughtfully at their desks. Emphasize diversity of teaching contexts (urban, rural, international), grade levels, and subject areas. Lighting should be natural or ambient (avoid harsh studio lighting). Composition should show educators as focused professionals engaged in intellectually demanding work. Avoid stock-photo perfection, overly staged classroom scenes, or imagery that reduces teachers to facilitators. Color grading should be warm but not oversaturated, maintaining realistic skin tones and environmental authenticity.


**Illustration**: Clean, diagrammatic illustration style for explaining platform systems (credit algorithms, quality review processes, content taxonomies). Use line-based illustrations with selective color fills from brand palette. Style should prioritize clarity and information communication over decoration—think infographic precision rather than whimsical elements. Illustrations should feel modern and professional, using geometric shapes, clear hierarchies, and the circular motif to reinforce brand identity. Avoid cartoonish or overly playful styles that undermine professional credibility.


**Iconography**: Rounded line icons (2px stroke weight, rounded caps and joins) that balance approachability with precision. Primary icon color is Deep Teal (#0D7C7C) for active/selected states, Slate Gray (#718096) for inactive states. Icon style should be clean and immediately recognizable at small sizes (16px minimum). Use consistent visual metaphors: circular arrows for contribution cycles, connected nodes for collaboration, stacked layers for quality review, growing plants/arrows for value appreciation. Avoid filled icons except for critical alerts or selected states. Maintain consistent optical sizing across icon set.


**Graphic Elements**: Subtle circular motifs and arcs echo the "wheel" concept and suggest cycles of contribution and benefit. Use thin circular progress indicators (teal) for credit accumulation visualizations. Flowing curved connector lines (2-3px, teal or sage green) suggest network effects and collaborative relationships in diagrams. Dot patterns or subtle grid overlays in off-white/light gray add texture without distraction. Avoid heavy geometric patterns, busy backgrounds, or decorative elements that compete with content. All graphic elements should support information hierarchy and guide user attention, not simply decorate.


**Layout Principles**: 12-column grid system for desktop (1200px max-width content area), 4-column for mobile. Generous white space (minimum 40px margins, 24px between major sections) conveys professionalism and prevents overwhelming density. Consistent vertical rhythm using 8px baseline grid. Card-based layouts for content browsing with clear visual hierarchy: large preview image, prominent title (Outfit 600), metadata in smaller type (Inter 400, Slate Gray). Asymmetric layouts for landing pages create visual interest while maintaining clear F-pattern reading flow. Use teal section headers with ample padding to create clear content breaks. Modular design system allows complex information (taxonomies, credit calculations, analytics dashboards) to remain scannable and digestible.


## 5. Module Specifications

### 1. Authentication Module
**Purpose:** Technical specification for the Authentication Module

**Key Features:**
1. Email/Password and Social Login (Google/Microsoft for Edu)
2. Role-based access control (Subscriber, Provisional, Admin)
3. Session persistence and token refreshment
4. Account recovery and secure password reset
5. MFA support for Admin accounts
6. Profile claim logic for invited teachers

**Documentation URL:** https://bigbrainplan.com/projects/LfaKyxkUbgsvBZntXQBo/specifications/12MDrS8roXRfx0dm0cEp
**Local Documentation:** /specs/Authentication-Module.md

---

## 6. Implementation Plan

Based on the modular architecture defined above, create a detailed implementation plan organized by module. For each module specification listed in section 5:

**Note:** The documentation URLs referenced in section 5 should be organized in a `specs/` folder at the root of your project. Create corresponding .md files for each module specification (e.g., `specs/authentication-module.md`, `specs/user-profile-module.md`) where you can upload or document the current specifications for full implementation details. This allows you to maintain comprehensive module documentation alongside this implementation plan.

### Per-Module Implementation Tasks

For each module, provide a structured breakdown following this format:

#### Module: [Module Name]

**1. Data Layer**
- Define data models and schemas
- Specify database collections/tables
- Define data validation rules
- List required indexes

**2. API Layer**
- Define API endpoints (list each with method and path)
- Specify request/response schemas
- Define authentication/authorization requirements
- List error handling scenarios

**3. Business Logic**
- Core functions and their responsibilities
- Data transformation logic
- Validation rules
- State management requirements

**4. Integration Points**
- Dependencies on other modules (list specific modules)
- External service integrations
- Shared utilities needed
- Event triggers or listeners

**5. Testing Requirements**
- Unit test scenarios
- Integration test scenarios
- End-to-end test flows
- Test data requirements

**6. Implementation Tasks**
Create specific, actionable tasks:
- Task description
- Acceptance criteria
- Dependencies (on other tasks or modules)
- Estimated complexity (High/Medium/Low)

### Module Implementation Order

After defining tasks for all modules, recommend:
1. **Foundation Modules**: Which modules should be implemented first (typically shared utilities, auth, data models)
2. **Core Modules**: Primary business logic modules
3. **Integration Modules**: Modules that connect core functionality
4. **Enhancement Modules**: Additional features that build on core functionality

For each recommendation, explain the reasoning based on module dependencies identified above.

Use the technical stack specifications and module documentation to ensure all tasks are aligned with the project's architecture and modular design patterns.