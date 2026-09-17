# FitHub — Gym Management Database

FitHub is a PostgreSQL database project for managing gym operations: members, membership
plans, subscriptions, attendance, equipment, and fitness progress tracking. It's built as a
fully normalized (3NF/BCNF) relational schema with views, functions, stored procedures, and
triggers implementing the core business logic in the database itself.

## Contents

| File | Description |
|---|---|
| [`schema`](schema) | DDL for all 8 tables, indexes, and 3 views |
| [`dataset`](dataset) | Sample data — ~95 members and realistic records across all tables |
| [`pgsql implementation`](pgsql%20implementation) | 6 functions, 6 stored procedures, 6 triggers, and a cursor-based procedure |
| [`demo queries for eval`](demo%20queries%20for%20eval) | ~11 sections of demo/evaluation queries (SELECTs, joins, CTEs, window functions, views, functions, procedures, triggers, cursors) |
| [`func, procedures,triggers verification`](func%2C%20procedures%2Ctriggers%20verification) | Verification & smoke-test queries for routines and triggers, with expected results |
| [`frontend`](frontend) | Spec/requirements for a planned Flask + Bootstrap dashboard on top of the database (not yet implemented) |

> Note: these files are plain `.sql` scripts without file extensions. Run them with `psql`,
> e.g. `psql -d gym_management -f schema`.

## Database Schema

**8 tables**, normalized to 3NF/BCNF:

| Table | Purpose |
|---|---|
| `members` | Member profiles and contact information |
| `membership_plans` | Catalog of subscription plans and pricing |
| `member_subscriptions` | Subscription & payment history (FK → members, membership_plans) |
| `attendance` | Daily check-in / check-out log (FK → members) |
| `equipment` | Equipment inventory |
| `equipment_maintenance` | Maintenance history and scheduling (FK → equipment) |
| `member_fitness_goals` | Member fitness goals and progress (FK → members) |
| `member_body_metrics` | Body measurements over time — weight, BMI, body fat %, etc. (FK → members) |

14 indexes cover foreign keys and common filter columns (email, status, dates).

### Views

- **`active_members_view`** — active members joined with their current plan and days remaining
- **`equipment_maintenance_view`** — equipment health, classified as Overdue / Due Soon / Upcoming / Scheduled
- **`member_progress_view`** — weight trend (via `LAG`) and goal counts per member

### Functions

| Function | Purpose |
|---|---|
| `calculate_bmi(weight, height)` | Computes BMI from weight (kg) and height (cm) |
| `get_days_until_expiry(member_id)` | Days remaining on a member's current subscription |
| `get_member_status(member_id)` | Returns name, subscription status, days remaining, 30-day visit count |
| `calculate_attendance_score(member_id, days)` | Attendance score (0–100) over a trailing window |
| `calculate_goal_progress_score(member_id)` | Average progress score across a member's fitness goals |
| `get_equipment_status(equipment_id)` | Maintenance status of a piece of equipment |

### Stored Procedures

| Procedure | Purpose |
|---|---|
| `process_expired_subscriptions()` | Marks members with lapsed subscriptions as inactive |
| `generate_monthly_revenue_report(year, month)` | Revenue breakdown by plan for a given month |
| `member_checkin(member_id)` / `member_checkout(member_id)` | Validated attendance check-in/out |
| `register_new_member(...)` | Creates a member and their initial subscription in one call |
| `schedule_equipment_maintenance(...)` | Schedules a maintenance record for equipment |
| `generate_member_activity_report()` | Cursor-based report of visits & goals per active member |
| `cleanup_old_data()` | Utility procedure for purging stale records |

### Triggers

1. Auto-calculate BMI on `member_body_metrics` insert/update
2. Auto-update `updated_at` on `members`
3. Validate subscription dates before insert/update
4. Validate attendance check-out time
5. Log member status changes (`AFTER UPDATE`)
6. Prevent deletion of equipment with pending maintenance

## Getting Started

Requires **PostgreSQL 14+**.

```bash
# 1. Create the database and load the schema (tables, indexes, views)
psql -U postgres -f schema

# 2. Load sample data (~95 members across all 8 tables)
psql -U postgres -d gym_management -f dataset

# 3. Install functions, procedures, and triggers
psql -U postgres -d gym_management -f "pgsql implementation"

# 4. (Optional) Run the demo/evaluation query set
psql -U postgres -d gym_management -f "demo queries for eval"

# 5. (Optional) Verify routines and triggers behave as expected
psql -U postgres -d gym_management -f "func, procedures,triggers verification"
```

### Try it out

```sql
-- BMI for a 70kg, 170cm member
SELECT calculate_bmi(70, 170);  -- ~24.22

-- Active members with current plan and days remaining
SELECT * FROM active_members_view;

-- Equipment due for maintenance
SELECT * FROM equipment_maintenance_view WHERE maintenance_status IN ('Overdue', 'Due Soon');

-- Check a member in
CALL member_checkin(1);
```

## Frontend (planned)

The [`frontend`](frontend) file captures the spec for a lightweight Flask + Bootstrap 5 +
Chart.js dashboard (dashboard, members list, analytics pages) intended to sit on top of this
database via `psycopg2`. It is currently a requirements document rather than working code —
contributions implementing it are welcome.

## Tech Stack

- **Database:** PostgreSQL 14+ (PL/pgSQL for functions, procedures, triggers, cursors)
- **Planned frontend:** Python (Flask), psycopg2, Bootstrap 5, Chart.js
