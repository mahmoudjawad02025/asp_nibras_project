# 🎓 Nibras Learning Platform — ASP.NET Core API

A scalable, **3-layer** Web API (DAL → BLL → PL) with a generic repository, JWT authentication, progress counts, and lesson file uploads.

![.NET](https://img.shields.io/badge/.NET-9.0-blue)

<hr>
<br>

## 📌 Table of Contents
- [🚀 Overview](#-overview)
- [📐 Architecture](#-architecture)
- [🧩 Key Features](#-key-features)
- [🚀 Tech Stack](#-tech-stack)
- [📁 Project Structure](#-project-structure)
- [🔑 Authentication Flow](#-authentication-flow)
- [📦 API Modules](#-api-modules)
- [❌ Error Handling](#-error-handling)
- [⚙️ Getting Started](#setup)
- [🔐 Environment Variables](#-environment-variables)
- [📘 API Documentation](#-api-documentation)
- [📞 Contact](#-contact)

<br>
<hr>
<br>

## 🚀 Overview
The **Nibras API** is a backend for courses, lessons, quizzes, Quran, Hadith, Thikr, and user accounts.

1. Built a REST API with 154 endpoints across 10 modules and 25 controllers using ASP.NET Core 9 and EF Core (Code First, SQL Server), in a 3-layer layout (DAL/BLL/PL) with dependency injection, a generic repository, and Mapster DTO mapping. OpenAPI is exposed in Development through Scalar. There is no global exception handler.

2. Implemented JWT authentication with refresh tokens, logout that keeps revoked access tokens in memory and blocks them in middleware, and role checks for `Admin`, `Student`, and `SuperAdmin`, plus email confirmation, forgot/reset password, and change email and password.

3. Courses and lessons with file uploads. Quiz create and read, with no answer submission. Progress counts for Thikr, Hadith, and Category. Quran, Hadith, and Thikr with search. Hadith has filters and stats. Admin content tools, plus block, unblock, and role change.

<br>

## 🧩 Key Features
* 📈 **Scalable architecture:** The 3-layer separation (DAL / BLL / PL), dependency injection, and a generic repository organize the code across 10 modules and 154 endpoints.
* 🔐 **Identity:** JWT access tokens and refresh tokens. Logout stores the access token in an in-memory set. Roles are `Admin`, `Student`, and `SuperAdmin`. Email confirmation is required. Forgot-password, reset-password, change-email, and change-password endpoints exist.
* 🗂️ **Data access:** A generic repository is used by several services, with Mapster mapping. It is not exposed as full CRUD for every entity.
* 📚 **Courses and lessons:** Admin create, update, delete, and status toggle. Students can read them. Lesson create and update accept file uploads.
* 📝 **Quizzes:** Admin create, update, delete, and status toggle. Students can read a quiz. There is no endpoint to submit answers or record a score.
* 📈 **Progress:** A student can add or increment a count and read their own counts. Counts apply to Thikr, Hadith, and Category only.
* 📖 **Quran, Hadith, and Thikr:** Read and search endpoints. Hadith also has filters and stats. Admin can create, update, delete, and toggle Hadith and Thikr. Quran endpoints are read-only.

<br>

## 🚀 Tech Stack
* **Framework:** ASP.NET Core 9 (Web API)
* **ORM:** Entity Framework Core (Code First)
* **Database:** SQL Server
* **Security:** JWT Bearer Authentication
* **Documentation:** OpenAPI and Scalar in Development. There is no Swagger UI.
* **Architecture:** 3-layer (DAL / BLL / PL). The PL project references DAL.
* **Mapping:** Mapster
* **Dependency Injection**

<br>

## 📐 Architecture
This project follows a **3-layer architecture**:
```
PL  → Controllers / API
BLL → Business Logic & Services
DAL → Data Access (EF Core + Repositories)
```
Controllers call BLL service interfaces. PL also references DAL for DTOs and models.

<br>

## 📁 Project Structure
```plaintext
Nibras_App
│
├── DAL
│   ├── Data_Base
│   │   └── ApplicationDbContext.cs
│   ├── Migrations
│   ├── Models
│   │   ├── DTO
│   │   ├── Entities
│   │   ├── Enums
│   │   └── JsonModels
│   ├── Utils
│   └── Repositories
│       ├── Interfaces
│       └── Classes
│
├── BLL
│   └── Services
│       ├── Interfaces
│       └── Classes
│
└── PL
    ├── Areas (Controllers)
    │   ├── Admin
    │   ├── Identity
    │   └── Student
    ├── PL_Utils
    ├── appsettings.json
    └── Program.cs
```

<br>
<hr>
<br>

## 🔑 Authentication Flow

Authentication is implemented using **JWT Bearer Tokens**.

```
Authorization: Bearer <token>
```

Register can send a confirmation email. Login returns an access token and a refresh token after the email is confirmed. `POST /Refresh` issues a new access token and returns the same refresh token. Logout adds the access token to an in-memory `HashSet` and revokes the refresh token. An inline middleware in `Program.cs` returns 401 when that access token is presented again. `JwtBlacklistMiddleware` exists in `PL_Utils` and is not registered. Issuer and audience checks are turned off. The in-memory list is cleared when the process restarts.

<br>

## 📦 API Modules

These 10 modules are implemented. Admin and Student each have their own controllers for most of them.

* **Authentication:** Register, login, refresh, logout, confirm email, forgot password, and reset password.
* **User:** Admin list, get, block, unblock, block check, and role change. Admin and Student can read and update their profile, change password, and change email.
* **UserProgress:** Add or increment a count, and get the current user's counts. Types are Thikr, Hadith, and Category.
* **Category:** Admin create, read, update, delete, and status toggle. Students can read.
* **Course:** Admin create, read, update, delete, and status toggle. Students can read.
* **Lesson:** Admin create, read, update, delete, status toggle, and list by course, with file uploads. Students can read.
* **Quiz:** Admin create, read, update, delete, and status toggle. Students can read. No answer submission.
* **Quran:** List surahs, get one surah, get one ayah, and search. Read-only for Admin and Student.
* **Hadith:** Books, chapters, and hadiths. Search, filters, random items, and stats. Admin can also create, update, delete, and toggle status.
* **Thikr:** Categories and items. Search, filter by category, and filter by count. Admin can also create, update, delete, and toggle status.

<br>

## ❌ Error Handling

There is no global exception middleware. Several services throw `Exception`. Some admin create and update actions catch exceptions locally and return the exception message.

<br>
<hr>
<br>

<a name="setup"></a>
## ⚙️ Getting Started

### Prerequisites
- .NET SDK 9.0
- SQL Server

### Installation
From the repository folder:

```
dotnet restore Nibras_App.sln
```

### Database Setup
EF Core migrations run on startup through `db.Database.Migrate()`. Quran, Hadith, and Thikr JSON files under `PL/wwwroot/data` are seeded when those tables are empty.

### Run Application
```
dotnet run --project PL
```

### API Access
In Development the Scalar UI is at:

```
https://localhost:7050/scalar
```

OpenAPI is mapped in Development. There is no `/swagger` page.

<br>

## 🔐 Environment Variables

Set these in `appsettings.json` or as environment variables. Do not commit real secrets.

| Key                                  | Description                  |
|--------------------------------------|------------------------------|
| ConnectionStrings:DefaultConnection  | SQL Server connection string |
| jwtOptions:SecretKey                 | JWT signing secret key       |

<br>
<hr>
<br>

## 📘 API Documentation
[To see the api document of this project click here](./Docs/Api_Document.md)

<br>

## 📞 Contact

- 📧 **Email**: [mahmoudjawad02025@gmail.com](mailto:mahmoudjawad02025@gmail.com)
- 💻 **GitHub Profile**: [@mahmoudjawad-2025](https://github.com/mahmoudjawad-2025/)
- 💼 **LinkedIn:** [linkedin.com/in/mahmoud-abu-alsebaa](https://linkedin.com/in/mahmoud-abu-alsebaa)
