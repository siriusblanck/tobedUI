# Generative UI: who is doing it and how

Research as of October 11, 2026. Sources are linked inline. "(unverified)" means the claim could not be confirmed. "(secondary source)" means only press or blog coverage was found, not a primary announcement. Vendor numbers (customers, downloads, savings) are self-reported unless noted.

## Short answers to the questions

Is it OpenAI and Anthropic? Partly, but Google is the one pushing hardest. Google published the main research paper on fully generated interfaces (Nov 2025), shipped generated layouts and tools in Search to everyone in English (Aug 2026), and created A2UI, one of the main open formats for describing UI. OpenAI and Anthropic matter because they co-wrote MCP Apps, the standard that lets third-party apps show interactive UI inside ChatGPT, Claude, Microsoft Copilot and VS Code. Microsoft, Vercel, Salesforce, AWS and Shopify are the other large platform players.

Are there research teams dedicated to this? Yes. Google Research, Stanford (Diyi Yang's and Michael Bernstein's groups), Apple ML Research, Microsoft Research Cambridge, UCSD's Foundation Interface Lab, KAIST, and a CMU/MIT/Purdue group all publish on it directly. CHI 2026 had a dedicated workshop, "What does Generative UI mean for HCI Practice?", and there are now benchmarks built specifically for generated interfaces.

Are there companies that do this? Yes, a few dozen. The largest independent one is CopilotKit, which created the AG-UI protocol and raised a $27M Series A in May 2026. Others include Thesys, assistant-ui, Hashbrown, Alpic, and Vercel's json-render project, plus consumer apps such as Monogram, Sekai and Wabi. Far more money sits in the neighboring category of AI app builders (Lovable, Replit, v0, Bolt). Those generate code at design time, which is a different thing from generating UI at runtime.

Are there consulting companies? A handful, almost all small. Very Good Ventures, Fuselab Creative, Lazarev, Punchcut, Fantasy and scandiweb sell generative UI or ChatGPT/MCP app work by name. The large firms (Accenture, Deloitte, McKinsey and others) don't sell it as its own offering. They fold it into "agentic AI" and "agentic commerce" work. Analysts have not made it a category yet, and nobody has published market sizing.

## 1. What people mean by "generative UI"

Generative UI is software where an AI model decides, at the moment of use, what interface to show, instead of only replying in text or always showing the same fixed screens.

Two different things get called generative UI, and it helps to separate them:

- Runtime generative UI. The interface is chosen or built per request inside a running product. Ask Gemini about mortgage rates and get a working calculator; ask Claude to compare options and get a chart in the chat. This report is mostly about this.
- Design-time generation. Tools like v0, Lovable, Figma Make and Google Stitch write code that a person then deploys. Once shipped, the UI is fixed. Gartner's 2024 note "Innovation Insight: Generative AI for User Interfaces" mostly meant this kind (https://www.gartner.com/en/documents/5924175).

### The three technical approaches

CopilotKit's "Developer's Guide to Generative UI in 2026" (Jan 29, 2026) is the most widely cited framing. It lays the approaches on a spectrum from high control to high freedom (https://copilotkit.ai/blog/the-developer-s-guide-to-generative-ui-in-2026).

| Approach | How it works | Examples | Tradeoffs |
|---|---|---|---|
| Controlled | Developers build components ahead of time. The model picks one through a tool call and fills in the data. | Vercel AI SDK tool parts, CopilotKit, LangGraph, Tambo, Salesforce Custom Lightning Types | Most on-brand, safest and fastest. Every new use case needs a new component built and maintained. |
| Declarative | The model outputs a structured spec (JSON or a small language). The app renders it using its own trusted component catalog. | Google A2UI, Vercel json-render, Thesys OpenUI, OpenAI ChatKit widgets, Microsoft Adaptive Cards | No generated code runs, the spec can be streamed, logged and audited, and it picks up the host's styling. Limited to what the catalog can express. Critics worry "every UI will become the same" (https://www.infoq.com/news/2026/07/google-a2ui-genui/). |
| Open-ended | HTML, JS or React runs in a sandboxed iframe. | Claude Artifacts and inline visuals, Gemini dynamic view, Google Search generated tools, MCP Apps, ChatGPT apps | Most flexible. Slower, more expensive, harder to secure and harder to keep on-brand. Google reports a full generated page can take one to two minutes. |

