# Step 1 — Create the Mini CRM Foundation

Build a clean and modern Mini CRM application.

The application manages customers and their scheduled activities.

Requirements:

- Create a responsive layout.
- Add a Home page.
- Add a Customers page.
- Define a customer model containing:
  - id
  - name
  - company (optional)
  - email (optional)
  - phone (optional)
  - notes (optional)
- Define an activity model containing:
  - id
  - customerId
  - title
  - description (optional)
  - dueDate
  - completed
- Use mock data stored in memory.
- Keep the code modular and easy to extend.

---

# Step 2 — Implement Customer Management

Implement the Customers page.

Features:
- Display all customers in a searchable list.
- Add a new customer.
- Edit an existing customer.
- Delete a customer.
- Clicking a customer opens its details.
- Use reusable forms and components.
- Keep the UI simple and modern.

---

# Step 3 — Build the Activity Dashboard

Implement the Home page as a task dashboard.

Features:

- Show all upcoming activities sorted by due date (ascending).
- Display the associated customer for every activity.
- Each activity must provide the following actions:
  - Mark as completed
  - Postpone (choose another date)
  - Delete
- Completed activities should be visually distinguished.
- Keep the list automatically ordered after every modification.

---

# Step 4 — Add Quick Activity Creation

Implement a quick activity creation panel directly on the Home page.

Requirements:

- Add a customer search field with instant filtering.
- Allow selecting a customer quickly.
- Enter:
  - activity title
  - optional description
  - due date
- Save the activity.
- After creation:
  - immediately update the dashboard
  - insert the activity in the correct chronological position
  - clear the form automatically.
- Keep the workflow optimized for speed so users can create activities in just a few clicks.
- Make all legible in terms of color used for selected or not

---

# Step 5 — Make the Dashboard Cards Interactive

Improve the dashboard by making all four statistic cards clickable.

Requirements:

- Each dashboard card must behave like an interactive button with hover and click animations.
- Clicking a card should open a detailed view related to that metric.

Card behavior:

### Customers
- Display the complete customer list.
- Allow selecting a customer to open its details.

### Total Activities
- Display every activity, regardless of status.
- Keep activities ordered by due date.
- Allow filtering and searching.

### Pending Activities
- Display only activities that are not completed.
- Allow:
  - Mark as completed
  - Postpone
  - Delete

### Completed Activities
- Display only completed activities.
- Allow:
  - Reopen the activity (mark as pending)
  - Delete

General requirements:

- The transition between the dashboard and the detailed view should be smooth.
- Add a breadcrumb or back button to return to the dashboard.
- The dashboard statistics must automatically refresh after every action.
- Keep the UI consistent with the rest of the application.
- Avoid page reloads by updating the interface reactively.

---
