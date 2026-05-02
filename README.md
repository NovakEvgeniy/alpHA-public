# Alpine Hospitality Analytics - alpHA 

**Long-term modeling of the hotel business of an Alpine ski resort for investors**

> **Keywords:** Alpine ski resort financial model • Hotel investment analysis tool • Ski resort EBITDA modeling • Hospitality analytics software • Ski resort CAPEX calculator
> Mountain hotel revenue projection • Alpine resort feasibility study • Ski season financial planning • Hotel DSCR LTV calculation • Winter resort investor metrics
> Ski resort OPEX modeling • Hospitality business valuation tool • Alpine real estate investment analysis • Mountain tourism financial forecasting • Ski resort cash flow modeling
> Hotel development pro forma • Alpine property investment software • Ski resort ROI NPV calculator • Hospitality financial engineering • Mountain resort equity multiple  

## 1.  About the Project

### 1.1. Purpose

The **alpHA** application is a demonstration corporate web system for:
- Description of analytical requirements for a web application of the financial and economic model of a ski resort hotel project

### 1.2. Registered Application Users

-   Project customer
-   Investors
-   Analysts
-   Developers

### 1.3. Important Limitations

-   The application represents a **basic implementation** with the ability to be extended for customer requirements
-   The program is designed for desktop use on standard office monitors (optimally 1920×1080 pixels)
-   The interface will be less user-friendly on tablets and phones, but the API functionality will remain functional

### 1.4.Scope - The system is designed for:

-   assessing the investment attractiveness of a project
-   calculating financial indicators and analyzing the financing structure
-   long-term investment modeling of a ski resort hotel (10–20 years)
-   modeling seasonality and weather risks

## 2. Core System Entities

### 2.1. Input Data 

-   **Scenario** - Poor seasons, Normal seasons, Great Seasons
-   **Revenue drivers** - Rooms Occupied, Skiers Count, Visitors Count, Prices and others
-   **Cost & Expense drivers** - Staff, Utilities, Maintenance and others

### 2.2. Output Data 

-   **Investor Money** - Equity, Payback Period, Investor Profit and others
-   **Banking Summary** - Loan Amount, Loan Term and others
-   **Accounting Summary** - CAPEX, Depreciation Period and others 

### 2.3. Levels of representation

-   **Economics View** - Start of calculation for the entire application system. Outputs Total Revenue and OPEX for Finance View
-   **Finance View** - Calculation of all financial and accounting indicators
-   **Investor View** - Derivation of key indicators for assessing the investment attractiveness of a project

## 3. Architecture

### 3.1. Web Application Format

-   A feature-based modular monolithic system organized by feature 

### 3.2. Frontend

-   **Web Interface Architecture**: Server-Side Rendered (SSR) Multi-Page Application (MPA)
-   **Rendering**: Server-side rendering Via Thymeleaf
-   **Technology Stack**: HTML, CSS, JavaScript, Thymeleaf
-   **Ajax**: In the current architecture, connecting Ajax does not make sense

### 3.3. Backend

-   **Architecture**: Modular Monolith Feature-based with division into layers (controllers, facades/services, engines) and the “Core” pattern (Core Engines)
-   **Database**: MySQL + Hibernate
-   **Security**: no Spring Security (Custom auth via /economics/finance/investor). The password is not verified
-   **Technology Stack**: Java 20, Spring Boot 3.2.0, Spring Data JPA, Maven 3.6+

