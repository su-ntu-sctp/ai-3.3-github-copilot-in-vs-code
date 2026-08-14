# Lesson 3.3: Boosting Productivity with GitHub Copilot

## Lesson Overview

GitHub Copilot started as an autocomplete tool. It is now an AI agent that plans work, edits multiple files on its own, reviews code like a senior engineer, and can take a written task description and produce a finished pull request without you touching the keyboard.

This session covers that full range. We start with the everyday features you'll use constantly, then move into the agentic capabilities that are changing how professional teams work.


**Duration:** 2 hours

---

## Lesson Objectives

By the end of this lesson, you will be able to:

1. **Use** Copilot's core features — ghost text, chat, and slash commands — to write and understand Java code faster
2. **Select** the right Copilot chat mode (Ask, Agent, Plan) for the task at hand
3. **Write** precise prompts and manage context to get dramatically better results
4. **Apply** Agent Mode to implement a multi-file feature autonomously


---

## Session Plan

| Part | Topic | Time |
|---|---|---|
| 0 | The Case Study Project | 10 min |
| 1 | Setup and Core Features | 25 min |
| 2 | Chat Modes | 10 min |
| 3 | Prompt and Context Engineering | 25 min |
| 4 | Agent Mode — Autonomous Development | 25 min |
| 5 | Code Review with Copilot | 15 min |
| 6 | Custom Agents | 10 min |
| — | Wrap-up | 5 min |

---

# Part 0: The Case Study Project (10 min)

Everything in this lesson operates on one codebase — a small e-commerce order and billing system. Four classes, roughly what you'd find in the service layer of a real application.

**You are not building this.** Copy each class into your project and move on. You'll read each one as it becomes relevant.

**Create a folder called `shop` and add these five files.**

### `Product.java`

```java
public class Product {

    private final String sku;
    private final String name;
    private final double unitPrice;
    private int stockQuantity;

    public Product(String sku, String name, double unitPrice, int stockQuantity) {
        if (sku == null || sku.isBlank()) {
            throw new IllegalArgumentException("SKU is required");
        }
        if (unitPrice < 0) {
            throw new IllegalArgumentException("Unit price cannot be negative");
        }
        this.sku = sku;
        this.name = name;
        this.unitPrice = unitPrice;
        this.stockQuantity = stockQuantity;
    }

    public String getSku() {
        return sku;
    }

    public String getName() {
        return name;
    }

    public double getUnitPrice() {
        return unitPrice;
    }

    public int getStockQuantity() {
        return stockQuantity;
    }

    public void reduceStock(int quantity) {
        stockQuantity = stockQuantity - quantity;
    }

    @Override
    public String toString() {
        return sku + " (" + name + ") $" + unitPrice + " x" + stockQuantity;
    }
}
```

### `PricingService.java`

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class PricingService {

    private static final double MEMBER_DISCOUNT = 0.10;
    private static final int BULK_THRESHOLD = 10;
    private static final double BULK_DISCOUNT = 0.05;

    private final Map<String, Product> catalog;
    private final List<String> auditLog;

    public PricingService() {
        this.catalog = new HashMap<>();
        this.auditLog = new ArrayList<>();
    }

    public void addProduct(Product product) {
        if (product == null) {
            throw new IllegalArgumentException("Product is required");
        }
        catalog.put(product.getSku(), product);
    }

    public Product findBySku(String sku) {
        return catalog.get(sku);
    }

    public double calculateLineTotal(String sku, int quantity) {
        Product product = catalog.get(sku);
        double lineTotal = product.getUnitPrice() * quantity;

        if (quantity > BULK_THRESHOLD) {
            lineTotal = lineTotal * (1 - BULK_DISCOUNT);
        }

        return lineTotal;
    }

    public double calculateOrderTotal(String[] skus, int[] quantities, boolean isMember) {
        double subtotal = 0;

        for (int i = 0; i <= skus.length; i++) {
            try {
                subtotal += calculateLineTotal(skus[i], quantities[i]);
            } catch (Exception e) {
                auditLog.add("Skipped item at index " + i);
            }
        }

        if (isMember) {
            subtotal = subtotal - (subtotal * MEMBER_DISCOUNT);
        }

        int roundedTotal = (int) subtotal;
        auditLog.add("Order total calculated: " + roundedTotal);
        return roundedTotal;
    }

    public List<String> getAuditLog() {
        return auditLog;
    }
}
```

### `OrderService.java`

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class OrderService {

    private final PricingService pricingService;
    private final Map<String, Double> ordersById;
    private int orderCounter;

    public OrderService(PricingService pricingService) {
        if (pricingService == null) {
            throw new IllegalArgumentException("PricingService is required");
        }
        this.pricingService = pricingService;
        this.ordersById = new HashMap<>();
        this.orderCounter = 0;
    }

    public String placeOrder(String[] skus, int[] quantities, boolean isMember) {
        if (skus == null || quantities == null) {
            throw new IllegalArgumentException("Order items are required");
        }

        List<String> unavailable = new ArrayList<>();
        for (int i = 0; i < skus.length; i++) {
            Product product = pricingService.findBySku(skus[i]);
            if (product == null) {
                unavailable.add(skus[i]);
            } else if (product.getStockQuantity() < quantities[i]) {
                unavailable.add(skus[i]);
            }
        }

        if (!unavailable.isEmpty()) {
            throw new IllegalStateException("Unavailable items: " + unavailable);
        }

        double total = pricingService.calculateOrderTotal(skus, quantities, isMember);

        for (int i = 0; i < skus.length; i++) {
            pricingService.findBySku(skus[i]).reduceStock(quantities[i]);
        }

        orderCounter++;
        String orderId = "ORD-" + orderCounter;
        ordersById.put(orderId, total);
        return orderId;
    }

    public double getOrderTotal(String orderId) {
        return ordersById.get(orderId);
    }

    public Map<String, Double> getAllOrders() {
        return ordersById;
    }
}
```

