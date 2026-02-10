# Fithub
DBMS gym management system 
OVERVIEW:
FITHUB is a relational database–driven Gym Management System designed as part of a Database Management Systems (DBMS) course project.
The system efficiently manages gym members, subscriptions, attendance, equipment, maintenance, fitness progress, and engagement analytics using PostgreSQL.
The project emphasizes:
Proper database design
Normalization (3NF/BCNF)
Use of constraints, indexes, views, and triggers
Real-world query scenarios
OBJECTIVES:
Design a well-normalized relational schema
Implement data integrity constraints
Support analytical and reporting queries
Demonstrate practical DBMS concepts through SQL
Enable smooth local demonstration and collaboration
KEY FEATURES:
Member profile and subscription management
Membership plans and payment tracking
Daily attendance logging
Equipment inventory and maintenance tracking
Member body metrics and fitness goal tracking
Engagement scoring for churn prediction
Optimized queries using indexes and views
TECH STACK:
Database: PostgreSQL 14+
Query Language: SQL (DDL, DML, Views, Triggers)
Version Control: Git & GitHub
Optional Integration: Node.js + Express (for demo UI)
DATABASE SCHEMA SUMMARY:
Total Tables: 9
Core Tables
members
membership_plans
member_subscriptions
attendance
equipment
equipment_maintenance
Advanced Feature Tables
member_body_metrics
member_fitness_goals
member_engagement_scores
✔ All tables follow 3NF / BCNF
✔ Referential integrity maintained using foreign keys



FUTURE SCOPE:
Role-based access control
Web dashboard integration
Automated engagement scoring
optional Cloud deployment
