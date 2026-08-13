# Lesson 3.3: Boosting Productivity with GitHub Copilot

## Lesson Overview

GitHub Copilot has changed significantly. It started as an autocomplete tool. Today it is an AI agent that can plan work, edit multiple files on its own, review your code, and even take a task description and produce a finished pull request without you touching the keyboard.

This session covers the full range — starting with the everyday features you'll use constantly, then moving into the agentic capabilities that are reshaping how professional teams actually work.

**Prerequisites:** Java classes and objects, collections (`ArrayList`, `HashMap`), VS Code, basic Git (`clone`, `pull`, `push`)

**Duration:** 2 hours

---

## Lesson Objectives

By the end of this lesson, you will be able to:

1. **Use** Copilot's core features — ghost text, chat, and slash commands — to write and understand Java code faster
2. **Select** the right Copilot chat mode (Ask, Edit, Agent, Plan) for the task at hand
3. **Write** effective prompts and manage context to get dramatically better results
4. **Apply** Agent Mode to autonomously implement a multi-file feature
5. **Explain** how Copilot's cloud agent, code review, and MCP integration work in professional teams

---

## Session Plan

| Part | Topic | Time |
|---|---|---|
| 1 | Setup and Core Features | 20 min |
| 2 | Chat Modes | 15 min |
| 3 | Prompt and Context Engineering | 20 min |
| 4 | The Case Study Project | 10 min |
| 5 | Agent Mode — Autonomous Development | 25 min |
| 6 | Code Review with Copilot | 15 min |
| 7 | The Cloud Coding Agent (Demo) | 10 min |
| 8 | MCP — Where This Is Heading | 5 min |

---

# Part 1: Setup and Core Features (20 min)

## What Copilot Actually Is

Copilot is an AI model that reads your code as context and predicts what should come next. That's the core mechanism behind everything in this lesson — inline suggestions, chat answers, and autonomous agents are all the same underlying capability, exposed through different interfaces.

The practical implication: **the quality of what you get out is determined by the quality of the context you put in.** We'll return to this repeatedly.

## Step 1: Install and Sign In

1. Open VS Code
2. Click **Extensions** in the sidebar (`Ctrl+Shift+X`)
3. Search for **GitHub Copilot** and install it
4. Also install **GitHub Copilot Chat**
5. Click the **Accounts** icon at the bottom of the sidebar → **Sign in with GitHub**
6. Check the bottom status bar — the Copilot icon should be visible and active

> **Note:** You have GitHub Copilot Business through this programme, which includes every feature in this lesson.

## Step 2: Ghost Text

As you type, Copilot shows suggestions in grey. This is called ghost text.

| Action | Shortcut |
|---|---|
| Accept suggestion | `Tab` |
| Reject suggestion | `Esc` |
| Next suggestion | `Alt + ]` |
| Previous suggestion | `Alt + [` |
| Trigger manually | `Alt + \` |

**Try it now.** Create a file called `Scratch.java` and type:

```java
public class Scratch {

    // Returns the number of business days between two dates, excluding weekends
    
}
```

Press `Enter` after the comment and wait. Copilot will write the method. Press `Tab` to accept.

Notice what just happened: you described intent in a comment, and Copilot produced an implementation. This is the simplest form of prompt engineering — and it's already the core skill.

## Step 3: Copilot Chat

Open Chat with `Ctrl+Alt+I`. This is a conversation about your code rather than an autocomplete.

**Slash commands** are shortcuts for common requests:

| Command | What it does |
|---|---|
| `/explain` | Explains the selected code |
| `/fix` | Finds and fixes bugs in the selected code |
| `/tests` | Generates unit tests |
| `/doc` | Generates documentation comments |

**Try `/fix` now.** Create `Buggy.java`:

```java
public class Buggy {

