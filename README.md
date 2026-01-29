# Tally TDL Skill for Claude Code

A Claude Code skill that helps you write Tally Definition Language (TDL) code for TallyPrime customizations.

## What is TDL?

Tally Definition Language (TDL) is a definition-based language developed by Tally Solutions for extending and customizing TallyPrime ERP software. TDL enables:

- Custom reports and dashboards
- Menu modifications
- Data integrations (XML, JSON, HTTP APIs)
- Form customizations
- Business logic extensions

## Installation

### Option 1: Personal Skill (Available in all your projects)

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/geongeorge/Claude-Tally-TDL-Skill.git ~/.claude/skills/tdl
```

### Option 2: Project Skill (Available only in a specific project)

```bash
cd your-project
mkdir -p .claude/skills
git clone https://github.com/geongeorge/Claude-Tally-TDL-Skill.git .claude/skills/tdl
```

## Usage

Once installed, the skill activates automatically when you ask Claude about TDL or Tally development. You can also invoke it directly:

```
/tdl create a report showing all ledgers with their closing balances
```

### Example Prompts

- "Write TDL code to add a custom menu item to Gateway of Tally"
- "Create a TDL report that fetches data from a REST API"
- "Help me build a ledger balance report in TDL"
- "/tdl add a button that exports data to JSON"

## What's Included

| File | Description |
|------|-------------|
| `SKILL.md` | Main skill instructions - TDL fundamentals and patterns |
| `reference.md` | Complete TDL technical reference with all syntax details |

## Features

The skill helps Claude:

- **Write correct TDL syntax** - Proper definition structure and attributes
- **Use symbol prefixes correctly** - `@`, `@@`, `#`, `##`, `$`, `$$`
- **Follow the definition hierarchy** - Report → Form → Part → Line → Field
- **Integrate with external systems** - HTTP, XML, JSON data sources
- **Modify existing Tally definitions** - Using `#` prefix
- **Apply best practices** - Naming conventions, modularity, comments

## TDL Quick Reference

### Definition Structure
```
[<Definition Type> : <Definition Name>]
  <Attribute> : <Value>
```

### Symbol Prefixes
| Symbol | Purpose |
|--------|---------|
| `@` | Local formula |
| `@@` | Global/System formula |
| `#` | Field reference / Modify definition |
| `##` | Variable value |
| `$` | Object method |
| `$$` | Built-in function |

### Example
```tdl
;; Add custom report to Tally menu
[#Menu: Gateway of Tally]
  Add: Key Item: My Report: M: Display: MyReport

[Report: MyReport]
  Form: MyForm
  Title: "Custom Ledger Report"

[Form: MyForm]
  Part: MyPart

[Part: MyPart]
  Line: MyLine
  Repeat: MyLine: LedgerCollection

[Line: MyLine]
  Field: LedgerName, LedgerBalance

[Field: LedgerName]
  Set as: $Name
  Width: 40

[Field: LedgerBalance]
  Set as: $ClosingBalance
  Width: 20
  Align: Right
```

## Resources

- [Official Tally TDL Documentation](https://help.tallysolutions.com/developer-reference/tally-definition-language/tdl/)
- [TallyPrime Developer Portal](https://help.tallysolutions.com/)

## License

MIT License - See [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome! Please open an issue or submit a pull request.
