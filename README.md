# Smart Car Rental System

A car-rental management system for a single branch, built with **C# / .NET 10**. It ships as two front-ends on top of the same business rules:

- **Web app** (ASP.NET Core MVC + Identity + EF Core + SQLite): login, role-based access, fleet browsing, rentals, returns with late fees, and a driver admin area.
- **Console app**: an 8-option menu that implements the original requirements spec (branch info, users, fleet, rent, return, history, register).

## Features

| Area | What it does |
|---|---|
| Authentication | Register / login / logout with ASP.NET Core Identity; roles `Admin` and `Customer` |
| Fleet | Browse all cars with status badges (Available, Rented, Maintenance) and a details page |
| Rentals | Rent an available car; due date is always today + 14 days |
| Returns | Admin processes a return; late fee = overdue days x 150 EGP |
| Drivers (Admin) | List drivers, view a driver's rental history, register a new driver |
| Dashboard | Branch address, total fleet, available cars, total drivers |
| Validation | Name required, phone must contain a digit, email must contain `@` and `.` |

### Business rules

- Rental period is fixed at **14 days**.
- Late fee is **150 EGP per overdue day**; no fee when returned on time.
- Only **Available** cars can be rented.
- Normal users can only rent for their own customer profile; admins can rent for any driver.

## Tech stack

- C#, .NET 10, ASP.NET Core MVC (Razor views)
- Entity Framework Core 10 + SQLite, code-first migrations (applied automatically at startup)
- ASP.NET Core Identity (cookie auth, roles)
- Bootstrap 5, Bootstrap Icons, custom dark "glass" theme

## Project structure

```
src/
├── CarRentalLibrary/        Shared class library (models, validation, in-memory RentalSystem)
├── CarRentalDemo/           Console app (menu-driven, uses CarRentalLibrary)
└── SmartCarRental.Web/      ASP.NET Core MVC web app
    ├── Controllers/         Account, Cars, Customers, Rentals, Home
    ├── Data/                DbContext, entities, seeder
    ├── Migrations/          EF Core migrations
    ├── ViewModels/          Login / Register models
    └── Views/               Razor views
docs/                        Architecture, plan, progress notes
```

## Getting started

Requirements: [.NET 10 SDK](https://dotnet.microsoft.com/download).

### Web app

```powershell
dotnet run --project .\src\SmartCarRental.Web\SmartCarRental.Web.csproj
```

Open `http://localhost:5067`. The SQLite database (`SmartCarRental.db`) is created and seeded automatically on first run with 1 branch, 4 cars, 2 sample customers and the roles `Admin` / `Customer`.

**Admin account (Development only):** `admin@smartcarrental.com` / `Admin123!`

For any non-Development environment the admin is **not** created unless you set a password:

```powershell
$env:Admin__Password = "a-strong-password"
$env:Admin__Email    = "admin@yourdomain.com"   # optional
```

### Console app

```powershell
dotnet run --project .\src\CarRentalDemo\CarRentalDemo.csproj
```

## Known limitations / roadmap

- No admin screens yet to add, edit or delete cars (the fleet is seeded).
- Customers cannot return their own cars; returns are admin-only.
- Concurrent rentals of the same car are not guarded by a concurrency token.
- Web entities are separate from the console library models (only `ValidationHelper` is shared today); merging them would remove duplication.
- No automated tests yet.
