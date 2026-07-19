# R&D Document: Python Todo App

## 1. Project Overview

This project is a lightweight, browser-based todo application built as a small full-stack prototype. The application allows users to create, view, complete, edit, and delete tasks through a simple web interface. The implementation emphasizes clarity and minimal dependencies rather than enterprise-scale architecture.

From a product perspective, the app serves as a practical demonstration of how a basic client-server application can be built using:

- Python for the backend server
- HTML, CSS, and JavaScript for the frontend experience
- MySQL for persistent task storage

The current implementation is best understood as a local development prototype or educational example rather than a production-ready application.

## 2. Objectives

The primary objectives of the project are:

- Provide a simple todo list experience in the browser
- Demonstrate CRUD-style functionality through a web interface
- Persist task data in a relational database
- Keep the architecture easy to read and easy to run locally
- Use only minimal libraries and standard web technologies

## 3. Scope and Functional Goals

The current feature set includes:

- Adding a new task
- Displaying all tasks from storage
- Marking tasks as complete or incomplete
- Editing task titles
- Deleting tasks
- Showing modal dialogs for confirmation and input

The application does not currently include advanced features such as:

- User authentication
- Task categories or priorities
- Due dates or reminders
- Search and filtering
- Sync across devices
- API versioning or authentication layers

## 4. Architectural Summary

The project follows a simple three-layer architecture:

1. Presentation layer
   - Managed by [index.html](index.html), [style.css](style.css), and [script.js](script.js)
   - Handles the UI and browser-side behavior

2. Application layer
   - Managed by [server.py](server.py)
   - Receives HTTP requests, routes them, and delegates work to the data layer

3. Data layer
   - Managed by [db.py](db.py)
   - Connects to MySQL and performs CRUD operations

At a high level, the flow is:

- The browser requests the app shell
- The server returns static assets
- JavaScript calls backend endpoints
- The backend uses database functions to read or update task records
- The UI refreshes after each operation

## 5. Folder and File Structure

The project contains the following key files:

- [index.html](index.html): Main app page and modal UI shell
- [style.css](style.css): Styling for layout, buttons, task rows, and modal dialogs
- [script.js](script.js): Client-side logic for loading, creating, updating, toggling, and deleting tasks
- [server.py](server.py): HTTP server and request routing logic
- [db.py](db.py): MySQL connection management and database operations
- [test.py](test.py): Basic database connectivity smoke test
- [TODO_APP_OVERVIEW.md](TODO_APP_OVERVIEW.md): Existing project overview documentation

## 6. Module Responsibilities

### 6.1 [index.html](index.html)

This file defines the page structure for the app. It contains:

- The title and main container
- The input field for adding tasks
- The task list container
- The modal dialog used for editing and confirmation flows

It acts as the root UI document and loads the CSS and JavaScript assets.

### 6.2 [style.css](style.css)

This file provides the visual styling for the application. It handles:

- Page background and spacing
- The input row and task list layout
- Button colors and icon styling
- Completed-task visual state
- Responsive adjustment for mobile screens
- Modal dialog appearance and transitions

### 6.3 [script.js](script.js)

This file contains the browser-side application logic. Its responsibilities include:

- Loading tasks from the backend on page load
- Rendering task cards dynamically into the page
- Sending POST requests for create, toggle, edit, and delete actions
- Showing modal dialogs for confirmation and task editing
- Binding UI event handlers such as Enter key submission and modal interactions

The script uses the browser Fetch API to communicate with the backend over HTTP.

### 6.4 [server.py](server.py)

This file implements the Python HTTP server. It is responsible for:

- Serving the front-end files at routes such as `/`, `/style.css`, and `/script.js`
- Handling JSON-based API endpoints for task operations
- Delegating work to database functions from [db.py](db.py)
- Initializing the database schema when the server starts

The server uses Python’s built-in `http.server` module rather than a web framework.

### 6.5 [db.py](db.py)

This file forms the persistence layer. It is responsible for:

- Establishing a connection to MySQL
- Ensuring the database and `tasks` table exist
- Reading all tasks
- Inserting new tasks
- Updating completion status
- Editing task titles
- Deleting tasks

The module centralizes all database interaction so the server code remains simple and focused on request handling.

### 6.6 [test.py](test.py)

This file performs a minimal connectivity check by importing the database connection helper and verifying that a MySQL connection can be established.

## 7. Workflow

### 7.1 Startup Workflow

1. The user launches the server by running the Python entry point.
2. [server.py](server.py) initializes the database schema via `setup_database()`.
3. The server begins listening on `localhost:8000`.
4. The browser loads the app from `/` and receives the HTML, CSS, and JavaScript assets.

### 7.2 Initial Load Workflow

1. The page loads in the browser.
2. [script.js](script.js) calls `GET /tasks`.
3. [server.py](server.py) invokes the database layer.
4. [db.py](db.py) queries the MySQL database.
5. The resulting task list is returned as JSON to the browser.
6. The client renders the task list visually.

### 7.3 Task Manipulation Workflow

