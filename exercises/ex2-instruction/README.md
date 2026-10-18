# Exercise 2 (Instruction Extension) - Add an AI Disclaimer to the Journal Entry Agent

In this exercise, you will create an **Instruction Extension** for the pre-delivered Journal Entry Agent using Joule Studio's Intent-Based Development (IBD) flow. The extension enforces a mandatory disclaimer on every agent response — ensuring users are always informed that the output is AI-generated.

### What you will build

A single-instruction agent extension that injects the following disclaimer at the end of every Journal Entry Agent response:

> **DISCLAIMER: This is an AI-generated response.**

This is a simple but important real-world use case: regulatory or compliance policies frequently require AI systems to identify themselves. You will see how Joule Studio takes a plain language intent statement, asks targeted clarifying questions, generates a deployable extension artifact, and deploys it — without any manual YAML editing.

### Why this matters

The Journal Entry Agent processes financial data and surfaces results directly to finance professionals. Depending on your organization's governance framework, every AI-assisted output may need to carry a visible disclaimer. By building this as an **Instruction Extension**, the requirement is enforced at the agent level — not through UI customization or post-processing — so every response is covered, with no exceptions.

---

## Exercise 2.1 - Access Joule Studio and Initiate a New Extension

After completing these steps you will have opened Joule Studio and started creating a new Agent Extension targeting the Journal Entry Agent.

1. Log in to SAP Joule Works using the credentials provided at your desk.

2. In the left navigation panel, select **Joule Studio**.

<br>![](/exercises/ex2-instruction/images/01_joule_studio_nav.png)

3. On the Joule Studio home screen, click **+ New** and select **New Solution** from the dropdown.

<br>![](/exercises/ex2-instruction/images/02_new_solution_menu.png)

4. The **What do you want to create?** dialog opens. Select **Extension** and click **Create**.

   > The four options are Agent, n8n Workflow, Data Product, and Extension. An Extension lets you add capabilities or custom instructions to an existing pre-delivered SAP agent — without building a new agent from scratch.

<br>![](/exercises/ex2-instruction/images/03_select_extension.png)

---

## Exercise 2.2 - Configure the Extension and Select the Target Agent

After completing these steps you will have configured the extension form and selected the Journal Entry Agent from the Agent Catalog.

1. The **Create an Agent Extension** dialog appears. Click the lookup icon next to the **Select Agent** field to open the Agent Catalog.

<br>![](/exercises/ex2-instruction/images/04_create_extension_form.png)

2. In the **Agent Catalog**, type **Journal entry** in the search box to filter the results. Locate the **Journal Entry Agent (S4H PuC, V 1.0.1)** and click **Add**.

   > The Agent Catalog lists all pre-delivered SAP agents that have been published as extensible. Only agents with the extensible flag enabled appear here.

<br>![](/exercises/ex2-instruction/images/05_agent_catalog_search.png)

3. The agent detail page shows the **Agent Description** and its available **Extension Points**. The **Instruction** extension point is listed. Click **Select Agent** to return to the extension form.

<br>![](/exercises/ex2-instruction/images/06_select_journal_entry_agent.png)

---

## Exercise 2.3 - Enter the Intent Statement and Create the Extension

After completing these steps you will have submitted your intent statement and triggered the Intent-Based Development flow in Joule Studio.

1. Back in the **Create an Agent Extension** dialog, confirm that:
   - **Agent** is set to **Journal Entry Agent**
   - **Name** field shows **TM266-XXX** — replace **XXX** with your participant number (for example, TM266-001)
   - **Quick Create** is **NOT** checked

   > Leaving Quick Create off activates IBD, which walks you through a structured flow and produces a more accurate specification. Quick Create skips the clarifying questions and generates immediately.

2. In the **Intent Statement** field, enter the following text, then click **Create**:

   ```
   Please create an agent extension. The extension should add the following CRITICAL instruction
   that must be followed: PUT DISCLAIMER: This is an AI-generated response when responding back
   to the user.
   ```

<br>![](/exercises/ex2-instruction/images/07_extension_form_filled.png)

---

## Exercise 2.4 - Answer the Clarifying Questions

After completing these steps you will have provided the business context that Joule Studio uses to shape the extension specification.

1. Joule Studio creates the solution and begins the IBD flow. You will see it execute a series of steps — Thought, Activate Skill, List Files, Read File — before surfacing the first clarifying question.

<br>![](/exercises/ex2-instruction/images/08_joule_processing.png)

2. Joule asks: **"To complete the intent analysis, I need to understand the business goal behind adding this AI disclaimer. What outcome are you looking to achieve?"**

   Select **Ensure users are aware responses are AI-generated (regulatory/compliance requirement)** and click **Submit Answer**.

<br>![](/exercises/ex2-instruction/images/09_clarify_business_goal.png)

