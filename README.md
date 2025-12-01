# Attendance Management System API

A comprehensive employee attendance tracking and reporting system built with ASP.NET Core 8.0, implementing clean architecture principles with role-based access control and automated deduction calculations.

## Project Overview

This project is an enterprise-grade attendance management system designed to track employee attendance, generate reports, and automate salary deductions based on configurable business rules. The system implements a hierarchical management structure with multiple organizational levels (CEO, Department Manager, Line Manager, Employee) and provides detailed attendance analytics with customizable reporting periods.

**Key Goals:**
- Automate attendance tracking and reporting across organizational hierarchies
- Implement complex business rules for absence and lateness deductions
- Provide role-specific dashboards and reporting capabilities
- Enable scalable multi-company and multi-department management
- Ensure data integrity with soft deletes and audit trails

**Use Cases:**
- Corporate attendance management systems
- Multi-location business operations
- Hierarchical organizational structures
- HR departments requiring automated payroll deductions
- Companies with complex attendance policies

## Tech Stack

**Backend:**
- .NET 8.0 (C#)
- ASP.NET Core Web API
- Entity Framework Core 8.0.20

**Database:**
- SQL Server LocalDB (Development)
- SQL Server (Production-ready)

**Key Libraries:**
- BCrypt.Net-Next 4.0.3 (password hashing)
- Microsoft.AspNetCore.Authentication.JwtBearer 8.0.20 (JWT authentication)
- Microsoft.AspNetCore.Identity.EntityFrameworkCore 8.0.20
- Swashbuckle.AspNetCore 6.6.2 (API documentation)

**Patterns & Architecture:**
- Clean/N-Tier Architecture
- Repository Pattern
- Unit of Work Pattern
- Generic Repository Pattern
- Dependency Injection
- Soft Delete Pattern

## Architecture

The project follows **Clean Architecture** with three-tier separation:

```
┌─────────────────────────────────────────┐
│      Presentation Layer                 │
│  (Controllers, Middleware, DTOs)        │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│      Business Logic Layer               │
│  (Services, Business Rules, ViewModels) │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│      Data Access Layer                  │
│  (Repositories, EF Core, UnitOfWork)    │
└─────────────────────────────────────────┘
```

**Major Components:**

1. **Attendance_system.web**: API controllers, middleware, authentication configuration
2. **BussinessLogic**: Service layer with attendance calculation logic and business rules
3. **DataAccess**: Entity Framework Core implementation with repositories and Unit of Work

**Organizational Hierarchy:**
```
CEO (Sub_Company level)
  ↓
Department Manager
  ↓
Line Manager
  ↓
Employee
```

**Data Flow:**
```
Client Request → Controller → Service (Business Logic) → Repository → Database
                                  ↓
                    Attendance Calculation Engine
```

## Features

### Core Features

- ✅ **Employee Management**
  - Full CRUD operations for employee records
  - Manager-employee relationship tracking
  - Role assignment (CEO, DepartmentManager, LineManager, Employee)
  - Department assignment
  - Soft delete support

- ✅ **Attendance Tracking**
  - Daily check-in/check-out recording
  - Automatic late detection (configurable threshold: 8:30 AM)
  - Absence tracking
  - Attendance status calculation
  - Composite primary key (Employee + Date)

- ✅ **Advanced Reporting System**
  - **Employee Reports**: Individual attendance history with deductions
  - **Line Manager Reports**: Team attendance summaries
  - **Department Manager Reports**: All line managers' team performance
  - **CEO Dashboard**: Company-wide department summaries
  - Date range filtering for all reports

- ✅ **Automated Deduction Calculations**
  - **Lateness Penalties:**
    - On-time (≤8:30 AM): No deduction
    - 15-30 min late: 0.25 day deduction
    - 30-60 min late: 0.5 day deduction
    - >60 min late: 1.0 day deduction
  - **Monthly Rules**: Special handling for ≤2 absences with ≤4 hours worked
  - **Yearly Allowances**: 
    - Managers: 35 days
    - Employees: 21 days
    - Deductions apply after allowance exhausted

- ✅ **Department & Sub-Company Management**
  - Multi-company support
  - Department hierarchies
  - Manager assignments
  - Company-level CEO management

- ✅ **Authentication & Authorization**
  - JWT-based authentication
  - Role-based access control (CEO, DepartmentManager, LineManager, Employee)
  - BCrypt password hashing
  - Secure user registration and login

### Additional Features

- 🔧 Custom error handling middleware with environment-aware responses
- 🔧 Swagger UI with JWT Bearer authorization
- 🔧 Standardized API response wrapper (`ApiResponse<T>`)
- 🔧 Soft delete implementation across all entities
- 🔧 Generic repository pattern for DRY code
- 🔧 Stored procedures for complex attendance queries

## Setup & How to Run

### Prerequisites

- .NET 8.0 SDK or later ([Download](https://dotnet.microsoft.com/download))
- SQL Server LocalDB (included with Visual Studio) or SQL Server Express/Full
- Visual Studio 2022 / VS Code / Rider (recommended)
- SQL Server Management Studio (optional, for database management)

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd Attendance_system-master
   ```

2. **Update Connection String**
   
   Edit `Attendance_system/appsettings.json`:
   ```json
   {
     "ConnectionStrings": {
       "DefualtConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=Attendance_System;Integrated Security=True"
     }
   }
   ```
   
   For SQL Server Express/Full:
   ```json
   {
     "ConnectionStrings": {
       "DefualtConnection": "Server=YOUR_SERVER;Database=Attendance_System;Trusted_Connection=True;TrustServerCertificate=True"
     }
   }
   ```

3. **Configure JWT Settings**
   
   The default JWT configuration in `appsettings.json`:
   ```json
   {
     "Jwt": {
       "Key": "KvXODmG1pKrhg/IVa4Uzc/+j9y4GjTcRC9sXoEerIr37IAy0o9CYM44zZ1hCRcGW",
       "Issuer": "AttendanceSystem",
       "Audience": "AttendanceSystemUsers"
     }
   }
   ```
   
   ⚠️ **Production**: Replace the `Key` with your own secure 256-bit key.

4. **Restore NuGet Packages**
   ```bash
   dotnet restore
   ```

5. **Create Database and Apply Migrations**
   
   The database will be created automatically on first run with EF Core migrations.
   
   To manually apply migrations:
   ```bash
   dotnet ef database update --project DataAccess --startup-project Attendance_system
   ```

6. **Seed Initial Data (Optional)**
   
   You'll need to manually create initial data:
   - First CEO user
   - Sub-companies
   - Departments
   - Employees

7. **Run the Application**
   ```bash
   dotnet run --project Attendance_system
   ```
   
   Or open in Visual Studio and press F5.

   The API will be available at:
   - HTTPS: `https://localhost:7055`
   - HTTP: `http://localhost:5038`
   - Swagger UI: `https://localhost:7055/swagger`

### Common Commands

```bash
# Build the solution
dotnet build

# Run with hot reload
dotnet watch run --project Attendance_system

# Create a new migration
dotnet ef migrations add MigrationName --project DataAccess --startup-project Attendance_system

# Remove last migration
dotnet ef migrations remove --project DataAccess --startup-project Attendance_system

# Update database to specific migration
dotnet ef database update MigrationName --project DataAccess --startup-project Attendance_system

# Drop database
dotnet ef database drop --project DataAccess --startup-project Attendance_system
```

### Environment Variables (Alternative Configuration)

```bash
# Using dotnet user-secrets
dotnet user-secrets init --project Attendance_system
dotnet user-secrets set "ConnectionStrings:DefualtConnection" "YOUR_CONNECTION_STRING" --project Attendance_system
dotnet user-secrets set "Jwt:Key" "YOUR_JWT_SECRET" --project Attendance_system
```

## Testing

Currently, the project does not include automated tests.

**Manual Testing via Swagger:**
1. Navigate to Swagger UI at `https://localhost:7055/swagger`
2. Register a user via `/api/Auth/register`
3. Login via `/api/Auth/login` to get JWT token
4. Click "Authorize" button and enter: `Bearer YOUR_TOKEN_HERE`
5. Test endpoints based on your role

**Recommended Test Coverage:**
- Unit tests for attendance calculation logic
- Unit tests for deduction rules
- Integration tests for repositories
- API tests for controllers
- End-to-end tests for reporting workflows

**Future Testing Setup:**
```bash
dotnet new xunit -n Attendance.Tests.Unit
dotnet new xunit -n Attendance.Tests.Integration
```

## Folder Structure

```
Attendance_system-master/
├── Attendance_system/                       # Web API Project
│   ├── Controllers/                        # API Controllers
│   │   ├── AttendanceReportController.cs  # Attendance reporting endpoints
│   │   ├── AuthController.cs              # Authentication endpoints
│   │   ├── DepartmentController.cs        # Department CRUD
│   │   ├── EmployeeController.cs          # Employee CRUD
│   │   └── SubCompanyController.cs        # Sub-company CRUD
│   ├── Middlewares/                       # Custom middleware
│   │   └── ErrorHandling.cs               # Global error handler
│   ├── Properties/                        
│   │   └── launchSettings.json            # Launch configuration
│   ├── appsettings.json                   # Configuration
│   ├── appsettings.Development.json
│   ├── Program.cs                         # Application entry point
│   └── Attendance_system.web.csproj
│
├── BussinessLogic/                         # Business Logic Layer
│   ├── Models/                            # DTOs
│   │   ├── AuthDto/                       # Login/Register DTOs
│   │   ├── _AttendanceDto/                # Attendance report DTOs
│   │   ├── _DepartmentDto/                # Department DTOs
│   │   ├── _EmployeeDto/                  # Employee DTOs
│   │   ├── _ManagerSummary/               # Manager report DTOs
│   │   └── _SubCompanyDto/                # Sub-company DTOs
│   ├── Services/                          # Service implementations
│   │   ├── AttendanceService.cs           # Attendance business logic
│   │   ├── AuthService.cs                 # Authentication service
│   │   ├── DepartmentService.cs
│   │   ├── EmployeeServices.cs
│   │   └── Sub_CompanyService.cs
│   ├── ServicesAbstraction/               # Service interfaces
│   ├── ViewModels/
│   │   └── ApiResponse.cs                 # Standard API response wrapper
│   └── BussinessLogic.csproj
│
└── DataAccess/                             # Data Access Layer
    ├── Data/
    │   ├── Entities/                      # EF Core entities
    │   │   ├── Attendance.cs              # Attendance entity
    │   │   ├── BaseEntity.cs              # Base with soft delete
    │   │   ├── Department.cs
    │   │   ├── Employee.cs
    │   │   └── Sub_Company.cs
    │   ├── Repositories/                  # Repository implementations
    │   │   ├── _AttendanceRepository/
    │   │   ├── _DepartmentRepository/
    │   │   ├── _EmployeeRepository/
    │   │   ├── _Generic/                  # Generic repository
    │   │   └── _SubCRepository/
    │   ├── _UnitOfWork/
    │   │   ├── IUnitOfWork.cs
    │   │   └── UnitOfWork.cs
    │   └── Attendance_SystemContext.cs    # EF Core DbContext
    ├── efpt.config.json                   # EF Core Power Tools config
    └── DataAccess.csproj
```

**Key Files:**
- `Program.cs` - DI configuration, middleware pipeline, JWT setup
- `Attendance_SystemContext.cs` - Database context with entity configurations
- `AttendanceService.cs` - Core business logic for attendance calculations
- `ErrorHandling.cs` - Global exception handler
- `UnitOfWork.cs` - Coordinates repository transactions

## Configuration & Secrets

### Required Configuration (appsettings.json)

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefualtConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=Attendance_System;Integrated Security=True"
  },
  "Jwt": {
    "Key": "YOUR_SECURE_256_BIT_SECRET_KEY_MINIMUM_64_CHARACTERS",
    "Issuer": "AttendanceSystem",
    "Audience": "AttendanceSystemUsers"
  }
}
```

### Environment-Specific Settings

**Development** (`appsettings.Development.json`):
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

**Production (use environment variables)**:
```bash
export ConnectionStrings__DefualtConnection="YOUR_PROD_CONNECTION"
export Jwt__Key="YOUR_PROD_JWT_SECRET"
```

### Security Notes

⚠️ **CRITICAL SECURITY REMINDERS:**
- **Never commit secrets** to version control
- Replace default JWT key in production
- Use strong database passwords
- Enable HTTPS in production
- Implement rate limiting for authentication endpoints
- Consider Azure Key Vault or AWS Secrets Manager for production
- Rotate JWT keys periodically
- Use separate databases for dev/test/prod

## API Endpoints Reference

### Authentication
```http
POST   /api/Auth/register       # Register new user
POST   /api/Auth/login          # Login (returns JWT token)
```

### Employee Management
```http
GET    /api/Employee            # Get all employees [LineManager, DepartmentManager]
GET    /api/Employee/{id}       # Get employee by ID
POST   /api/Employee            # Create employee
PUT    /api/Employee/{id}       # Update employee
DELETE /api/Employee/{id}       # Delete employee (soft delete)
```

### Department Management
```http
GET    /api/Department/GetAllDepartments        # Get all [CEO]
GET    /api/Department/GetDepartmentById/{id}   # Get by ID [CEO]
POST   /api/Department/CreateDepartment         # Create [CEO]
PUT    /api/Department/UpdateDepartment/{id}    # Update [CEO]
DELETE /api/Department/DeleteDepartment/{id}    # Delete [CEO]
```

### Sub-Company Management
```http
GET    /api/SubCompany/GetAll          # Get all [CEO]
GET    /api/SubCompany/GetById/{id}    # Get by ID [CEO]
POST   /api/SubCompany/Create          # Create [CEO]
PUT    /api/SubCompany/Update/{id}     # Update [CEO]
DELETE /api/SubCompany/Delete/{id}     # Delete [CEO]
```

### Attendance Reporting
```http
GET    /api/AttendaceReport/employee/{employeeId}                              # Employee report [LineManager]
GET    /api/AttendaceReport/manager-report/{managerId}                         # Manager's team report [CEO]
GET    /api/AttendaceReport/department-manager/line-manager-summaries          # Line manager summaries [DepartmentManager]
       ?departmentManagerId={id}&startDate={date}&endDate={date}
GET    /api/AttendaceReport/ceo/department-summaries                           # Department summaries [CEO]
       ?startDate={date}&endDate={date}
```

## Business Rules

### Attendance Deduction Rules

**Lateness Calculation:**
- **On-time** (≤8:30 AM): 0.0 days deducted
- **Up to 30 min late** (8:30-9:00 AM): 0.25 days deducted
- **30-60 min late** (9:00-9:30 AM): 0.5 days deducted
- **Over 60 min late** (>9:30 AM): 1.0 day deducted (considered absent)

**Monthly Rules:**
- If employee has ≤2 absences AND total hours worked ≤4 in a month: No deduction
- Otherwise: Full deduction applies

**Yearly Allowances:**
- **Managers**: 35 days paid leave per year
- **Employees**: 21 days paid leave per year
- Allowance resets on March 1st annually
- Deductions apply only after allowance exhausted

### Stored Procedures

The system uses stored procedures for performance:
- `GetEmployeeAttendanceReport` - Individual employee attendance
- `GetAttendanceReportsByManager` - Team attendance for line managers

## Future Improvements

### Short-term Roadmap
- [ ] Implement real-time check-in/check-out via mobile app
- [ ] Add email/SMS notifications for absences
- [ ] Implement leave request workflow
- [ ] Add overtime tracking
- [ ] Create employee self-service portal
- [ ] Implement shift management
- [ ] Add public holiday calendar
- [ ] Export reports to PDF/Excel

### Medium-term Roadmap
- [ ] Biometric integration (fingerprint/face recognition)
- [ ] Geofencing for location-based check-ins
- [ ] Advanced analytics dashboard with charts
- [ ] Integration with payroll systems
- [ ] Automated absence alerts for managers
- [ ] Performance review integration
- [ ] Mobile apps (iOS/Android)
- [ ] Multi-timezone support

### Long-term Roadmap
- [ ] AI-powered attendance prediction
- [ ] Blockchain-based attendance records
- [ ] Integration with HR management systems
- [ ] Multi-tenant SaaS architecture
- [ ] Advanced reporting with Power BI
- [ ] Compliance reporting (labor law)
- [ ] Workflow automation engine
- [ ] Real-time dashboard with SignalR

## Contribution Guidelines

We welcome contributions! Please follow these guidelines:

### How to Contribute

1. **Fork the repository**
2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes**
   - Follow existing code style and conventions
   - Add XML documentation comments for public APIs
   - Update README if adding new features
4. **Commit your changes**
   ```bash
   git commit -m "Add: brief description of your changes"
   ```
5. **Push to your fork**
   ```bash
   git push origin feature/your-feature-name
   ```
6. **Open a Pull Request**
   - Provide clear description of changes
   - Reference any related issues
   - Ensure all checks pass

### Code Style
- Follow C# coding conventions
- Use meaningful variable and method names
- Keep methods focused and small (Single Responsibility Principle)
- Add comments for complex business logic
- Use async/await for I/O operations
- Implement proper error handling

### Pull Request Process
- PRs require at least one review before merging
- All tests must pass (when implemented)
- Code coverage should not decrease
- Update documentation for public API changes

### Reporting Issues
- Use GitHub Issues to report bugs
- Provide detailed reproduction steps
- Include environment information (.NET version, SQL Server version, OS)
- Attach relevant logs or screenshots

## License

This project is licensed under the **MIT License**.

**Rationale:** The MIT License is permissive, allows commercial use, and encourages open collaboration while providing liability protection for contributors.

```
MIT License

```

## Notes / Known Issues

- 📝 **No Automated Tests**: Project currently lacks unit and integration tests
- 📝 **Typo in Connection String**: `DefualtConnection` should be `DefaultConnection`
- ⚠️ **JWT Key in Config**: Default JWT key should be replaced in production
- 📝 **Missing Migrations**: Database schema generated via EF Core Power Tools
- 📝 **Stored Procedures**: `GetEmployeeAttendanceReport` and `GetAttendanceReportsByManager` must be created manually in database
- 📝 **No Seed Data**: Initial users and data must be created manually
- 📝 **Error in Employee Update**: `UpdateEmployee` method has potential null reference issue
- 📝 **Missing Swagger XML Comments**: Controllers lack XML documentation for Swagger

## Author & Contact

**Project Maintainers:** [Ahmed Khaled],[Ayman Elkilany]

[![GitHub](https://img.shields.io/badge/GitHub-Profile-black?logo=github)](https://github.com/ahmed-khalid2004)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://linkedin.com/in/ahmed-khalid-5b6349259)
[![Email](https://img.shields.io/badge/Email-Contact-red?logo=gmail)](mailto:engahmedkhalid3s@gmail.com)

---

### Acknowledgments
- Built with ASP.NET Core and Entity Framework Core
- Password security by BCrypt
- Authentication powered by JWT

### Support
If you find this project helpful, please ⭐ star the repository!

---

**What I Looked At:**
- Analyzed the complete three-tier architecture (Attendance_system, BussinessLogic, DataAccess)
- Reviewed all controllers, services, repositories, and entities
- Examined the complex attendance calculation logic with monthly/yearly rules
- Studied the role-based authorization structure and hierarchical reporting system
- Identified the stored procedure integration for performance optimization