#### 3.4. Frontend & Backend Modular Architecture
```
    alpHA/                  
  ├── pom.xml             
  ├── shared
  │   └── contracts          # Common DTOs and Enumerations
  │       └── common
  │           └── UserRole.java
  │       ├── economics
  │           └── EconomicsView.java
  │       ├── finance
  │           └── FinanceView.java
  │       └── investor
  │           └── InvestorView.java
  ├── modules
  │   ├── economics           # MicroEconomics View module
  │   │   ├── application
  │   │   │   └── EconomicsFacade.java
  │   │   └── web
  │   │       └── EconomicsPageController.java
  │   ├── finance             # Finance View module
  │   │   ├── application
  │   │   │   └── FinanceFacade.java
  │   │   └── web
  │   │       └── FinancePageController.java
  │   ├── investor            # Investor View module
  │   │   └── web
  │   │       └── InvestorPageController.java
  │   └── calculation_core    # Business logic and calculation engines
  │       └── engines
  │           ├── RevenueEngine.java
  │           ├── EconomicsEngine.java
  │           ├── FinanceEngine.java
  │           └── FinanceCalculationEngine.java
  ├── infrastructure          # Data access and persistence
  │   └── persistence
  │       ├── ModelInputEntity.java
  │       └── ModelInputRepository.java
  └── api                     # Web interface and top-level controllers
      ├── platform
      │   ├── web
      │   │   └── IndexController.java
      │   └── auth
      │       └── AuthController.java
      ├── resources           
      │   ├── templates
      │   │   ├── index.html
      │   │   ├── economics.html
      │   │   ├── finance.html
      │   │   └── investor.html
      │   ├── static
      │       ├──  css
      │       │   ├── index.css
      │       │   ├── economics.css
      │       │   ├── finance.css
      │       │   └── investor.css
      │       └── js
      │           ├── economics.js
      │           └── finance.js
      └── AlpHaApplication.java
```

## 4. Launch and Deployment

### 4.1. Prerequisites

-   Java 20
-   Maven 3.6+
-   MySQL 8.0+

### 4.2. Build and Run the Project Using JAR File

#### 4.2.1. Create the Database

> TABLE model_input:
>
> **id scenario other...**\
> **1  Poor**\
> **2  Normal**\
> **3  Great**\
> **4  GLOBAL**
> 

``` sql
CREATE DATABASE alpha_db;

USE alpha_db;
CREATE TABLE model_input (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    scenario VARCHAR(50),
    rooms_occupied INT,
    adr_rooms DOUBLE,
    ski_pass_price DOUBLE,
    ski_days DOUBLE,
    fnb_avg_check DOUBLE,
    rental_price DOUBLE,
    lesson_price DOUBLE,
    retail_avg DOUBLE,
    season_days INT,
    snow_days INT,
    staff_normalRevenue_ratio DOUBLE,
    utilities_normalRevenue_ratio DOUBLE,
    marketing_normalRevenue_ratio DOUBLE,
    maintenance_normalRevenue_ratio DOUBLE,
    cost_ratio_rooms_fnb DOUBLE,
    cost_ratio_ski DOUBLE,
    cost_ratio_rental_retail DOUBLE,
    skiers_per_room DOUBLE,
    visitors_per_skier DOUBLE,
    rental_days_per_skier DOUBLE,
    lessons_per_skier DOUBLE,
    other_spend_per_visitor DOUBLE,
    capex_multiplier DOUBLE,
    ltv_ratio DOUBLE,
    discount_rate DOUBLE,
    tax_rate DOUBLE,
    interest_rate DOUBLE,
    loan_term_years INT,
    depreciation_rate DOUBLE,
    term_after_payback INT,
    capex_manual DOUBLE,
    equity_manual DOUBLE,
    rooms_capex_share DOUBLE,
    cost_per_room DOUBLE
);
```
-  Then, seed the initial data for all 4 required rows (3 scenarios + 1 global finance input):

