# Lesson: Coaching: Boosting Productivity with GitHub Copilot

## Lesson Overview

This Saturday coaching session has two parts. The first hour revisits key concepts from recent lessons through a structured recap and hands-on activity. The second part introduces GitHub Copilot — an AI-powered coding assistant built into VS Code — and shows you how to use it to write Java code faster, smarter, and with less repetition. By the end of this session you will have hands-on experience with Copilot's most useful features, an understanding of how Copilot's chat modes work, and a live demo of its most advanced agentic capabilities.

**Prerequisites:** Basic Java knowledge — classes, methods, and familiarity with VS Code

## Lesson Objectives

By the end of this lesson, students will be able to:

1. **Install** and configure GitHub Copilot in VS Code and use inline suggestions to generate Java code
2. **Apply** Copilot Chat, slash commands, and chat modes (Ask / Edit / Agent / Plan) to explain, fix, refactor, and document code
3. **Review** Copilot-generated unit tests and Javadoc, understanding what's possible today versus what will unlock once a build tool is introduced
4. **Use** Copilot's agentic features — Copilot CLI and Agent Mode — to autonomously extend an existing Java class across multiple files
5. **Evaluate** Copilot's suggestions critically and understand its limitations, best practices, and how quickly its feature set evolves

---

## Part 1: Setup and First Contact

### What is GitHub Copilot?

GitHub Copilot is an AI-powered coding assistant developed by GitHub and OpenAI. It integrates directly into VS Code and suggests code as you type — from single lines to entire methods and classes. It is trained on billions of lines of public code and understands context from your current file, comments, and method names.

Think of it as a very experienced pair programmer sitting next to you, offering suggestions in real time. You are always in control — you decide what to accept, modify, or reject.

**Why learn this?**
AI coding assistants are rapidly becoming a standard part of professional development workflows. Knowing how to use Copilot effectively makes you significantly faster and more productive — not just in Java, but in any language.

### Free Tier vs Pro

GitHub Copilot offers a **free tier** which includes:
- Code completions (limited number per month)
- Copilot Chat in VS Code
- Access to slash commands (`/explain`, `/fix`, `/tests`, `/doc`)
- Copilot CLI in the terminal

**Pro features** (paid, but you will see a live demo):
- Unlimited completions
- Higher usage limits for Agent Mode and multi-file editing
- Custom instructions at scale across large projects

For this lesson, everything in Parts 1–4 works on the free tier. Parts 5 onward are instructor demo, with several sections now also practiced hands-on.

### Step 1: Install GitHub Copilot in VS Code

1. Open VS Code
2. Click the **Extensions** icon in the sidebar (or `Ctrl+Shift+X`)
3. Search for **GitHub Copilot**
4. Install **GitHub Copilot** (by GitHub)
5. Also install **GitHub Copilot Chat** (by GitHub) if not already bundled

### Step 2: Sign In with Your GitHub Account

1. After installation, click the **Accounts** icon at the bottom of the VS Code sidebar
2. Select **Sign in with GitHub**
3. Follow the browser prompt to authorise VS Code
4. Once signed in, you will see the Copilot icon in the bottom status bar — a green circle confirms it is active

> ⚠️ **Free tier:** You will need a GitHub account. If you don't have one, create one at https://github.com. The free tier is available without a credit card.

### Step 3: Understanding Suggestions (Ghost Text)

When Copilot is active, it shows suggestions as **grey ghost text** as you type. Here are the key shortcuts:

| Action | Shortcut |
|---|---|
| Accept suggestion | `Tab` |
| Reject suggestion | `Esc` |
| Next suggestion | `Alt + ]` |
| Previous suggestion | `Alt + [` |
| Open all suggestions | `Ctrl + Enter` |
| Trigger suggestion manually | `Alt + \` |

Create a new file `CopilotDemo.java` and just start typing — observe how Copilot immediately starts suggesting code.

---

## Part 2: Code Completion

This is Copilot's most fundamental feature. As you type, it predicts what you want to write next based on the context of your file.

### Completing Methods from Method Names

Create a new file `MathHelper.java`. Just type the method signature and let Copilot fill in the body.

```java
public class MathHelper {

    // Just type the signature and press Tab when Copilot suggests the body
    public int add(int a, int b) {
    
    }

    public boolean isEven(int number) {
    
    }

    public int factorial(int n) {
    
    }
}
```

Observe how Copilot infers the correct implementation from the method name alone.

### Comment-Driven Development

One of the most powerful techniques is to **write a comment describing what you want** and let Copilot write the code.

```java
public class StringUtils {

    // Method that checks if a string is a palindrome
    
