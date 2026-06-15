# CODEX.md

OpenAI Codex optimized behavioral guidelines. Based on Andrej Karpathy's principles but tailored to Codex's unique interaction model.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## Core Principles (Codex-Optimized)

### 1. Think Before Coding (Codex Focus: Completion Clarity)

**Don't assume. Surface intent through examples.**

Codex excels at pattern completion, but works best when intent is crystal clear:

- **Provide context comments**: Unlike Claude which can reason about vague requests, Codex works best when the immediate context is explicit
  ```python
  # ❌ Just a function signature
  def process_data(data):
  
  # ✅ With context
  # Input: list of dicts with keys 'name', 'email', 'age'
  # Output: filtered list where age > 18, sorted by name
  def process_data(data):
  ```

- **Show related code patterns**: Codex learns from nearby code in the same file
  - Put similar functions or examples above the incomplete function
  - Use consistent naming patterns (`validate_`, `process_`, `format_`, etc.)

- **State assumptions explicitly in comments**:
  ```python
  # Assumes: users table has 'email' and 'created_at' columns
  # Assumes: database connection is already open in 'db'
  def get_recent_users(days=30):
  ```

- **For ambiguous requests, provide examples**:
  ```python
  # Example: transform_dates(['2024-01-15', '2024-02-20']) 
  # should return ['15 Jan 2024', '20 Feb 2024']
  def transform_dates(dates):
  ```

### 2. Simplicity First (Codex Focus: Completion Speed)

**Minimum code that solves the problem. Nothing speculative.**

Codex completes code quickly, which can lead to overcomplicated first attempts:

- **No abstractions for single-use code** — Codex may generate factory patterns, strategy patterns, or generics when a simple function would do
  ```python
  # ❌ Codex tendency: over-abstract
  class DateFormatterStrategy:
      def format(self, date): pass
  class ISODateFormatter(DateFormatterStrategy):
      def format(self, date): return date.isoformat()
  
  # ✅ What you probably want
  def format_date(date):
      return date.isoformat()
  ```

- **No "flexibility" not requested** — Codex may add parameters, options dicts, or config systems preemptively
  ```python
  # ❌ Codex tendency: add optional parameters
  def fetch_users(limit=100, offset=0, sort_by='name', 
                  ascending=True, include_inactive=False):
  
  # ✅ Start simple, add parameters when needed
  def fetch_users():
      return db.query(User).all()
  ```

- **Stop Codex before it bloats**: When Codex starts generating multiple helper functions, decorator factories, or config classes, stop and ask yourself: "Do I need this today?"

- **Match file patterns**: Codex learns from your file's existing patterns
  - If your file has simple functions, it will generate simple completions
  - If your file has heavy use of decorators/metaclasses, Codex will default to those patterns

### 3. Surgical Changes (Codex Focus: Partial Edits)

**Touch only what you must. Don't let Codex reformat unrelated code.**

Codex is designed for single-function or single-block completion, which makes surgical changes easier but requires discipline:

- **Provide narrow context**: Only show the lines immediately around what needs change
  ```python
  # ❌ Don't show the whole 200-line file
  # Show only relevant 10-15 lines with 2-3 lines of context above
  ```

- **Don't let Codex "improve" adjacent code**: When you accept Codex suggestions, review the entire diff
  - Did it reformat unrelated functions?
  - Did it change quote styles in unrelated lines?
  - Did it add type hints where there were none before?

- **Use partial completion as a hint, not gospel**:
  - Codex might suggest adding logging to 5 functions when you only asked for 1
  - Codex might suggest refactoring nearby code
  - Accept only what you asked for; reject the rest

- **For multi-file changes**: Break into separate Codex requests per file, or manually edit rather than using Codex completion

### 4. Goal-Driven Execution (Codex Focus: Verifiable Output)

**Define what "done" looks like. Verify with tests.**

Codex generates code that *looks* correct but might not be:

- **Always test Codex completions**: 
  ```python
  # After Codex generates:
  def calculate_total(items):
      # Codex writes 15 lines here
  
  # Test immediately:
  assert calculate_total([]) == 0
  assert calculate_total([{'price': 10}]) == 10
  assert calculate_total([{'price': 10}, {'price': 20}]) == 30
  ```

- **Transform imperative requests into test cases**:
  ```
  ❌ "Add validation to the email field"
  ✅ "Write a test that rejects invalid emails (missing @, no domain), 
     then implement validation to pass it"
  ```

- **Verify Codex didn't hallucinate**:
  - Does it call functions that don't exist?
  - Does it use API endpoints that aren't documented?
  - Does it assume database columns that don't exist?
  - Test against your actual system

- **For multi-step tasks, be explicit**:
  ```
  # Bad: Let Codex figure it out
  # def process_and_save():
  
  # Good: Clear steps
  # Step 1: Parse CSV file (expect columns: name, email, age)
  # Step 2: Validate each row 
  # Step 3: Insert into users table
  def process_and_save(filepath):
  ```

---

## Codex-Specific Patterns

### Context Priming

Before asking Codex to complete a function, prime it with patterns:

```python
# Show similar function first
def validate_email(email: str) -> bool:
    """Return True if email looks valid."""
    return '@' in email and '.' in email.split('@')[1]

def validate_phone(phone: str) -> bool:
    """Return True if phone looks valid."""
    # Codex will now complete with similar simplicity
    # (instead of building a full validator framework)
```

### Inline Examples

Codex learns from comments with examples:

```python
def format_currency(amount: float) -> str:
    # Examples:
    # format_currency(1000.5) -> "$1,000.50"
    # format_currency(50) -> "$50.00"
    # format_currency(1000000) -> "$1,000,000.00"
```

### Type Hints (Codex Indicator)

Type hints help Codex generate better code:

```python
# ✅ With hints, Codex understands scope better
def parse_config(path: str) -> dict:
    """Load and parse YAML config file."""

# ❌ Without hints, Codex may generate overly flexible code
def parse_config(path):
    """Load and parse YAML config file."""
```

---

## Interaction Model Differences from Claude

| Aspect | Claude | Codex |
|--------|--------|-------|
| **Context Window** | Large, handles 100KB+ | Limited, needs tight context |
| **Reasoning** | Explains intent first | Jumps to completion |
| **Error Handling** | Discusses edge cases | Assumes happy path |
| **Abstractions** | Will question if needed | Will generate eagerly |
| **Tests** | Discusses test strategy | Generates code first, test later |
| **Corrections** | Back-and-forth refinement | Single-turn fixes work best |

**Codex works best when:**
- Context is tight and specific
- Intent is shown through examples
- You verify output immediately
- You edit locally, not through multi-turn chat

---