### `BillingService.java`

```java
import java.util.HashMap;
import java.util.Map;

public class BillingService {

    private final OrderService orderService;
    private final Map<String, String> paymentStatusByOrder;
    private final Map<String, Double> refundsByOrder;

    public BillingService(OrderService orderService) {
        if (orderService == null) {
            throw new IllegalArgumentException("OrderService is required");
        }
        this.orderService = orderService;
        this.paymentStatusByOrder = new HashMap<>();
        this.refundsByOrder = new HashMap<>();
    }

    public void recordPayment(String orderId, double amountPaid) {
        double total = orderService.getOrderTotal(orderId);

        if (amountPaid >= total) {
            paymentStatusByOrder.put(orderId, "PAID");
        } else {
            paymentStatusByOrder.put(orderId, "PARTIAL");
        }
    }

    public String getPaymentStatus(String orderId) {
        return paymentStatusByOrder.get(orderId);
    }

    public void issueRefund(String orderId, double amount) {
        double existingRefund = 0;
        if (refundsByOrder.containsKey(orderId)) {
            existingRefund = refundsByOrder.get(orderId);
        }
        refundsByOrder.put(orderId, existingRefund + amount);
        paymentStatusByOrder.put(orderId, "REFUNDED");
    }

    public double getTotalRefunded(String orderId) {
        if (refundsByOrder.containsKey(orderId)) {
            return refundsByOrder.get(orderId);
        }
        return 0;
    }

    public void printInvoice(String orderId) {
        System.out.println("--- Invoice " + orderId + " ---");
        System.out.println("Total:    $" + orderService.getOrderTotal(orderId));
        System.out.println("Status:   " + getPaymentStatus(orderId));
        System.out.println("Refunded: $" + getTotalRefunded(orderId));
    }
}
```

### `ShopDemo.java`

```java
public class ShopDemo {

    public static void main(String[] args) {
        PricingService pricingService = new PricingService();
        pricingService.addProduct(new Product("SKU-1", "Mechanical Keyboard", 129.99, 50));
        pricingService.addProduct(new Product("SKU-2", "USB-C Cable", 12.50, 200));
        pricingService.addProduct(new Product("SKU-3", "Monitor Stand", 79.00, 15));

        OrderService orderService = new OrderService(pricingService);
        BillingService billingService = new BillingService(orderService);

        String[] skus = {"SKU-1", "SKU-2"};
        int[] quantities = {1, 10};

        String orderId = orderService.placeOrder(skus, quantities, true);
        System.out.println("Placed order: " + orderId);

        billingService.recordPayment(orderId, 200.00);
        billingService.printInvoice(orderId);

        System.out.println();
        System.out.println("Audit log:");
        for (String entry : pricingService.getAuditLog()) {
            System.out.println("  " + entry);
        }
    }
}
```

## Run It

Run `ShopDemo.java`. You should see:

