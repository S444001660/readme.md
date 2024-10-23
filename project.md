# **To-Do List App in Swift with SwiftUI & Core Data**

This guide walks you through building a **To-Do List** application using **Swift**, **SwiftUI**, and **Core Data**. You will learn how to create a task manager that stores tasks with emojis and titles, allows task creation and deletion, and uses images for enhanced visualization. 

We will include:
- **Core Data** for task persistence.
- **SwiftUI** for the user interface.
- **GitHub** for version control.
- **Emojis** and **images** to make the app more interactive.

---

## **Project Overview**

### **Features:**
1. Create tasks with **titles** and **emojis**.
2. Display tasks in a list with options to **delete** tasks.
3. Store tasks persistently using **Core Data**.
4. Use **GitHub** for version control and collaboration.
5. Add **images** for visual elements (such as task icons or screenshots).
6. Use **SwiftUI** for building a modern user interface with ease.

### **Requirements:**
- **Xcode 12 or later**
- **SwiftUI**
- **Core Data**
- Basic knowledge of Swift and UI design.

---

## **Step 1: Set Up the Xcode Project**

### **1.1 Create a New Xcode Project**

1. Open **Xcode**.
2. Select **Create a new Xcode project**.
3. Choose **App** under **iOS**.
4. Set **SwiftUI** as the interface and enable **Core Data**.
5. Name your project something like **ToDoListApp**.
6. Choose a location to save your project and click **Create**.

---

## **Step 2: Configure Core Data**

### **2.1 Set Up the Core Data Model**

1. In the Project Navigator, click on **ToDoListApp.xcdatamodeld** to open the data model editor.
2. **Create an entity**:
   - Click the "+" button at the bottom of the left-hand panel.
   - Name the entity `Task`.
3. **Add attributes** to the `Task` entity:
   - **`title`**: String (for the task title).
   - **`emoji`**: String (to store an emoji associated with the task).

---

## **Step 3: Building the User Interface with SwiftUI**

### **3.1 Setting Up ContentView.swift**

In this section, we’ll create the UI to display tasks, add new tasks with emojis, and delete tasks.

1. Open `ContentView.swift` and replace the default content with the following code:

```swift
import SwiftUI
import CoreData

struct ContentView: View {
    @Environment(\.managedObjectContext) private var viewContext
    @FetchRequest(
        sortDescriptors: [NSSortDescriptor(keyPath: \Task.title, ascending: true)],
        animation: .default)
    private var tasks: FetchedResults<Task>
    
    @State private var newTaskTitle: String = ""
    @State private var newTaskEmoji: String = ""

    var body: some View {
        NavigationView {
            VStack {
                HStack {
                    TextField("Enter Task Title", text: $newTaskTitle)
                        .textFieldStyle(RoundedBorderTextFieldStyle())
                        .padding()
                    
                    TextField("Enter Emoji", text: $newTaskEmoji)
                        .textFieldStyle(RoundedBorderTextFieldStyle())
                        .padding()
                    
                    Button(action: addTask) {
                        Text("Add Task")
                    }
                    .padding()
                }
                
                List {
                    ForEach(tasks) { task in
                        HStack {
                            Text(task.emoji ?? "📝")
                            Text(task.title ?? "")
                        }
                    }
                    .onDelete(perform: deleteTasks)
                }
                .navigationTitle("To-Do List")  // App title
                .toolbar {
                    EditButton()
                }
            }
        }
    }
    private func addTask() {
        withAnimation {
            let newTask = Task(context: viewContext)
            newTask.title = newTaskTitle
            newTask.emoji = newTaskEmoji

            do {
                try viewContext.save()
                newTaskTitle = ""
                newTaskEmoji = ""
            } catch {
                print("Error saving task: \(error.localizedDescription)")
            }
        }
    }
    private func deleteTasks(offsets: IndexSet) {
        withAnimation {
            offsets.map { tasks[$0] }.forEach(viewContext.delete)
            do {
                try viewContext.save()
            } catch {
                print("Error deleting task: \(error.localizedDescription)")
            }
        }
    }
}
```

### **3.2 Explanation of the UI Elements**

- **TextField for Task Title**: Allows the user to input a task title.
- **TextField for Emoji**: Allows the user to input an emoji that represents the task.
- **Button ("Add Task")**: Triggers the `addTask` function to save the new task to Core Data.
- **List**: Displays the tasks in the database with their title and emoji.
- **EditButton**: Allows the user to delete tasks from the list.

---

## **Step 4: Configure Core Data in the App**

### **4.1 Modify ToDoListApp.swift**

1. Open the file `ToDoListApp.swift`.
2. Replace its content with the following to properly configure Core Data in your app:

```swift
import SwiftUI

@main
struct ToDoListApp: App {
    let persistentContainer = NSPersistentContainer(name: "ToDoListApp")

    init() {
        persistentContainer.loadPersistentStores { description, error in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        }
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(\.managedObjectContext, persistentContainer.viewContext)
        }
    }
}
```

### **Explanation:**

- The `persistentContainer` is responsible for managing the Core Data stack, loading the model, and providing access to the database.
- The `ContentView` is provided with the `managedObjectContext`, which it will use to interact with Core Data.

---

## **Step 5: Running the Application**

1. **Build** and **Run** the application on a simulator or a real device.
2. **Test the functionality**:
   - Add new tasks with emojis.
   - Delete tasks.
   - Ensure that the tasks are persisted between app launches.

---

## **Step 6: Version Control with GitHub**

### **6.1 Initializing a GitHub Repository**

Once the app is functional, you can upload the project to GitHub for version control and collaboration.

1. Open **Terminal**.
2. Navigate to the project directory.
3. Run the following commands:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <YOUR_GITHUB_REPO_URL>
git push -u origin main
```

### **6.2 Push Changes to GitHub**

After making changes, push them to GitHub:

```bash
git add .
git commit -m "Updated the app with new features"
git push
```

---

## **Step 7: Potential Extensions and Improvements**

### **7.1 Task Prioritization**

- Allow users to mark tasks as **high, medium, or low priority**.
- You can create a separate attribute in Core Data for `priority` and add color-coded labels in the UI.

### **7.2 Task Deadlines and Notifications**

- Add deadlines to each task.
- Use **Local Notifications** to remind users of upcoming or overdue tasks.

### **7.3 User Authentication**

- Integrate **Firebase** or **Sign in with Apple** to allow users to create accounts and sync tasks across devices.

---

## **Conclusion**

By following this guide, you’ve built a fully functional **To-Do List** app with **Core Data**, **SwiftUI**, and **

emojis**. This app can be further expanded by adding more features, such as task categorization, prioritization, and notifications. It also supports version control using GitHub for tracking changes and collaborating on the project.

If you have any questions or need further assistance, feel free to ask!

---
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg5MTE1NjczMV19
-->