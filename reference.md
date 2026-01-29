# Tally Definition Language (TDL) - Complete Technical Reference

**Source:** https://help.tallysolutions.com/developer-reference/tally-definition-language/tdl/

**Last Updated:** January 29, 2026

---

## Table of Contents

1. [Overview](#overview)
2. [Symbols and Prefixes](#symbols-and-prefixes)
3. [Definition Syntax](#definition-syntax)
4. [Data Types and Operators](#data-types-and-operators)
5. [Formulas](#formulas)
6. [Variables](#variables)
7. [Functions](#functions)
8. [Definition Types](#definition-types)
9. [Attributes Reference](#attributes-reference)
10. [Code Examples](#code-examples)

---

## Overview

TDL (Tally Definition Language) is a definition-based language developed to provide flexibility and power to extend the default capabilities of Tally and integrate them with external applications. TDL provides capabilities for:

- Rapid Development
- Rendering
- Data Management
- Integration

### Key Characteristics

- **Definition-Based**: Unlike procedural languages, TDL uses named definitions with properties/attributes
- **Object-Oriented**: Strongly emphasizes re-usability
- **Case and Space Insensitive**: Definition names are case and space insensitive
- **Declarative**: The order of attributes/properties comes into effect based on user interaction

---

## Symbols and Prefixes

### Access Specifiers/Symbol Prefixes

| Symbol | Usage |
|--------|-------|
| `@` | Access Local formula |
| `@@` | Get the value of a System formula |
| `#` | Get the value of a field when prefixed to Field name |
| `##` | Get the value of a Global variable |
| `$` | Access the value of an Object Method |
| `$$` | Call a Function |

### General Symbols

| Symbol | Usage |
|--------|-------|
| `;` `;;` `/* */` | Comments in TDL |
| `+` | Line continuation character |
| `_` (underscore) | Expose methods to ODBC SQL Procedure |
| `*` | Reinitialize a Definition |
| `!` | Create an Optional Definition |
| `#` | Definition modifier |

---

## Definition Syntax

### Basic Definition Structure

```
[<Definition Type> : <Definition Name>]
  <Attribute> : <Value>
  <Attribute> : <Value>
```

### Naming Conventions for Definitions

- Definition name must be unique for a particular definition type
- Definition names are case and space insensitive
- Special characters are not allowed
- Definition names should not start with numerals
- Reserved words cannot be used as a definition name
- Default TDL definitions cannot be used as user-defined names (unless modified)

### Example

```
[Part: PartOne]
  Width: 100
  Height: 50
```

---

## Symbols and Prefixes - Detailed Usage

### Local Formula (@)

A Local Formula is defined and retrieved within a single Interface Definition. The scope is limited to the current definition.

**Syntax:**
```
[Field : FieldName]
  LocalFormula1 : <value>
  LocalFormula2 : <value>
  Set as : @LocalFormula1 + @LocalFormula2
```

**Example:**
```
[Field : CompanyNameandAddress]
  Company : "Tally India Pvt Ltd, "
  Address : "No 23 & 24, AMR Tech Park II, Hongasandra, "
  City    : "Bangalore"
  Set as  : @Company + @Address + @City
```

**Naming Conventions for Formulas:**
- Case insensitive
- Only alphanumeric characters allowed
- Space insensitive at definition time (no spaces allowed during deployment/usage)

### Global Formula (@@)

A Global Formula is available globally across all definitions. Must be defined within `[System: Formula]`.

**Syntax:**
```
[System : Formula]
  FormulaName : <value>

[Field : FieldName]
  Width : @@FormulaName
```

**Example:**
```
[System : Formula]
  AmtWidth : 20

[Field : RepTitleAmt]
  Width : @@AmtWidth

[Field : RepDetailAmt]
  Width : @@AmtWidth

[Field : RepTotalAmt]
  Width : @@AmtWidth
```

### Field Reference (#)

The `#` symbol retrieves values from another Field or modifies existing definitions.

**Referencing a Field:**
```
[Field : HW]
  Set as : "Hello World"

[Field : HW1]
  Set as : #HW
```

**Modifying Existing Definitions:**
```
[#Menu : Gateway of Tally]
  Add : Key Item : Hello World: H : Display : HWReport
  Title : "Tally Gateway"

[#Field : LedParticulars]
  Width : 50
```

### Global Variable (##)

Variables are named containers of data that can be altered as needed. Both Local and Global Variables can be accessed using `##`.

**Syntax:**
```
[Field : FieldName]
  Set as : ##VariableName

[Report : ReportName]
  Title : if ##VariableName = " " then "Default" else ##VariableName
```

**Example:**
```
[Field : FGField]
  Set as : ##RTitle

[Report : DBLedReport]
  Title : if ##LedgerName = " " then "Daybook" else "Ledger Report"
```

### Object Method ($)

The `$` prefix invokes or deploys values from a Method or UDF of any Object.

**Syntax:**
```
[Field: FieldName]
  Set As: $MethodName
```

**Example:**
```
[Field: MyField]
  Set As: $Name
```

### Internal Function ($$)

Functions are in-built and can accept zero or more arguments. Spaces and special characters (except brackets) are not allowed in function parameters.

**Syntax:**
```
[Field : FieldName]
  Set as : $$FunctionName:Parameter1:Parameter2
```

**Examples:**
```
[Field : Current Date]
  Set as : $$MachineDate

[Field : Credit Amt]
  Set as : if $$IsDr:$ClosingBalance then 0 else $ClosingBalance

[Field : StringPart Field]
  Set as : $$StringPart($Email:Company:##SVCurrentCompany):0:5
```

---

## Comments

### Single Line Comment (;;)

```
;; This is a single line comment
```

### Part Line Comment (;)

```
; This is a part line comment
```

### Multi-Line Comment (/* */)

```
/*
This is a multi-line comment
spanning multiple lines
*/
```

---

## Line Continuation Character (+)

Used to split lengthy lines into shorter lines for better readability and debugging.

**Syntax:**
```
[#Menu: Gateway of Tally]
  Add : Key Item : Before : @@locQuit : LineCtn : C : Display : +
  LineCtn : NOT $$IsEmpty:$$SelectedCmps
```

**Note:** When concatenating strings using `+` operator, if the resultant string exceeds 2048 bytes, use `$$SPrintf` function instead.

---

## Exposing Methods to ODBC (_)

The underscore prefix exposes methods to ODBC interface.

**Syntax:**
```
[#Object: ObjectName]
  _MethodName: <expression>
```

**Example:**
```
[#Object: Ledger]
  _Difference: $ClosingBalance – $OpeningBalance
```

### Creating SQL Procedures (_)

By prefixing `_` to a Collection Name, it becomes a procedure that can be referenced externally.

**Syntax:**
```
[Collection: _ProcedureName]
  Type : <ObjectType>
  Fetch : <FetchExpression>
```

---

## Optional Definition (!)

The exclamation mark creates optional definitions that may or may not be present.

**Syntax:**
```
[!Definition Type : Definition Name]
  <Attributes>
```

---

## Definition Reinitialization (*)

The asterisk reinitializes a definition.

**Syntax:**
```
[*Definition Type : Definition Name]
  <Attributes>
```

---

## Definition Types

### 1. Interface Definitions

Control construction of interfaces and presentation layers.

#### Menu
Displays a list of options. Based on menu item selected, the action is determined.

```
[Menu: MyMenu]
  Add: Item: ItemName: Key: Display: ReportName
  Title: "Menu Title"
```

#### Report
All screens in Tally (input or output) are created using Report definition.

```
[Report: MyReport]
  Form: FormName
  Title: "Report Title"
```

#### Form
Area used to display contents of a report.

```
[Form: FormName]
  Part: PartName
```

#### Part
Divides form area into sections. Contains one or more lines.

```
[Part: PartName]
  Line: LineName
  Height: 50
```

#### Line
Consists of one or more fields. Specifies the order of field display.

```
[Line: LineName]
  Field: FieldName1
  Field: FieldName2
```

#### Field
Place where data is displayed or entered.

```
[Field: FieldName]
  Set as: "Value"
  Width: 50
  Height: 20
```

#### Button
Displays a button on the Button bar and performs an action.

```
[Button: ButtonName]
  Title: "Click Me"
  Action: ActionName
```

#### Table
Alias of Collection definition. Displays list of values as tables.

```
[Table: TableName]
  Collection: CollectionName
```

#### Resource
Allows accessing and using resources (images, icons, cursors) from local disk, HTTP/FTP, or DLL/EXE.

**Syntax:**
```
[Resource: ResourceName]
  Source: <Path to Image file>
  Resource: <Name Of Resource>[:<DLL/EXE Name with path>]
  Resource Type: Bitmap / Icon / Jpeg / Cursor
```

**Supported Formats:** BMP, JPEG, ICON, CUR (for PDF export: BMP and JPEG only)

**Usage in Part:**
```
[Part: PartName]
  Image: ResourceName
```

### 2. Data Definitions

Handle data gathering, processing, storage, and manipulation.

#### Collection
Groups related data objects.

```
[Collection: CollectionName]
  Type: ObjectType
  Fetch: FetchExpression
```

#### Object
Represents data entities.

```
[Object: ObjectName]
  Method: MethodName
```

#### System
Defines system-level configurations and formulas.

```
[System: Formula]
  GlobalFormula: Value
```

### 3. Formatting Definitions

Control presentation and styling.

### 4. Integration Definitions

Handle external data integration.

### 5. Action Definitions

Define actions and events.

### 6. System Definitions

System-level configurations.

### 7. Procedural Definitions

Define procedures and logic flow.

---

## Data Types and Operators

### Data Types

| Type | Description | Example |
|------|-------------|---------|
| String | Text values | "Hello World" |
| Number | Numeric values | 100, 50.25 |
| Boolean | True/False values | True, False |
| Date | Date values | 01-01-2024 |
| Amount | Currency values | 1000.00 |

### Operators

#### Arithmetic Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Addition | 10 + 5 |
| `-` | Subtraction | 10 - 5 |
| `*` | Multiplication | 10 * 5 |
| `/` | Division | 10 / 5 |
| `%` | Modulus | 10 % 3 |

#### Comparison Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `=` | Equal to | Value = 100 |
| `<>` | Not equal to | Value <> 100 |
| `<` | Less than | Value < 100 |
| `>` | Greater than | Value > 100 |
| `<=` | Less than or equal | Value <= 100 |
| `>=` | Greater than or equal | Value >= 100 |

#### Logical Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `AND` | Logical AND | Condition1 AND Condition2 |
| `OR` | Logical OR | Condition1 OR Condition2 |
| `NOT` | Logical NOT | NOT Condition |

#### String Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Concatenation | "Hello" + " " + "World" |

---

## Formulas

### Local Formula

Defined and used within a single definition.

```
[Field: FieldName]
  FirstName: "John"
  LastName: "Doe"
  FullName: @FirstName + " " + @LastName
  Set as: @FullName
```

### Global Formula

Defined in `[System: Formula]` and accessible globally.

```
[System: Formula]
  DefaultWidth: 50
  DefaultHeight: 20

[Field: Field1]
  Width: @@DefaultWidth
  Height: @@DefaultHeight
```

---

## Variables

### Local Variables

Retain value only within a particular Report.

```
[Report: MyReport]
  LocalVar: "Initial Value"
```

### Global Variables

Retain values throughout the session or permanently.

```
[Variable: GlobalVarName]
  Value: "Persistent Value"
```

### Accessing Variables

```
[Field: FieldName]
  Set as: ##VariableName
```

---

## Functions

### Common Built-in Functions

| Function | Description | Syntax |
|----------|-------------|--------|
| `$$MachineDate` | Get current system date | `$$MachineDate` |
| `$$IsDr` | Check if debit | `$$IsDr:Amount` |
| `$$IsEmpty` | Check if empty | `$$IsEmpty:Value` |
| `$$StringPart` | Extract string part | `$$StringPart(String):Start:Length` |
| `$$SPrintf` | String formatting | `$$SPrintf(Format, Args)` |

### Function Syntax

```
$$FunctionName:Parameter1:Parameter2:Parameter3
```

### Examples

```
[Field: CurrentDate]
  Set as: $$MachineDate

[Field: CreditAmount]
  Set as: if $$IsDr:$ClosingBalance then 0 else $ClosingBalance

[Field: EmailPart]
  Set as: $$StringPart($Email:Company:##SVCurrentCompany):0:5

[Field: FormattedString]
  Set as: $$SPrintf("Name: %s, Amount: %d", $Name, $Amount)
```

---

## Attributes Reference

### Common Attributes

| Attribute | Description | Example |
|-----------|-------------|---------|
| `Title` | Display title | `Title: "Report Title"` |
| `Width` | Width in characters | `Width: 50` |
| `Height` | Height in lines | `Height: 20` |
| `Set as` | Value/expression | `Set as: "Value"` |
| `Color` | Text color | `Color: White` |
| `Background` | Background color | `Background: Blue` |
| `Bold` | Bold text | `Bold: Yes` |
| `Italic` | Italic text | `Italic: Yes` |
| `Underline` | Underlined text | `Underline: Yes` |
| `Align` | Text alignment | `Align: Right` |
| `Format` | Number format | `Format: "##,##0.00"` |
| `Type` | Data type | `Type: String` |
| `Mandatory` | Required field | `Mandatory: Yes` |
| `Visible` | Visibility | `Visible: Yes` |
| `Enabled` | Enable/disable | `Enabled: Yes` |

### Form Attributes

| Attribute | Description |
|-----------|-------------|
| `Part` | Contains Part definitions |
| `Form Type` | Type of form |
| `Background` | Form background |

### Part Attributes

| Attribute | Description |
|-----------|-------------|
| `Line` | Contains Line definitions |
| `Image` | Display image resource |
| `Height` | Part height |
| `Width` | Part width |

### Field Attributes

| Attribute | Description |
|-----------|-------------|
| `Set as` | Field value/expression |
| `Width` | Field width |
| `Height` | Field height |
| `Align` | Text alignment |
| `Color` | Text color |
| `Background` | Background color |
| `Format` | Number/date format |
| `Type` | Data type |
| `Mandatory` | Required field |

### Report Attributes

| Attribute | Description |
|-----------|-------------|
| `Form` | Associated form |
| `Title` | Report title |
| `Width` | Report width |
| `Height` | Report height |
| `Background` | Background color |

---

## Code Examples

### Example 1: Simple Report with Fields

```
[Report: SampleReport]
  Form: SampleForm
  Title: "Sample Report"

[Form: SampleForm]
  Part: HeaderPart
  Part: DetailPart

[Part: HeaderPart]
  Line: HeaderLine
  Height: 5

[Line: HeaderLine]
  Field: CompanyName
  Field: ReportDate

[Field: CompanyName]
  Set as: "My Company"
  Width: 50
  Bold: Yes

[Field: ReportDate]
  Set as: $$MachineDate
  Width: 20
  Align: Right

[Part: DetailPart]
  Line: DetailLine
  Height: 20

[Line: DetailLine]
  Field: ItemName
  Field: ItemAmount

[Field: ItemName]
  Set as: $Name
  Width: 40

[Field: ItemAmount]
  Set as: $Amount
  Width: 20
  Format: "##,##0.00"
  Align: Right
```

### Example 2: Menu with Multiple Items

```
[Menu: MainMenu]
  Add: Item: Reports: R: Display: ReportsMenu
  Add: Item: Settings: S: Display: SettingsMenu
  Add: Item: Exit: E: Call: ExitFunction
  Title: "Main Menu"

[Menu: ReportsMenu]
  Add: Item: Sales Report: S: Display: SalesReport
  Add: Item: Purchase Report: P: Display: PurchaseReport
  Title: "Reports"

[Menu: SettingsMenu]
  Add: Item: Company Settings: C: Display: CompanySettings
  Add: Item: User Settings: U: Display: UserSettings
  Title: "Settings"
```

### Example 3: Using Formulas and Variables

```
[System: Formula]
  PageWidth: 80
  PageHeight: 60
  DefaultColor: White

[Report: FinancialReport]
  Form: FinancialForm
  Title: "Financial Report"

[Form: FinancialForm]
  Part: TitlePart
  Part: DetailPart
  Part: TotalPart

[Part: TitlePart]
  Line: TitleLine
  Height: 3

[Line: TitleLine]
  Field: ReportTitle

[Field: ReportTitle]
  Set as: "Balance Sheet"
  Width: @@PageWidth
  Align: Center
  Bold: Yes
  Color: @@DefaultColor

[Part: DetailPart]
  Line: DetailLine
  Height: 50

[Line: DetailLine]
  Field: AccountName
  Field: AccountAmount

[Field: AccountName]
  Set as: $Name
  Width: 40

[Field: AccountAmount]
  Set as: $Amount
  Width: 20
  Format: "##,##0.00"
  Align: Right

[Part: TotalPart]
  Line: TotalLine
  Height: 3

[Line: TotalLine]
  Field: TotalLabel
  Field: TotalAmount

[Field: TotalLabel]
  Set as: "TOTAL"
  Width: 40
  Bold: Yes

[Field: TotalAmount]
  Set as: ##TotalAmount
  Width: 20
  Format: "##,##0.00"
  Align: Right
  Bold: Yes
```

### Example 4: Conditional Logic

```
[Field: StatusDisplay]
  Set as: if ##Status = "Active" then "Active" else "Inactive"

[Field: DiscountAmount]
  Set as: if $Amount > 10000 then $Amount * 0.10 else 0

[Field: DisplayColor]
  Set as: if ##IsWarning = "Yes" then Red else Green
```

### Example 5: Using Object Methods and Functions

```
[Field: LedgerBalance]
  Set as: $ClosingBalance

[Field: LedgerDifference]
  Set as: $ClosingBalance - $OpeningBalance

[Field: IsDebit]
  Set as: if $$IsDr:$ClosingBalance then "Debit" else "Credit"

[Field: EmailDomain]
  Set as: $$StringPart($Email:Company:##CurrentCompany):0:5

[Field: FormattedAmount]
  Set as: $$SPrintf("Amount: %d", $Amount)
```

### Example 6: Modifying Existing Definitions

```
;; Adding new menu item to Gateway of Tally
[#Menu: Gateway of Tally]
  Add: Key Item: Custom Report: C: Display: CustomReport
  Title: "Tally Gateway - Enhanced"

;; Modifying existing field width
[#Field: LedParticulars]
  Width: 60

;; Adding new method to Ledger object
[#Object: Ledger]
  _NetBalance: $ClosingBalance - $OpeningBalance
```

### Example 7: Using Local and Global Formulas

```
[System: Formula]
  CompanyName: "ABC Corporation"
  FinancialYear: "2024-2025"
  CurrencySymbol: "₹"

[Report: AnnualReport]
  Form: ReportForm
  Title: @@CompanyName + " - Annual Report"

[Form: ReportForm]
  Part: HeaderPart
  Part: ContentPart

[Part: HeaderPart]
  Line: HeaderLine

[Line: HeaderLine]
  Field: CompanyHeader
  Field: YearHeader

[Field: CompanyHeader]
  Set as: @@CompanyName
  Width: 50
  Bold: Yes

[Field: YearHeader]
  Set as: @@FinancialYear
  Width: 30
  Align: Right

[Part: ContentPart]
  Line: AmountLine

[Line: AmountLine]
  Field: AmountLabel
  Field: AmountValue

[Field: AmountLabel]
  Set as: "Total Amount"
  Width: 40

[Field: AmountValue]
  Set as: @@CurrencySymbol + " " + $Amount
  Width: 30
  Align: Right
  Format: "##,##0.00"
```

---

## Best Practices

1. **Use Meaningful Names**: Define names that clearly indicate functionality
2. **Use Prefixes**: Use prefixes like "MBS" for "My Balance Sheet" to ensure uniqueness
3. **Comments**: Use `;;` for single-line comments and `/* */` for multi-line comments
4. **Reusability**: Create global formulas and variables for values used across multiple definitions
5. **Line Continuation**: Use `+` to break long lines for better readability
6. **Naming Conventions**: Follow consistent naming conventions across your TDL program
7. **Error Handling**: Use conditional logic to handle edge cases and invalid data
8. **Performance**: Optimize formulas and avoid complex nested expressions
9. **Testing**: Test all definitions thoroughly before deployment
10. **Documentation**: Document complex formulas and procedures for future maintenance

---

## Common Patterns

### Pattern 1: Conditional Display

```
[Field: ConditionalField]
  Set as: if Condition then Value1 else Value2
```

### Pattern 2: String Concatenation

```
[Field: ConcatenatedField]
  FirstPart: "Part 1"
  SecondPart: "Part 2"
  Set as: @FirstPart + " - " + @SecondPart
```

### Pattern 3: Calculated Fields

```
[Field: CalculatedField]
  Set as: $Value1 + $Value2 - $Value3
```

### Pattern 4: Formatted Output

```
[Field: FormattedField]
  Set as: $$SPrintf("Format: %s", $Value)
```

### Pattern 5: Global Access

```
[System: Formula]
  GlobalValue: 100

[Field: UsingGlobal]
  Set as: @@GlobalValue + $LocalValue
```

---

## Additional Resources

- Official Tally Help: https://help.tallysolutions.com/
- TallyPrime Developer Documentation
- TDL FAQ and Troubleshooting
- Community Forums and Support

---

**Note:** This documentation is a technical reference compiled from Tally Solutions official documentation. For the most current and complete information, please refer to the official Tally Help portal.


---

## Integration Capabilities

Tally Definition Language (TDL) provides robust capabilities for integrating TallyPrime with external applications and services. This section covers data import/export, URL fetching, and other integration features.

### Data Import and Export

TDL supports data import and export in various formats, primarily XML and JSON. This allows for seamless data exchange between TallyPrime and other systems.

#### Importing Data

Data can be imported into TallyPrime from external files (XML, JSON) or from HTTP sources. The `Import` action is used for this purpose.

**Syntax (File Import):**
```
[#Import File: Vouchers]
  File: "/path/to/your/data.xml"
```

**Syntax (HTTP Import):**
```
[#Import File: Vouchers]
  URL: "https://example.com/data.xml"
```

#### Exporting Data

Data can be exported from TallyPrime to XML or JSON formats. The `Export` action is used for this purpose.

**Syntax:**
```
[Report: MyExportReport]
  Export: Yes
  Format: XML
  File: "/path/to/your/export.xml"
```

### URL Fetching and HTTP Requests

TDL allows you to make HTTP requests to external APIs and consume the responses. This is crucial for real-time data integration.

#### HTTP GET and POST Requests

The `HTTPRequest` action is used to send both GET and POST requests.

**Syntax:**
```
Action : HTTP Request : <URL> : <HTTP Method> : <Request Format> : <Encoding> : <Request Report> : <Post Action Function>
```

-   **URL**: The API endpoint.
-   **HTTP Method**: `GET` or `POST`.
-   **Request Format**: `XML`, `JSON`, or `JSONEx`.
-   **Encoding**: `ASCII` or `UNICODE`.
-   **Request Report**: A TDL report that generates the request body (for POST requests).
-   **Post Action Function**: A function to be called after the request is complete, to process the response.

**Example (GET Request):**
```
[Function: GetDataFromAPI]
  100 : HTTPRequest : "https://api.example.com/data" : GET : JSON : ASCII : : ProcessResponse

[Function: ProcessResponse]
  ;; Code to process the JSON response in the 'Parameter Collection'
```

#### HTTP POST Action

The `HTTP Post` action is a specialized action for sending POST requests.

**Syntax:**
```
Action : HTTP Post : <URL Formula> : <Encoding> : <Request Report> : <Error Report> : <Success Report>
```

### Integration Functions

TDL provides several built-in functions to facilitate integration.

| Function | Description |
|---|---|
| `$$HTTPInfo` | Retrieves details about the HTTP request (URL, headers, content length). |
| `$$ImportInfo` | Provides information about the import process (number of created, altered, ignored objects). |
| `$$LastImportError` | Returns the error description for the last imported object. |
| `$$ImportType` | Determines the source of the import (Sync, Migration, SOAP, etc.). |
| `$$ImportAction` | Indicates the status of the last import action (Created, Altered, Ignored, etc.). |
| `$$GetMethodNameValue` | Extracts values from XML or JSON tags that contain special characters. |
| `$$IsFileTypeSupported` | Checks if a given file format is supported by the system. |
| `$$ExcelInfo` | Provides information about the installed version of Microsoft Excel. |
| `$$WordInfo` | Provides information about the installed version of Microsoft Word. |

---
## Integration Functions - Detailed Reference

### $$GetMethodNameValue

This function is used to extract values from XML or JSON tags that contain special characters, which cannot be parsed directly as method names in TDL.

**Syntax:**
```
$$GetMethodNameValue: <Tag Name in double quotes>
```

**Example:**
```
[Collection: CATLedgers]
  Data Source : HTTP JSON Ex : "https://webhook.site/9ecdcd81-b7f9-40b6-9fa3-453c9a914baf"

[Field: LedgerName]
  Set as: $$GetMethodNameValue:"cat.ledname"
```

### $$HTTPInfo

Retrieves HTTP request details such as URL, headers, and content length. Useful for debugging and logging HTTP interactions.

**Syntax:**
```
$$HTTPInfo:<InfoType>[:<InfoSubType>]
```

**Parameters:**
-   **InfoType**: Can be `URL`, `Header`, or `ContentLength`.
-   **InfoSubType**: Optional, used when InfoType is `Header` to specify the header name.

**Example:**
```
[Field: RequestURL]
  Set as: $$HTTPInfo:URL

[Field: ContentLen]
  Set as: $$HTTPInfo:ContentLength

[Field: AuthHeader]
  Set as: $$HTTPInfo:Header:Authorization
```

### $$ImportInfo

Provides statistics about the import process, including the number of objects created, altered, ignored, combined, and errors encountered.

**Syntax:**
```
$$ImportInfo:<InfoType>
```

**Parameters:**
-   **InfoType**: Can be `Created`, `Altered`, `Ignored`, `Combined`, `Deleted`, `Cancelled`, `Errors`, or `Exceptions`.

**Example:**
```
[Field: CreatedCount]
  Set as: $$ImportInfo:Created
  XMLTag: "Created"

[Field: ErrorCount]
  Set as: $$ImportInfo:Errors
  XMLTag: "Errors"

[Field: AlteredCount]
  Set as: $$ImportInfo:Altered
  XMLTag: "Altered"
```

### $$LastImportError

Returns the error description for the last imported object. Returns empty if no error occurred.

**Syntax:**
```
$$LastImportError
```

**Example:**
```
[Function: LogImportError]
  Parameter: pLogInfo: String: ""
  
  001: If: NOT $$IsEmpty:$$LastImportError
  002:   Log: "Error: " + $$LastImportError
  003: Else
  004:   Log: "Import successful for object"
  005: End If
```

### $$ImportType

Determines the source or type of the import operation.

**Syntax:**
```
$$ImportType
```

**Possible Values:**
-   `Sync`: Synchronization import
-   `Migration`: Migration import
-   `Remote`: Remote API import
-   `NatLang`: Natural language import
-   `SOAP`: SOAP/XML import
-   `Manual`: Manual import

**Example:**
```
[Field: ImportSource]
  Set as: $$ImportType

[Function: LogImportType]
  001: Log: "Import Type: " + $$ImportType
```

### $$ImportAction

Indicates the status of the last import action for an object.

**Syntax:**
```
$$ImportAction
```

**Possible Values:**
-   `Created`: New object was created
-   `Altered`: Existing object was modified
-   `Ignored`: Object was ignored
-   `Combined`: Object was combined with another
-   `Error`: Error occurred during import

**Example:**
```
[Field: ObjectStatus]
  Set as: $$ImportAction

[Function: LogImportAction]
  001: Log: "Action: " + $$ImportAction + " - Error: " + if $$IsEmpty:$$LastImportError then "None" else $$LastImportError
```

### $$TplLine

Provides the line number or object index in the current collection, useful for document templates and repeated rows.

**Syntax:**
```
$$TplLine
```

**Example:**
```
[Form: InvoiceForm]
  Resource: InvoiceWord
  XML Map: Line: $$TplLine
```

### $$WordInfo

Checks if Microsoft Word is installed and retrieves version information.

**Syntax:**
```
$$WordInfo:<InfoType>
```

**Parameters:**
-   **InfoType**: Can be `Version` or `IsDocxSupported`.

**Example:**
```
[Field: WordVersion]
  Set as: $$WordInfo:Version

[Field: DocxSupport]
  Set as: $$WordInfo:IsDocxSupported
```

### $$ExcelInfo

Checks if Microsoft Excel is installed and retrieves version information.

**Syntax:**
```
$$ExcelInfo:<InfoType>
```

**Parameters:**
-   **InfoType**: Can be `Version` or `IsXlsxSupported`.

**Example:**
```
[Field: ExcelVersion]
  Set as: $$ExcelInfo:Version

[Field: XlsxSupport]
  Set as: $$ExcelInfo:IsXlsxSupported
```

### $$IsFileTypeSupported

Checks if a given file format is supported by the system.

**Syntax:**
```
$$IsFileTypeSupported:<FileExtension>
```

**Example:**
```
[Field: ODTSupported]
  Set as: $$IsFileTypeSupported:".odt"

[Field: ODSSupported]
  Set as: $$IsFileTypeSupported:".ods"
```

---

## XML Integration Using TDL

TDL allows seamless integration with external systems through XML. This enables fetching data from remote APIs or sending data to external services.

### XML Data Source Attributes

#### DataSource: HTTPXML

Fetches XML data from an HTTP endpoint.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: HTTPXML
  Remote URL: "https://api.example.com/data.xml"
```

#### DataSource: FileXML

Reads XML data from a local file.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: FileXML
  File: "/path/to/data.xml"
```

#### DataSource: XMLString

Parses XML from a string variable.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: XMLString
  XML String: ##XMLVariable
```

### XML Attributes

| Attribute | Description |
|-----------|-------------|
| `XML Tag` | Maps a TDL field to an XML tag |
| `XML Object` | Specifies the XML object to iterate over |
| `XML Object Path` | XPath-like path to locate XML objects |
| `Plain XML` | Exports data as plain XML without wrapper tags |
| `Export Header` | Includes HTTP headers in the XML export |

### XML Integration Example

```
[Collection: LedgerCollection]
  Type: Ledger
  DataSource: HTTPXML
  Remote URL: "https://api.example.com/ledgers"
  XML Object: "Ledger"
  XML Object Path: "/Response/Data"

[Report: LedgerReport]
  Form: LedgerForm

[Form: LedgerForm]
  Part: LedgerPart

[Part: LedgerPart]
  Line: LedgerLine
  Repeat: LedgerLine: LedgerCollection

[Line: LedgerLine]
  Field: LedgerName
  Field: LedgerBalance

[Field: LedgerName]
  Set as: $Name
  XML Tag: "Name"

[Field: LedgerBalance]
  Set as: $ClosingBalance
  XML Tag: "Balance"
```

---

## JSON Integration Using TDL

TDL supports modern JSON-based integrations for exchanging data with contemporary APIs and web services.

### JSON Data Source Attributes

#### DataSource: HTTPJSON

Fetches JSON data from an HTTP endpoint.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: HTTPJSON
  Remote URL: "https://api.example.com/data.json"
```

#### DataSource: HTTPJSONEX

Extended JSON support for complex JSON structures with custom mapping.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: HTTPJSONEX
  Remote URL: "https://api.example.com/data.json"
```

#### DataSource: FileJSON

Reads JSON data from a local file.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: FileJSON
  File: "/path/to/data.json"
```

#### DataSource: JSONString

Parses JSON from a string variable.

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: JSONString
  JSON String: ##JSONVariable
```

### JSON Attributes

| Attribute | Description |
|-----------|-------------|
| `JSON Tag` | Maps a TDL field to a JSON property |
| `JSON Tag as Desc Name` | Uses JSON tag as the field description |
| `JSON Object` | Specifies the JSON object to iterate over |
| `JSON Object Path` | JSONPath-like path to locate JSON objects |
| `Plain JSON` | Exports data as plain JSON without wrapper objects |
| `Export Header` | Includes HTTP headers in the JSON export |
| `Export Empty Fields` | Includes empty fields in the JSON export |

### JSON Integration Example

```
[Collection: BranchCollection]
  Type: Branch
  DataSource: HTTPJSONEX
  Remote URL: "https://api.example.com/branches"
  JSON Object: "Branch"
  JSON Object Path: "$.data.branches"

[Report: BranchReport]
  Form: BranchForm

[Form: BranchForm]
  Part: BranchPart

[Part: BranchPart]
  Line: BranchLine
  Repeat: BranchLine: BranchCollection

[Line: BranchLine]
  Field: BranchCode
  Field: BranchName
  Field: BranchCity

[Field: BranchCode]
  Set as: $Code
  JSON Tag: "code"

[Field: BranchName]
  Set as: $Name
  JSON Tag: "name"

[Field: BranchCity]
  Set as: $City
  JSON Tag: "city"
```

---

## HTTP Request Action

The `HTTP Request` action enables sending GET and POST requests to external APIs and processing the responses.

**Syntax:**
```
Action : HTTP Request : <URL> : <HTTP Method> : <Request Format> : <Encoding> : <Request Report> : <Post Action Function>
```

**Parameters:**

-   **URL**: The API endpoint URL. Can be a list of URLs; if the first fails, subsequent URLs are tried.
-   **HTTP Method**: `GET` or `POST`. For GET requests, only headers are considered.
-   **Request Format**: `XML`, `JSON`, or `JSONEx`.
-   **Encoding**: `ASCII` or `UNICODE`.
-   **Request Report**: A TDL report that generates the request body (for POST requests).
-   **Post Action Function**: A function called after the request completes to process the response.

**Response Handling:**

The response is received in the `Parameter Collection`, which has a limited scope to the post-action function. To use this collection in a report, associate it via the `Inheritable Collection` attribute.

**Example:**

```
[Function: FetchBranchList]
  100 : HTTPRequest : "https://api.example.com/branches" : GET : JSONEX : ASCII : : ProcessBranchResponse

[Function: ProcessBranchResponse]
  Variable: vStatus: Number
  
  001 : Walk Collection: Parameter Collection
  002 :   Set: vStatus: $Status
  003 :   Log: ##vStatus
  006 : End Walk
  
  007 : If: ##vStatus = 0
  008 :   Msg Box: "Error": "Failed to retrieve branch list"
  009 : Else
  010 :   Display: BranchListReport
  011 : End If

[Report: BranchListReport]
  Inheritable Collection: Parameter Collection
  Form: BranchListForm

[Form: BranchListForm]
  Part: BranchListPart

[Part: BranchListPart]
  Line: BranchListLine
  Repeat: BranchListLine: Parameter Collection
```

---

## HTTP Post Action

The `HTTP Post` action submits data to a server and handles the response.

**Syntax:**
```
Action : HTTP Post : <URL Formula> : <Encoding> : <Request Report> : <Error Report> : <Success Report>
```

**Parameters:**

-   **URL Formula**: A system formula that resolves to the target URL.
-   **Encoding**: `ASCII` or `UNICODE`.
-   **Request Report**: Generates the XML request body.
-   **Error Report**: Displayed if the POST fails.
-   **Success Report**: Displayed if the POST succeeds.

**Response Determination:**

Success or failure is determined by the `<STATUS>` tag in the response. If `<STATUS>` is `1`, it's a success; otherwise, it's a failure.

**Example:**

```
[System: Formula]
  PostURL: "https://api.example.com/submit"

[Report: SubmitDataReport]
  Form: SubmitDataForm

[Form: SubmitDataForm]
  Part: SubmitDataPart

[Part: SubmitDataPart]
  Line: SubmitDataLine

[Line: SubmitDataLine]
  Field: SubmitButton

[Field: SubmitButton]
  Type: Button
  Title: "Submit"
  Action: PostDataAction

[Action: PostDataAction]
  100 : HTTP Post : @@PostURL : ASCII : RequestDataReport : ErrorReport : SuccessReport

[Report: RequestDataReport]
  Form: RequestDataForm
  Export: Yes
  Export Header: "Content-Type: application/xml"

[Report: ErrorReport]
  Form: ErrorForm

[Report: SuccessReport]
  Form: SuccessForm
```

---

## Import File Definition

The `Import File` definition allows you to configure how data should be imported into TallyPrime.

**Syntax:**
```
[Import File: ObjectType]
  Option: OptionName
  Response Report: ReportName
```

**Import Events:**

-   **Start Import**: Triggered when import begins
-   **Import Object**: Triggered for each object being imported
-   **After Import Object**: Triggered after each object is imported
-   **End Import**: Triggered when import completes

**Example:**

```
[#Import File: Vouchers]
  Option: CustomImportOption
  Response Report: CustomResponseReport

[!Import File: CustomImportOption]
  Add: On: After Import Object: Yes: Call: LogVoucherImport

[Function: LogVoucherImport]
  001 : Log: "Voucher " + $$ImportAction + " - " + $$ImportType
  002 : If: NOT $$IsEmpty:$$LastImportError
  003 :   Log: "Error: " + $$LastImportError
  004 : End If

[Report: CustomResponseReport]
  Form: ResponseForm

[Form: ResponseForm]
  Part: ResponsePart

[Part: ResponsePart]
  Line: ResponseLine

[Line: ResponseLine]
  Field: ImportStatus
  Field: ImportMessage

[Field: ImportStatus]
  Set as: $$ImportInfo:Created + $$ImportInfo:Altered
  XMLTag: "TotalProcessed"

[Field: ImportMessage]
  Set as: "Import completed with " + $$ImportInfo:Errors + " errors"
  XMLTag: "Message"
```

---

## Remote Request Attribute

The `Remote Request` attribute is used to specify custom request headers and parameters for HTTP requests.

**Syntax:**
```
[Collection: CollectionName]
  Remote Request: Yes
  Export Header: "Authorization: Bearer <token>"
  Export Header: "Content-Type: application/json"
```

---

## DLL Integration with TallyPrime

TDL can interact with external DLLs (Dynamic Link Libraries) for advanced integrations with databases, cloud services, and hardware devices.

### DLL Integration in JSON Format

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: Plugin JSON
  Input JSON: ##JSONInput
  Input Parameter: ##Parameters
```

### DLL Integration in XML Format

**Syntax:**
```
[Collection: CollectionName]
  Type: ObjectType
  DataSource: Plugin XML
  Input XML: ##XMLInput
  Input Parameter: ##Parameters
```

**Key Use Cases:**

-   Connect to SQL Server, MySQL, or other relational databases
-   Interact with external ERP platforms or cloud APIs
-   Integrate with hardware like barcode scanners or POS terminals
-   Execute custom business logic through external libraries

---

## Integration Best Practices

1. **Error Handling**: Always check for errors using `$$LastImportError` and `$$ImportInfo:Errors` after import operations.

2. **Response Validation**: Validate HTTP responses before processing them in collections or reports.

3. **Timeout Management**: Set reasonable timeouts for HTTP requests to avoid hanging operations.

4. **Data Mapping**: Carefully map external data structures to TDL objects using appropriate attributes like `XML Tag` or `JSON Tag`.

5. **Logging**: Use the `Log` action to record import/export operations for debugging and auditing.

6. **Authentication**: Include necessary authentication headers in HTTP requests using the `Export Header` attribute.

7. **Rate Limiting**: Implement rate limiting when making multiple HTTP requests to external APIs.

8. **Data Validation**: Validate imported data before creating or altering objects in TallyPrime.

9. **Incremental Updates**: Use the `$$ImportAction` function to determine if an object was created or altered, and handle accordingly.

10. **Documentation**: Document your integration configurations and data mappings for future maintenance.

---

## Advanced Integration Patterns

### Pattern 1: Fetch and Display External Data

```
[Function: FetchAndDisplayData]
  100 : HTTPRequest : "https://api.example.com/data" : GET : JSON : ASCII : : DisplayDataReport

[Report: DisplayDataReport]
  Inheritable Collection: Parameter Collection
  Form: DataForm
```

### Pattern 2: User-Driven Data Retrieval

```
[Function: SearchLedgers]
  Parameter: pLedgerName: String: ""
  
  100 : HTTPRequest : "https://api.example.com/ledgers?name=" + ##pLedgerName : GET : JSON : ASCII : : ShowSearchResults

[Report: SearchResultsReport]
  Inheritable Collection: Parameter Collection
  Form: ResultsForm
```

### Pattern 3: Batch Import with Error Logging

```
[#Import File: Vouchers]
  Option: BatchImportWithLogging

[!Import File: BatchImportWithLogging]
  Add: On: After Import Object: Yes: Call: LogImportDetails

[Function: LogImportDetails]
  001 : Log: "Object: " + $$ImportType + " | Action: " + $$ImportAction + " | Error: " + if $$IsEmpty:$$LastImportError then "None" else $$LastImportError
```

### Pattern 4: Conditional Data Submission

```
[Function: ConditionalSubmit]
  Parameter: pAmount: Number: 0
  
  100 : If: ##pAmount > 10000
  101 :   HTTPRequest : "https://api.example.com/high-value" : POST : JSON : ASCII : HighValueRequest : ProcessHighValue
  102 : Else
  103 :   HTTPRequest : "https://api.example.com/normal" : POST : JSON : ASCII : NormalRequest : ProcessNormal
  104 : End If
```

---
