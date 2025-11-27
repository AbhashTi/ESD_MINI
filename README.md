

# 🎓 Student Bill Payment System

A full-stack web application for managing student bill payments with dual authentication support (Email/Password + Google OAuth).

## 📋 Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [Project Structure](#project-structure)
- [API Documentation](#api-documentation)
- [Authentication Flow](#authentication-flow)
- [Database Schema](#database-schema)
- [Screenshots](#screenshots)
- [Contributing](#contributing)
- [License](#license)

## ✨ Features

### Authentication
- 🔐 **Dual Authentication System**
  - Email/Password login with JWT tokens
  - Google OAuth 2.0 integration
  - Secure session management with HTTP-only cookies
  - Case-insensitive email matching
  - Restricted OAuth access (registered students only)

### Bill Management
- 💰 **View Bills**
  - Display all unpaid bills
  - Show bill details (amount, deadline, description)
  - Calculate remaining amounts
  - Empty state handling

- 💳 **Payment Processing**
  - Partial payment support
  - Full payment option
  - Real-time balance updates
  - Payment validation

- 📜 **Payment History**
  - View all past transactions
  - Detailed payment information
  - Chronological ordering
  - Export capabilities

### User Experience
- 🎨 Modern, responsive UI
- 🔔 Real-time notifications
- ⚡ Fast page loads
- 📱 Mobile-friendly design
- 🌙 Clean interface

## 🛠️ Tech Stack

### Frontend
- **Framework**: React.js 18.x
- **Routing**: React Router DOM
- **HTTP Client**: Fetch API
- **Authentication**: JWT Decode
- **Styling**: CSS3

### Backend
- **Framework**: Spring Boot 3.3.5
- **Language**: Java 17
- **Security**: Spring Security
- **Authentication**: JWT + Google OAuth 2.0
- **Database**: MySQL 8.0
- **ORM**: Hibernate/JPA
- **Password Encryption**: BCrypt

### Development Tools
- **Build Tool**: Maven
- **Version Control**: Git
- **IDE**: IntelliJ IDEA / VS Code

## 📦 Prerequisites

Before you begin, ensure you have the following installed:

- **Java Development Kit (JDK)** 17 or higher
- **Node.js** 16.x or higher
- **npm** 8.x or higher
- **MySQL** 8.0 or higher
- **Git**

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd Mini-Project-main
```

### 2. Database Setup

1. Start MySQL server
2. Create a new database:

```sql
CREATE DATABASE MiniProject;
```

3. The application will automatically create tables on first run using Hibernate
4. Initial data will be loaded from `BackEnd/src/main/resources/data.sql`

### 3. Backend Setup

```bash
cd BackEnd

# Install dependencies and build
./mvnw clean install

# Or on Windows
mvnw.cmd clean install
```

### 4. Frontend Setup

```bash
cd Frontend

# Install dependencies
npm install
```

## ⚙️ Configuration

### Backend Configuration

Edit `BackEnd/src/main/resources/application.properties`:

```properties
# Server Configuration
server.port=8080

# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/MiniProject?createDatabaseIfNotExist=true&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=YOUR_MYSQL_PASSWORD
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA/Hibernate Configuration
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# JWT Configuration
jwt.secret=YOUR_SECRET_KEY_HERE
jwt.expiration=18000000

# Google OAuth Configuration
spring.security.oauth2.client.registration.google.client-id=YOUR_GOOGLE_CLIENT_ID
spring.security.oauth2.client.registration.google.client-secret=YOUR_GOOGLE_CLIENT_SECRET
spring.security.oauth2.client.registration.google.scope=openid,profile,email
spring.security.oauth2.client.registration.google.redirect-uri=http://localhost:8080/oauth2/callback

# Google Token Validation
google.tokeninfo.url=https://oauth2.googleapis.com/tokeninfo
```

### Google OAuth Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable Google+ API
4. Create OAuth 2.0 credentials:
   - Application type: Web application
   - Authorized redirect URIs: `http://localhost:8080/oauth2/callback`
5. Copy Client ID and Client Secret to `application.properties`

### Frontend Configuration

The frontend is configured to connect to `http://localhost:8080` by default. If you change the backend port, update the API URLs in:
- `src/components/login/Login.jsx`
- `src/components/viewBills/BillPayments.jsx`
- `src/components/paymentHistory/PaymentHistoryPage.jsx`
- `src/auth/GetData.js`

## 🏃 Running the Application

### Start Backend Server

```bash
cd BackEnd

# Using Maven wrapper
./mvnw spring-boot:run

# Or on Windows
mvnw.cmd spring-boot:run
```

Backend will start on `http://localhost:8080`

### Start Frontend Server

```bash
cd Frontend

# Start development server
npm start
```

Frontend will start on `http://localhost:3000`

### Access the Application

Open your browser and navigate to:
```
http://localhost:3000
```

## 📁 Project Structure

```
Mini-Project-main/
├── BackEnd/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/abhay/
│   │   │   │   ├── configuration/     # Security & CORS config
│   │   │   │   ├── controller/        # REST controllers
│   │   │   │   ├── dto/               # Data Transfer Objects
│   │   │   │   ├── entity/            # JPA entities
│   │   │   │   ├── exception/         # Custom exceptions
│   │   │   │   ├── helper/            # JWT & auth helpers
│   │   │   │   ├── repo/              # JPA repositories
│   │   │   │   └── service/           # Business logic
│   │   │   └── resources/
│   │   │       ├── application.properties
│   │   │       └── data.sql           # Initial data
│   │   └── test/                      # Unit tests
│   ├── pom.xml                        # Maven dependencies
│   └── mvnw                           # Maven wrapper
│
├── Frontend/
│   ├── public/                        # Static files
│   ├── src/
│   │   ├── auth/                      # Authentication utilities
│   │   ├── components/
│   │   │   ├── footer/                # Footer component
│   │   │   ├── header/                # Header & navigation
│   │   │   ├── login/                 # Login page
│   │   │   ├── mainScreen/            # Home dashboard
│   │   │   ├── paymentHistory/        # Payment history
│   │   │   ├── payemntpage/           # All transactions
│   │   │   └── viewBills/             # Bill management
│   │   ├── App.js                     # Main app component
│   │   └── index.js                   # Entry point
│   ├── package.json                   # npm dependencies
│   └── README.md
│
├── DATABASE_SETUP.md                  # Database setup guide
└── README.md                          # This file
```

## 📡 API Documentation

### Authentication Endpoints

#### Email/Password Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "test@test.com",
  "password": "test123"
}

Response: {
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Google OAuth Login
```http
GET /oauth2/login
Redirects to Google OAuth consent screen
```

#### OAuth Callback
```http
GET /oauth2/callback?code=AUTHORIZATION_CODE
Sets HTTP-only cookies and redirects to frontend
```

#### Get Current User (OAuth)
```http
GET /oauth2/user
Cookie: id_token=...

Response: {
  "id": 1,
  "email": "user@gmail.com",
  "firstName": "John",
  "lastName": "Doe",
  "rollNo": "R2023001"
}
```

#### Logout
```http
POST /api/auth/signout
Clears all authentication cookies
```

### Bill Management Endpoints

#### View Unpaid Bills
```http
POST /api/students/viewbills
Authorization: Bearer <token>
Content-Type: application/json

{
  "studentId": 1
}

Response: [
  {
    "billId": 1,
    "description": "Spring 2026 Tuition Fee",
    "amount": 5000.00,
    "deadline": "2026-01-15",
    "remaining": 2000.00,
    "paid": 3000.00
  }
]
```

#### Pay Bill
```http
POST /api/students/payFees
Authorization: Bearer <token>
Content-Type: application/json

{
  "studentId": 1,
  "billId": 1,
  "amount": 1000.00,
  "description": "Spring 2026 Tuition Fee"
}

Response: {
  "message": "Payment successful"
}
```

#### View Payment History
```http
POST /api/students/paidbills
Authorization: Bearer <token>
Content-Type: application/json

{
  "studentId": 1
}

Response: [
  {
    "paymentId": 1,
    "amount": 1000.00,
    "paymentDate": "2025-11-27",
    "billId": 1,
    "description": "Spring 2026 Tuition Fee",
    "remainingAmount": 2000.00
  }
]
```

## 🔐 Authentication Flow

### JWT Authentication (Email/Password)

1. User enters email and password
2. Backend validates credentials
3. Backend generates JWT token containing user data
4. Token stored in `localStorage`
5. Token sent in `Authorization` header for subsequent requests
6. Backend validates token on each request

### Google OAuth Authentication

1. User clicks "Sign in with Google"
2. Redirected to Google OAuth consent screen
3. User grants permissions
4. Google redirects to backend with authorization code
5. Backend exchanges code for tokens (id_token, access_token, refresh_token)
6. Backend validates email exists in database
7. Tokens stored in HTTP-only cookies
8. Frontend fetches user data from `/oauth2/user` endpoint
9. Cookies sent automatically with each request

## 🗄️ Database Schema

### Students Table
```sql
CREATE TABLE students (
    id BIGINT PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    roll_number VARCHAR(255) UNIQUE NOT NULL,
    first_name VARCHAR(255) NOT NULL,
    last_name VARCHAR(255),
    password VARCHAR(255) NOT NULL,
    cgpa FLOAT,
    total_credits FLOAT,
    graduation_year INT,
    domain VARCHAR(255),
    specialisation VARCHAR(255),
    placement_id INT,
    photograph_path VARCHAR(255)
);
```

### Bills Table
```sql
CREATE TABLE bills (
    id BIGINT PRIMARY KEY,
    amount DECIMAL(38,2) NOT NULL,
    bill_date DATE NOT NULL,
    deadline DATE NOT NULL,
    description VARCHAR(255) NOT NULL
);
```

### Student Bills (Junction Table)
```sql
CREATE TABLE student_bills (
    id BIGINT PRIMARY KEY,
    student_id BIGINT NOT NULL,
    bill_id BIGINT NOT NULL,
    FOREIGN KEY (student_id) REFERENCES students(id),
    FOREIGN KEY (bill_id) REFERENCES bills(id)
);
```

### Student Payment Table
```sql
CREATE TABLE student_payment (
    id BIGINT PRIMARY KEY,
    student_id BIGINT NOT NULL,
    bill_id BIGINT NOT NULL,
    amount DECIMAL(38,2) NOT NULL,
    payment_date DATE NOT NULL,
    description VARCHAR(255),
    FOREIGN KEY (student_id) REFERENCES students(id),
    FOREIGN KEY (bill_id) REFERENCES bills(id)
);
```

## 🔑 Default Credentials

### Email/Password Login
- **Email**: `test@test.com`
- **Password**: `test123`

### Google OAuth
Only the following Gmail addresses are allowed (configured in `data.sql`):
- `abhashtiwari12@gmail.com`
- `abhashtiwari344@gmail.com`

To add more allowed emails, update the `students` table in `data.sql`.

## 🐛 Troubleshooting

### Backend Issues

**Issue**: `java.sql.SQLException: Access denied for user 'root'@'localhost'`
- **Solution**: Update MySQL password in `application.properties`

**Issue**: `Table 'MiniProject.students' doesn't exist`
- **Solution**: Ensure `spring.jpa.hibernate.ddl-auto=create-drop` is set in `application.properties`

**Issue**: Google OAuth not working
- **Solution**: Verify Google Client ID and Secret are correct in `application.properties`

### Frontend Issues

**Issue**: CORS errors
- **Solution**: Ensure backend CORS configuration allows `http://localhost:3000`

**Issue**: "Roll No: N/A" for OAuth users
- **Solution**: Clear browser cache and hard refresh (Ctrl+Shift+R)

**Issue**: Login redirects to home but shows "Loading..."
- **Solution**: Check browser console for errors, verify backend is running

## 📝 Adding New Students

To allow new students to log in with Google:

1. Open `BackEnd/src/main/resources/data.sql`
2. Add a new entry to the students table:

```sql
INSERT INTO students (id, cgpa, domain, email, first_name, graduation_year, last_name, password, photograph_path, placement_id, roll_number, specialisation, total_credits) VALUES
(6, 3.5, 'Computer Science', 'newstudent@gmail.com', 'New', 2026, 'Student', '$2a$10$stI6eRm3SaICEHRSrPIn/eznPi3FlTzfz88FtiVrANPFIcOVTftau', '/photos/default.jpg', 106, 'R2023006', 'General', 0);
```

3. Restart the backend server

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👥 Authors

- **Your Name** - *Initial work*

## 🙏 Acknowledgments

- Spring Boot Documentation
- React Documentation
- Google OAuth 2.0 Documentation
- MySQL Documentation