    public double calculateAverage(int[] numbers) {
        int sum = 0;
        for (int i = 0; i <= numbers.length; i++) {
            sum += numbers[i];
        }
        return sum / numbers.length;
    }
}
```

Select all the code, open Chat, type `/fix`, and press Enter.

Copilot should find three problems: the loop runs one index too far (`<=` should be `<`), integer division truncates the result, and there's no handling for an empty array.

> **About `/tests`:** Copilot will generate a complete JUnit test class, but we don't have a build tool (Maven or Gradle) in this project yet, so there's no JUnit library available to actually run them. For today, use `/tests` to **read and review** what good test coverage looks like. You'll run these for real once we move to Spring Boot.

> **About `/doc`:** Depending on your version, `/doc` may ask what kind of documentation you want — a docs folder, HTML output, or code comments. Choose **Java comments** for inline Javadoc.

---

# Part 2: Chat Modes (15 min)

This is the most important concept in the lesson, and the one most people miss.

Copilot Chat operates in four different modes. There's a **mode selector dropdown in the chat input box** — it's small and easy to overlook, but it fundamentally changes what Copilot does with your request.

| Mode | Behaviour | Use it when |
|---|---|---|
| **Ask** | Answers questions and explains code. Never modifies files. | You want to understand something |
| **Edit** | Makes changes to files you specify. You review each change. | You know exactly what you want changed |
| **Agent** | Decides which files to change, makes the changes, runs commands, and iterates on errors. | You want a task completed, not a specific edit |
| **Plan** | Explores the codebase and produces a written plan for your approval before writing any code. | The task is large or risky |

**The key distinction:** Ask and Edit do what you tell them. **Agent decides for itself** what needs to happen — which files to open, what to change, whether to run a command, and whether the result is correct.

**Try this now.** Open Chat and find the mode dropdown. Switch between Ask and Agent and notice the interface changes slightly. Set it back to **Agent** — we'll use it later.

> **Note:** Agent is often the default mode. If Copilot has ever edited a file you didn't explicitly point it at, that's why.

---

# Part 3: Prompt and Context Engineering (20 min)

Two things control the quality of Copilot's output. Getting these right is the difference between a tool that occasionally helps and one that meaningfully speeds you up.

## Context: What Copilot Can See

Copilot doesn't read your entire project on every request. It sees:

- The file you're currently in
- Code you've selected
- **Other files you have open in tabs**
- Files you explicitly attach to a chat message
- In Agent mode: files it searches for and opens on its own

**Practical rule:** if your question involves two classes, open both files before asking. If you ask "how does `TicketQueue` use `Ticket`?" with only one file open, Copilot is working with half the picture.

**To attach a file explicitly:** click the paperclip / **Add Context** button in the chat input and select the file. This is more reliable than hoping the right tab is open.

## Prompting: Say What You Actually Want

Here's a demonstration worth doing carefully, because the contrast is stark.

**Create `Order.java`:**

```java
public class Order {
    private String customerId;
    private double amount;
    private String status;

    public Order(String customerId, double amount, String status) {
        this.customerId = customerId;
        this.amount = amount;
        this.status = status;
    }

    public double applyDiscount(double percent) {
        if (status.equals("ACTIVE")) {
            if (amount > 100) {
                if (percent > 0 && percent <= 50) {
                    amount = amount - (amount * percent / 100);
                }
            }
        }
        return amount;
    }
}
```

**Prompt A — vague:**

```
make this better
```

**Prompt B — specific:**

```
Refactor applyDiscount to use guard clauses instead of nested if statements. 
Throw IllegalArgumentException if percent is outside 0-50. Do not change 
the method signature or the discount calculation itself.
```

Run both. Prompt A produces *a* change — possibly renaming things, possibly restructuring the whole class, possibly adding features you didn't ask for. Prompt B produces exactly the change you specified.

**The pattern that works:**

| Element | Example |
|---|---|
| **Action** | "Refactor", "Add", "Extract" |
| **Target** | "the `applyDiscount` method" |
| **Constraint** | "without changing the method signature" |
| **Standard** | "using guard clauses" |

If you can write a clear Jira ticket, you can write a good Copilot prompt. It's the same skill.

## Custom Instructions: Prompting Once, Permanently

Rather than repeating your standards in every prompt, write them once in a file Copilot reads automatically.

**Create `.github/copilot-instructions.md`** in your project root:

```bash
mkdir -p .github
```

Then create `.github/copilot-instructions.md` with:

```markdown
# Project Conventions

