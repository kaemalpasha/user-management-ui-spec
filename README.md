# User Management Screen – UI Specification

## 1. Overview

This document describes the **User Management screen** UI for developers. It covers all components, behaviors, initial state, form validations, and interaction flows required to implement the screen.

---

## 2. Initial State

When the page loads:

- **User List Table** (left side) displays all users.
- **Columns**: ID, User Name, Email, Enabled (true/false).
- Table supports:
  - Sortable columns (by ID, User Name, Email, Enabled).
  - Row selection highlighting the active user.

- **Control Buttons/Checkboxes** (top-left):
  - `+ New User` button — resets form for creating a new user.
  - `Hide Disabled User` checkbox — toggles visibility of users with `Enabled = false`.

- **New User Form** (right side) initially shows fields empty and disabled until a user is selected or new user is initiated. Fields include:
  - Username
  - Display Name
  - Phone
  - Email
  - User Roles (multi-select dropdown)
  - Enabled (checkbox)
  - `Save User` button (disabled until form is dirty or valid).

---

## 3. UI Components & Behavior

### 3.1 `+ New User` Button
- **Action**: On click:
  1. Clears form fields.
  2. Sets form mode to "Create".
  3. Enables form inputs.
- **Visual**: Form resets to blank, `Save User` becomes enabled when form is valid.

### 3.2 `Hide Disabled User` Checkbox
- **Action**: On toggle:
  - **Checked**: Filter table to show only users where Enabled = true.
  - **Unchecked**: Show all users.
- **Immediate Effect**: Table refreshes instantly without page reload.

### 3.3 User List Table
- **Columns**: ID (#), User Name, Email, Enabled.
- **Sorting**: Click column header to sort ascending/descending.
- **Row Selection**:
  - Highlights selected row.
  - Loads selected user into form (edit mode).

### 3.4 User Form (Right Panel)

#### Fields
| Field         | Type           | Required | Notes |
|---------------|----------------|----------|-------|
| Username      | Text input     | Yes      | Unique, 3–30 chars |
| Display Name  | Text input     | Yes      | 1–50 chars |
| Phone         | Text input     | No       | International format |
| Email         | Text input     | Yes      | Valid email format |
| User Roles    | Multi-select   | Yes      | Options: Guest, Admin, SuperAdmin |
| Enabled       | Checkbox       | Yes      | Default = true |

#### Buttons
- `Save User`
  - Disabled until all required fields are valid.
  - **Create Mode**: POST to `/api/users`.
  - **Edit Mode**: PUT to `/api/users/{id}`.
  - On success: Table refresh, highlight saved user, show success toast.
  - On failure: Show error message near top of form.

- `Cancel` (optional; if included)
  - When clicked: 
    - In create mode: Clears form and disables it.
    - In edit mode: Reverts changes to original user data.

### 3.5 Form Validation
- **Client-side** validation before saving:
  - Required fields must be filled.
  - Email must match `^\S+@\S+\.\S+$`.
  - Username length 3–30 chars; Display Name 1–50 chars.
- Errors displayed inline (red text under fields).
- `Save User` remains disabled until all validations pass.

---

## 4. Interaction Flows

### Flow A — Create New User
1. Click `+ New User`.
2. Form becomes editable; fields blank.
3. Enter all required data; select roles; check 'Enabled'.
4. Click `Save User`.
5. On success:
   - New user appears in table.
   - Row automatically selected and highlighted.
   - Form fields populated with saved data.
6. On error:
   - Display inline or banner error; do not close form.

---

### Flow B — Edit Existing User
1. Click a row in the user table.
2. Form populated with selected user's data.
3. Make edits.
4. Click `Save User`.
5. On success:
   - Table updated; row persists selected.
   - Success toast appears.
6. On error:
   - Show error; form remains for correction.

---

### Flow C — Hide Disabled Users
- Toggle `Hide Disabled User`.
- Table re-filters users immediately.
- If selected user is now hidden:
  - Form remains visible.
  - Optionally, display banner: "Selected user is now hidden because they are disabled."

---

## 5. API & Data Hooks

- **Fetch Users**: `GET /api/users` & optionally `?hideDisabled=true`
- **Create User**: `POST /api/users`
- **Update User**: `PUT /api/users/{id}`
- (Optional) **Fetch Roles**: `GET /api/roles`
- (Optional) **Field-level validation endpoints** for uniqueness checks.

---

## 6. Accessibility & Design Notes

- Use proper labels and `aria-` attributes for form controls.
- Ensure tab order follows logical UI flow.
- Make sure buttons (`Save User`, `+ New User`) have accessible names.
- Include keyboard navigation for table sorting and row selection.

---

## 7. Mockups / Visual Reference

*(Add wireframe image here if available)*

---

## 8. Future Enhancements (Out of Scope)

- Filtering/searching user table.
- Pagination or infinite scrolling for large user lists.
- Role management UI.
- Password reset, last login date, 2FA status.