    // Method that counts the number of vowels in a string
    
    // Method that reverses the words in a sentence
    
    // Method that capitalises the first letter of every word
}
```

Type each comment, press `Enter`, and wait for Copilot's suggestion. Accept with `Tab`.

### Completing Repetitive Code

Copilot is excellent at completing repetitive boilerplate. Create a `Customer.java` POJO:

```java
public class Customer {
    private String firstName;
    private String lastName;
    private String email;
    private int yearOfBirth;

    // Start typing "public String get" and watch Copilot complete all getters
    // Then type "public void set" and watch it complete all setters
}
```

### 🧑‍💻 Activity **(15 minutes)**

Using **only comments** and Copilot suggestions, build a `Calculator.java` class that includes:

1. `add`, `subtract`, `multiply`, `divide` methods
2. A method that returns the maximum of three numbers
3. A method that checks if a number is prime
4. A method that converts Celsius to Fahrenheit

Do not type any implementation code yourself — write the comment, accept Copilot's suggestion, and move on.

---

## Part 3: GitHub Copilot Chat

Copilot Chat is a conversational AI assistant built into VS Code. Unlike ghost text suggestions, Chat lets you ask questions, request explanations, and have a dialogue about your code.

### Opening Copilot Chat

- Click the **Copilot Chat** icon in the VS Code sidebar (speech bubble icon)
- Or use the keyboard shortcut `Ctrl+Alt+I`

### Explaining Code

Paste the following buggy, hard-to-read code into a new file `Mystery.java`:

```java
public class Mystery {
    public static int m(int[] a) {
        int x = 0;
        for (int i = 0; i < a.length; i++) {
            if (a[i] > x) x = a[i];
        }
        return x;
    }

    public static boolean c(String s) {
        for (int i = 0; i < s.length() / 2; i++) {
            if (s.charAt(i) != s.charAt(s.length() - 1 - i)) return false;
        }
        return true;
    }
}
```

Select all the code, then in Copilot Chat type:

```
Explain what this code does
```

Observe how Copilot gives a clear, detailed explanation.

### Fixing Bugs

Paste this buggy code into a file `BuggyService.java`:

```java
public class BuggyService {

    public double calculateAverage(int[] numbers) {
        int sum = 0;
        for (int i = 0; i <= numbers.length; i++) {
            sum += numbers[i];
        }
        return sum / numbers.length;
    }

    public String formatName(String firstName, String lastName) {
        return lastName.toUpperCase() + ", " + firstName.toUppercase();
    }
}
```

Select the code and in Chat type:

```
/fix
```

Copilot will identify both bugs — the off-by-one error in the loop and the incorrect method name `toUppercase()` — and suggest fixes.

### Refactoring Code

Select this repetitive code:

```java
public class OrderProcessor {

    public void processOnlineOrder(String item, int qty) {
        System.out.println("Processing online order");
        System.out.println("Item: " + item);
        System.out.println("Quantity: " + qty);
        double price = qty * 10.0;
        System.out.println("Total: $" + price);
    }

    public void processInStoreOrder(String item, int qty) {
        System.out.println("Processing in-store order");
        System.out.println("Item: " + item);
        System.out.println("Quantity: " + qty);
        double price = qty * 10.0;
        System.out.println("Total: $" + price);
    }
}
```

In Chat type:

```
Refactor this to remove duplication
```

### 🧑‍💻 Activity **(15 minutes)**

You have been given the following `CustomerValidator.java` with multiple issues:

```java
public class CustomerValidator {

    public boolean validateEmail(String email) {
        if (email == null) {
            return true; // should be false
        }
        return email.contains("@");
    }

    public boolean validateAge(int yearOfBirth) {
        int currentYear = 2024;
        int age = currentYear - yearOfBirth;
        if (age >= 18 & age <= 100) { // wrong operator
            return true;
        }
        return false;
    }

