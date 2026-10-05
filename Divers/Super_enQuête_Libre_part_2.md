# Super enQuête Libre [2/4]

We portray the role of a detective to solve a case of theft inside of a prestigious school (Télématique NordRouan).
We're given access to a database with the names of all students and teachers aside.

## Objective
For this particular challenge, we have to identify the last student to have visited
four different classrooms in the same day.

Flag format expected: `404CTF{firstname_lastname}`

## Resolution
We start by logging to solve the challenge:
```bash
nc spawn.404ctf.fr 10401
```

In the `AccessLog` table, we start by searching badges that got used four times in the
same day
```sql
select badge_id, date from AccessLog group by badge_id, date having count(*) = 4;
+----------+------------+
| badge_id | date       |
+----------+------------+
| 184      | 2026-05-04 |
| 214      | 2025-12-11 |
+----------+------------+
```

We have two results and since we have to find the last student who visited four
different classrooms in the same day, we continue with badge `184`.

In the `Badge` table, we then search for the `person_id` linked to this badge
```sql
select person_id from Badge where badge_id = 184;
+-----------+
| person_id |
+-----------+
| 63        |
+-----------+
```

All's left to do now is to find in the `Person` table who does this `person_id`
belong to
```sql
select first_name, last_name from Person where person_id = 63;
+------------+-----------+
| first_name | last_name |
+------------+-----------+
| Noémie     | Robert    |
+------------+-----------+
```

We have the identity of the student who last visited four different classrooms in the
same day, therefore the flag for this challenge is `404CTF{noémie_robert}`.
