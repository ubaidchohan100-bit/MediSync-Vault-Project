# Clinic Management System

A lightweight RESTful API for managing patient records, appointments, and medicine history using **Node.js**, **Express.js**, and **MongoDB**.

---

## Project Structure

```
clinic-management/
├── config/
│   └── db.js                    # MongoDB connection
├── controllers/
│   ├── authController.js        # Register / Login
│   ├── patientController.js     # Patient CRUD
│   ├── appointmentController.js # Appointment CRUD
│   └── medicineController.js    # Medicine CRUD
├── middleware/
│   └── auth.js                  # JWT protect + authorize
├── models/
│   ├── User.js
│   ├── Patient.js
│   ├── Appointment.js
│   └── Medicine.js
├── routes/
│   ├── authRoutes.js
│   ├── patientRoutes.js
│   ├── appointmentRoutes.js
│   └── medicineRoutes.js
├── .env
├── .gitignore
├── app.js
└── package.json
```

---

## Setup & Installation

### Prerequisites
- Node.js (v16+)
- MongoDB running locally on port 27017

### Steps

```bash
# 1. Clone or download the project
cd clinic-management

# 2. Install dependencies
npm install

# 3. Configure environment (already set up in .env)
#    MONGO_URI=mongodb://localhost:27017/clinicDB
#    JWT_SECRET=clinic_super_secret_key_2026
#    PORT=3000

# 4. Start the server
npm start

# For auto-reload during development:
npm run dev
```

Server runs at: `http://localhost:3000`

---

## Authentication

All routes except `/api/auth/register` and `/api/auth/login` are **protected**.

Add this header to every request after logging in:
```
Authorization: Bearer <your_token_here>
```

### User Roles
| Role           | Permissions                                  |
|----------------|----------------------------------------------|
| `admin`        | Full access including patient delete         |
| `doctor`       | Create/read/update patients, appointments    |
| `receptionist` | Create/read/update patients, appointments    |

---

## API Reference

### AUTH

#### Register
```
POST /api/auth/register
Body:
{
  "name": "Dr. Ahmed",
  "email": "ahmed@clinic.com",
  "password": "123456",
  "role": "admin"
}
```

#### Login
```
POST /api/auth/login
Body:
{
  "email": "ahmed@clinic.com",
  "password": "123456"
}
Response: { token: "..." }
```

#### Get Current User
```
GET /api/auth/me
Header: Authorization: Bearer <token>
```

---

### PATIENTS

| Method | Endpoint             | Description              |
|--------|----------------------|--------------------------|
| GET    | /api/patients        | Get all patients         |
| GET    | /api/patients/:id    | Get patient by ID        |
| POST   | /api/patients        | Create new patient       |
| PUT    | /api/patients/:id    | Update patient           |
| DELETE | /api/patients/:id    | Delete patient (admin)   |

#### Query Filters (GET /api/patients)
- `?name=Ali` — search by name
- `?gender=Male`
- `?bloodGroup=B+`
- `?isActive=true`

#### Create Patient Body
```json
{
  "name": "Sara Khan",
  "age": 30,
  "gender": "Female",
  "contact": "0300-1234567",
  "email": "sara@gmail.com",
  "address": "Karachi",
  "bloodGroup": "B+",
  "medicalHistory": "Diabetes Type 2"
}
```

---

### APPOINTMENTS

| Method | Endpoint                | Description                  |
|--------|-------------------------|------------------------------|
| GET    | /api/appointments       | Get all appointments         |
| GET    | /api/appointments/:id   | Get appointment by ID        |
| POST   | /api/appointments       | Schedule new appointment     |
| PUT    | /api/appointments/:id   | Update / reschedule          |
| DELETE | /api/appointments/:id   | Delete appointment           |

#### Query Filters
- `?patient=<patientId>`
- `?status=Scheduled` (Scheduled / Completed / Cancelled / No-Show)
- `?doctorName=Ali`
- `?date=2026-06-10`

#### Create Appointment Body
```json
{
  "patient": "<patient_id>",
  "doctorName": "Dr. Usman",
  "department": "Cardiology",
  "date": "2026-06-15",
  "time": "10:30 AM",
  "reason": "Chest pain checkup",
  "notes": "Fasting required"
}
```

#### Update Status
```json
{
  "status": "Completed",
  "notes": "Patient examined, prescription given"
}
```

---

### MEDICINES

| Method | Endpoint                          | Description                       |
|--------|-----------------------------------|-----------------------------------|
| GET    | /api/medicines                    | Get all medicine records          |
| GET    | /api/medicines/:id                | Get medicine record by ID         |
| GET    | /api/medicines/patient/:patientId | Full history for a patient        |
| POST   | /api/medicines                    | Add medicine record               |
| PUT    | /api/medicines/:id                | Update medicine record            |
| DELETE | /api/medicines/:id                | Delete medicine record            |

#### Query Filters (GET /api/medicines)
- `?patient=<patientId>`
- `?isActive=true`
- `?medicineName=Paracetamol`

#### Create Medicine Body
```json
{
  "patient": "<patient_id>",
  "appointment": "<appointment_id>",
  "medicineName": "Metformin",
  "dosage": "500mg",
  "frequency": "Twice daily",
  "startDate": "2026-06-01",
  "endDate": "2026-08-01",
  "prescribedBy": "Dr. Usman",
  "notes": "Take after meals"
}
```

---

## Postman Quick-Start

1. Register → copy the `token` from the response
2. In Postman, go to **Headers** → add `Authorization: Bearer <token>`
3. Create a patient → copy the patient `_id`
4. Use that `_id` to create appointments and medicine records
5. Test GET with filters, PUT to update, DELETE to remove