    public String formatCustomerName(String first, String last) {
        return first + last; // missing space
    }
}
```

Using **Copilot Chat only**:
1. Ask Copilot to explain what the class does
2. Use `/fix` to identify and fix all bugs
3. Ask Copilot to refactor `validateAge` to be more concise
4. Ask Copilot "What other validation methods would be useful for a Customer class?"

---

## Part 4: Slash Commands, Tests, and Documentation

### Slash Commands Reference

Slash commands are shortcuts you type in Copilot Chat to trigger specific actions.

| Command | What it does |
|---|---|
| `/explain` | Explains selected code in plain English |
| `/fix` | Identifies and fixes bugs in selected code |
| `/tests` | Generates unit tests for selected code (view/review only until we have a build tool — see below) |
| `/doc` | Generates Javadoc comments for selected code (may show an options menu — see below) |

### Generating Unit Tests (View and Review, Not Create — Yet)

Select your `MathHelper.java` from Part 2 and in Chat type:

```
/tests
```

Copilot will generate a complete JUnit 5 test class with multiple test cases including edge cases you might not have thought of, shown directly in the chat response.

> ℹ️ **Important:** Because our project doesn't have a build tool (Maven/Gradle) or JUnit on the classpath yet, we're using `/tests` here purely as a **"view and learn"** exercise — reading and understanding what a good test class looks like — rather than inserting it as a real, runnable test file. Once we build a Spring Boot project with a build tool later in the course, we'll come back and actually run `/tests` hands-on to create working test classes.

Review the generated tests — Copilot usually covers:
- Happy path (normal inputs)
- Edge cases (zero, negative numbers)
- Boundary values

### Generating Javadoc

Select your `Calculator.java` from the activity and in Chat type:

```
/doc
```

> ℹ️ **Note:** Depending on your Copilot Chat version, `/doc` may not insert Javadoc directly — it may first show you a menu asking what kind of documentation you want (e.g. a `/docs` folder with markdown, Javadoc HTML output, or Java comments). **Select "Java comments"** to get the inline Javadoc block described below.

Copilot generates complete Javadoc with `@param`, `@return`, and `@throws` tags for every method.

### Inline Chat with `Ctrl+I`

Inline Chat lets you edit code without leaving your editor — you don't need to open the Chat panel.

1. Place your cursor inside a method
2. Press `Ctrl+I`
3. Type an instruction directly in the editor

For example, place your cursor inside `calculateAverage` and press `Ctrl+I`, then type:

```
Handle the case where the array is empty or null
```

Copilot will modify the method in place. You can accept (`Enter`) or discard (`Esc`).

### 🧑‍💻 Activity **(15 minutes)**

Take the `Library.java` class you built in the revision activity and use Copilot to:

1. Use `/doc` to select one method and generate Javadoc for it — if prompted with an options menu, choose **"Java comments"**
2. Use `/tests` to generate and review unit tests for your methods — **don't add this to the project yet**, since we don't have a build tool set up (we'll do this hands-on later with Spring Boot)
3. Use `Ctrl+I` inline chat to add a `removeBook(String title)` method
4. Use `Ctrl+I` to add input validation to `addBook()` — it should throw an `IllegalArgumentException` if the book is null

---

## Part 5: Advanced Features (Instructor Demo + Hands-On)

> ℹ️ **A quick word before we dive in:** Copilot's chat features are built around different **modes** — Ask, Edit, Agent, and Plan. Understanding these will make everything in this section click.

### Understanding Copilot Chat's Modes

Copilot Chat isn't just one thing — it operates in different modes, selectable from a dropdown at the top (or bottom) of the chat input box. It's small and easy to miss, but it changes Copilot's behavior significantly:

| Mode | What it does |
|---|---|
| **Ask** | Answers questions and explains code. Never edits anything — pure Q&A. |
| **Edit** | Applies inline edits across chosen files based on your instruction — more manual and scoped than Agent. |
| **Agent** | Autonomously plans and executes multi-step, multi-file changes. Applies edits automatically, showing "keep/undo" per file rather than showing its reasoning up front. |
| **Plan** | Works like Agent, but explicitly explores the codebase and shows its reasoning/plan *before* any code changes start — use this mode if you want to see the thinking, not just the result. |

**Why this matters:** if you've noticed Copilot editing multiple files, or answering workspace-wide questions, without you explicitly asking it to — that's because **Agent mode is often the default** in current Copilot Chat. It reasons across your whole workspace and applies changes automatically, which is why some older, separate features (`@workspace`, a distinct "Edits" panel) have quietly folded into this same agentic behavior. We'll see this play out through the rest of this section.

### Commit Message Generation (Free Tier)

When you have staged changes in VS Code's Source Control panel, Copilot can suggest a commit message automatically.

1. Make changes to a file and **stage them** first
2. In the Source Control panel, look for the **sparkle icon** at the edge of the commit message box
3. Copilot generates a meaningful commit message based on your diff

> ℹ️ **Note:** The sparkle icon only appears/activates once you have staged changes. If you don't see it, double-check that something is actually staged first.

This saves time and encourages consistent, descriptive commit messages.

### Asking Workspace-Scoped Questions (Free Tier)

You can ask Copilot Chat questions about your whole project directly, in natural language — no special syntax required:

```
How does the customer validation logic work in this project?
Which classes depend on CustomerRepository?
How is the database configured in this project?
```

Copilot reads relevant parts of your workspace automatically to answer — this is implicit, automatic behavior in current Copilot Chat (older versions required typing `@workspace` first; that explicit tag is no longer necessary in most cases).

> ℹ️ **Related but different: `@newWorkspace`.** You may see this appear in autocomplete — it's a separate feature for **scaffolding a brand-new project from a description** (e.g. "create a new Spring Boot REST API project"), not for asking questions about a project you're already working in. Don't confuse the two.

### Copilot CLI — Your Agent, Right in the Terminal (Free Tier)

Copilot's terminal integration has moved beyond simple command suggestions — it now offers **Copilot CLI**, a full agentic session that runs directly in your terminal.

1. Open the integrated terminal (`` Ctrl+` ``)
2. Type:
   ```
   copilot
   ```
