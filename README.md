# PlantUML for Event Modeling

This repository provides a simple yet powerful PlantUML library to create diagrams using the **Event Modeling** methodology. The goal is to enable teams to quickly and consistently visualize system behavior as a chronological storyboard, focusing on the flow of information over time.

This library is inspired by the work of [Adam Dymitruk](https://eventmodeling.org/) and the clear, step-by-step examples from [Martin Dilger](https://www.linkedin.com/in/martindilger/).

## Overview

Event Modeling is a method of describing a system by illustrating the flow of information through it. It visualizes the entire system on a single diagram, making it a powerful tool for collaboration between business stakeholders and technical teams.

This PlantUML library provides macros to easily create the core building blocks and patterns of an Event Model.

## Core Building Blocks

The `styling.puml` library file defines macros for the essential elements of an Event Model, each with its standard color and purpose:

  * **Trigger** (White): Represents a UI or API endpoint where a user or system initiates an action.
  * **Command** (Blue 🔵): An intention to change the state of the system.
  * **Event** (Orange 🟠): A factual record of something that has happened and has been saved. This is the source of truth.
  * **Read Model** (Green 🟢): A projection or query of one or more events, tailored for a specific view or need.
  * **Automation** (Gear Icon ⚙️): A system-driven, automated process, often triggered by a Read Model.
  * **EventModel\_Slice** (Package): A container that represents a single, vertical slice of functionality in the chronological storyboard.

## How to Use

1.  **Clone or Download the Repository**: Get the `styling.puml` file and the example `.puml` files.
    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    ```
2.  **Include the Library**: At the top of your PlantUML diagram file, add the following line to include the styles and macros:
    ```plantuml
    !include styling.puml
    ```
3.  **Generate Diagrams**: Use a local PlantUML installation or an online tool to render your `.puml` files into images. See the "Offline Rendering" section below for a recommended local setup.

## Creating Your Own Event Models

The library makes it simple to create new diagrams by using the provided macros. The key is to structure your model as a series of chronological slices.

### 1\. Adding Elements

Use the macros to define the building blocks of your system. Each macro follows the format `ElementType(alias, "Display Label")`.

```plantuml
' Define elements for a user registration flow
Trigger(regForm, "Registration UI")
Command(regCmd, "Register User")
Event(userRegistered, "User Registered")
Read_Model(profileView, "User Profile View")
```

### 2\. Structuring with Slices

Group related elements into a vertical `EventModel_Slice`. This represents one step in your system's story.

```plantuml
EventModel_Slice("Register User", registerSlice) {
  Trigger(regForm, "Registration UI")
  Command(regCmd, "Register User")
  Event(userRegistered, "User Registered")
}
```

### 3\. Defining Information Flow

Use the `Flow()` macro to draw relationships between elements. This macro handles optional labels correctly.

```plantuml
' A user interacts with a form, which issues a command
Flow(user, regForm)
Flow(regForm, regCmd, "Submits")

' A command is handled by a system and produces an event
Flow(regCmd, authSystem, "Handled by")
Flow(authSystem, userRegistered, "Produces")
```

## Complete Example: Library System

This example models the process of registering and borrowing a book in a library. It demonstrates the use of all core concepts, including command patterns, view patterns, and automation.

```plantuml
@startuml Library Example
!include styling.puml

left to right direction

' --- Actors & External Systems ---
actor "Library\nClerk" as clerk
actor "Customer" as customer

' --- SLICE 1: Register a new Book ---
EventModel_Slice("Register Book", registerBook) {
  Trigger(regBookForm, "Register Book UI")
  Command(regBookCmd, "Register Book")
  Event(bookRegistered, "Book Registered")
}

' --- SLICE 2: Publish a Book ---
EventModel_Slice("Publish Book", publishBook) {
  Read_Model(allBooksView, "All Books")
  Trigger(publishBookBtn, "Publish Button")
  Command(publishBookCmd, "Publish Book")
  Event(bookPublished, "Book Published")
}

' --- SLICE 3: Customer Reserves a Book ---
EventModel_Slice("Reserve Book", reserveBook) {
  Read_Model(booksToBorrow, "Books To Borrow")
  Trigger(reserveBookBtn, "Reserve Button")
  Command(reserveBookCmd, "Reserve Book")
  Event(bookReserved, "Book Reserved")
}

' --- SLICE 4: Pick up a reserved Book ---
EventModel_Slice("Pickup Book", pickupBook) {
  Read_Model(reservedBooks, "Reserved Books")
  Automation(scanner, "Manual Pickup")
  Command(pickupBookCmd, "Pickup Book")
  Event(bookBorrowed, "Book Borrowed")
}

' --- Define Relationships (All at the end for stability) ---

' Flow within Slice 1
clerk --> regBookForm
regBookForm --> regBookCmd
regBookCmd --> bookRegistered

' Flow within Slice 2
bookRegistered --> allBooksView
allBooksView --> publishBookBtn
clerk --> publishBookBtn
publishBookBtn --> publishBookCmd
publishBookCmd --> bookPublished

' Flow within Slice 3
bookRegistered --> booksToBorrow
bookPublished --> booksToBorrow
booksToBorrow --> reserveBookBtn
customer --> reserveBookBtn
reserveBookBtn --> reserveBookCmd
reserveBookCmd --> bookReserved

' Flow within Slice 4
bookReserved --> reservedBooks
reservedBooks --> scanner
scanner --> pickupBookCmd
pickupBookCmd --> bookBorrowed

@enduml
```

## Steps to Render Diagrams in VSCode with Full Offline Setup

If using online rendering tools isn't an option, here's how you can ensure that your diagrams render correctly in VSCode using the **PlantUML** extension.

1.  **Install PlantUML Extension in VSCode**:

      * Open VSCode and go to the Extensions view (`Ctrl+Shift+X` or `Cmd+Shift+X` on Mac).
      * Search for "PlantUML" and install the extension by **jebbs**.

2.  **Install Java**:

      * The PlantUML extension requires **Java** to run locally.
      * Download and install a Java Development Kit (JDK), version 8 or higher.

3.  **Install Graphviz**:

      * Graphviz is required for PlantUML to render complex diagrams.
      * You can download Graphviz from its [official site](https://graphviz.gitlab.io/download/).

4.  **Configure PlantUML Extension Settings**:

      * Open VSCode settings (`Ctrl+,` or `Cmd+,` on Mac).
      * Search for `PlantUML` in the settings.
      * Ensure that `plantuml.render` is set to `Local`. This will tell VSCode to use the local Java and Graphviz installations.

5.  **Render Diagrams in VSCode**:

      * Open your `.puml` file in VSCode.
      * To view a live preview of the diagram, press `Alt+D` (`Option+D` on Mac).

## License

This project is available for use under the [MIT License](https://www.google.com/search?q=LICENSE).
