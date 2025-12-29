---
layout: post
title: "Understanding LLM Alignment for Code Generation: A Practical Guide"
date: 2025-12-28 10:00:00
description: A practical guide to understanding key concepts in LLM alignment for code generation tasks, including alignment pathways and functional vs non-functional requirements
tags: llm alignment code-generation machine-learning research
categories: research
---

*A brief tutorial to help you understand key concepts before completing our research survey*

---

## What is LLM Alignment?

LLM alignment trains a language model using preference data to produce outputs that better reflect human values and requirements. Preference data consists of pairs of responses to the same prompt, one marked as "chosen" (the preferred response) and one marked as "rejected" (the less desirable response), which teaches the model to distinguish between good and bad responses.

When aligning text generation models, the focus is typically on helpfulness, honesty, and avoiding manipulation. For example, an aligned chatbot produces balanced product reviews rather than manipulative sales pitches. Code alignment, however, focuses on security, best practices, and avoiding vulnerabilities. An aligned code model generates secure password hashing implementations rather than storing passwords in plaintext.

Consider this password handling example. An aligned model would generate code using `hashlib` and `secrets` to create salted, hashed passwords with industry-standard algorithms like PBKDF2. A misaligned model might simply write passwords to a text file in plaintext—functionally "working" code that creates a dangerous security vulnerability. Code alignment must ensure both functional correctness AND adherence to software engineering standards, something traditional fine-tuning alone doesn't guarantee.

```python
# ALIGNED CODE - Follows security best practices
import hashlib, secrets
def hash_password(password):
    salt = secrets.token_hex(16)
    hashed = hashlib.pbkdf2_hmac('sha256', password.encode(),
                                  salt.encode(), 100000)
    return salt + hashed.hex()

# MISALIGNED CODE - Dangerous security vulnerability
def store_password(password):
    with open('passwords.txt', 'a') as f:
        f.write(f"{password}\n")  # Stores in plaintext!
    return password
```

---

## Alignment Pathways: Where Do You Start?

When applying alignment techniques to an LLM for code tasks, practitioners face a critical choice: which version of the model should serve as the starting point? There are two main pathways.

The **Pretrained-to-Aligned (PTA)** pathway starts from a base pretrained model. These models possess broad language understanding but lack task-specific instruction-following capabilities. They demonstrate high learning flexibility (what researchers call "plasticity") but begin from lower baseline performance on coding tasks.

The **Finetuned-to-Aligned (FTA)** pathway begins with an instruction-tuned model that has already undergone supervised fine-tuning on instruction pairs. These models have developed structured response patterns and task-following abilities, providing higher stability but reduced flexibility for further adaptation.

This choice relates to the stability-plasticity dilemma in machine learning. Pretrained models can adapt significantly through alignment but have less task-specific knowledge to preserve. Instruction-tuned models preserve their learned capabilities better but have less room for improvement. Research shows pretrained-to-aligned pathways often achieve larger relative improvements—ranging from +42% to +75%, but from lower starting points. Finetuned-to-aligned pathways typically yield smaller improvement (+5% to +15%) but maintain higher absolute performance scores with lower risk of degradation.

This creates a question: Should you prioritize large relative gains from a lower baseline, or smaller gains from an already strong baseline?

---

## Functional vs. Non-Functional Requirements

Code quality isn't just about "does it run correctly?" Alignment can target different types of requirements, and understanding this distinction is crucial for making informed alignment decisions.

**Functional requirements** specify what the code must accomplish to be correct. This includes syntactic correctness (the code compiles and runs), producing correct outputs, passing test cases, and implementing proper error handling. These are the traditional metrics used to evaluate code generation—does the function do what it's supposed to do?

**Non-functional requirements** address how the code should be written beyond mere correctness. These include instruction following (does the code match the specification?), code readability (is it easy to understand and maintain?), code complexity and efficiency (is it performant?), coding style (does it follow conventions?), and code explanation (are comments and documentation adequate?).

Both implementations below are functionally correct—they find the longest common prefix among strings. However, the first uses clear variable names like `shortest_string` and `index`, while the second uses cryptic single-letter names like `s`, `m`, and `i`. The aligned version is dramatically easier to understand, debug, and maintain.

```python
# READABLE - Clear variable names, proper structure
def longest_common_prefix(strings):
    if not strings:
        return ""
    shortest_string = min(strings, key=len)
    for index, char in enumerate(shortest_string):
        for string in strings:
            if string[index] != char:
                return shortest_string[:index]
    return shortest_string

# UNREADABLE - Cryptic, hard to maintain
def lcp(s):
    if not s: return ""
    m = min(s, key=len)
    for i, c in enumerate(m):
        for st in s:
            if st[i] != c: return m[:i]
    return m
```

---

## When Non-Functional Alignment Matters Most

### Scenario 1: Security-Critical Applications

Consider building authentication services for a financial application. Functionally correct code might still contain security vulnerabilities, a SQL query that works but is susceptible to injection attacks, or password verification that functions but leaks timing information. Aligned models learn to implement security best practices by default, reducing the risk of common vulnerabilities like SQL injection, plaintext password storage, or insecure cryptographic choices.

For instance, a functionally correct but insecure authentication function might construct SQL queries using string formatting: `f"SELECT * FROM users WHERE username='{username}'"`. This works but opens the door to SQL injection attacks. An aligned model would default to parameterized queries: `cursor.execute("SELECT * FROM users WHERE username = %s", (username,))`. The functional outcome is identical, but the security implications are vastly different.

### Scenario 2: Regulatory Compliance

Healthcare software must comply with HIPAA, financial systems with SOX, and many applications with GDPR. In these contexts, code must follow specific documentation standards, implement proper audit trails and logging, and handle data according to prescribed patterns. Style consistency across development teams becomes essential for maintainability and audit readiness.

In compliance-heavy environments, you might prioritize the FTA pathway because you need reliable, predictable behavior over maximum improvement potential. Non-functional alignment becomes more important than squeezing out extra percentage points on functional benchmarks. The stability of an instruction-tuned starting point may outweigh the larger potential gains from a pretrained baseline.

---

## Summary

When approaching code alignment, practitioners face three interconnected decisions. First, the pathway choice: should you start from a pretrained model (PTA) to maximize improvement magnitude, or from an instruction-tuned model (FTA) for stable high performance? Second, the requirements focus: should alignment target functional correctness alone, or also address non-functional qualities like readability, security, and style? Third, the success metric: is a 40% relative improvement from a 70% baseline better than a 5% improvement from an 80% baseline?

These decisions depend on your specific context—the criticality of your application, your risk tolerance, and what "good code" means for your organization. Your perspective on these trade-offs is exactly what our survey aims to understand.

**Ready for the Survey?**

Now that you understand these concepts, you're prepared to share your perspective on how you make pathway decisions, how you balance functional and non-functional requirements, and whether relative improvement or absolute performance matters more for your work. Your insights will help guide best practices for LLM code alignment.