3. This launches an interactive Copilot CLI session — describe what you want in plain English, and Copilot works out the right command(s), shows you what it's about to run, and you approve before it executes.

Try it:
```
find all java files modified in the last 7 days
```

**To exit the CLI session and return to your normal terminal:** type `/exit`, or press `Ctrl+D`, or press `Ctrl+C` twice.

> ℹ️ **How this relates to Agent Mode:** Copilot CLI uses the **same agentic engine as Agent Mode** — the difference is where it lives. Agent Mode (below) works inside the editor, planning and executing changes across your codebase. Copilot CLI works in the terminal, independent of the editor, which is useful for scripting, file management, and Git operations without ever leaving the command line. We'll see the fuller, multi-file version of this same capability next, in Agent Mode.

### Copilot Edits — Multi-File Editing (Free Tier / Hands-On)

Copilot lets you describe a change and apply it **across multiple related files simultaneously** — you no longer need a separate "Edits" panel or mode-switch for this; simply describe the change directly in the regular Chat input, and Copilot detects when it spans multiple files.

**Demo 1 — `Customer.java` + `CustomerValidator.java`**

With both files open, type in Chat:

```
Add a phoneNumber field to the Customer class, and update 
CustomerValidator to validate that phoneNumber is not null 
and contains only digits.
```

Copilot proposes changes to **both** files, shown as diffs you review and accept file by file.

**Demo 2 / Activity — `Library.java` + `Book`**

```
Add an isbn field to the Book class, and update Library's 
addBook, removeBook, and any display methods to include it.
```

This works well as a student-led follow-up, since you already built `Library.java` yourselves in Part 4.

### Custom Instructions (Free Tier / Hands-On)

Custom instructions tell Copilot about your project conventions so every suggestion follows your standards automatically, without you having to repeat them each time.

