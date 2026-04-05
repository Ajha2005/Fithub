#	Table	Key columns	FK references	Purpose
1	members	member_id, email, status	—	Member profiles
2	membership_plans	plan_id, price, duration_months	—	Plan catalog
3	member_subscriptions	subscription_id, start/end_date	members, membership_plans	Payment history
4	attendance	attendance_id, check_in/out_time	members	Daily visit log
5	equipment	equipment_id, status, category	—	Equipment inventory
6	equipment_maintenance	maintenance_id, next_maintenance_date	equipment	Service history
7	member_fitness_goals	goal_id, goal_type, status	members	Goal tracking
8	member_body_metrics	metric_id, weight_kg, bmi	members	Body

Views (3)
VIEW
active_members_view
(Active members + current plan + days_remaining)
equipment_maintenance_view
(Overdue / Due Soon / Upcoming / Scheduled)
member_progress_view
(LAG weight change + goal counts per member)

Functions (6)
FUNCTION
calculate_bmi(weight, height)
get_days_until_expiry(member_id)
get_member_status(member_id)
calculate_attendance_score(id, days)
calculate_goal_progress_score(id)
get_equipment_status(equipment_id)

Procedures (7) + Triggers (6)
PROC
process_expired_subscriptions()
generate_monthly_revenue_report()
member_checkin / member_checkout()
register_new_member()
schedule_equipment_maintenance()
generate_member_activity_report()
cleanup_old_data()