``` sql
-- Poor scenario (id=1)
INSERT INTO model_input (id, scenario, rooms_occupied, adr_rooms, ski_pass_price, ski_days, fnb_avg_check, rental_price, lesson_price, retail_avg, season_days, snow_days, staff_normalRevenue_ratio, utilities_normalRevenue_ratio, marketing_normalRevenue_ratio, maintenance_normalRevenue_ratio, cost_ratio_rooms_fnb, cost_ratio_ski, cost_ratio_rental_retail, skiers_per_room, visitors_per_skier, rental_days_per_skier, lessons_per_skier, other_spend_per_visitor)
VALUES (1, 'Poor', 80, 150.0, 40.0, 3.0, 50.0, 25.0, 45.0, 20.0, 120, 90, 0.15, 0.05, 0.03, 0.04, 0.20, 0.25, 0.30, 1.5, 0.5, 2.0, 0.5, 25.0);

-- Normal scenario (id=2)
INSERT INTO model_input (id, scenario, rooms_occupied, adr_rooms, ski_pass_price, ski_days, fnb_avg_check, rental_price, lesson_price, retail_avg, season_days, snow_days, staff_normalRevenue_ratio, utilities_normalRevenue_ratio, marketing_normalRevenue_ratio, maintenance_normalRevenue_ratio, cost_ratio_rooms_fnb, cost_ratio_ski, cost_ratio_rental_retail, skiers_per_room, visitors_per_skier, rental_days_per_skier, lessons_per_skier, other_spend_per_visitor)
VALUES (2, 'Normal', 100, 180.0, 45.0, 4.0, 60.0, 30.0, 50.0, 25.0, 130, 110, 0.12, 0.04, 0.03, 0.03, 0.18, 0.22, 0.28, 1.8, 0.7, 2.5, 0.6, 30.0);

-- Great scenario (id=3)
INSERT INTO model_input (id, scenario, rooms_occupied, adr_rooms, ski_pass_price, ski_days, fnb_avg_check, rental_price, lesson_price, retail_avg, season_days, snow_days, staff_normalRevenue_ratio, utilities_normalRevenue_ratio, marketing_normalRevenue_ratio, maintenance_normalRevenue_ratio, cost_ratio_rooms_fnb, cost_ratio_ski, cost_ratio_rental_retail, skiers_per_room, visitors_per_skier, rental_days_per_skier, lessons_per_skier, other_spend_per_visitor)
VALUES (3, 'Great', 110, 200.0, 50.0, 5.0, 70.0, 35.0, 55.0, 30.0, 140, 135, 0.10, 0.03, 0.02, 0.02, 0.15, 0.20, 0.25, 2.0, 0.8, 3.0, 0.7, 35.0);

-- Global finance input (id=4)
INSERT INTO model_input (id, scenario, discount_rate, tax_rate, depreciation_rate, interest_rate, loan_term_years, term_after_payback, capex_manual, equity_manual, rooms_capex_share, cost_per_room)
VALUES (4, 'GLOBAL', 0.10, 0.20, 0.04, 0.07, 10, 5, 7000000.0, 2000000.0, 0.40, 100000.0);
```

#### 4.2.2. Create the `application.properties` file and place it in any folder

```bash
spring.datasource.url=jdbc:mysql://localhost:3306/alpha_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=none
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.show-sql=true
```
-  Adopt the sample data values ​​to the actual figures of your project

#### 4.2.3. Download the executable `alpha-0.0.1-SNAPSHOT.jar` using the provided link and place it in the same folder as `application.properties`

👉 [** Download alpha-0.0.1-SNAPSHOT.jar**](https://github.com/NovakEvgeniy/alpHA-public/releases/download/v1.0.0/alpha-0.0.1-SNAPSHOT.jar)

#### 4.2.4. Run the executable file in the folder containing `application.properties` and `prms2-0.0.1-SNAPSHOT.jar`

``` bash
java -jar alpha-0.0.1-SNAPSHOT.jar
```

#### 4.2.5. Launch in the Browser

-   **URL: http://localhost:8080/**

> Use login:
>
> **Analyst-economist's Login:** `economics`\
> **Financial Analyst's Login:** `finance`\
> **Investor's Login:** `investor`\
> **Password:** `No password required`
>

## 5. Future Development

-   Custom modules according to client technical requirements
-   Advanced BI analytics
-   Integrations with 1C, ERP, CRM
-   Export of reports to Excel / PDF

## 6. Licensing

-   This software is proprietary.
-   The source code is closed and represents the intellectual property of the author.

## 7. Commercial Use

-   Source code and extended project versions are provided by agreement.

## 8. Contacts

-   **Email:** **novakevgeniy1953@gmail.com**
-   **GitHub:** **https://github.com/NovakEvgeniy?tab=repositories**