**Theory example — Spring Boot conventions (you'll apply this later):**

Create a file `.github/copilot-instructions.md` in a project:

```markdown
# Copilot Instructions

- Always use constructor injection, never field injection with @Autowired
- Always return ResponseEntity from controller methods
- Always use Optional for repository findById calls
- Follow the service-repository pattern — no business logic in controllers
- Use Lombok annotations (@Getter, @Setter, @Builder) on all entity classes
```

**Hands-on activity — scoped to what you know today (Java + Collections, no Spring Boot yet):**

Create `.github/copilot-instructions.md` in your current workspace with:

```markdown
# Copilot Instructions

- Always add Javadoc comments to every public method (summary, @param, @return)
- Use meaningful variable names — no single-letter names except loop counters (i, j, k)
- Prefer ArrayList over raw arrays for collections that may change size
- When removing items from a List/Set/Map during iteration, use removeIf() 
  or Iterator.remove() — never modify a collection directly inside a for-each loop
- Always check for null before accessing object fields or calling methods on 
  method parameters
- Follow standard Java naming conventions: camelCase for methods/variables, 
  PascalCase for class names
- Include a main() method with a small demo/test of the class when generating 
  a new class from scratch
```

Then, in a fresh file, ask Copilot Chat:

```
Create a Java class called Inventory that stores product names and quantities. 
Include methods to add stock, remove stock, and update the quantity of an 
existing product.
```

**Verify the generated code against your rules:** Is there Javadoc on every public method? Meaningful names? Null-checks on parameters? A `main()` demo? 

> ℹ️ Frame this for students as: **custom instructions are a strong influence, not a guarantee.** Always review the output — don't assume the rules were followed 100% of the time.

### Agent Mode — Autonomous Multi-Step Tasks (Hands-On + Pro Demo)

Agent Mode is Copilot's most powerful capability — instead of suggesting one piece of code at a time, it plans and executes multi-step tasks autonomously across your entire project, applying changes automatically (surfacing risky actions for your review).

**Theory demo (Spring Boot — you'll do this later):**

```
Add a new Interaction endpoint to simple-crm. 
Create the entity with fields: id, remarks, interactionDate, customerId.
Add the JPA repository, service interface and implementation, 
and REST controller with full CRUD. Follow the existing patterns in the project.
```

**Hands-on activity — Inventory Reservation System (extends the class you just built):**

Using your `Inventory.java` from the Custom Instructions activity, switch to **Agent** mode (or simply proceed — your version may detect this automatically) and type:

```
Extend my Inventory class into a reservation system. Add the ability to 
reserve stock for an order (without removing it from available inventory 
yet), confirm a reservation (which permanently deducts the stock), or 
cancel a reservation (which releases the reserved stock back to available 
inventory). Track reservations by a reservation ID. Also create a Main 
class with a runnable demo showing: a successful reservation, a 
confirmation, a cancellation, and one case where a reservation fails 
because there isn't enough available stock.
```

Watch Copilot:
1. Analyse the existing `Inventory.java` structure
2. Plan the new state it needs (tracking reserved vs. available stock)
3. Propose changes across files, showing diffs for review
4. Generate a runnable `Main` class demonstrating all four scenarios

**Want to see the planning stage explicitly, before any code changes?** Switch to **Plan** mode instead of Agent — it will explore the codebase and lay out its plan for your review first, rather than applying changes directly.

This demonstrates the direction AI-assisted development is heading — from suggestion, to execution, to full autonomous multi-step reasoning.

---

## Part 6: Best Practices and Limitations

### When to Trust Copilot

✅ **Copilot is reliable for:**
- Boilerplate and repetitive code (getters, setters, constructors)
- Standard algorithm implementations
- Generating test cases
- Writing documentation
- Explaining unfamiliar code

⚠️ **Always review carefully:**
- Business logic — Copilot doesn't know your requirements
- Security-sensitive code — always audit authentication, validation, and data handling
- Database queries — verify correctness against your schema
- Edge cases — Copilot may miss unusual scenarios

### Copilot Can Be Wrong

Copilot is a prediction engine, not an oracle. It can:
- Generate code that compiles but produces incorrect results
- Suggest outdated APIs or deprecated methods
- Miss edge cases in complex logic
- Hallucinate method names that don't exist

**Rule:** Always read and understand every suggestion before accepting it. You are responsible for the code in your repository — not Copilot.

### Copilot's Features Change Fast

As you've seen throughout this lesson, Copilot's exact behavior — which commands exist, whether a feature needs an explicit tag or mode, even where a button lives in the UI — shifts frequently between versions. The underlying *concepts* (completions, chat, agentic multi-file editing) are stable; the *exact mechanics* are not. When in doubt, test live before relying on documentation or a lesson written even a few months ago.

### Privacy on the Free Tier

On the free tier, your code snippets may be used to improve GitHub Copilot's models. For private or proprietary code, check your organisation's policy before enabling Copilot. Pro and Enterprise tiers offer options to opt out of data sharing.

### The Right Mental Model

Copilot is a productivity multiplier, not a replacement for understanding. It makes good developers faster — but it cannot replace the foundations. If you don't understand Java, Copilot suggestions won't make sense and you won't be able to evaluate whether they're correct.

**The best way to use Copilot:** Learn the foundations deeply, then let Copilot handle the routine parts so you can focus on what matters.

---

## Summary

| Feature | What it does | Tier |
|---|---|---|
| Ghost text completion | Suggests code as you type | Free |
| Comment-driven generation | Write a comment, get the code | Free |
| Copilot Chat (Ask / Edit / Agent / Plan modes) | Conversational AI in VS Code, with modes for answering, scoped edits, and full autonomous execution | Free |
| `/explain` `/fix` `/tests` `/doc` | Slash command shortcuts (`/tests` and `/doc` currently limited by our lack of a build tool — see Part 4) | Free |
| Inline chat (`Ctrl+I`) | Edit code in place | Free |
| Commit message generation | Suggests git commit messages from staged changes | Free |
| Workspace-scoped questions | Ask about your whole project directly, in natural language | Free |
| Copilot CLI | Agentic session in the integrated terminal (type `copilot`) | Free |
| Copilot Edits (multi-file, via Chat) | Describe a change once, applied across related files as reviewable diffs | Free |
| Custom instructions | Project-specific conventions applied automatically | Free (scales further on Pro) |
| Agent Mode | Autonomous multi-step, multi-file tasks | Free (higher limits on Pro) |

GitHub Copilot changes how fast you can write code — but your understanding of Java, Spring Boot, and software design is what lets you use it well. The goal is not to replace thinking, but to remove the friction so you can focus on the parts that matter most.

---

END