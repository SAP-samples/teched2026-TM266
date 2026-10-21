# Exercise 2 - Extend the Journal Entry Agent with Intent

In this exercise, we will extend the pre-delivered Journal Entry Agent using intent-based development in Joule Studio.

### Where we left off

In Exercise 1, you ran the standard Journal Entry Agent in Joule Works. You entered a natural language prompt, the agent processed it, and the document was posted directly in SAP S/4HANA Cloud, Public Edition — no additional checks, no validations, no enrichment. The agent did exactly what it was built to do out of the box.

### The business problem

Real-world finance processes are rarely that simple. Consider what a finance team actually needs:

- **Before posting**: Flag unusually high amounts so a controller can review before the document hits the books.
- **During processing**: Check whether the combination of cost center, GL account, profit center, and segment is valid for your organization — rules that are specific to your chart of accounts and not built into the standard agent.
- **With more context**: Pull in existing posting data for the same cost center and GL account so the agent can make a more informed decision.
- **After posting**: Automatically generate a structured summary for audit, reporting, or team notification — without the user having to do anything extra.

None of this is in the standard agent. But all of it can be added — without building a new agent from scratch.

### How agent extensibility works

SAP Joule Works allows you to extend a pre-delivered agent by layering your own business logic on top of it. You describe what you need in plain language. Joule Studio analyzes your intent, generates a structured specification, and produces the extension artifacts — ready to deploy. No boilerplate code, no manual YAML editing.

### What you will build

In this exercise, you will configure four extension layers for the Journal Entry Agent:

- **Pre-hook**: Runs *before* the posting is executed. Checks whether the GL amount exceeds €45 and flags it so the agent can warn the user before the document is created.
- **Tool Extension**: Connects the agent to the **Journal Entry Line Items API** via MCP Hub. Retrieves existing posting data for the same cost center and GL account — giving the agent more context when processing the request.
- **Instruction Extension**: A plain language business rule the agent follows at runtime. Validates that the combination of Cost Center, GL Account, Profit Center, and Segment is valid before accepting the posting.
- **Post-hook**: Runs *after* a successful posting. Generates an HTML summary report of the completed transaction for review or audit.

By the end of this exercise, you will run the same prompt from Exercise 1 — and see exactly how each extension layer changes the agent's behavior. The final deployment to production will be demonstrated live by the speaker.

## Exercise 2.1 - Access Joule Studio and Create a New Extension

After completing these steps you will have opened Joule Studio and initiated a new extension project targeting the Journal Entry Agent.

Joule Studio is the development environment within SAP Joule Works where you create, configure, and manage agent extensions. It provides a guided flow that takes you from a business problem statement all the way through to a generated, deployable extension.

1. Log in to SAP Joule Works using the credentials provided at your desk.

2. In the left navigation panel, select **Joule Studio**.
   
<br>![](/exercises/ex2/images/ex2_1_01_joule_studio_nav.png)

3. On the Joule Studio home screen, select **New → Extension**.
   
<br>![](/exercises/ex2/images/ex2_1_02_new_extension.png)

<br>![](/exercises/ex2/images/ex2_1_02_new_extension1.png)

4. In the Create an Agent Extension dialog that appears:

  1. Solution — keep it set to New Solution
  2. Name — replace **TM266-XXX** with your participant ID, for example TM266-001
  3. Agent — click the icon next to the **Select Agent** field to open the **Agent Catalog**
  4. Intent Statement — leave this for the next step

 <br>![](/exercises/ex2/images/ex2_1_03_new_solution_dialog.png)

5. The Agent Catalog opens showing all available SAP prebuilt agents. In the search box, type "**Journal entry**" to filter the results. From the results, locate the **Journal Entry Agent (S4H PuC, V 1.0.1)** and click **Add **to select it and return to the previous dialog.

   > **Note:** The Agent Catalog shows all pre-delivered agents available in your landscape that have been published as extensible. Only agents marked as extensible can be extended through Joule Studio.
   
 <br>![](/exercises/ex2/images/ex2_1_03_new_solution_dialog1.png)