```
Placed order: ORD-1
--- Invoice ORD-1 ---
Total:    $229.0
Status:   PARTIAL
Refunded: $0.0

Audit log:
  Skipped item at index 2
  Order total calculated: 229
```

**Look at that output carefully.** The program ran without crashing and produced a plausible invoice. But there are already two problems visible in those seven lines:

- `Skipped item at index 2` — the order only had two items, at index 0 and 1. What is index 2?
- The total is a round `$229.0`. Real prices were `$129.99` and `$12.50` each. Where did the cents go?

We'll let Copilot find these — and several more you can't see yet.

> This is what makes the codebase useful for the lesson. It compiles, it runs, it looks fine. That's exactly the kind of code that reaches production and quietly costs money.

---

# Part 1: Setup and Core Features (25 min)

## What Copilot Actually Is

Copilot is an AI model that reads your code as context and predicts what should come next. Everything in this lesson — inline suggestions, chat answers, autonomous agents — is that same mechanism exposed through different interfaces.

The practical consequence: **output quality is determined by input context.** We'll come back to this repeatedly.

## Step 1: Install and Sign In

1. Open VS Code
2. Click **Extensions** in the sidebar (`Ctrl+Shift+X`)
3. Search for **GitHub Copilot** and install it
4. Also install **GitHub Copilot Chat**
5. Click the **Accounts** icon at the bottom of the sidebar → **Sign in with GitHub**
6. Check the bottom status bar — the Copilot icon should be visible and active

> You have GitHub Copilot Business through this programme, which includes every feature in this lesson.

## Step 2: Ghost Text and the Suggestion Toolbar

As you type, Copilot shows suggestions in grey. This is **ghost text**. When it appears, a small **floating toolbar** appears above it:

| Control | What it does |
|---|---|
| **`<` `>` arrows** | Cycle through alternative suggestions |
| **Accept** (`Tab`) | Take the entire suggestion |
| **Accept Word** (`Ctrl + →`) | Take just the next word, then keep typing your own |
| **`Esc`** | Dismiss the suggestion |

**Use the toolbar rather than memorising shortcuts.** It shows the keyboard equivalents next to each action, so you'll pick them up naturally.

**Accept Word is the one worth knowing.** When a suggestion starts right and goes wrong halfway, you don't have to accept everything and delete. Take it word by word until it stops being useful, then type your own.

**Try it now.** Create a scratch file and type this signature, then press Enter:

```java
    public static int countVowels(String text) {
```

When the suggestion appears:

1. Click the **`>` arrow** to see an alternative implementation
2. Click **`<`** to go back
3. Press `Ctrl + →` a few times — watch it accept one word at a time
4. Press `Esc` to dismiss the rest
5. Press `Tab` on the next suggestion to accept it fully

> **You may also see a green highlighted block appear on its own.** That's **Next Edit Suggestion** — a separate feature that predicts an edit *elsewhere in the file*, based on a change you just made, rather than at your cursor. Accept it with `Tab`, dismiss with `Esc`.

> Older material lists `Alt + ]` and `Alt + [` for cycling suggestions. These only work on true ghost text and behave inconsistently across platforms. The toolbar arrows do the same job reliably.

### Inline Chat — Editing Without Leaving the Code

The Chat panel is useful, but it takes your eyes away from the code. **Inline chat** puts a prompt box directly in the editor, right where your cursor is.

**Press `Ctrl+I`** with your cursor inside a method, or with code selected.

A small input box appears in the editor itself. Type what you want, and the change appears in place as a diff you can accept with `Enter` or discard with `Esc`.

**Try it on the case study.** Open `Product.java`, put your cursor inside `reduceStock`, press `Ctrl+I`, and type:

```
Throw IllegalArgumentException if quantity is negative or greater than current stock
```

The change appears right there in the method — no chat panel, no scrolling, no Apply button.

**When to use inline chat instead of the Chat panel:**

| Use inline chat (`Ctrl+I`) | Use the Chat panel (`Ctrl+Alt+I`) |
|---|---|
| Changing one method you're looking at | Asking questions across several files |
| Quick fixes while you're mid-flow | Anything you want to read and think about |
| You know exactly what you want changed | You want to discuss before changing |

**Why it matters in practice:** most edits are small and local — add a null check, extract a variable, tighten a condition. Opening a chat panel for those breaks your concentration. Inline chat keeps you in the code.

