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

- **Pre-hook**: Runs *before* the posting is executed. Checks whether the GL amount exceeds €15,000 and flags it so the agent can warn the user before the document is created.
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

4. In the dialog that appears, select **New Solution** and enter a unique name — use `TM266-XX` where `XX` is your participant number from your desk card. The solution acts as a container for your extension project and all its artifacts.
<br>![](/exercises/ex2/images/ex2_1_03_new_solution_dialog.png)

5. Open the **Agent Catalog** and search for the **Journal Entry Agent**. Select it to open the agent detail page.

   > **Note:** The Agent Catalog shows all pre-delivered agents available in your landscape that have been published as extensible. Only agents marked as extensible can be extended through Joule Studio.

<br>![](/exercises/ex2/images/ex2_1_04_agent_catalog.png)

6. Review the available **Extension Points** on the agent detail page — you will see: Instruction, Pre-hooks, Post-hooks, and MCP Servers. These are the four layers you will configure in this exercise.
<br>![](/exercises/ex2/images/ex2_1_05_extension_points.png)

## Exercise 2.2 - Enter Your Intent Statement

After completing these steps you will have submitted your business requirements as a natural language intent statement and triggered the Intent-Based Development (IBD) flow in Joule Studio.

The Intent Statement is the starting point for everything Joule Studio builds. Instead of navigating configuration screens manually, you describe what your extension should do in plain business language. Joule Studio analyzes your input and generates a complete extension specification — including business rules, data connections, and workflow hooks — ready for you to review and refine.

1. In the **Intent Statement** box, enter the following prompt exactly as shown:

   > *Extend the Journal Entry Agent to flag GL postings above €15,000 before posting, look up existing journal entry line items for the same cost center and GL account, validate the combination of cost center, GL account, profit center, and segment, and generate an HTML summary report after each successful posting.*

<br>![](/exercises/ex2/images/ex2_2_01_intent_statement.png)

2. Make sure **Quick Create is NOT selected**. Quick Create generates an extension immediately with minimal guidance. Leaving it off activates Intent-Based Development (IBD), which walks you through a structured design flow and produces a richer, more accurate specification based on your input.
<br>![](/exercises/ex2/images/ex2_2_02_quick_create_off.png)

3. Select **Continue**.

## Exercise 2.3 - Review and Accept the Generated Specification

After completing these steps you will have reviewed the Joule Studio-generated specification and confirmed the extension design covering all four extension layers.

The Specification screen is where Joule Studio presents its interpretation of your intent — a structured design document generated automatically from your natural language input. It describes exactly what the extension will do and which extension points it will use. You can review and edit this before anything is generated.

1. Joule Studio presents a **Specification** summarizing the proposed extension design. Review each section:
   - **Pre-hook**: GL amount threshold check — flags amounts above €15,000 before the posting is executed
   - **Tool Extension**: Journal Entry Line Items lookup via MCP Hub for the relevant cost center and GL account
   - **Instruction Extension**: Validation rule for the combination of Cost Center, GL Account, Profit Center, and Segment
   - **Post-hook**: HTML summary report generated after each successful posting
<br>![](/exercises/ex2/images/ex2_3_01_specification_overview.png)

2. Review the **Instruction Extension** text in the editable field. This is the plain language business rule the agent will follow at runtime. You can edit it directly to refine the wording if needed.
<br>![](/exercises/ex2/images/ex2_3_02_instruction_edit.png)

3. When satisfied with the specification, select **Build** to proceed to project generation.
<br>![](/exercises/ex2/images/ex2_3_03_build_button.png)

## Exercise 2.4 - Review the Generated Extension Artifacts

After completing these steps you will have inspected the extension artifacts generated by Joule Studio and understood what each one does.

When you select Build, Joule Studio automatically generates a set of artifacts that define your extension — the technical representation of the specification you approved. You do not need to write or edit these manually.

1. Once the project is generated, open the artifacts overview to see everything that was created.
<br>![](/exercises/ex2/images/ex2_4_01_artifacts_overview.png)

2. Open the **Instruction definition**. This contains the natural language business rule that will be injected into the agent's context at runtime — telling it how to validate the account combination before accepting a posting.
<br>![](/exercises/ex2/images/ex2_4_02_instruction_artifact.png)

3. Open the **Tool configuration**. This defines the connection to the Journal Entry Line Items API via MCP Hub, including any credential assignments that were automatically applied.
<br>![](/exercises/ex2/images/ex2_4_03_tool_artifact.png)

4. Open the **Pre-hook definition**. This is the workflow that executes before each posting attempt — checking the GL amount and returning a flag to the agent if the €15,000 threshold is exceeded.
<br>![](/exercises/ex2/images/ex2_4_04_prehook_artifact.png)

5. Open the **Post-hook definition**. This is the workflow that fires after a successful posting and produces the HTML summary report.
<br>![](/exercises/ex2/images/ex2_4_05_posthook_artifact.png)

> **Note:** Instruction extensions are fully supported at GA. Tool and hook artifacts may be subject to feature availability in your landscape — verify with your administrator if any artifacts are not visible.

## Exercise 2.5 - Test the Extended Agent in Preview

After completing these steps you will have run the extended Journal Entry Agent and observed all four extension layers firing in sequence using the same prompt from Exercise 1.

Before deploying to production, Joule Studio allows you to test your extension in a preview chat experience connected to your development environment. This lets you validate the agent's behavior end-to-end and confirm that each extension layer fires correctly before any changes reach the live system.

1. Open the **chat experience** in Joule Works.
<br>![](/exercises/ex2/images/ex2_5_01_chat_open.png)

2. Enter the same prompt you used in Exercise 1:

```
Post JE – CC 4400 – GL 618000 – €75,000 intercompany
```

3. The **pre-hook** fires first and flags the €75,000 amount — because it exceeds the €15,000 threshold configured in your extension.
<br>![](/exercises/ex2/images/ex2_5_02_prehook_flag.png)

4. The agent then uses the **Journal Entry Line Items tool** to look up existing postings for CC 4400 and GL 618000, applies the **instruction rule** to validate the account combination, and once the posting is confirmed, the **post-hook** fires and generates the HTML summary report.
<br>![](/exercises/ex2/images/ex2_5_03_posthook_report.png)

5. Compare this result to what you saw in Exercise 1. The prompt is identical — but the agent's behavior is now shaped entirely by the business logic you defined through intent.

## Summary

You've now successfully extended the pre-delivered Journal Entry Agent using intent-based development in Joule Studio. Starting from a plain language description of your business requirements, Joule Works guided you through agent selection, specification review, artifact generation, and end-to-end preview testing — all without writing a single line of code.

This is the core value of agent extensibility on SAP Autonomous Suite: you bring the business knowledge, Joule Works handles the translation into a working extension. The same pattern applies to any pre-delivered SAP agent — giving you a repeatable, low-effort path to tailoring agent behavior for your organization's specific needs.

> **Deployment Note:** Deploying the extension to production requires the **Joule Studio Administrator** role. The speaker will now demonstrate the full deployment flow and run the extended agent live in the production environment.

Continue to - [Summary](../../README.md)
