# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Sign up for activities (authenticated teachers only)
- Publicly view activities and registered students

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                               | Description                                                         |
| ------ | ---------------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                          | Get all activities with their details and current participant count |
| POST   | `/auth/login`                                                          | Log in as a teacher and receive a bearer token                      |
| POST   | `/auth/logout`                                                         | Revoke the current teacher session                                  |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu`      | Sign up a student (teacher token required)                          |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu`  | Unregister a student (teacher token required)                       |

## Teacher Accounts

Teacher accounts are configured in `teachers.json`. Start by copying `teachers.example.json` to `teachers.json`; the real file is excluded from Git so credentials are not committed. Passwords are stored as PBKDF2-SHA256 hashes, not plaintext. Generate a salt and hash with:

```sh
python -c "import getpass, hashlib, json, secrets; password=getpass.getpass(); salt=secrets.token_hex(16); digest=hashlib.pbkdf2_hmac('sha256', password.encode(), bytes.fromhex(salt), 310000).hex(); print(json.dumps({'salt': salt, 'password_hash': digest}))"
```

Add the resulting `salt` and `password_hash`, along with a `username`, to the `teachers` array in `teachers.json`. Login sessions are held in memory and expire after eight hours; restarting the server invalidates them. With no local teacher accounts configured, login is unavailable and changes to registrations are rejected.

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