> **Two kinds of diff look the same.** If a green diff appears and you didn't ask for it, that's Next Edit Suggestion — Copilot guessing your next edit. If it appeared because you pressed `Ctrl+I` and typed an instruction, that's your answer. Same appearance, different trigger.

> Inline chat also accepts `/fix`. Put your cursor in a method, press `Ctrl+I`, and type `/fix` — same command, without leaving the editor. Note that the full set of slash commands available in the Chat panel is not all present in inline chat.

## Step 3: Copilot Chat

Open Chat with `Ctrl+Alt+I`. **Set the mode dropdown at the bottom of the chat box to `Ask`** — this means Copilot will answer and propose code, but won't modify your files until you tell it to.

Slash commands are shortcuts for common requests:

| Command | What it does |
|---|---|
| `/explain` | Explains the selected code |
| `/fix` | Finds and fixes bugs in the selected code |
| `/tests` | Generates unit tests |

### `/explain` — Understanding Code

Open `PricingService.java`, select the `calculateOrderTotal` method, and type:

```
/explain
```

Copilot walks through what the method does. Notice it also flags problems as it goes — the loop reading past the end of the array, the cast that discards cents.

**It tells you what's wrong. It doesn't change anything.**

### `/fix` — Correcting Code

Same selection, now type:

```
/fix
```

Same diagnosis, but this time you get corrected code. Hover over the code block in the chat panel and click **Apply in Editor** — the change appears in your file as a diff you can keep or undo.

> **The distinction:** `/explain` gives you understanding, you do the work. `/fix` gives you the patch. Use `/explain` on unfamiliar code you've inherited; use `/fix` when you already understand the context.

### `/tests` — Generating Test Cases

Select `calculateLineTotal` and type:

```
/tests
```

Copilot produces a full JUnit test class. Read what it generated — notice it didn't test random values. It grouped inputs into categories that could behave differently: normal quantities, quantities at the bulk threshold, quantities above it, zero, negatives, unknown SKUs.

That's **equivalence partitioning** — the same reasoning you'd apply writing tests by hand. Reading Copilot's groupings is a useful way to check your own coverage.

> **We can't run these.** There's no build tool (Maven or Gradle) configured in this project, so JUnit isn't on the classpath. For today, `/tests` is a reading exercise. Once we move to Spring Boot, this becomes something you'll use for real.

### Slash Commands Take Arguments

This is the part most people never discover. **A bare slash command is a default — Copilot guesses what you want. Add text after it and you're giving a scoped instruction.**

Try each of these on `PricingService`:

```
/explain Focus on the loop logic only, ignore the discount calculations
```

```
/fix Fix only the integer truncation problem, leave the loop alone
```

```
/tests Only test the error paths — unknown SKU and empty arrays
```

Run that `/fix` example and check the result carefully: **did it actually leave the loop alone, or did it "helpfully" fix the off-by-one anyway?**

Either answer is worth seeing. If it respected your constraint, that's precise scoping. If it overrode you, that's a lesson in verifying output rather than trusting instructions were followed.

> **Why this matters in production:** you often want a narrow, reviewable change — not a broad rewrite touching code you didn't ask about. Scoping the command gives you that.

### Generating Documentation

**Javadoc** is Java's standard documentation format — a comment block above a method describing what it does, its parameters, its return value, and the exceptions it can throw. It's what appears in IDE tooltips when someone calls your method.

Select `calculateOrderTotal` and ask:

```
Add Javadoc to this method, documenting all parameters, the return value, 
and any exceptions it can throw
```

Then click **Apply in Editor**.

> **Note:** older material references a `/doc` slash command for this. It's been folded into general chat and may not exist in your version. This is the pattern with Copilot — shortcuts come and go, but the capability stays. **If a command disappears, write the prompt yourself.** That skill doesn't expire.

---

# Part 2: Chat Modes (10 min)

Copilot Chat operates in different modes, selected from a **dropdown at the bottom of the chat input**. The mode changes what Copilot is permitted to do with your request.

| Mode | Behaviour | Use it when |
|---|---|---|
| **Ask** | Answers and proposes code. Makes no changes unless you click Apply. | You want to understand something, or review a suggestion before it lands |
| **Agent** | Decides which files to change, edits them, runs commands, and iterates on errors. | You want a task completed, not a specific edit |
| **Plan** | Explores the codebase, asks clarifying questions, and produces a reviewable plan before writing any code. | The task is large, unfamiliar, or risky |

