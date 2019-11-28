# iOS projects general rules

Comments and suggestions are **STRONGLY ENCOURAGED**, so feel free to open an issue.

# Contents
* [Project general rules](#project-general-rules)
  * [Project Tree Structure](#project-tree-structure)
  * [File blocks sctructure](#file-blocks-sctructure)
  * [Security Guide](#security-guide)
  * [Branch Guide](#branch-guide)
* [Design Architecture](#design-architecture)
   * [Design Pattern](#design-pattern)
   * [Managers &amp; Services](#managers--services)
   * [Helpers](#helpers)
* [Code style rules](#code-style-rules)
   * [Conventions](#conventions)
   * [Naming](#naming)
   * [Code Organization](#code-organization)
   * [Assets](#assets)
      * [Images](#images)
      * [Fonts](#fonts)
      * [Colors](#colors)

# Project general rules

## Project Tree Structure

<img width="338" alt="Project Tree Structure" src="https://user-images.githubusercontent.com/25178444/69141915-6018cc00-0ace-11ea-912d-06fe31cc86b4.png">

## File blocks structure

### Code structure for files
*(All sections are optional and used if needed)*

```swift

// MARK: - Private properties (views, DateFormatter etc.)

// MARK: - Properties

// MARK: - Closure/Delegate

// MARK: - Lifecycle

// MARK: - Setup with model

// MARK: - Actions

// MARK: - Override

// MARK: - Help methods

```

## Security Guide

1) Use password on your Mac
2) When you leave your workspace lock Mac (use hot key Command+Control+Q). For detail information  <a href="https://macpaw.com/how-to/lock-mac-screen">goto</a>
3) Use FileVault on Mac.  <a href="https://support.apple.com/en-us/HT204837">Setup Guide</a>
4) Сhange your password from time to time, don't pass password.
5) If it possible setup SSL
6) Use 2-Factor Authentication for Accounts Used for Work:
   * Atlassian (Jira, Confluence)
   * Bitbucket
   * Gmail/Google
   * Github
   * Slack (including workspace used for client work)
   * Trello
7) Do not add certificates or profiles to the repository, do not store passwords in the repository/code
8) Don't use the main branch for work

## Branch Guide

1) Don't use the main branch for work! Create:
   * branch "development" for development
   * branch "release" for the release version
   * branch "beta-testing" for versions delivered to the client for a test (e.g. TestFlight)

2) Commit names:
   * `development` branch:
     * use Jira task name (or task name/number from trello etc) and short description of the work done in this commit. For example: `PB-248, added profile screen`
   * `release` and `beta-testing` branches:
     * when sending a build to the Store, or to the Testflight, you can use the number of the release/beta verion, e.g: "1.0.2 #19"

3) Create commit for every new task
4) If you work in a team, use one branch for every separate task. When done, merge task branch into the "develepment" branch
5) Always work on latest code: before starting new feature, make sure to sync with main active branch

# Design Architecture

## Design Pattern

The design pattern we will begin adopting should make the app more modularized and allow for easy navigation through the codebase. It is inspired by MVVM+Coordinator, an alternative to standard MVC, where the business logic is separated from the UI in a `ViewModel`.

Each component in `Application` (see [Project Tree Structure](#project-tree-structure)) should include `$VCName$ViewController.swift`, `$VMName$ViewModel.swift`, (several) `$VName$View.swift` and (several) `$MName$Model.swift`.

**`Model.swift`**
* Represents a specific model e.g. `UserModel`, `PropertyModel`, `History` etc.
* Does not contain any logic.

**`ViewController.swift`**
* Does not contain any business logic (networking, database, configuring cells, converting variables etc.)
* Can't interact with the `Model`s directly, only with the `ViewModel`s and `View`s.

**`View.swift`**
* Represents a UI element e.g. `GuestDetailsView`, `StretchyHeaderView` etc.
* Does not contain any logic.
* Should not be tight to the view controller in order to be extracted as a framework (if applicable).

**`ViewModel.swift`**
* Contains all the business logic.
* Communicates with the `Coordinators`s, `Model`s and `ViewController`s(with callbacks, ViewModel should not know about ViewController) but can't interact with the UI (`View`s).

**`Coordinator.swift`**
* Contains all transition logic
* Crates new controllers and their view models

![MVVM+Coordinator+MoyaNetworking Architecture](https://user-images.githubusercontent.com/25178444/69136881-5db17480-0ac4-11ea-9a74-329e8a56f5b5.png)

Moya <a href="https://github.com/Moya/Moya">Documentation</a> , <a href="https://www.raywenderlich.com/5121-moya-tutorial-for-ios-getting-started">Tutorial</a>

Snapkit <a href="http://snapkit.io">Documentation</a> , <a href="https://www.raywenderlich.com/3225401-snapkit-for-ios-constraints-in-a-snap">Tutorial</a>

Use the principle of Single Responsible, one class - should perform one action.

For TableView sections Header/Foother use `UITableViewHeaderFooterView`

Similar situation is when the controller has a UIView with some UI components it should be transferred to a separate class that is inherited from the UIView and communicates with the VC

## Managers & Services

There should be clear distinguishing between `Managers` and `Services`. In simple terms:

**Manager** - a singleton object which includes functionality required during the lifetime of the application. We should keep the number of managers to a minimum and ONLY create one when needed.

**Services** - a service is NOT a singleton and serves a very specific purpose. It should be independent of any custom logic and be fully reusable. Most services will eventually be transformed into frameworks (swift packages). E.g. `PDFSplitService`.

## Helpers

`Helpers` are NOT `Services`, `Managers` or `Data Models`. `Helpers` are classes, structs, extensions with the purpose of extracting some basic logic. Typically, one helper is used across multiple classes.

**Helpers CAN include:**
* Extensions (e.g. `String+Split.swift`).
* Methods for converting data.
* Handle configurations (e.g. `Device.swift`)

**Helpers CAN NOT include:**
* Complex networking calls.
* Database interactions.
* Data models.

# Code style rules

## Conventions

### Viewer

For best practice use select some code and use Editor -> Format -> Re - Indent

### Names of types and variables

Follow case conventions. Names of **types and protocols** are UpperCamelCase. Everything else is lowerCamelCase. Acronyms and initialisms that commonly appear as all upper case in US English should be uniformly up- or down-cased
according to case conventions. Other acronyms should be treated as ordinary words.

##### Preferred:

```swift
var linesArray: [Line]
var shape: Shape

var utf8Bytes: [UTF8.CodeUnit]
var isRepresentableAsASCII = true
var userSMTPServer: SecureSMTPServer
```

##### Not Preferred:

```swift
var LinesArray: [Line]
var lines_array: [Line]
var sHaPe: Shape

var UTF8Bytes: [UTF8.CodeUnit]
```

### Sort Values by equal type

##### Preferred:

```swift
var tableView = UITableView()

var headerView = UIView()
var buttonsView = UIView()
var footherView = UIView()

var news: [News]
var sortedNews: [News]
```

##### Not Preferred:

```swift
var headerView = UIView()
var tableView = UITableView()
var buttonsView = UIView()
var news: [News]
var sortedNews: [News]
var footherView = UIView()

```

### Extensions

Use extension to expand classes functionality against global functions.

##### Preferred:

```swift
extension CGRect {

  func doesIntersect(_ anotherRect: CGRect) -> Bool { ... }

}
```

##### Not Preferred:

```swift
// Global Scope

func doRectanglesIntersect(_ rect1: CGRect, _ rect2: CGRect) -> Bool { ... }
```
### Methods names

Methods can share a base name when they share the same basic meaning or when they operate in distinct domains.
Examples:

1. The following is encouraged, since the methods do
essentially the same things.

2. And since geometric types and collections are separate
domains, this is also fine in the same program.

3. These index methods have different semantics, and should
have been named differently.

4. Avoid “overloading on return type” because it causes
ambiguities in the presence of type inference.

##### Preferred:

```swift
// 1
extension Shape {
  func contains(_ other: Point) -> Bool { ... }

  func contains(_ other: Shape) -> Bool { ... }

  func contains(_ other: LineSegment) -> Bool { ... }
}

// 2
extension Collection where Element : Equatable {
  func contains(_ sought: Element) -> Bool { ... }
}
```

##### Not Preferred:

```swift
// 3
extension Database {
  // Rebuilds the database's search index
  func index() { ... }

  // Returns the n`th row in the given table.
  func index(_ n: Int, inTable: TableID) -> TableRow { ... }
}

// 4
extension Box {
  // Returns the `Int` stored in `self`, if any, and `nil` otherwise.
  func value() -> Int? { ... }

  // Returns the `String` stored in `self`, if any, and `nil` otherwise.
  func value() -> String? { ... }
}
```

### Use short context

Use compiler inferred context to write shorter, clear code.

##### Preferred:

```swift
let selector = #selector(viewDidLoad)

view.backgroundColor = .red

let toView = context.view(forKey: .to)

let view = UIView(frame: .zero)
```

##### Not Preferred:

```swift
let selector = #selector(ViewController.viewDidLoad)

view.backgroundColor = UIColor.red

let toView = context.view(forKey: UITransitionContextViewKey.to)

let view = UIView(frame: CGRect.zero)
```

## Naming

### Funcions parameters names

Choose parameter names to serve documentation. Even though parameter names do not appear at a function’s point of use, they play an important explanatory role.

Choose names to make it easy to read.

##### Preferred:
These names make documentation read naturally.

```swift
// Return an `Array` containing the elements of `self` that satisfy `predicate`.
func filter(_ predicate: (Element) -> Bool) -> [Generator.Element]

// Replace the given `subRange` of elements with `newElements`.
mutating func replaceRange(_ subRange: Range, with newElements: [E])
```

##### Not Preferred:

These make the code awkward and ungrammatical.

```swift
// Return an Array containing the elements of self that satisfy includedInResult.
func filter(_ includedInResult: (Element) -> Bool) -> [Generator.Element]

// Replace the range of elements indicated by r with the contents of with.
mutating func replaceRange(_ r: Range, with: [E])
```

### Delegate methods naming

When creating custom delegate methods, an unnamed first parameter should be the delegate source.

##### Preferred:

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

##### Not Preferred:

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```
### Avoid ambiguity naming

Include all the words needed to avoid ambiguity for a person reading code where the name is used.

 Example:
 If we were to omit the word **at** from the function signature, it could imply to the reader that the function searches for and
 removes an element equal to x, rather than using x to indicate the
 position of the element to remove.

##### Preferred:

```swift
extension List {
  func remove(at position: Index) -> Element
}

employees.remove(at: x)
```

##### Not Preferred:

```swift
// unclear: are we removing x?
employees.remove(x)
```

### Omit needless words

Every word in a name should convey useful information at the use site. More words may be needed to clarify intent or disambiguate meaning, but those that are redundant with information the reader already possesses should be omitted. In particular, omit words that merely repeat type information.
Example:
In this case, the word Element adds nothing useful at the call site.

##### Preferred:

```swift
func remove(_ member: Element) -> Element?

allViews.remove(cancelButton)
```

##### Not Preferred:

```swift
func removeElement(_ member: Element) -> Element?

allViews.removeElement(cancelButton)
```

### Name should match the role

Name variables, parameters, and associated types according to their roles.

##### Preferred:

```swift
var greeting = ""Hello""

protocol ViewController {
  associatedtype ContentView : View
}

class ProductionLine {
  func restock(from supplier: Factory)
}

//

let greetingString = ""http://...""
let greetingURL = URL(string: greetingString)
```

##### Not Preferred:

```swift
var string = ""Hello""

protocol ViewController {
  associatedtype ViewType : View
}

class ProductionLine {
  func restock(from factory: Factory)
}

//

let greeting1 = ""http://...""
let greeting2 = URL(string: greeting1)
```

### Selectors naming

Functions that are being used as button selectors directly should be named using the suffix -ButtonAction.

##### Preferred:

```swift
@objc func saveButtonAction() {

}
```

##### Not Preferred:

```swift
@objc func save() {

}
```

### Bool functions naming

Uses of Bool functions and properties should be read as assertions about the receiver when the use is non-mutating. A verb at the beginning of the name is strongly recommended.

##### Preferred:

```swift
x.isEmpty
line1.doesIntersect(line2)
```

##### Not Preferred:

```swift
x.empty
line1.intersects(line2)
```

## Code Organization

### Parentheses around conditionals

Parentheses around conditionals are not required and should be omitted.

##### Preferred:

```swift
if name == ""Hello"" {
  print(""World"")
}
```

##### Not Preferred:

```swift
if (name == ""Hello"") {
  print(""World"")
}
```

### Avoid self

Avoid self. when it's possible. This will make your code cleaner.

##### Preferred:

```swift
tableView.reloadData()
```

##### Not Preferred:

```swift
self.tableView.reloadData()
```

### Avoid useless methods for computed properties

For conciseness, if a computed property is read-only, omit the get clause. The get clause is required only when a set clause is provided.

##### Preferred:

```swift
var diameter: Double {
  return radius * 2
}
```

##### Not Preferred:

```swift
var diameter: Double {
  get {
     return radius * 2
  }
}
```

### Omit needless arguments for target functions.

##### Preferred:

```swift
@objc func saveButtonActtion() {
  // sender is not needed
}
```

##### Not Preferred:

```swift
@objc func saveButtonActtion(_ sender: UIButton) {
  // sender is not needed
}
```

### Ternary operator

Prefer to use ternary operator ? : during assignment of a variable with a simple condition.

##### Preferred:

```swift
label.textColor = isAllowed ? .gray : .red
```

##### Not Preferred:

```swift
if isAllowed {
  label.textColor = .gray
} else {
  label.textColor = .red
}
```

### Method braces

Method braces and other braces (if / switch / while etc.) always open on the same line as the statement but close on a new line.

##### Preferred:

```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

##### Not Preferred:

```swift
if user.isHappy
{
  // Do something
} else
{
  // Do something else
}
```

### Separate methods logic

Whitespaces and comments within methods should separate logic blocks of functionality. But if your function has to many blocks maybe it's a chance to split the logic to make it less complex.

##### Preferred:

```swift
func hugeFunction() {

  //
  // parsing the input data

  [blockOfFunctionality1]

  //
  // filtering data

  [blockOfFunctionality2]

  //
  // sorting results

  [blockOfFunctionality3]
}
```

##### Not Preferred:

```swift
func hugeFunction() {
  [blockOfFunctionality1]
  [blockOfFunctionality2]
  [blockOfFunctionality3]
}
```

### Function declarations

Keep short function declarations on one line including the opening brace.
For functions with long signatures, use line breaks after each parameter and add an extra indent on subsequent lines.

```swift
func shortFunction(_ argument: Int) -> Bool {

}
```

```swift
func longFunction(
    argument1: Int,
    argument2: Double,
    argument3: Float) -> Bool {

}
```

### Closure return value

Don't use (Void) to represent the lack of an input. Use () instead. Only use Void instead of () for closure and function outputs.

##### Preferred:

```swift
let closure: () -> Void
```

##### Not Preferred:

```swift
let closure: (Void) -> Void
let closure: () -> ()
```

### Avoid generic declaration

Prefer the shortcut versions of type declarations over the full generics syntax.

##### Preferred:

```swift
"var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?"
```

##### Not Preferred:

```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

### Use weak self for closures

[Apple documentation](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html) article which describes how to use weak and unowned.

Extend object lifetime using [weak self] and guard let self = self else { return } idiom.

[weak self] is preferred over [unowned self] where it is not immediately obvious that self outlives the closure.

Global closures don't capture variables. In this case you don't have to use [weak self] or [unowned self], except completion handlers, since there is no guarantee that self will be still in the memory on closure call.

Explicitly extending lifetime is preferred to optional chaining.

##### Preferred:

```swift
resource.request().onComplete { [weak self] response in
  guard let self = self else { return }

  let model = self.updateModel(response)
  self.updateUI(model)
}
```

##### Not Preferred:

```swift
resource.request().onComplete { response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```

## For-Where Loops

##### Preferred:
```swift
for item in collection where item.hasProperty {
  // ...
}
```

##### Not Preferred:

```swift
for item in collection {
  if item.hasProperty {
    // ...
  }
}
```

## Guard

Use Guard instead of if conditions where is possible

##### Preferred:

```swift
func doSmth() {
   guard isLogin else {
      return
   }

   // some actions
}

```

##### Not Preferred:

```swift
func doSmth() {
   if isLogin {
      // some actions
   }
}
```

## Use Defaults and optional values in method

##### Preferred:

```swift
func doSmth(x: Int? = nil, y: Int? = nil) {
    print(x)
    print(y)
}

doSmth()
doSmth(x:5)
doSmth(y:7)
doSmth(x:5, y:7)

```

##### Not Preferred:

```swift
func doSmth() {
    print(x)
    print(y)
}

func doSmth(x: Int? = nil) {
    print(x)
    print(y)
}

....

doSmth()
doSmth(x:5)
doSmth(y:7)
doSmth(x:5, y:7)
```

## Access Level

Omitting an explicit access level is permitted on declarations. For top-level declarations, the default access level is internal. For nested declarations, the default access level is the lesser of internal and the access level of the enclosing declaration.

Specifying an explicit access level at the file level on an extension is forbidden. Each member of the extension has its access level specified if it is different than the default.

##### Preferred:

```swift
extension String {
  public var isUppercase: Bool {
    // ...
  }

  public var isLowercase: Bool {
    // ...
  }
}
```

##### Not Preferred:

```swift
public extension String {
  var isUppercase: Bool {
    // ...
  }

  var isLowercase: Bool {
    // ...
  }
}
```

## Tuple Patterns

Assigning variables through a tuple pattern (sometimes referred to as a tuple shuffle) is only permitted if the left-hand side of the assignment is unlabeled.


##### Preferred:

```swift
let (a, b) = (y: 4, x: 5.0)
```

##### Not Preferred:

```swift
let (x: a, y: b) = (y: 4, x: 5.0)
```


## Literals

Don't use literals as some property

##### Preferred:

```swift
imageView.image = #ImageLiteral()
```

##### Not Preferred:

```swift
let image = #ImageLiteral()
imageView.image = image
```

## Unused Code

Remove all unused delegate, protocol method

## Separete Functionality

Protocols should be implemented in separate extensions.
Class should include only the code that describes inner functionality.
Add "// MARK: -  some extensions" for define extension

##### Preferred:

```swift
class ProfileVC: UIViewController {

}

extension ProfileVC: UITableViewDelegate {

}
```

##### Not Preferred:

```swift
class ProfileVC: UIViewController, UITableViewDelegate {

}
```

## Use multiple optional binding

Use multiple optional binding in an if let statement where possible to avoid the pyramid of doom:

### Prefered

```swift
if let id = jsonObject[Constants.id] as? Int,
    let name = jsonObject[Constants.name] as? String,
    let initials = jsonObject[Constants.initials] as? String {
    // Flat
    let user = User(id: id, name: name, initials: initials)
    // ...
}
```

##### Not Preferred:

```swift
if let id = jsonObject[Constants.id] as? Int {
    if let name = jsonObject[Constants.name] as? String {
        if let initials = jsonObject[Constants.initials] as? String {
            // Deep nesting
            let user = User(id: id, name: name, initials: initials)
            // ...
        }
    }
}

```

## Marks

Separate code struct by Marks if needed

```swift
// MARK: - Network Requests

... some methods

// MARK: - Update UI

... some methods

```

## Assets

Assets include **Images**, **Fonts** and **Colors**.

### Images

All images are stored inside a single `.xcassets` folder for each target.
- Main app assets folder path: `~Assets/Images.xcassets`

1. Assets folder has subgroups for individual components in the app, based on the `Application` folder structure.
2. Naming the images must be done using the existing notation: `<tag>_<first word>_<second word>` where tag is `icon` for icons and `image` for larger images
3. For icons and flat vector images always use `.pdf`. For complex images and illustrations use `.png` (1x size), `@2x.png` (2x size) and `@3x.png` (3x size).

### Fonts

All fonts are located inside their specific extensions, so you can obtain it easily from there.

For example we will use **SFCompactDisplay** font.

**HOW TO ADD A NEW FONT?**

To add a new font, download it using the `.otf` extension, either from the web or from the design team. (Always make sure the font is free or we have a license).

**Note**
*If it is not possible to find files with `.otf` extension but you can find `.ttf` font you can just change extension in Finder and click
"Use .otf" in the alert you will see.*

1) Drag and drop all new fonts to the Font folder with the next path *~Assets/Fonts*
2) Add all new fonts to the target's plist app to the main dictionary. Then app will know about new fonts in the project.

```xml
<key>UIAppFonts</key>
<array>
     <string>SF-Compact-Display-Semibold.otf</string>
     ...
     <string>SF-Compact-Display-Medium.otf</string>
</array>
```

**HOW TO USE AN EXISTING FONT**

**Note**
*Sometimes when you try to use your custom font your app will return nil because it could not find Font Family with the font file name.
To resolve this problem you need to check all possible font families in the app and look for the font you just added and his real name.*

To check all possible font families throught the code you can use next code in didFinishLaunchWithOption function:

```swift
 UIFont.familyNames.forEach({ familyName in
    let fontNames = UIFont.fontNames(forFamilyName: familyName)
     print(familyName, fontNames)
 })
```
Since we have custom font we must create UIFont extension with next template: **UIFont+<FontFamilyName>**

- UIFont+SFCompactDisplay.swift

In UI font extension we need to add next code:

```swift
 extension UIFont {
     // 1
     enum SFCompactFontWeight: String {
         case regular = "Regular"
         case bold = "Bold"
         case medium = "Medium"
         case semibold = "Semibold"
     }
     // 2
     /**
      Creates SFCompactDisplay font.

      - Parameter size: The size of the font.
      - Parameter weight: The weight of the font.

      - Returns: An instance of SFCompactDisplay font, if it exist, otherwise a system font.
      */
     static func sfCompactDisplay(ofSize size: CGFloat, weight: SFCompactFontWeight = .regular) -> UIFont {
         return UIFont(name: "SFCompactDisplay-\(weight.rawValue)", size: size) ?? .systemFont(ofSize: size, weight: .regular)
     }
 }
```

// 1
For *SFCompactFont*. font have different weights, then you need to add enum with possible font's weights.
// 2
Create method which will return an instance of the font using UIFont inirializator with font name and size

**Example**

`SFCompactDisplay` font with UILabel:

```swift
 lazy var subtitleLabel = UILabel().then {
     $0.font = .sfCompactDisplay(ofSize: Device.IS_4_7_INCHES_OR_SMALLER() ? 12 : 14)
     $0.textColor = .blueyGrey
     $0.numberOfLines = 0

     rightView.addSubview($0)
 }
```

**HOW TO DELETE AN EXISTING FONT?**

1) Update with new font all places in code where the font is used.
2) Delete font extension.
3) Delete font `.otf` files from folder **Fonts**.
4) Delete font name from plist file

### Colors

The project must have separate **.xcassets** folder for app colors, which will help to see which color is added to be sure that we don't have duplicates. [Here](https://blog.zeplin.io/asset-catalog-colors-on-xcode-9-c4fdccc0381a) is an article how to add colors to **.xcasset** folder.

<img width="1348" alt="Screen Shot 2019-09-19 at 10 51 13" src="https://user-images.githubusercontent.com/25178444/65224470-e95e5300-dacb-11e9-807d-8b8e341c4e2e.png">

---

### Authors

* [Anton Shepetuha](https://github.com/Ferdens)
* [Oleg Mytsovda](https://github.com/olegek)
* [Valentyn Mialin](https://github.com/olegek)

### Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-awesome-update`)
3. Commit your changes (`git commit -am 'Added a cool update'`)
4. Push to the branch (`git push origin my-awesome-update`)
5. Create new Pull Request