When a user adds, edits, completes, or deletes a task:

1. The browser sends a POST request to the corresponding endpoint.
2. The backend receives the request and extracts JSON payload data.
3. The backend calls the appropriate database helper.
4. The database layer updates MySQL.
5. The frontend reloads the task list to reflect the latest state.

## 8. Technologies and Dependencies

### 8.1 Frontend Technologies

- HTML5 for page structure
- CSS3 for visual styling and responsive behavior
- JavaScript for DOM manipulation and HTTP requests
- The Fetch API for asynchronous communication with the backend

### 8.2 Backend Technologies

- Python 3
- Python standard library module `http.server`
- JSON handling for request and response payloads

### 8.3 Data Technologies

- MySQL relational database
- `mysql.connector` for Python-to-MySQL communication

### 8.4 Dependency Notes

The project uses a small dependency footprint. The most important dependency is the MySQL connector library, which is imported in [db.py](db.py). No build system, framework, or package manifest appears to be present in the repository snapshot.

## 9. Data Model

The application uses a single table named `tasks`.

### Suggested logical schema

- `id`: integer primary key, auto-incremented
- `title`: string task description, required
- `completed`: boolean-like field stored as `TINYINT(1)` with default `0`

This schema is sufficient for the present feature set and supports the basic task lifecycle.

## 10. Data Flow Design

### 10.1 Fetch Tasks

- Browser calls `GET /tasks`
- Server calls `get_tasks()`
- Database returns rows ordered by descending ID
- JSON is sent back to the client

### 10.2 Add Task

- Browser sends `{ "title": "..." }`
- Server calls `add_task(title)`
- Database inserts a new row into `tasks`
- Confirmation response is returned

### 10.3 Toggle Completion

- Browser sends `{ "id": 123 }`
- Server calls `toggle_task(task_id)`
- Database flips the `completed` value using `NOT completed`

### 10.4 Edit Task

- Browser sends `{ "id": 123, "title": "new title" }`
- Server calls `update_task(task_id, title)`
- Database updates the existing row

### 10.5 Delete Task

- Browser sends `{ "id": 123 }`
- Server calls `delete_task(task_id)`
- Database removes the matching row

## 11. Coding Practices and Implementation Style

The codebase demonstrates a straightforward, educational style with clear separation between UI, server, and database responsibilities.

### Strengths

- The code is easy to follow and concise
- Each layer has a clear responsibility
- Database access is centralized in one module
- The frontend is simple and dependency-light
- The implementation uses parameterized SQL queries, which is a good practice for preventing SQL injection

### Observations

- The design favors simplicity over abstraction
- The server uses a single handler class and inline route logic
- The database layer directly calls `setup_database()` before each operation
- Error handling is minimal and mostly absent beyond basic input validation on the client
- The project does not include automated unit or integration tests beyond a simple connection check

## 12. Limitations and Risks

Although the project is functional, it has several limitations:

- It is designed for local development rather than production deployment
- It uses hard-coded MySQL credentials, which is not suitable for shared or secure environments
- The server is a simple single-process implementation and does not support high concurrency or advanced scaling
- There is no authentication or authorization layer
- There is no explicit handling for database connection failures beyond the basic flow
- The interface is lightweight but does not provide advanced task management features
- There is no formal test suite, CI pipeline, or packaging structure

## 13. Security and Operational Considerations

The current implementation is suitable for a demo or learning environment, but it should be treated carefully if used beyond that context.

Key concerns include:

- Hard-coded database credentials
- Lack of environment-based configuration
- Absence of user authentication
- Limited server hardening and logging
- Minimal validation and error reporting at the backend layer

## 14. Future Improvement Opportunities

The project has strong potential as a foundation for a more mature application. Recommended next steps include:

### Product Improvements

- Add user accounts and authentication
- Support task categories, priorities, and due dates
- Add filtering, sorting, and search capabilities
- Improve the UX with drag-and-drop or inline editing

### Technical Improvements

- Replace the custom HTTP server with a framework such as Flask or FastAPI
- Introduce a formal API structure with better separation of routing and business logic
- Add automated unit and integration tests
- Move configuration to environment variables
- Add Docker support for easier setup and deployment
- Improve error handling and logging
- Introduce pagination or database indexing for larger datasets

### Architectural Improvements

- Introduce a service layer between the server and data access layer
- Separate concerns more strongly between request parsing, business rules, and persistence
- Support asynchronous or multi-threaded request handling for better scalability

## 15. Conclusion

This project is a clear example of a minimalist full-stack application built with Python, MySQL, and vanilla web technologies. It successfully demonstrates how a browser-based todo app can be implemented with a simple server and a relational database.

Its strengths lie in simplicity, readability, and ease of local setup. Its main weaknesses are the lack of production-readiness features, limited testing, and the use of a very lightweight server architecture. As a learning project or prototype, it is effective; as a production product, it would require substantial hardening and feature expansion.

Overall, the codebase is a solid foundation for further development and can be evolved into a more robust task management system with moderate effort.