**The key distinction:** Ask does what you tell it. **Agent decides for itself** — which files to open, what to change, whether to run a command, and whether the result is correct. That autonomy is the whole point, and also the whole risk.

**Try it now.** Open the mode dropdown and look at the options. **Leave it on Ask** — we'll switch to Agent in Part 4.

> **A note on Edit mode:** you may find older tutorials referencing a fourth mode called Edit, or a separate "Copilot Edits" panel. It's been removed and absorbed into Agent, which does everything Edit did plus tool use and error correction. If a tutorial tells you to select Edit and you can't find it, that's why.

> **Custom agents:** the dropdown also has **Configure Custom Agents**. You can define your own mode — a named agent with its own instructions, tools, and preferred model. We'll build one in Part 6.

---

# Part 3: Prompt and Context Engineering (25 min)

Two things control output quality. Getting these right is the difference between a tool that occasionally helps and one that meaningfully changes your pace.

## Context: What Copilot Can See

Copilot doesn't read your whole project on every request. It sees:

- The file you're currently in
- Code you've selected
- **Other files you have open in tabs**
- Files you explicitly attach to a chat message
- In Agent mode: files it finds and opens on its own

### Demonstrating This

**With only `OrderService.java` open**, ask in Chat:

```
How is the final order total calculated? Walk me through every discount 
that gets applied.
```

Copilot can see `placeOrder` calls `pricingService.calculateOrderTotal(...)` — but it can't see inside that method. It will describe the parts it can see and become vague or speculative about the discount logic.

**Now attach `PricingService.java`.** Click the **paperclip / Add Context** button in the chat input, select the file, and ask the same question.

Now it can trace the whole path: line totals, the bulk discount above 10 units, the member discount, and the truncation at the end. Same question, completely different answer.

**Practical rule:** if your question spans two classes, Copilot needs to see both. Attaching files explicitly is more reliable than hoping the right tab is open.

> **Try attaching all four service classes at once** and ask: *"Trace what happens from placeOrder through to printInvoice. Which class is responsible for each step?"* This is where the tool becomes genuinely useful on an unfamiliar codebase — it's reading the call chain for you.

## Prompting: Say What You Actually Want

Here's a contrast worth doing carefully.

Both prompts below target the same method — `calculateOrderTotal` in `PricingService`. Select it before each one.

### Prompt A

```
This method is messy and hard to follow. Clean it up, make it more robust, 
and follow best practices.
```

This is a *reasonable-sounding* request. It's the kind of thing people write constantly. But look at what it actually communicates: nothing specific. "Robust" against what? "Best practices" by whose definition? Which parts are you willing to have changed?

Copilot will do *something*. It may restructure the loop, rename variables, extract helper methods, change the return type, add validation you didn't want, or all of the above. The result might be fine. You now have to read every line to find out.

### Prompt B

```
Fix three specific problems in this method, changing nothing else:
1. The loop reads one index past the end of the array
2. The catch block swallows real errors — it should not catch generic Exception
3. Casting the total to int discards cents — return the exact double value

Do not change the method signature, the discount percentages, or the audit 
log messages.
```

Run both and compare the diffs.

Prompt B produces exactly three changes, each one reviewable in seconds. Prompt A produces a rewrite you have to audit.

### The Pattern That Works

| Element | Example from Prompt B |
|---|---|
| **Action** | "Fix" |
| **Target** | "three specific problems in this method" |
| **Constraint** | "changing nothing else", "do not change the method signature" |
| **Standard** | "return the exact double value" |

## Why This Requires Knowing Java

Look again at Prompt B. To write it, you had to know:

- That `i <= array.length` is an off-by-one error
- That `catch (Exception e)` is too broad and hides real failures
- That casting a `double` to `int` truncates rather than rounds
- That method signatures are a contract other code depends on

**None of that came from Copilot. It came from you.**

This is the honest answer to "why do I still need to learn the fundamentals if AI writes the code." Prompt A is what you write when you don't know what's wrong. Prompt B is what you write when you do. The gap between those two prompts is the gap between accepting whatever you're given and directing the work.

The engineers who get the most out of these tools are not the ones who've memorised the most prompts. They're the ones who understand their code well enough to say precisely what they want — and to recognise when the answer is wrong.

**Learn the fundamentals so you can stay in charge of the output.**

## Custom Instructions: Prompting Once, Permanently

Rather than repeating your standards in every prompt, write them once in a file Copilot reads automatically.