One nuance matters a lot. In MCP Apps and ChatGPT apps, the app developer usually writes the HTML in advance and the model only decides when to show it. UI that the model writes from scratch at runtime is still mostly limited to the labs' own products: Claude, Gemini and Google Search.

## 2. The big platforms

### Google

Google is the most aggressive on fully generated UI and also owns a major open format.

- Research paper. "Generative UI: LLMs are Effective UI Generators" by Yaniv Leviathan, Dani Valevski, Yossi Matias and others, announced Nov 18, 2025 (https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/, paper at https://arxiv.org/abs/2604.09577). Gemini 3 Pro writes a complete custom web page for each prompt. Raters preferred it to markdown 82.8% of the time, and it scored just below pages made by human web developers (Elo 1736 vs 1800). Speed was left out of the comparison.
- Gemini app. "Dynamic view" and "visual layout" experiments launched Nov 18, 2025 (https://support.google.com/gemini/answer/16741341).
- Search. Generated layouts started in AI Mode for paying US users in Nov 2025. At I/O in May 2026 Google said they would become free for everyone. By Aug 19, 2026 they were live worldwide in English in AI Mode and rolling into AI Overviews (https://www.searchenginejournal.com/google-expands-generative-ui-beyond-ai-mode-into-ai-overviews/586452/). This is probably the largest consumer deployment of fully generated UI anywhere. It also competes directly with calculator and tool websites.
- A2UI. An open declarative format. Agents send JSON that refers only to components the client already has, so no agent code runs on the device.
  - v0.8 was open-sourced Dec 15, 2025 (https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/).
  - v0.9 shipped Apr 17, 2026 with two-way messaging and an official React renderer next to Flutter, Lit and Angular. Named partners are AG2, Vercel json-render and Oracle Agent Spec. v0.9.1 is the production release and a v1.0 release candidate is out (https://developers.googleblog.com/a2ui-v0-9-generative-ui/).
  - On June 17, 2026 Google described A2UI and MCP Apps as complementary and showed ways to combine them (https://developers.googleblog.com/a2ui-and-mcp-apps/).
- Flutter GenUI SDK (alpha, built on A2UI) and an Android A2UI library with a Jetpack Compose renderer on the way (May 2026, https://android-developers.googleblog.com/2026/05/android-ai-intelligence-system.html).
- Stitch, the rebranded Galileo AI that Google acquired in 2025, is a design tool, not runtime UI.

### OpenAI

OpenAI's philosophy is chat plus widgets, with strict rules on what a widget can do.

- Apps in ChatGPT and the Apps SDK launched Oct 6, 2025. Developers write HTML widgets, an MCP server serves them, and ChatGPT shows them in an iframe (https://openai.com/index/introducing-apps-in-chatgpt/). App submissions opened Dec 17, 2025 and the App Directory went live the next day.
- ChatKit, part of AgentKit, offers declarative widgets such as cards, forms and buttons (https://developers.openai.com/api/docs/guides/chatkit).
- ChatGPT fully supports MCP Apps as of Feb 2026. OpenAI's docs now tell developers to build to MCP Apps first and use the ChatGPT-only `window.openai` API only for ChatGPT-specific features (https://developers.openai.com/apps-sdk/mcp-apps-in-chatgpt).
- Interactive learning visuals for 70+ math and science concepts launched Mar 10, 2026.
- Apps are now packaged as "plugins" in one directory shared by ChatGPT and Codex (switch date unverified). At DevDay on Sept 29, 2026, OpenAI announced plugin extensions: sidebar apps, side panels, and file viewers and editors, all built on MCP Apps (https://developers.openai.com/plugins/build/extensions).
- Commerce: Instant Checkout launched Sept 2025 and was reportedly withdrawn around Mar 2026, with merchants now running checkout inside their own apps (secondary source, https://forkast.news/openai-built-in-chat-checkout-for-ai-commerce-five-months-later-it-walked-away/).
- The design guidelines are tight: inherit the system font, at most two actions per card, no deep navigation inside a card (https://developers.openai.com/plugins/concepts/ui-guidelines.md).

### Anthropic

- Artifacts (2024): Claude writes code that runs in a sandboxed side panel.
- Imagine with Claude (Sept 29, 2025): a five-day research preview in which Claude built the whole interface live as the user clicked, with no prewritten code (https://www.anthropic.com/news/claude-sonnet-4-5).
- MCP Apps in Claude (Jan 26, 2026): interactive tools from Asana, Figma, Slack, Canva, Box, Amplitude, Hex, Clay and monday.com (https://claude.com/blog/interactive-tools-in-claude).
- Inline visuals (Mar 12, 2026, beta on all plans): Claude decides on its own when to draw a chart or diagram in the conversation (https://claude.com/blog/claude-builds-visuals).
- Claude Design (research preview, Apr 2026) for prototypes and decks, and live artifacts in Cowork that refresh from connected data (both secondary source).

### MCP Apps: the standard OpenAI and Anthropic built together

- Proposed Nov 21, 2025 as SEP-1865 by MCP maintainers at Anthropic and OpenAI together with the MCP-UI creators, Ido Salomon and Liad Yosef (https://blog.modelcontextprotocol.io/posts/2025-11-21-mcp-apps/).
- Became the first official MCP extension on Jan 26, 2026 (https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) and was included in the 2026-07-28 MCP spec release.
- How it works: a tool points to a `ui://` HTML resource, the host draws it in a sandboxed iframe, and the two talk over JSON-RPC through postMessage.
- Hosts include Claude, ChatGPT, VS Code, Goose, Microsoft 365 Copilot, Postman, Vercel AI SDK 7 and Salesforce.
- MCP itself moved to the Linux Foundation's Agentic AI Foundation in Dec 2025, alongside OpenAI's AGENTS.md and Block's goose. Google's A2A joined in Aug 2026 (https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-Agentic-ai-foundation).

### Microsoft

- Adaptive Cards, Microsoft's long-running JSON card format, are still the default rich response in Copilot Studio.
- Microsoft 365 Copilot Chat supports MCP Apps and OpenAI Apps SDK widgets (Mar 9, 2026, https://devblogs.microsoft.com/microsoft365dev/mcp-apps-now-available-in-copilot-chat/).
- SharePoint Copilot Apps were announced June 23, 2026, with GA targeted for Oct 2026. Copilot Studio support for MCP Apps is in preview in Oct 2026 with GA planned for Nov 2026.
- VS Code has supported MCP Apps since Jan 2026.
- On the research side, Microsoft Research Cambridge leads the academic genUI conversation (see section 5).

### Vercel

- Vercel popularized the term in 2024 with `streamUI` in AI SDK RSC. That path is now paused, and Vercel recommends mapping tool results to client components instead (https://ai-sdk.dev/docs/ai-sdk-ui/generative-user-interfaces).
- json-render (Jan 2026) is a declarative approach: developers define a component catalog, and the model outputs JSON limited to it. It has about 18.6k GitHub stars and supports A2UI (https://github.com/vercel-labs/json-render). Guillermo Rauch called it "very disruptive technology."
- AI SDK 7 (June 2026) added an experimental renderer for MCP Apps (https://vercel.com/blog/ai-sdk-7).
- v0 is a design-time app builder.

### Others

- Salesforce: Headless 360, announced Apr 2026, renders Agentforce components in Slack and in MCP Apps hosts like ChatGPT, Claude and Teams. Beta started late July 2026 (secondary source, https://www.salesforceben.com/salesforce-headless-360-and-agentforce-vibes-2-0-revealed-at-tdx-2026/).
- Shopify: an early production user of MCP-UI for commerce widgets, including product cards, variant pickers and cart flows. Its widgets "bubble up intents" to the model instead of changing state directly (https://shopify.engineering/mcp-ui-breaking-the-text-wall).
- AWS: Bedrock AgentCore Runtime added AG-UI in Mar 2026, and Strands Agents supports it (https://aws.amazon.com/about-aws/whats-new/2026/03/amazon-bedrock-agentcore-runtime-ag-ui-protocol).
- Apple: little in products, but strong research output (see section 5).
- Meta: nothing found.

## 3. The standards layer

| Layer | Standard | Owner | Status, Oct 2026 |
|---|---|---|---|
| Tools that ship their own UI | MCP Apps | Anthropic, OpenAI and the MCP-UI community, under the Linux Foundation | Official since Jan 2026, broad host support |
| Event stream between agent and frontend | AG-UI | CopilotKit | Supported by Microsoft Agent Framework, Google ADK, AWS, Mastra, LlamaIndex, Pydantic AI and others. 16.5k GitHub stars |
| UI description format | A2UI | Google | v0.9.1 in production, v1.0 release candidate. Runs over MCP, A2A or AG-UI |
| UI description format | OpenUI (openui-lang) | Thesys | Open-sourced Mar 2026. Claims about half the tokens of JSON formats |
| UI description format | json-render | Vercel Labs | Released Jan 2026 |
| Agent to agent | A2A | Started by Google, now under the Linux Foundation | No UI layer of its own |

Where things stand:

- The iframe tier has converged on MCP Apps. Host behavior still differs in details such as fullscreen support and calling tools from inside the UI (https://alpic.ai/blog/mcp-apps-goes-official-claude-chatgpt-support).
- The declarative tier is still fragmented: A2UI, json-render, OpenUI, ChatKit widgets, Adaptive Cards and Salesforce's own types all compete.
- Governance is split. MCP and A2A sit in a foundation, while AG-UI and A2UI are still led by single vendors.
- Thoughtworks Technology Radar Vol. 34 (Apr 2026) moved AG-UI to "Trial" but noted that "the need for a separate UI protocol layer such as AG-UI is being questioned" now that MCP servers ship UI with their tools. It put MCP Apps at "Assess" and advised teams to check "whether the added complexity over plain text responses is warranted" (https://www.thoughtworks.com/radar/platforms/ag-ui-protocol).
- A W3C Generative UI Community Group was proposed in Jan 2026 (launch unverified).

## 4. Startups and open source

### SDKs, protocols and hosted APIs

| Company or project | What it does | Approach | Money and traction |
|---|---|---|---|
| CopilotKit | React/Angular stack for putting agents inside apps. Created AG-UI. | Supports all three | $27M Series A, May 2026, led by Glilot, NFX and SignalFire. Claims 4M weekly downloads and customers including DocuSign, S&P Global, Cisco and Deutsche Telekom (https://copilotkit.ai/blog/series-a) |
| Thesys | C1 API (Apr 2025), now sold as OpenUI Cloud. Also released OUI-1, a small open model that generates UI locally (Sept 2026, secondary source) | Declarative language | $4M seed (Together Fund, 8VC) (https://www.thesys.dev/) |
| assistant-ui (YC) | React chat components with MCP Apps and A2UI renderers | Controlled plus protocol renderers | Used by LangChain, Mastra, Browser Use. 12.5k stars |
| Tambo | React SDK where the agent picks registered components and streams their props | Controlled | 1.0 in Feb 2026. Hosted cloud shuts down Oct 31, 2026 (https://tambo.co/blog/posts/tambo-is-shutting-down) |
| MCP-UI | Community SDKs that came before MCP Apps and now implement it | Open-ended | Co-creator Liad Yosef is now CTO of Ora Labs, which raised a ~$10M seed in July 2026 |
| Hashbrown (LiveLoveApp) | Angular/React framework with a sandboxed JS runtime for model-written code | Controlled plus code | Small, about 730 stars |
| Alpic / Skybridge | Framework for building one app that runs in both ChatGPT and Claude | Open-ended | $6M pre-seed, Partech, Sept 2025 |
| Manufact (mcp-use, YC) | Hosting and tooling for MCP Apps, pitched as "the Vercel for MCP" | Infrastructure | (https://manufact.com/) |
| Mastra, LangGraph, LlamaIndex, CrewAI | Agent backends with hooks for generative UI | Mostly controlled | Mastra raised a $22M Series A, Apr 2026 (secondary source) |

Tambo's shutdown stands out. Its stated reason is that models now write front-end code well enough that its layer lost much of its value. That is a warning sign for thin SDK businesses in this area.

### Consumer products that generate apps or interfaces at runtime

- Monogram: an iPhone app that builds a custom visual interface for each question. $40M seed co-led by DST Global and Lux Capital, July 2026. Co-founded by Udemy co-founder Eren Bali (secondary source).
- Sekai: mobile platform for mini apps made from prompts. $20M Series A, June 2026. Reports 15M+ mini apps created (https://dealroom.co/news/130706-sekai-raises-20m-series-a-to-scale-ai-built-mini-apps/).
- Wabi, from Replika founder Eugenia Kuyda: $20M pre-seed in Nov 2025 for a "YouTube of apps," relaunched in Sept 2026 as a messenger that generates mini apps for group chats (https://techcrunch.com/2025/11/05/replika-founder-raises-20m-pre-seed-for-wabi-the-youtube-of-apps).
- Dreamer (formerly /dev/agents, founded by David Singleton and Hugo Barra): consumer beta in Feb 2026 for building and remixing agent-powered apps.
- Adaptive ($7M seed, 2025) and Websim are smaller players in the same vein.

### The neighboring category: AI app builders

These generate code at design time. They are where most of the money is.

| Company | Recent funding | Notes |
|---|---|---|
| Lovable | $400M at $13.3B, Aug 2026 | About $600M ARR (https://www.vktr.com/ai-platforms/lovable-raises-400-million-at-133-billion-valuation/) |
| Replit | $400M at $9B, Mar 2026 | Targeting $1B ARR in 2026 |
| Vercel (v0) | $300M Series F at $9.3B, Sept 2025 | |
| Bolt.new | $105.5M Series B, Jan 2025 | |
| Figma Make | Part of Figma | GA July 2025 |

Acquisitions: Google bought Galileo AI (became Stitch), Wix bought Base44 for $80M upfront, and Miro bought Uizard. No company selling a runtime generative UI SDK has been acquired yet.

## 5. Research teams and what they have found

### Industry labs

- Google Research (Yaniv Leviathan, Dani Valevski, Yossi Matias). The paper described in section 2. Two more findings: the ability appears to emerge with newer models (Gemini 3 produced no errors where Gemini 2.0 Flash-Lite failed 60% of the time), and the team built PAGEN, a set of expert-made websites to compare against (https://arxiv.org/html/2604.09577). A follow-up on generative UI for education came out in Sept 2026 (https://arxiv.org/abs/2609.20738).
- Google DeepMind with UCLA (Xiang 'Anthony' Chen, Tiffany Knearem, Yang Li). "The GenUI Study" (DIS 2025) followed 37 UX professionals for a week. GenUI helped early ideation and first drafts and let non-designers make interfaces (https://arxiv.org/abs/2501.13145).
- Apple ML Research (Jason Wu, Titus Barik, Jeffrey Nichols, Amanda Swearngin). UICoder trains a model on its own compiler-checked SwiftUI code (https://machinelearning.apple.com/research/uicoder). A CHI 2026 paper trained UI generators on comments, sketches and direct edits from 21 designers and beat every baseline, including GPT-5 (https://arxiv.org/abs/2509.16779). FlowEval (May 2026) scores generated UIs by comparing their navigation with real sites (https://arxiv.org/abs/2605.04165).
- Microsoft Research Cambridge (Siân Lindley, Jack Williams, Abigail Sellen). Organized the CHI 2026 workshop. Earlier work includes DynaVis, a CHI 2024 best paper on generating editing widgets for charts. GenUI-Harness (Oct 2026) reports that training raised a small 4B model's success rate from 9% to 58% (https://arxiv.org/abs/2610.11123).
- Adobe Research with UC Santa Barbara. CrowdGenUI and AlignUI steer generated widgets using crowdsourced preferences (https://arxiv.org/abs/2601.17614).

### Academic labs

- Stanford, Diyi Yang's SALT lab. "Generative Interfaces for Language Models" (Aug 2025) turns a question into a spec, then a structured model of the interface, then code. Generated interfaces beat plain chat 84% of the time overall and 93.8% for data analysis, but chat stayed competitive for simple explanations and math (https://arxiv.org/abs/2508.19227).
- Stanford, Michael Bernstein's group. "Just-In-Time Objectives" (CHI 2026) infers what the user is trying to do and generates a specialized tool for it (https://arxiv.org/abs/2510.14591). His essay "The Interface of Theseus" (UIST Visions 2026) argues for disposable, just-in-time interfaces and asks whether designers will build interface generators instead of interfaces (https://arxiv.org/abs/2609.06770).
- UCSD Foundation Interface Lab (Haijun Xia). JELLY (CHI 2025) generates a data model for the task first, then a UI users can reshape (https://arxiv.org/abs/2503.04084).
- KAIST KIXLAB (Juho Kim). Maru (Aug 2026) remembers a user's layout choices as rules so regenerated UIs stay consistent across sessions (https://arxiv.org/abs/2608.25565).
- CMU, MIT and Purdue (Jeffrey Bigham, Yi-Hao Peng, Jason Wu). In a personalization study, 20 people judged 600 UI pairs and barely agreed with each other (Krippendorff's alpha 0.25), which is a strong argument for per-user tuning (https://arxiv.org/abs/2604.09876).
- Politecnico di Torino (CHI 2026). Found 541 accessibility violations in 300 UIs from three commercial models, mostly of a kind automated checkers miss (https://iris.polito.it/handle/11583/3008330).
- Ink & Switch (Geoffrey Litt and others), "Malleable Software," June 2025. Argues that "AI code generation alone does not address all the barriers to malleability" and that people need stable tools they can reshape, not throwaway apps (https://www.inkandswitch.com/essay/malleable-software/). Litt joined Notion in Sept 2025.
- Aalto (Antti Oulasvirta) and ETH Zurich (Christian Holz) carry the older adaptive and computational UI tradition that current work builds on.

### The older lineage

Generative UI is newer as a name than as an idea. SUPPLE (Krzysztof Gajos and Dan Weld, University of Washington, 2004) treated UI generation as an optimization problem over device constraints and user abilities (https://kgajos.seas.harvard.edu/papers/supple-iui04.pdf). Jeffrey Nichols's work at CMU (2003 to 2006) generated appliance interfaces, and he now co-organizes the genUI workshop from Apple. The ACM IUI conference (Intelligent User Interfaces) has been the home venue for decades.

### Benchmarks

| Benchmark | What it measures | Notable result |
|---|---|---|
| Design2Code (Stanford, 2025) | Rebuilding 484 real web pages from screenshots | Models lag most on layout and remembering visual elements |
| UICrit (Berkeley and Google, 2024) | 3,059 designer critiques of mobile UIs | Raised LLM feedback quality by 55% |
| ArtifactsBench (Tencent, 2025) | 1,825 interactive generation tasks | Automated scores agree 94.4% with human rankings |
| EvoGenUI-Bench (EMNLP 2026) | Multi-turn UI generation | Best model passed 74.9% of turns but finished only 37.3% of five-turn sessions (https://arxiv.org/abs/2608.29387) |
| Design Theater (AIES 2026) | Whether tools build what they say they built | Over 25% of stated design decisions never appear in the UI, and 34% of functional requirements fail (https://arxiv.org/abs/2607.22928) |

### Venues

- CHI 2026 workshop "What does Generative UI mean for HCI Practice?" (Barcelona, Apr 2026), organized by people from Microsoft Research, UCSD, Apple, MIT and MBZUAI. It splits the field along four axes: designer-facing vs end-user, static vs dynamic, user-driven vs system-driven change, and short-lived vs long-lived (https://doi.org/10.1145/3772363.3778757).
- Work also appears regularly at DIS, UIST, IUI, ACL and EMNLP.

### Thought leaders

- Nielsen Norman Group (Kate Moran and Sarah Gibbons, Mar 2024) defined generative UI and "outcome-oriented design," and warned that constant change forces users to relearn the interface (https://www.nngroup.com/articles/generative-ui/).
- Jakob Nielsen is the loudest optimist. He calls this "disposable interfaces" and argued in 2024 that generated, individualized UIs could do more for accessibility than current approaches. In his July 2026 mid-year check he scored his own 2026 genUI prediction at only 47%: it works "in constrained use cases," but static software is not dying (https://www.uxtigers.com/post/2026-predictions-halfway).
- Andrej Karpathy (Dec 2025) compared chatting with AI to "issuing commands to a computer console in the 1980s" and said models should answer with images, slides, whiteboards and web apps (https://karpathy.bearblog.dev/year-in-review-2025/).
- a16z, "Disposable Software" (Anish Acharya, Aug 2025) (https://a16z.com/disposable-software/).

## 6. Consulting and services

The market is thin, young and follows the platforms. Offerings appeared in waves after each launch: the Apps SDK (Oct 2025), MCP Apps (early 2026), then A2UI and Flutter GenUI (mid 2026).

### Large consultancies

- None sells "generative UI" as its own practice. The work gets bought under "generative AI consulting and implementation" (Gartner's category) or under agentic commerce and customer experience deals.
- Accenture Song shows the most concrete signals. It invested in DaVinci Commerce in Mar 2026 so brands can "transact in agent-led environments" (https://newsroom.accenture.com/news/2026/accenture-invests-in-davinci-commerce-to-advance-agentic-ai-led-shopping), and it hired designers in 2026 to work on agent dashboards and "AI-native design systems."
- Thoughtworks has the most useful published view, through its Technology Radar (see section 3).
- Both OpenAI ($150M, June 2026) and Anthropic ($100M, Mar 2026, with a services track added in June) launched partner networks for consultancies. Neither has a track specific to generative UI or apps (https://www.anthropic.com/news/services-track-partner-hub).
- Not checked because of search limits: BCG X, IBM iX, Slalom, Infosys, TCS, Wipro, frog, Huge, R/GA, Instrument, Work & Co, Fjord.

### Boutiques and agencies that sell it by name

| Firm | What they sell | Notes |
|---|---|---|
| Very Good Ventures | Flutter GenUI Kit (July 2026) and 4 to 6 week pilots | Says it built the first enterprise GenUI app, with Norwegian Cruise Line, and calls itself an A2UI showcase partner (self-reported) (https://www.verygood.ventures/resources/genui-generative-ui) |
| Fuselab Creative | Full programs (12 to 20 weeks), generative UI audits, evaluation and QA | Its pitch: "The deliverable is the constraint system the model renders from" (https://fuselabcreative.com/services/generative-ai-design/) |
| scandiweb | MCP apps for e-commerce on ChatGPT and Claude | The only firm found with public prices: €9,900 for 10 to 15 days, €29,900 for a 4 to 6 week pilot, from €74,900 for 8 to 12 weeks, from €4,900 a month for support (https://scandiweb.com/services/mcp-app-development) |
| Lazarev.agency | Generative UI design, starting from a design system audit | (https://www.lazarev.agency/articles/generative-ui-design) |
| Punchcut | Strategy, with a published genUI framework (Oct 5, 2026) | Advises that "Generative UI should earn its flexibility" (https://punchcut.com/perspectives/generative-ui/) |
| Fantasy | "Apps in ChatGPT Playbook" and brand advisory | No named clients (https://fantasy.co/latest/the-apps-inside-chatgpt-playbook-how-brands-are-preparing-for-openai-app-sdk) |
| Akveo, Keenethics, TELUS Digital | ChatGPT app development | TELUS built a proof of concept in 48 hours |

### How the services market is shaping up

- Packaged MCP and ChatGPT app builds, priced like e-commerce integrations (scandiweb).
- Design-system and constraint work: preparing a company's components and tokens so a model can use them (Fuselab, Lazarev, Punchcut).
- Pilots tied to one framework (Very Good Ventures with Flutter and A2UI).
- Because the protocols are still shifting, few firms want to build a whole practice on one stack. That helps explain why so many sell readiness audits and design-system groundwork instead of builds.

### Analysts

- Gartner predicted in Aug 2025 that by 2028 "one-third of user experiences will transition from using native applications to interacting with agentic front ends" (https://itbrief.com.au/story/ai-agents-to-power-40-of-enterprise-apps-by-2026-says-gartner). Its 2026 UX Hype Cycle covers adaptive experiences and AI agents as a new kind of user. Whether it has a "generative UI" entry is unverified.
- No Forrester or IDC report on generative UI was found, and no market sizing exists for genUI or for ChatGPT/MCP app services.

## 7. How teams build it in practice

- Streaming partial UI. Interfaces render as the model writes. json-render streams patches, A2UI's parser renders components before a JSON block is complete, and the Vercel AI SDK uses each tool call's progress to show loading skeletons.
- Keeping agent and UI in sync. AG-UI sends state snapshots plus small JSON Patch updates, and both sides can write (https://docs.ag-ui.com/concepts/state). MCP Apps lets a widget push information into the model's next turn.
- Human approval. AG-UI agents pause with an interrupt and resume after the user responds. MCP Apps lets hosts require consent before a widget triggers a tool. Shopify's widgets send "intents" to the model instead of changing data directly.
- Feeding the model a design system. MCP Apps passes theme, locale and container size to widgets. A2UI v0.9 was reworked so agents use a company's existing component catalog, and json-render writes the model's prompt automatically from the catalog.
- Speed and cost. Predeclared templates that hosts can prefetch and cache, and compact formats that cut output tokens.
- Evaluation. LLM judges, simulated persona panels and comparisons with navigation on real sites, because people disagree so much about what a good UI is.

## 8. Debates and open problems

- Predictability vs novelty. If the interface changes every time, users lose muscle memory, documentation and screenshots go stale, and features become hard to find. NN/g, Nielsen himself and Hacker News commenters all raise this (https://news.ycombinator.com/item?id=46138473).
- Chat vs beyond chat. Karpathy, Amelia Wattenberger and Bernstein argue that chat is a poor interface and models should produce richer ones (https://wattenberger.com/thoughts/boo-chatbots). Ink & Switch pushes back on disposable apps. In practice, OpenAI's guidelines enforce a middle path of chat plus small widgets.
- Accessibility. Generated UIs have no fixed structure to audit, and the Torino study found violations that checkers miss. Streaming also breaks screen readers, since every new token changes the page (https://tianpan.co/blog/2026-06-29-streaming-tokens-meet-the-screen-reader). The optimistic view is that genUI could tailor interfaces to each person's abilities, but nobody has tested that at scale.
- Security. Generated UI opens the door to fake or impersonated interfaces and to prompt injection through widgets. This is the main reason enterprises prefer declarative formats, where no generated code runs.
- Latency. Full generation takes one to two minutes at Google and several minutes in Stanford's system, and Google's preference study left speed out.
- Reliability. Quality drops over multi-turn sessions (EvoGenUI), and tools often claim design decisions they did not implement (Design Theater).
- When genUI actually helps. Stanford found chat was still as good for simple explanations and math. A Mar 2026 paper argues generated interfaces are not always better than text (https://arxiv.org/abs/2603.21334).
- The designer's role. The common view is that designers shift from drawing screens to defining the components, rules and constraints a model works within. Nielsen puts it as moving from "I made this" to "I shaped this system."
- Hype. Nielsen scored his own prediction at 47%, Tambo shut down its cloud, and Thoughtworks asks whether MCP Apps' complexity is worth it over plain text. Meanwhile Google is shipping fully generated UI in Search to a global audience.

## 9. My read on where things stand

- A split is forming. The labs keep fully generated, model-written UI for their own products (Claude, Gemini, Search). Third parties get constrained options: prebuilt widgets through MCP Apps, or declarative specs rendered from a trusted catalog.
- The design system becomes the product. Whether you use A2UI, json-render or Tambo-style components, quality depends on a well-defined component catalog the model can draw from. Consultancies are already positioning around this.
- MCP Apps won the iframe tier. The declarative tier (A2UI vs json-render vs OpenUI and others) is the open fight, and Google has the strongest position there.
- Standalone SDK businesses are fragile. Models keep getting better at writing front-end code, and standards turn the plumbing into a commodity. The independents that look durable either own a protocol (CopilotKit with AG-UI) or sit inside a big platform.
- Areas that look under-served: evaluation tools for generated UIs, accessibility for streamed and generated interfaces, keeping generated UIs consistent for one user over time, and tooling to turn an existing design system into a model-ready catalog. Generative UI consulting as a distinct category barely exists yet.

## 10. Where to start reading

1. Google Research, "Generative UI" blog post and paper: https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/
2. CopilotKit, "The Developer's Guide to Generative UI in 2026": https://copilotkit.ai/blog/the-developer-s-guide-to-generative-ui-in-2026
3. MCP Apps announcement: https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/
4. Google, A2UI v0.9: https://developers.googleblog.com/a2ui-v0-9-generative-ui/
5. Google, how A2UI and MCP Apps fit together: https://developers.googleblog.com/a2ui-and-mcp-apps/
6. Stanford, "Generative Interfaces for Language Models": https://arxiv.org/abs/2508.19227
7. NN/g, "Generative UI and Outcome-Oriented Design": https://www.nngroup.com/articles/generative-ui/
8. Ink & Switch, "Malleable Software": https://www.inkandswitch.com/essay/malleable-software/
9. Thoughtworks Technology Radar, AG-UI entry: https://www.thoughtworks.com/radar/platforms/ag-ui-protocol
10. OpenAI UI guidelines for ChatGPT apps: https://developers.openai.com/plugins/concepts/ui-guidelines.md

## Notes on method

Done on Oct 11, 2026 as five parallel web research passes: the big labs, startups and tooling, academic research, consulting and analysts, and engineering practice and debates. The web search quota ran out partway through several passes, so some areas were not checked: a dozen large consultancies and design firms (listed in section 6), Meta, Sequoia, ICLR and NeurIPS 2026 benchmarks, and some funding figures (Tambo, Websim, Builder.io). Leaving something out means it was not checked, not that it doesn't exist.