- Validate method parameters and throw IllegalArgumentException for invalid input
- Use guard clauses rather than nested conditionals
- Add Javadoc to all public methods
- Use enums for fixed sets of values, never String constants
- Prefer immutable fields where a value should not change after construction
```

Every Copilot suggestion in this project now follows these rules without you asking. This is how teams keep AI-generated code consistent with their standards.

> Custom instructions strongly influence output, but don't guarantee it. Still review.

---

# Part 4: The Case Study Project (10 min)

We'll use a support ticket system for the rest of the lesson. It's small enough to understand quickly, but has enough real logic — state transitions, priority handling, assignment rules — that Copilot's agentic features have something meaningful to work with.

**Create these four files.**

### `Priority.java`

```java
public enum Priority {
    LOW,
    MEDIUM,
    HIGH,
    CRITICAL
}
```

### `TicketStatus.java`

```java
public enum TicketStatus {
    OPEN,
    IN_PROGRESS,
    RESOLVED,
    CLOSED
}
```

### `Ticket.java`

```java
public class Ticket {

    private final String id;
    private final String title;
    private final String description;
    private Priority priority;
    private TicketStatus status;
    private String assignedAgentId;
    private final long createdAtMillis;

    public Ticket(String id, String title, String description, Priority priority) {
        if (id == null || id.isBlank()) {
            throw new IllegalArgumentException("Ticket id is required");
        }
        if (title == null || title.isBlank()) {
            throw new IllegalArgumentException("Ticket title is required");
        }
        if (priority == null) {
            throw new IllegalArgumentException("Priority is required");
        }
        this.id = id;
        this.title = title;
        this.description = description;
        this.priority = priority;
        this.status = TicketStatus.OPEN;
        this.assignedAgentId = null;
        this.createdAtMillis = System.currentTimeMillis();
    }

    public String getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getDescription() {
        return description;
    }

    public Priority getPriority() {
        return priority;
    }

    public void setPriority(Priority priority) {
        if (priority == null) {
            throw new IllegalArgumentException("Priority is required");
        }
        this.priority = priority;
    }

    public TicketStatus getStatus() {
        return status;
    }

    public void setStatus(TicketStatus status) {
        if (status == null) {
            throw new IllegalArgumentException("Status is required");
        }
        this.status = status;
    }

    public String getAssignedAgentId() {
        return assignedAgentId;
    }

    public void setAssignedAgentId(String assignedAgentId) {
        this.assignedAgentId = assignedAgentId;
    }

    public long getCreatedAtMillis() {
        return createdAtMillis;
    }

    @Override
    public String toString() {
        return "[" + id + "] " + title
                + " | " + priority
                + " | " + status
                + " | agent=" + (assignedAgentId == null ? "unassigned" : assignedAgentId);
    }
}
```

### `SupportAgent.java`

```java
public class SupportAgent {

    private final String agentId;
    private final String name;
    private final int maxOpenTickets;
    private int currentOpenTickets;

    public SupportAgent(String agentId, String name, int maxOpenTickets) {
        if (agentId == null || agentId.isBlank()) {
            throw new IllegalArgumentException("Agent id is required");
        }
        if (maxOpenTickets < 1) {
            throw new IllegalArgumentException("maxOpenTickets must be at least 1");
        }
        this.agentId = agentId;
        this.name = name;
        this.maxOpenTickets = maxOpenTickets;
        this.currentOpenTickets = 0;
    }

    public String getAgentId() {
        return agentId;
    }

    public String getName() {
        return name;
    }

    public int getMaxOpenTickets() {
        return maxOpenTickets;
    }

    public int getCurrentOpenTickets() {
        return currentOpenTickets;
    }

    public boolean hasCapacity() {
        return currentOpenTickets < maxOpenTickets;
    }

    public void assignTicket() {
        if (!hasCapacity()) {
            throw new IllegalStateException("Agent " + agentId + " is at capacity");
        }
        currentOpenTickets++;
    }