6. Review the available Extension Points on the agent detail page — you will see Instruction as this is currently GA. Pre-hooks, Post-hooks, and MCP Servers are planned for release after GA post-TechEd, but we will create the extension using all these extension points to showcase the full process. Next click **Select Agent**.

<br>![](/exercises/ex2/images/ex2_1_05_extension_points.png)

## Exercise 2.2 - Enter Your Intent Statement

After completing these steps you will have submitted your business requirements as a natural language intent statement and triggered the Intent-Based Development (IBD) flow in Joule Studio.

The Intent Statement is the starting point for everything Joule Studio builds. Instead of navigating configuration screens manually, you describe what your extension should do in plain business language. Joule Studio analyzes your input and generates a complete extension specification — including business rules, data connections, and workflow hooks — ready for you to review and refine.

1. Back in the Create an Agent Extension dialog, you will now see the **Journal Entry Agent** populated in the **Agent field** and your participant ID in the **Name** field. In the Intent Statement field, copy and paste the following.  Once done, click **Create** to proceed.

Note- Make sure Quick Create is **NOT** selected. Quick Create generates an extension immediately with minimal guidance. Leaving it off activates Intent-Based Development (IBD), which walks you through a structured design flow and produces a richer, more accurate specification based on your input.

 
   > Before processing any journal entry, check if any line item amount exceeds €45. If it does, pause and ask the user for explicit approval before continuing.
   > 
   > Once approved, validate the uploaded journal entry proposal against historical journal entry posting patterns in SAP S/4HANA Cloud Public Edition.To do this, query the Universal Journal — the single source of truth    for all financial postings in S/4HANA Cloud — through the MCP tool to retrieve historical posting data. For each line item, compare the combination of Company Code, G/L Account, Cost Center, Profit Center, and Segment with previously posted journal entries.
   > 
   >Tag each line as:
   > - Commonly Used — the accounting combination is frequently used in historical postings
   > - Rare Pattern — the combination exists but is uncommon or differs from the normal posting pattern
   > - No Historical Match — no historical posting with the same accounting combination exists
   > 
   > For the validation outcome, automatically accept lines tagged as Commonly Used. For Rare Pattern and No Historical Match, provide an explanation and suggest commonly used historical combinations where available.
   > 
   > Once validation is complete, generate a local HTML summary file and open it automatically in the browser. The summary shows a color-coded table — green for accepted lines, yellow for rare patterns, red for no historical match — along with the document number, timestamp, and suggestions for any flagged lines.

<br>![](/exercises/ex2/images/ex2_2_01_intent_statement.png)


## Exercise 2.3 - Answer Intent Clarification Questions and Review the Generated Specification

> **Note:** The clarifying questions in this section are generated dynamically by the LLM based on your intent statement. The exact wording and number of questions may differ each time. There are no fixed right or wrong answers — read each question and select the response that best reflects your business goal. The screenshots below are examples to guide you, not a script to follow exactly.

Once you click Create, Joule Studio takes over — it analyzes your landscape, inspects the existing Journal Entry Agent, and kicks off the Intent-Based Development (IBD) flow. Rather than generating a specification immediately, Joule first asks you a series of focused, value-driven questions to ensure the output is grounded in your specific business context. Work through each question, select the options that best match your use case, and click **Submit Answer** to progress through the flow — Joule will then synthesize your answers into a structured business goals table and a complete Intent Analysis Summary for your final confirmation before writing the specification.

1. Joule Studio begins executing the Intent-Based Development flow — it calls internal tools to analyze your landscape, reads the existing Journal Entry Agent capabilities, and then asks you a series of focused, value-driven questions to sharpen the extension specification. The questions may vary, but below are some examples — read each question carefully and select the options that best fit your use case, then click Submit Answer to proceed.
<br>![](/exercises/ex2/images/ex2_3_01.png)
   
<br>![](/exercises/ex2/images/ex2_3_02.png)