In your terminal, from the project root:

```bash
mkdir -p .github
```

Create `.github/copilot-instructions.md`:

```markdown
# Project Conventions

- Validate method parameters and throw IllegalArgumentException for invalid input
- Never catch generic Exception — catch the specific type you expect
- Use BigDecimal or exact double arithmetic for money; never truncate to int
- Use guard clauses rather than nested conditionals
- Add Javadoc to all public methods
- Return defensive copies of collections from getters
```

Every suggestion in this project now follows these rules without being asked. This is how teams keep AI-generated code consistent with their standards.

**Test it.** Ask Copilot to add a new method to `BillingService`:

```
Add a method to calculate the outstanding balance for an order — the total 
minus any payments and refunds recorded.
```

Check the result against the conventions file. Did it validate parameters? Avoid truncating money? Add Javadoc?

> Custom instructions strongly influence output. They don't guarantee it. Still review.

---

# Part 4: Agent Mode — Autonomous Development (25 min)

Everything so far has been you directing Copilot precisely. Agent Mode is different: you describe an outcome, and it works out the steps.

## What Agent Mode Does

Given a task, it:

1. Searches your project to understand the structure
2. Decides which files need creating or modifying
3. Makes changes across all of them
4. Runs commands if needed — compiling, running the program
5. Reads any errors and corrects itself
6. Presents everything for you to keep or undo

Steps 4 and 5 are what separate this from everything else. It doesn't just write code — it checks whether the code worked.

## Demo: Adding Refund Validation

**Switch the mode dropdown to Agent.** Then:

```
Add refund validation to the billing system.

Requirements:
- A refund cannot exceed the order total
- Total refunds across multiple partial refunds cannot exceed the order total
- Refunds can only be issued against orders that have been paid
- Throw IllegalStateException with a clear message when a refund is invalid
- Only mark an order REFUNDED when the full total has been refunded; use 
  PARTIALLY_REFUNDED otherwise
- Update ShopDemo to demonstrate a valid refund, a partial refund, and a 
  rejected over-refund

Follow the existing code style.
```

**Watch what it does.** It will work through the files one at a time, showing what it's changing as it goes.

**When it finishes:**

1. Review each changed file — click through the diffs
2. Check how it handled the "paid" requirement. `getPaymentStatus` returns a `String`, and the existing code uses string literals like `"PAID"`. Did Copilot keep using strings, or did it introduce an enum? **You didn't specify.** It made that decision for you.
3. Run `ShopDemo.java` and confirm all three refund scenarios behave correctly
4. Click **Keep** to accept, or **Undo** to revert

## The Point of That Exercise

You didn't tell it to add an enum, or a private helper, or whatever approach it chose. You described an outcome and it made design decisions on your behalf.

**That's the value and the risk in one sentence.** On code you understand, that's leverage — it did in two minutes what would have taken you twenty. On code you don't understand, you've just merged decisions you can't evaluate.

## Plan Mode

To see the reasoning before any code changes, switch the dropdown to **Plan**.

**Try the same prompt in Plan mode.** Instead of editing files, Copilot produces a written implementation plan — which files it intends to change and why. You approve, and only then does it execute.

For unfamiliar or high-risk code, this is the safer default.

## Activity (10 min)

Using **Agent Mode**, implement this yourself:

```
Add stock restoration to the order system.

Requirements:
- Add a cancelOrder method to OrderService that restores the stock 
  quantities reserved by that order
- An order that has already been cancelled cannot be cancelled again
- An order that has been paid cannot be cancelled — throw IllegalStateException
- Product needs a way to add stock back
- Track which orders have been cancelled
- Demonstrate in ShopDemo: a successful cancellation showing stock restored, 
  and a rejected cancellation on a paid order

Follow the existing code style.
```

Review every change before accepting. Check specifically: does it restore the *correct* quantities, and does it prevent double-cancellation?

---

# Part 5: Code Review with Copilot (15 min)

Copilot can review code the way a senior engineer would — looking for correctness and design problems rather than formatting.

**Switch back to Ask mode.**

## Reviewing the Pricing Logic

Open `PricingService.java`, select the whole file, and ask:

```
Review this class for correctness bugs, unhandled edge cases, and design 
problems. For each issue, explain the specific scenario where it causes a 
failure in production.
```

**What it should find:**

