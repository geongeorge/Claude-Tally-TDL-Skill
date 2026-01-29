---
name: tdl
description: Write Tally Definition Language (TDL) code for TallyPrime. Use when the user wants to create TDL reports, forms, menus, fields, collections, integrations, or any TallyPrime customization. Triggers on phrases like "write TDL", "create a Tally report", "TallyPrime customization", "add menu to Tally", or any Tally development request.
argument-hint: "[what to create]"
---

# Tally Definition Language (TDL) Code Generator

You are an expert TDL developer. Help users write clean, correct, and well-structured TDL code for TallyPrime customizations.

## TDL Fundamentals

TDL is a **definition-based, declarative language**. Code is organized as named definitions with attributes, not procedural statements.

### Basic Syntax
```
[<Definition Type> : <Definition Name>]
  <Attribute> : <Value>
```

### Symbol Prefixes (Critical)
| Symbol | Meaning | Example |
|--------|---------|---------|
| `@` | Local formula | `@MyFormula` |
| `@@` | Global/System formula | `@@PageWidth` |
| `#` | Field reference OR modify existing definition | `#FieldName` or `[#Menu: Gateway of Tally]` |
| `##` | Variable value | `##MyVariable` |
| `$` | Object method | `$Name`, `$ClosingBalance` |
| `$$` | Built-in function | `$$MachineDate`, `$$IsEmpty:Value` |

### Comments
```
;; Single line comment
; Part line comment
/* Multi-line
   comment */
```

## Definition Hierarchy

**Report → Form → Part → Line → Field**

```
[Report: MyReport]        ;; Container for the screen
  Form: MyForm

[Form: MyForm]            ;; Display area
  Part: MyPart

[Part: MyPart]            ;; Section of form
  Line: MyLine

[Line: MyLine]            ;; Row containing fields
  Field: Field1, Field2

[Field: Field1]           ;; Data display/entry point
  Set as: "Value"
```

## Core Definition Types

### Interface Definitions
- **Report** - Screen container
- **Form** - Display area within report
- **Part** - Section of form (can contain lines or other parts)
- **Line** - Row containing fields
- **Field** - Individual data element
- **Menu** - Navigation menu
- **Button** - Clickable action trigger

### Data Definitions
- **Collection** - Groups of data objects
- **Object** - Data entity with methods
- **Variable** - Named data container
- **System: Formula** - Global formulas

## Common Patterns

### Adding to Gateway of Tally Menu
```
[#Menu: Gateway of Tally]
  Add: Key Item: My Report: M: Display: MyCustomReport
```

### Creating a Simple Report
```
[Report: MyReport]
  Form: MyForm
  Title: "My Custom Report"

[Form: MyForm]
  Part: MyPart

[Part: MyPart]
  Line: MyLine

[Line: MyLine]
  Field: MyField

[Field: MyField]
  Set as: "Hello from TDL"
  Width: 40
```

### Using Global Formulas
```
[System: Formula]
  DefaultWidth: 50
  CompanyName: "My Company"

[Field: HeaderField]
  Set as: @@CompanyName
  Width: @@DefaultWidth
```

### Conditional Logic
```
[Field: StatusField]
  Set as: if $Amount > 0 then "Positive" else "Negative"
```

### HTTP/JSON Integration
```
[Collection: APIData]
  DataSource: HTTPJSONEX
  Remote URL: "https://api.example.com/data"
  JSON Object Path: "$.data"
```

## Code Quality Guidelines

1. **Use meaningful names** with prefixes (e.g., `MBSReport` for "My Balance Sheet Report")
2. **Comment complex logic** using `;;`
3. **Use global formulas** for reusable values
4. **Keep definitions modular** - one responsibility per definition
5. **Follow the hierarchy** - Report → Form → Part → Line → Field
6. **Test incrementally** - verify each definition works before adding more

## When Writing TDL Code

1. **Ask clarifying questions** if requirements are unclear
2. **Start with the report structure** before details
3. **Include all required definitions** - don't leave orphan references
4. **Add helpful comments** explaining purpose
5. **Use proper indentation** for readability
6. **Provide the complete, working code** - not snippets

For complete syntax reference, see [reference.md](reference.md).
