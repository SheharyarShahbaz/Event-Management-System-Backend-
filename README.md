# Event Management System Database

A comprehensive MySQL database schema for managing events, bookings, venues, staff, sponsors, and all related operations for an event management company.

##  Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Database Schema](#database-schema)
- [Installation](#installation)
- [Database Structure](#database-structure)
- [Normalization](#normalization)
- [Sample Queries](#sample-queries)
- [Entity Relationship](#entity-relationship)
- [Technologies Used](#technologies-used)
- [Contributing](#contributing)
- [License](#license)

##  Overview

This Event Management System is designed to handle all aspects of event planning and execution, including:
- User and customer management
- Event scheduling and venue booking
- Staff assignment and tracking
- Food and decoration management
- Sponsorship tracking
- Ticket sales and booking confirmations
- Payment processing

##  Features

- **User Management**: Multi-role system (Admin, Organizer, Customer, Staff)
- **Event Planning**: Complete event lifecycle management
- **Venue Management**: Track venues with capacity and location details
- **Booking System**: Handle event bookings with payment modes
- **Staff Assignment**: Assign staff to events with role tracking
- **Food Management**: Categorize food items by type and cuisine
- **Decoration Tracking**: Manage decorations with supplier information
- **Sponsorship**: Track sponsor contributions and agreements
- **Ticket Sales**: VIP and General ticket management with seat assignments
- **Normalized Structure**: Database follows normalization principles for data integrity

##  Database Schema

The database consists of 15 core tables plus 6 normalized lookup tables:

### Core Tables
1. **Users** - System users with role-based access
2. **Customers** - Customer profiles with preferences
3. **Staff** - Employee information and assignments
4. **Venues** - Event locations with capacity details
5. **Events** - Event details and scheduling
6. **Bookings** - Event reservations and confirmations
7. **Tickets** - Individual ticket sales
8. **FoodItems** - Menu items with dietary information
9. **Decorations** - Available decoration items
10. **Sponsors** - Sponsor company information
11. **PaymentModes** - Payment method configurations

### Junction Tables (Many-to-Many Relationships)
- **Event_Food** - Links events with food items
- **Event_Decorations** - Links events with decorations
- **Event_Sponsors** - Links events with sponsors
- **Event_Staff** - Links events with staff assignments

### Normalized Lookup Tables
- **AddressDetails** - Normalized venue addresses
- **VenueManagers** - Venue manager information
- **Departments** - Staff departments
- **FoodCategories** - Food type and cuisine combinations
- **DecorationTypes** - Decoration categories
- **SponsorIndustries** - Sponsor industry classifications

## 🚀 Installation

### Prerequisites
- MySQL 5.7 or higher
- MySQL Workbench (optional, for GUI)
- Command line access to MySQL

### Setup Instructions

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/event-management-system.git
cd event-management-system
```

2. **Login to MySQL**
```bash
mysql -u root -p
```

3. **Run the SQL script**
```sql
source path/to/event_management_system.sql
```

Or import via MySQL Workbench:
- File → Run SQL Script
- Select the `event_management_system.sql` file
- Execute

4. **Verify installation**
```sql
USE event_managment_system;
SHOW TABLES;
```

## 📊 Database Structure

### Key Relationships

```
Users (1) ─────→ (1) Customers
Users (1) ─────→ (1) Staff
Users (1) ─────→ (N) Events [as Organizer]
Users (1) ─────→ (N) Bookings

Events (N) ───→ (1) Venues
Events (N) ───→ (N) FoodItems [via Event_Food]
Events (N) ───→ (N) Decorations [via Event_Decorations]
Events (N) ───→ (N) Staff [via Event_Staff]
Events (N) ───→ (N) Sponsors [via Event_Sponsors]
Events (1) ───→ (N) Tickets

Bookings (N) ──→ (1) PaymentModes
Customers (1) ─→ (N) Tickets
```

### Schema Highlights

**Users Table**
- Multi-role support (Admin, Organizer, Customer, Staff)
- Unique email constraint
- Phone number validation

**Events Table**
- DateTime tracking for start and end
- Status tracking (Planned, Confirmed, Cancelled)
- Links to venues and organizers

**Bookings Table**
- Confirmation code generation
- Payment mode tracking
- Status management (Pending, Confirmed)

## 🔄 Normalization

The database has been normalized to **3rd Normal Form (3NF)** to eliminate redundancy:

### Normalization Steps Implemented

1. **Address Normalization**
   - Extracted venue addresses to `AddressDetails` table
   - Eliminates duplicate address storage

2. **Venue Manager Normalization**
   - Created `VenueManagers` table
   - Separates manager information from venue data

3. **Department Normalization**
   - Created `Departments` table for staff departments
   - Allows for department-level management

4. **Food Category Normalization**
   - Created `FoodCategories` table
   - Combines food type and cuisine for better categorization

5. **Decoration Type Normalization**
   - Created `DecorationTypes` table
   - Centralizes decoration type definitions

6. **Sponsor Industry Normalization**
   - Created `SponsorIndustries` table
   - Standardizes industry classifications

### Before and After Normalization Example

**Before:**
```sql
Venues (VenueID, VenueName, Address, City, State, ManagerName, Gender, ContactPhone)
```

**After:**
```sql
Venues (VenueID, VenueTitle, Capacity, AddressID, ManagerID)
AddressDetails (AddressID, Address, City, State)
VenueManagers (ManagerID, ManagerName, Gender, ContactPhone)
```

## 🔍 Sample Queries

The script includes various sample queries:

### Basic Queries
```sql
-- Get all events with venue names
SELECT e.EventID, e.EventName, v.VenueTitle, e.EndDateTime 
FROM Events e 
JOIN Venues v ON e.VenueID = v.VenueID;

-- Show bookings with user information
SELECT Users.FirstName, Users.LastName, Bookings.BookingDate 
FROM Bookings 
JOIN Users ON Bookings.UserID = Users.UserID;
```

### Aggregate Queries
```sql
-- Count total events
SELECT COUNT(*) AS TotalEvents FROM Events;

-- Total payment amount
SELECT SUM(TotalAmount) AS TotalAmount FROM Bookings;

-- Bookings per event
SELECT EventID, COUNT(*) AS TotalBookings 
FROM Bookings 
GROUP BY EventID;
```

### Advanced Queries
```sql
-- Show all users, including those without bookings
SELECT Users.FirstName, Users.LastName, Bookings.BookingID 
FROM Users 
LEFT JOIN Bookings ON Users.UserID = Bookings.UserID;
```

## 🗺️ Entity Relationship

### Strong Entities
- Users
- Venues
- FoodItems
- Decorations
- Sponsors
- PaymentModes

### Weak Entities
- Customers (dependent on Users)
- Staff (dependent on Users)
- Bookings (dependent on Users and Events)
- Tickets (dependent on Customers and Events)

### Composite Attributes
- Address (Street, City, State) - normalized into AddressDetails

### Multi-valued Attributes
Handled through junction tables:
- Events can have multiple FoodItems
- Events can have multiple Decorations
- Events can have multiple Staff members
- Events can have multiple Sponsors

## 💻 Technologies Used

- **Database**: MySQL 8.0
- **SQL Features**:
  - Foreign Key Constraints
  - CHECK Constraints
  - AUTO_INCREMENT
  - JOIN Operations
  - Aggregate Functions
  - Subqueries
  - ALTER TABLE Operations

## 📈 Data Statistics

The sample database includes:
- 10 Users
- 10 Customers
- 10 Staff Members
- 10 Venues
- 10 Events
- 10 Bookings
- 10 Food Items
- 10 Decoration Items
- 10 Sponsors
- 10 Tickets
- Multiple junction table entries

## 🛠️ Customization

### Adding New Roles
Modify the CHECK constraint in Users table:
```sql
ALTER TABLE Users 
MODIFY Role VARCHAR(20) CHECK (Role IN ('Admin','Organizer','Customer','Staff','NewRole'));
```

### Adding New Payment Modes
Insert into PaymentModes table:
```sql
INSERT INTO PaymentModes (ModeID, ModeType, Description, ModeCode, IsActive)
VALUES (4, 'UPI', 'Payment through UPI', 'UPI001', TRUE);
```

### Extending Event Status
Modify the CHECK constraint:
```sql
ALTER TABLE Events 
MODIFY Status VARCHAR(20) CHECK (Status IN ('Planned','Confirmed','Cancelled','Completed'));
```

## 📝 Notes

- **Safe Updates**: The script includes `SET SQL_SAFE_UPDATES = 0;` for data migration during normalization
- **Data Integrity**: All foreign key constraints are properly defined
- **Unique Constraints**: Email addresses and confirmation codes are unique
- **Default Values**: Status fields have appropriate defaults
- **Validation**: CHECK constraints ensure data quality

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


##  Authors

- Sheharyar Shahbaz
- Saad Ajmal

##  Acknowledgments

- Inspired by real-world event management systems
- Database design follows standard normalization principles
- Sample data represents typical event management scenarios

## 📧 Contact

For questions or feedback, please reach out to bseds23021@itu.edu.pk

---