    public void releaseTicket() {
        if (currentOpenTickets > 0) {
            currentOpenTickets--;
        }
    }
}
```

### `TicketQueue.java`

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TicketQueue {

    private final Map<String, Ticket> ticketsById;
    private final Map<String, SupportAgent> agentsById;

    public TicketQueue() {
        this.ticketsById = new HashMap<>();
        this.agentsById = new HashMap<>();
    }

    public void registerAgent(SupportAgent agent) {
        if (agent == null) {
            throw new IllegalArgumentException("Agent is required");
        }
        agentsById.put(agent.getAgentId(), agent);
    }

    public void submitTicket(Ticket ticket) {
        if (ticket == null) {
            throw new IllegalArgumentException("Ticket is required");
        }
        if (ticketsById.containsKey(ticket.getId())) {
            throw new IllegalArgumentException("Duplicate ticket id: " + ticket.getId());
        }
        ticketsById.put(ticket.getId(), ticket);
    }

    public Ticket findById(String ticketId) {
        return ticketsById.get(ticketId);
    }

    public List<Ticket> findByStatus(TicketStatus status) {
        List<Ticket> result = new ArrayList<>();
        for (Ticket ticket : ticketsById.values()) {
            if (ticket.getStatus() == status) {
                result.add(ticket);
            }
        }
        return result;
    }

    public boolean assignTicket(String ticketId, String agentId) {
        Ticket ticket = ticketsById.get(ticketId);
        SupportAgent agent = agentsById.get(agentId);

        if (ticket == null || agent == null) {
            return false;
        }
        if (ticket.getStatus() != TicketStatus.OPEN) {
            return false;
        }
        if (!agent.hasCapacity()) {
            return false;
        }

        agent.assignTicket();
        ticket.setAssignedAgentId(agentId);
        ticket.setStatus(TicketStatus.IN_PROGRESS);
        return true;
    }

    public boolean resolveTicket(String ticketId) {
        Ticket ticket = ticketsById.get(ticketId);
        if (ticket == null) {
            return false;
        }
        if (ticket.getStatus() != TicketStatus.IN_PROGRESS) {
            return false;
        }

        SupportAgent agent = agentsById.get(ticket.getAssignedAgentId());
        if (agent != null) {
            agent.releaseTicket();
        }
        ticket.setStatus(TicketStatus.RESOLVED);
        return true;
    }

    public void printQueueSummary() {
        System.out.println("=== Ticket Queue ===");
        for (Ticket ticket : ticketsById.values()) {
            System.out.println(ticket);
        }
        System.out.println("Open: " + findByStatus(TicketStatus.OPEN).size()
                + " | In progress: " + findByStatus(TicketStatus.IN_PROGRESS).size()
                + " | Resolved: " + findByStatus(TicketStatus.RESOLVED).size());
    }

    public static void main(String[] args) {
        TicketQueue queue = new TicketQueue();

        queue.registerAgent(new SupportAgent("A1", "Priya", 2));
        queue.registerAgent(new SupportAgent("A2", "Marcus", 1));

        queue.submitTicket(new Ticket("T-100", "Login fails on mobile",
                "Users report 500 error on mobile login", Priority.CRITICAL));
        queue.submitTicket(new Ticket("T-101", "Export button misaligned",
                "Minor CSS issue on reports page", Priority.LOW));
        queue.submitTicket(new Ticket("T-102", "Payment webhook timing out",
                "Webhook retries exhausted after 30s", Priority.HIGH));

        queue.assignTicket("T-100", "A1");
        queue.assignTicket("T-102", "A2");
        queue.resolveTicket("T-100");

        queue.printQueueSummary();
    }
}
```

**Run `TicketQueue.java`** and confirm you see the summary output. This is our working system for the rest of the session.

---

# Part 5: Agent Mode — Autonomous Development (25 min)

Everything so far has been you directing Copilot precisely. Agent Mode is different: you describe an outcome, and Copilot works out the steps.

## What Agent Mode Actually Does

When you give Agent Mode a task, it:

1. Searches your project to understand the existing structure
2. Decides which files need to be created or modified
3. Makes the changes across all of them
4. Runs commands if needed (compiling, running the program)
5. Reads any errors and fixes them
6. Presents everything for you to keep or undo

Steps 4 and 5 are what separate this from everything else. It doesn't just write code — it checks whether the code worked and corrects itself.

## Demo: Adding SLA Tracking

**Set the mode dropdown to Agent.** Then enter this prompt:

```
Add SLA breach tracking to the support ticket system.

Requirements:
- Each Priority has a target resolution time: CRITICAL 1 hour, HIGH 4 hours, 
  MEDIUM 24 hours, LOW 72 hours
- Add a method to Ticket that reports whether it has breached its SLA, based 
  on how long it has been open and its current status
- Resolved and closed tickets never count as breached
- Add a method to TicketQueue that returns all breached tickets
- Update printQueueSummary to show a count of breached tickets
- Update main to demonstrate a breach by creating a ticket with an old timestamp

Follow the existing code style and validation approach.
```

**Watch what happens.** Copilot will work through the files one at a time. You'll see it open files, make edits, and show you a running summary of what it's doing.

**When it finishes:**

1. Review each changed file — click through the diffs
2. Look specifically at how it handled the "old timestamp" requirement, since `createdAtMillis` is `final` and set in the constructor. There's no obvious way to do this without changing the class design. **How did Copilot solve it?** This is worth discussing — it had to make a design decision you didn't specify.
3. Run `TicketQueue.java` and confirm the output shows breach information
4. Click **Keep** to accept, or **Undo** to revert

## The Point of That Exercise

Notice you didn't tell it to add a constructor overload, or a package-private setter, or whatever approach it chose. You described the outcome, and it made design decisions on your behalf.

**That's the risk and the value in the same sentence.** Agent Mode will make choices you didn't authorise, and it makes them fast. On a task you understand well, that's leverage. On a task you don't, you've just merged decisions you can't evaluate.

## Plan Mode

If you want to see the reasoning before any code changes, switch the dropdown to **Plan**.

**Try the same prompt in Plan mode.** Instead of editing files, Copilot produces a written implementation plan — which files it intends to change and why. You approve the plan, and only then does it execute.

For anything touching unfamiliar or high-risk code, this is the safer default.

## Activity (10 min)

Using **Agent Mode**, implement this yourself:

```
Add an escalation feature to the support ticket system.

Requirements:
- A ticket can be escalated, which raises its priority by one level 
  (LOW to MEDIUM, MEDIUM to HIGH, HIGH to CRITICAL)
- CRITICAL tickets cannot be escalated further and should throw 
  IllegalStateException
- Escalation should only be allowed on OPEN or IN_PROGRESS tickets
- Track how many times each ticket has been escalated
- Add a TicketQueue method to escalate a ticket by id, returning false 
  if the ticket doesn't exist
- Demonstrate escalation in main, including a failure case

Follow the existing code style.
```

Review every change before accepting. Specifically check: did it handle the `CRITICAL` boundary correctly? Did it validate status before escalating?

---

# Part 6: Code Review with Copilot (15 min)

Copilot can review code the way a senior engineer would — looking for correctness issues, edge cases, and design problems rather than formatting.

## Local Review in VS Code

Open `TicketQueue.java`, select the entire file, and in Chat (Ask mode) enter:

```
Review this class for correctness bugs, unhandled edge cases, and design 
problems. Focus on the assignment and resolution logic. For each issue, 
explain the specific scenario where it causes a problem.
```

**Things Copilot should surface:**

- `resolveTicket` releases the agent's capacity, but nothing handles a ticket being reassigned or cancelled — the count can drift out of sync
- There's no way to move a ticket to `CLOSED` at all, despite the enum defining it
- `findByStatus` returns a new list, but the `Ticket` objects inside are the live ones — callers can mutate queue state through the returned list
- `submitTicket` rejects duplicate IDs but `registerAgent` silently overwrites an existing agent
- No handling for what happens to assigned tickets when an agent is removed or unavailable