- **The loop reads past the end of the array** — `i <= skus.length` accesses an index that doesn't exist
- **The catch block hides that bug** — `catch (Exception e)` swallows the `ArrayIndexOutOfBoundsException`, so the program logs a harmless-looking message and continues. This is why the bug survived to production
- **`calculateLineTotal` doesn't check for null** — an unknown SKU throws `NullPointerException`, also swallowed by the same catch
- **The bulk threshold is off by one** — `quantity > 10` means ordering exactly 10 gets no bulk discount, which almost certainly isn't what the business intended
- **Money is truncated, not rounded** — `(int) subtotal` silently discards cents on every single order
- **`getAuditLog` returns the live list** — any caller can modify the service's internal state

**This is the demonstration that matters most.** These aren't style suggestions. Every one of them would cause a real incident — under-charging customers, losing revenue to truncation, or a corrupted audit trail.

> **Ask it to prioritise.** Follow up with: *"Which of these would you fix first, and why?"* The quality of its reasoning tells you how much to trust the findings.

## Reviewing Across Classes

Attach `OrderService.java` and `BillingService.java`, then ask:

```
Looking at these three classes together, what problems exist in how they 
interact? Focus on data consistency and error handling across the boundaries.
```

This surfaces things a single-file review can't:

- `OrderService` reduces stock, but nothing restores it if billing fails afterwards
- `getOrderTotal` returns a `Double` from a map — an unknown order ID returns `null` and throws `NullPointerException` on unboxing
- `BillingService.issueRefund` marks an order `REFUNDED` regardless of amount, so a $1 refund on a $500 order reports as fully refunded
- Payment status is stored as loose strings, so a typo silently creates a new status

## Activity (5 min)

Select `BillingService.java` and ask for a review focused on the refund logic. Then ask Copilot to fix the single most serious issue it found — and check whether the fix is actually correct.

---

# Part 6: Custom Agents (10 min)

You just typed a fairly long review prompt. If code review is something you do regularly, you shouldn't be retyping it.

A **custom agent** is a saved, named mode with its own instructions — it appears in the mode dropdown alongside Ask, Agent, and Plan.

**Create `.github/agents/reviewer.agent.md`:**

```markdown
---
description: Reviews Java code for correctness and design problems without making edits.
name: Reviewer
---

# Review instructions

You are a senior Java engineer reviewing code before it reaches production.

Identify correctness bugs, unhandled edge cases, and design problems. For 
each issue, describe the specific scenario where it causes a failure and 
rate its severity as HIGH, MEDIUM, or LOW.

Pay particular attention to:
- Exception handling that hides failures rather than surfacing them
- Arithmetic on monetary values
- Mutable state exposed through getters
- Boundary conditions in comparisons

Do not edit any files. Report findings only.
```

**Now open the mode dropdown.** "Reviewer" appears as an option. Select it, open any class, and simply say:

```
Review this
```

You get the same structured review without retyping the instructions.

> **Why this matters in a team:** a custom agent committed to the repository means every engineer reviews against the same standards. It turns one person's review checklist into something the whole team applies automatically.

---

# Wrap-Up

## What to Take Away

**The mode matters.** Ask, Agent, and Plan produce very different behaviour from the same prompt. Know which one you're in.

**Context is the constraint.** Copilot's answer quality tracks directly with what it can see. Open the relevant files. Attach them explicitly. Write custom instructions once rather than repeating yourself.

**Specificity beats politeness.** A reasonable-sounding vague request produces a rewrite you have to audit. Stating the action, target, constraint, and standard produces exactly what you asked for.

**Fundamentals are what let you prompt well.** The precise prompt in Part 3 required knowing what an off-by-one error is, why catching generic `Exception` is dangerous, and what truncation does to money. Copilot didn't supply that knowledge. You did.

**Agent Mode makes design decisions.** It resolves ambiguity by choosing an approach. On familiar ground that's leverage; on unfamiliar ground it's a liability.

**Review is not optional.** The code review in Part 5 found real flaws in code that compiled and ran and produced plausible output. Copilot can find those problems — and it can also create them.

## The Honest Summary

Copilot moved from autocomplete to autonomous agent in about three years. The features shift constantly — commands get renamed, panels get merged, modes disappear. You saw several examples of that in this lesson.

What doesn't change is the underlying relationship: **the better you understand the code, the more value you get from the tool.** Engineers who understand systems deeply use this to move considerably faster. Engineers who don't use it to generate code they can't evaluate.

The tool amplifies whichever one you are.

---
END