3. Joule asks a second question: **"What would a successful outcome look like for this requirement?"**

   Type the following in the text field and click **Submit Answer**:

   ```
   100% of responses include the disclaimer
   ```

<br>![](/exercises/ex2-instruction/images/10_clarify_success_criteria.png)

---

## Exercise 2.5 - Review the Intent Analysis and Confirm

After completing these steps you will have reviewed the Intent Analysis Summary generated by Joule Studio and confirmed the proposed extension design.

1. Joule Studio presents the **Intent Analysis Summary**. Review each section:

   | Section | Content |
   |---|---|
   | **Business Challenge** | Users must be clearly informed that responses are AI-generated, as required by regulatory/compliance obligations |
   | **Business Goals & Success Criteria** | AI disclaimer inclusion rate: 100% of responses, via Journal Entry (Record to Report) |
   | **Business Architecture (RBA)** | End-to-End Process: Record to Report; Mapped Sub-Process: Perform accounting and financial close |
   | **Fit Gap Analysis** | Standard Journal Entry Agent supports instructions — no tools or hooks needed; 100% fit |
   | **Recommended Solution** | Agent Extension: extend the Journal Entry Agent by adding a mandatory instruction appending the disclaimer to every reply |

2. Review the **Recommended Solution** section at the bottom. When satisfied, type **confirm** in the input field and press Enter to proceed.

<br>![](/exercises/ex2-instruction/images/11_intent_analysis_summary.png)

---

## Exercise 2.6 - Review the Product Requirements Document

After completing these steps you will have reviewed the generated PRD and confirmed the specification so Joule Studio can build the extension artifacts.

1. Joule Studio generates the **intent.md** file, visible in the File Explorer. On the right panel, review the **Product Requirements Document Summary**:

   - **Solution Category**: Agent Extension
   - **User Personas**: Finance Controller (compliance audits), Compliance Officer (AI transparency)
   - **Key Requirements**: Every response from the Journal Entry Agent must include the disclaimer; applied consistently with no exceptions
   - **Solution Architecture**: Pure instruction extension — no tools, hooks, or APIs required

2. Type **confirm** in the input field and press Enter to trigger artifact generation.

<br>![](/exercises/ex2-instruction/images/12_intent_md_prd.png)

---

## Exercise 2.7 - Review the Generated Artifact and Deploy

After completing these steps you will have inspected the generated `extension.yaml` artifact and deployed the extension to your landscape.

1. The File Explorer updates to show the generated artifacts. Open **assets > ai-disclaimer-agent-extension > extension.yaml**. Review its content:

   ```yaml
   schema-version: "0.1.0"
   kind: Extension

   metadata:
     name: "ai-disclaimer-agent-extension"

   agent:
     id: "sap.ag|ea:agent:journal-entry-agentv1"

   capabilityImplementations:
     - capabilityStyleId: "do_unit"
       subAbilities: []
       protocolVersion: 1
       instructions:
         - Always include the following disclaimer at the end of every response:
           "DISCLAIMER: This is an AI-generated response."
           This is required for compliance and transparency purposes.
   ```

   > Joule Studio generates this artifact automatically from your intent statement. The `instructionSupported: true` flag on the agent means the disclaimer is enforced directly as an extension instruction — no tools or hooks are needed.

2. On the right panel, click **Deploy** to deploy the extension to your landscape.

<br>![](/exercises/ex2-instruction/images/13_extension_yaml_deploy.png)

---

## Exercise 2.8 - Confirm the Deployment

After completing these steps the extension will be live and the Journal Entry Agent will begin appending the disclaimer to every response.

1. Joule Studio runs the deployment steps — Get Solution ID, Update File, Execute Contract, Deploy Solution. Watch the right panel for progress messages.

2. Once complete, the right panel displays:

   > **Deployment has been completed successfully. The expected behavior can now be tested in the agent.**

3. Select **Add to Joule** to make the extension available in the live Joule chat experience.

<br>![](/exercises/ex2-instruction/images/14_deployment_success.png)

---

## Summary

You have created and deployed an Instruction Extension for the pre-delivered Journal Entry Agent using Joule Studio's Intent-Based Development flow. Starting from a plain language intent statement, Joule Studio:

1. Asked targeted clarifying questions to understand your business goal
2. Produced a structured Intent Analysis Summary and PRD
3. Generated a deployable `extension.yaml` artifact
4. Deployed the extension to your landscape — with no manual YAML editing required

The Journal Entry Agent will now append a mandatory disclaimer to every response, satisfying your regulatory/compliance requirement.

This is the simplest form of an agent extension — a single instruction with no tools or hooks. In Exercise 2, you will see how the same IBD flow handles a much more complex scenario with four extension layers: a pre-hook, a tool integration, an instruction, and a post-hook.

Continue to - [Exercise 2 - Extend the Journal Entry Agent with Intent](../ex2/README.md)
