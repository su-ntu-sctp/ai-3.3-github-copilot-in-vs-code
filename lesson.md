# Lesson: Coaching: Boosting Productivity with GitHub Copilot

## Lesson Overview

This Saturday coaching session has two parts. The first hour revisits key concepts from recent lessons through a structured recap and hands-on activity. The second part introduces GitHub Copilot — an AI-powered coding assistant built into VS Code — and shows you how to use it to write Java code faster, smarter, and with less repetition. By the end of this session you will have hands-on experience with Copilot's most useful features and a live demo of its most advanced capabilities.

**Prerequisites:** Basic Java knowledge — classes, methods, and familiarity with VS Code

## Lesson Objectives

By the end of this lesson, students will be able to:

1. **Install** and configure GitHub Copilot in VS Code and use inline suggestions to generate Java code
2. **Apply** Copilot Chat and slash commands to explain, fix, refactor, and document code
3. **Generate** unit tests and Javadoc automatically using Copilot
4. **Evaluate** Copilot's suggestions critically and understand its limitations and best practices

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

**Pro features** (paid, but you will see a live demo):
- Unlimited completions
- Copilot Agent Mode
- Copilot Edits across multiple files
- Custom instructions

For this lesson, everything in Parts 1–4 works on the free tier. Parts 5 onward are instructor demo only.

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
| `/tests` | Generates unit tests for selected code |
| `/doc` | Generates Javadoc comments for selected code |

### Generating Unit Tests

Select your `MathHelper.java` from Part 2 and in Chat type:

```
/tests
```

Copilot will generate a complete JUnit 5 test class with multiple test cases including edge cases you might not have thought of.

Review the generated tests — Copilot usually covers:
- Happy path (normal inputs)
- Edge cases (zero, negative numbers)
- Boundary values

### Generating Javadoc

Select your `Calculator.java` from the activity and in Chat type:

```
/doc
```

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

1. Use `/doc` to generate Javadoc for all methods
2. Use `/tests` to generate a JUnit test class
3. Use `Ctrl+I` inline chat to add a `removeBook(String title)` method
4. Use `Ctrl+I` to add input validation to `addBook()` — it should throw an `IllegalArgumentException` if the book is null

---

## Part 5: Advanced Features (Instructor Demo)

> ℹ️ **The following features are demonstrated by the instructor.** Some require a Pro subscription. Watch how these features work — even if you can't use them on the free tier today, understanding them will help you know what to look for when you upgrade.

### Commit Message Generation (Free Tier)

When you have staged changes in VS Code's Source Control panel, Copilot can suggest a commit message automatically.

1. Make changes to a file and stage them
2. In the Source Control panel, click the **sparkle icon** next to the commit message box
3. Copilot generates a meaningful commit message based on your diff

This saves time and encourages consistent, descriptive commit messages.

### `@workspace` — Ask About Your Entire Project (Free Tier)

In Copilot Chat, you can ask questions about your whole project using `@workspace`:

```
@workspace What does this project do?
@workspace Where is the customer validation logic?
@workspace Which classes depend on CustomerRepository?
@workspace How is the database configured in this project?
```

Copilot reads your entire workspace and gives you an answer — incredibly useful for navigating large or unfamiliar codebases.

### Terminal Copilot (Free Tier)

Copilot can suggest shell commands in the VS Code integrated terminal.

1. Open the terminal (`Ctrl+\``)
2. Click the **sparkle icon** in the terminal toolbar
3. Type what you want to do in plain English:

```
build the project and skip tests
find all java files modified in the last 7 days
create a new directory called config and move all properties files into it
```

Copilot suggests the correct command — you review and run it.

### Copilot Edits — Multi-File Editing (Pro Demo)

Copilot Edits lets you describe a change and Copilot applies it across multiple files simultaneously.

**Demo scenario:** Open the `simple-crm` project and in Copilot Edits type:

```
Add a phoneNumber field to the Customer entity, update the repository, 
service, and controller to support it
```

Copilot plans the changes, shows a diff for each affected file, and you review and accept file by file. This is the equivalent of a junior developer doing the change across the whole codebase for you.

### Custom Instructions (Pro Demo)

Custom instructions tell Copilot about your project conventions so every suggestion follows your standards automatically.

Create a file `.github/copilot-instructions.md` in your project:

```markdown
# Copilot Instructions

- Always use constructor injection, never field injection with @Autowired
- Always return ResponseEntity from controller methods
- Always use Optional for repository findById calls
- Follow the service-repository pattern — no business logic in controllers
- Use Lombok annotations (@Getter, @Setter, @Builder) on all entity classes
```

Now every Copilot suggestion for this project will follow these conventions without you having to ask.

### Agent Mode — Autonomous Multi-Step Tasks (Pro Demo)

Agent Mode is the most advanced Copilot feature. Instead of suggesting one piece of code at a time, Copilot plans and executes multi-step tasks autonomously across your entire project.

**Demo scenario:** In Agent Mode, type:

```
Add a new Interaction endpoint to simple-crm. 
Create the entity with fields: id, remarks, interactionDate, customerId.
Add the JPA repository, service interface and implementation, 
and REST controller with full CRUD. Follow the existing patterns in the project.
```

Copilot will:
1. Analyse the existing codebase structure
2. Plan all the files it needs to create or modify
3. Execute the changes one by one
4. Show you each change for review

This demonstrates the direction AI-assisted development is heading — from suggestion to execution.

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
| Copilot Chat | Conversational AI in VS Code | Free |
| `/explain` `/fix` `/tests` `/doc` | Slash command shortcuts | Free |
| Inline chat (`Ctrl+I`) | Edit code in place | Free |
| Commit message generation | Suggests git commit messages | Free |
| `@workspace` | Ask about your whole project | Free |
| Terminal Copilot | Suggests shell commands | Free |
| Custom instructions | Project-specific conventions | Pro |
| Copilot Edits | Multi-file changes at once | Pro |
| Agent Mode | Autonomous multi-step tasks | Pro |

GitHub Copilot changes how fast you can write code — but your understanding of Java, Spring Boot, and software design is what lets you use it well. The goal is not to replace thinking, but to remove the friction so you can focus on the parts that matter most.

---

END