2. Once you have answered all the clarification questions, Joule Studio summarizes the business goals it derived from your responses into a structured table for your confirmation before generating the specification. Review the table to ensure the goals accurately reflect your intent — you can Confirm it as-is, or go back and Edit an entry, Add entry, or Remove entry to adjust before proceeding

<br>![](/exercises/ex2/images/ex2_3_03.png)

3. Joule then presents a complete Intent Analysis Summary — a structured breakdown of everything it has inferred from your intent statement and answers, before writing the final specification file. Next **confirm** and click **Submit Answer**.
<br>![](/exercises/ex2/images/ex2_3_04.png)

## Exercise 2.4 - Navigate the Solution Progress Flow and Review Generated Artifacts

Once the Intent Analysis Summary is confirmed, Joule Studio shifts into a fully guided delivery flow — systematically building out every layer of your extension from business intent to deployable code, with structured checkpoints at each stage for your review and confirmation

1. Once you confirm the Intent Analysis Summary, Joule Studio saves the intent.md file and transitions into a structured Solution Progress flow — guiding you through all four phases visible in the left panel: Intent Summary, Requirements, Specifications, and Solution Artifacts — taking you step by step from business intent all the way through to deployable extension artifacts.

  - The Intent Summary phase is now complete (green checkmark)
  - The flow moves automatically to the Requirements phase
  - On the right, Joule presents a summary of the confirmed solution approach — the €45 approval gate via Before Hook, historical pattern matching via Universal Journal API, and the color-coded HTML report
  - Joule then asks: "Would you like to proceed to creating the Product Requirements Document (PRD), which will detail all the functional requirements for this extension?"
  - Type Yes in the message box and hit send — this triggers Joule to generate the full PRD and populate the Requirements phase with all functional requirements for your extension

<br>![](/exercises/ex2/images/ex2_3_05.png)

2. From here, Joule will automatically walk you through the remaining phases — generating the PRD, then the technical Specifications — pausing at each stage to present a structured summary and ask for your confirmation before writing the next document and advancing to the next phase. Once each phase is completed, the full generated document appears in the center panel — feel free to scroll through and explore the content to understand what Joule has produced before moving on.

  > Note: After reviewing, always respond to Joule in the chat to confirm and instruct it to proceed to the next step — Joule will not advance automatically until you explicitly tell it to continue. If you prefer to skip the guided flow and generate the extension immediately, you can enable Quick Create — however this produces a less detailed specification with no structured review steps.

<br>![](/exercises/ex2/images/ex2_3_07.png)

3. Review the Generated Solution Artifacts

  - The Solution Artifacts phase generates the complete extension package, including the journal-entry-validation-agent-extension with wired Instructions and  Context, validation rules, and Before Hook logic
  - Explore the File tree on the left to browse all generated files and click through the artifacts to understand what Joule has produced
   
<br>![](/exercises/ex2/images/ex2_3_08.png)

4. Click on extension.yaml in the File tree to inspect the full agent extension definition
   
   - including the capability instructions, approval gate logic, and  the step-by-step validation rules Joule has generated directly from your intent
   - Take a moment to read through the file and appreciate how your plain-language intent statement has been translated into a structured, deployable extension
  specification
   
<br>![](/exercises/ex2/images/ex2_3_09.png)

 >  Please stop at this point and do not proceed with deployment.Deploying the extension to production requires the **Joule Studio Administrator** role. Your facilitator will walk through the full deployment and demonstrate the final running extension — as deployment can take several minutes, this will be demonstrated rather than completed individually.


## Summary

You've now successfully extended the pre-delivered Journal Entry Agent using intent-based development in Joule Studio. Starting from a plain language description of your business requirements, Joule Studio guided you through agent selection, specification review, artifact generation, and end-to-end preview testing — all without writing a single line of code. This is the core value of agent extensibility on SAP Autonomous Suite: you bring the business knowledge, Joule Studio handles the translation into a working extension. 

The same pattern applies to any pre-delivered SAP agent — giving you a repeatable, low-effort path to tailoring agent behavior for your organization's specific needs — no specialist development skills required.

Happy TechEd:)