**This is the demonstration that matters most for experienced engineers.** These aren't style suggestions — they're real design flaws that would cause production incidents. Read through them and decide which you agree with.

> **Ask it to justify itself.** Follow up with: "Which of these would you fix first and why?" The reasoning quality tells you how much to trust the findings.

## Activity (5 min)

Select `SupportAgent.java` and ask for a review focused on the capacity tracking. Then ask Copilot to fix the single most serious issue it found — and check whether the fix is actually correct.

---

# Part 7: The Cloud Coding Agent (Demo — 10 min)

> **Instructor demo.** This runs on GitHub rather than your machine and requires familiarity with GitHub issues and pull requests. Watch how it works — you can try it yourself afterwards.

## What It Is

Everything so far ran inside VS Code, with you watching. The **cloud coding agent** runs on GitHub's servers, without you present.

The workflow:

1. You create a **GitHub issue** describing a task — the same way you'd file a ticket for a colleague
2. You **assign the issue to Copilot**
3. Copilot works in a sandboxed environment on GitHub's infrastructure — reading the repository, writing code, running tests
4. It opens a **pull request** with the finished work
5. You review the pull request and merge it, or ask for changes

## Terms You Need

Since we haven't used these yet:

- **Branch** — a separate copy of the code where changes can be made without affecting the main version
- **Pull request (PR)** — a proposal to merge a branch into the main code. It shows exactly what changed and lets people review before it's accepted
- **Diff** — the view of what changed: green lines added, red lines removed

## What Makes This Different

The agent isn't waiting on you. You can file three issues, assign all of them, close your laptop, and come back to three pull requests.

Every PR it opens automatically goes through security scanning — vulnerability analysis, secret detection, and dependency checks — before you even look at it.

**Where it works well:** self-contained features, bug fixes with clear reproduction steps, adding test coverage, dependency updates.

**Where it struggles:** changes spanning many files with architectural implications. The more design judgement a task requires, the more review it needs.

## Agentic Code Review on Pull Requests

The same review capability we used locally also runs on pull requests — and it's more capable there, because it can see the full change in the context of the entire repository rather than just the file in front of it.

Teams also use this for governance: a `SKILL.md` file under `.github/skills` lets an organisation define its own review standards, and every review in that repository applies them automatically.

---

# Part 8: MCP — Where This Is Heading (5 min)

One last concept, because it explains why these tools keep getting more capable.

**Model Context Protocol (MCP)** is a standard way for AI agents to connect to external systems. With MCP configured, Copilot can — during a single task — query a database schema, read documentation from Confluence or Notion, check whether a CI pipeline passed, or pull design specs from Figma.

**Why this matters:** the limit on what Copilot can do has never really been the model. It's been context. An agent that can only see your open files is guessing about everything else. An agent that can read your actual schema, your actual documentation, and your actual build status is working with the same information you have.

That's the direction: not a smarter autocomplete, but an agent wired into the systems your team already uses.

---

# Wrap-Up

## What You Should Take Away

**The mode matters.** Ask, Edit, Agent, and Plan produce very different behaviour from the same prompt. Know which one you're in.

**Context is the constraint.** Copilot's answer quality tracks directly with what it can see. Open the relevant files. Attach them explicitly. Write custom instructions once rather than repeating yourself.

**Specificity beats politeness.** "Make this better" produces a guess. Stating the action, target, constraint, and standard produces what you asked for.

**Agent Mode makes design decisions.** It will resolve ambiguity in your request by choosing an approach. On familiar ground that's leverage; on unfamiliar ground it's a liability.

**Review is not optional.** The code review exercise found real design flaws in code that compiled and ran correctly. Copilot can find those problems — and it can also create them.

## The Honest Summary

Copilot has moved from autocomplete to autonomous agent in about three years. The features shift constantly — commands get renamed, panels get merged, defaults change. What doesn't change is the underlying relationship: **the better you understand the code, the more value you get from the tool.**

Engineers who understand systems deeply use this to move considerably faster. Engineers who don't use it to generate code they can't evaluate. The tool amplifies whichever one you are.

---

END