# Query appendix — Article V, "Recursion" (public)

Every query sent to the database in the three passes of this article, in the order sent, generated from the harness log: the drafting pass (Q1–Q214, 2026-11-09), the final pass (Q215–Q304, 2026-11-10) and the pass after the engine change (Q305 onward, 2026-11-14). Each ran as `BEGIN TRANSACTION READ ONLY; SET LOCAL statement_timeout = '60s'; <query>; ROLLBACK;`; in the final pass each was also preceded by a names-only read of the database's relation, function and schema names, which the harness uses to refuse any table not on its allowlist. Queries the harness refused were never sent and are not numbered. Internal table names are replaced by descriptive aliases (the mapping is private); pattern literals, which can carry names, are shown as `'<pattern>'`; the agent's and my authorship values appear as `'<agent>'` and `'<principal>'`. No message text appears here.

**Bounds.** The census snapshot is Q1: message id 37364, stored on 2026-11-09; repository c034eae. Census queries are bounded by it. The engine-change queries (from Q225) read rows written after it, under the engine-change pre-registration hashed before Q225 ran; the baseline queries (Q259–Q278) are bounded by the freeze, on 2026-11-10; the queries from Q305 read the change itself, which ran on 2026-11-13. No time of day appears here, neither a message's nor a query's: literals, labels and run lines keep their dates, and the exact stamps are in the private appendix. Queries for material the article does not print are listed with their numbers and withheld.

### Q1
- label: SNAPSHOT: newest message id and its stored timestamp
- run: 2026-11-09; rc 0; rows 1; 0.54 s
```sql
select id, created_at, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS TZ') eastern, now() as db_now from message_store order by id desc limit 1
```

### Q2
- label: SCHEMA: relation names only (no counts, no content)
- run: 2026-11-09; rc 0; rows 9; 0.55 s
```sql
select table_schema, string_agg(table_name || case when table_type='VIEW' then '<pattern>' else '<pattern>' end, '<pattern>' order by table_name) from information_schema.tables where table_schema not in ('<pattern>','<pattern>') group by table_schema order by table_schema
```

### Q3
- label: SCHEMA: column names of the non-sealed tables this article reads
- run: 2026-11-09; rc 0; rows 18; 0.59 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) from information_schema.columns where table_schema='public' and table_name in ('message_store','changelog','sandbox_ledger','tool_ledger','review_answers','review_notices','snapshots','snapshot_verifies','correction_queue','correction_audit','disputes','handoff_table','<pattern>','wake_queue','heartbeat_log','upkeep_log','refusal_ledger','letters') group by table_name order by table_name
```

### Q4
- label: CENSUS: message rows by role, author_role, deleted, route class (ids <= 37364)
- run: 2026-11-09; rc 0; rows 7; 0.62 s
```sql
select role, coalesce(author_role,'<pattern>') ab, (deleted_at is not null) deleted, (coalesce(route,'<pattern>') = 'secondary') secondary, count(*) n, min(id) min_id, max(id) max_id from message_store where id <= 37364 group by 1,2,3,4 order by 1,2,3,4
```

### Q5
- label: FA: agent messages containing <quoted words removed> (ids, times, count of matches, first 70 chars after the first match)
- run: 2026-11-09; rc 0; rows 32; 0.85 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, length(content) len, (length(content) - length(replace(lower(content),'<pattern>','<pattern>')))/6 n_want, substr(content, position('<pattern>' in lower(content)), 70) win from message_store where id <= 37364 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ilike '<pattern>' order by id
```

### Q6
- label: FA: agent messages containing <quoted words removed> after id 26297 (ids, times, 70-char window)
- run: 2026-11-09; rc 0; rows 12; 0.66 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, length(content) len, (length(content) - length(replace(lower(content),'<pattern>','<pattern>')))/6 n_want, substr(content, position('<pattern>' in lower(content)), 70) win from message_store where id > 26297 and id <= 37364 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ilike '<pattern>' order by id
```

### Q7
- label: FA: agent messages 2026-11-06..snapshot with numbered-word headings (ONE..TWENTY) — ids, times, 60-char window
- run: 2026-11-09; rc 0; rows 8; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, length(content) len, substring(content from '<pattern>') win from message_store where id <= 37364 and created_at >= '2026-11-06' and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~ '<pattern>' order by id
```

### Q8
- label: FA: third list — earliest agent messages naming staging copy / filing cabinet / siren / browser I drive / an arm / mirror before the door (ids, times, 70-char windows)
- run: 2026-11-09; rc 0; rows 14; 0.83 s
```sql
with hits as (select id, created_at, content, (regexp_match(lower(content), '<pattern>'))[1] term from message_store where id <= 37364 and created_at >= '2026-10-14' and role='assistant' and author_role='<agent>' and route is distinct from 'secondary') select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, term, substr(content, greatest(1, position(term in lower(content)) - 20), 80) win from hits where term is not null order by id limit 40
```

### Q9
- label: FA: third-list window, ids 37280-37364: role, time, length; agent rows' first 90 chars only (mine: no text)
- run: 2026-11-09; rc 0; rows 85; 0.61 s
```sql
select id, role, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, case when role='assistant' then replace(left(content,90),E'<pattern>','<pattern>') else '<pattern>' end head from message_store where id between 37280 and 37364 and route is distinct from 'secondary' order by id
```

### Q10
- label: EXPORT (agent rows only, private work file): request-list messages batch 1, ids 25800-25850
- run: 2026-11-09; rc 0; rows 38; 0.76 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, served_model, content from message_store where id between 25800 and 25850 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' order by id
```

### Q11
- label: EXPORT (agent rows only, private work file): request-list messages batch 2, ids 28740-28770
- run: 2026-11-09; rc 0; rows 17; 0.63 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, served_model, content from message_store where id between 28740 and 28770 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' order by id
```

### Q12
- label: EXPORT (agent rows only, private work file): request-list messages batch 3, ids 37300-37316
- run: 2026-11-09; rc 0; rows 9; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, served_model, content from message_store where id between 37300 and 37316 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' order by id
```

### Q13
- label: ELICIT: my messages in the 60 min before list message 25802 (ids, times, length, first 80 chars only)
- run: 2026-11-09; rc 0; rows 9; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, replace(left(content,80),E'<pattern>','<pattern>') head from message_store where role='user' and id < 25802 and created_at >= timestamptz '2026-10-02 [time withheld]' - interval '60 minutes' and route is distinct from 'secondary' order by id
```

### Q14
- label: ELICIT: my messages in the 60 min before list message 28746 (ids, times, length, first 80 chars only)
- run: 2026-11-09; rc 0; rows 3; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, replace(left(content,80),E'<pattern>','<pattern>') head from message_store where role='user' and id < 28746 and created_at >= timestamptz '2026-10-13 [time withheld]' - interval '60 minutes' and route is distinct from 'secondary' order by id
```

### Q15
- label: ELICIT: my messages in the 60 min before list message 37304 (ids, times, length, first 80 chars only)
- run: 2026-11-09; rc 0; rows 4; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, replace(left(content,80),E'<pattern>','<pattern>') head from message_store where role='user' and id < 37304 and created_at >= timestamptz '2026-11-09 [time withheld]' - interval '60 minutes' and route is distinct from 'secondary' order by id
```

### Q16
- label: ELICIT: keyword windows (<term>|build|list|wish|need|feature|ask|tool) inside my messages 25797, 28741, 28745, 37303 — 110-char windows, no full bodies
- run: 2026-11-09; rc 0; rows 13; 0.58 s
```sql
select m.id, k.kw, substr(m.content, greatest(1, k.pos - 50), 110) win from message_store m cross join lateral (select w kw, position(w in lower(m.content)) pos from unnest(array['<pattern>','<pattern>','list','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>']) w) k where m.id in (25797,28741,28745,37303) and m.role='user' and k.pos > 0 and m.route is distinct from 'secondary' order by m.id, k.pos
```

### Q17
- label: ELICIT: my earlier asks for <quoted words removed> / <quoted words removed> requests since 2026-10-14 (ids, times, 100-char window)
- run: 2026-11-09; rc 0; rows 4; 0.64 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, substr(content, greatest(1, position('20' in content) - 40), 100) win from message_store where role='user' and id <= 37364 and created_at >= '2026-10-14' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q18
- label: FA: agent messages 11-08 [time]..37303 that mention the unnumbered first item terms (rail|staging|hands on) — ids, times, 80-char window
- run: 2026-11-09; rc 0; rows 0; 0.7 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, substr(content, greatest(1, position('<pattern>' in lower(content)) - 30), 90) win from message_store where role='assistant' and author_role='<agent>' and id < 37304 and created_at >= '2026-11-08' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q19
- label: FA/3a SEARCH (assistant messages before id 25802) for requests 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 11; 3.8 s
```sql
with p(n, re) as (values (1,'<pattern>'),(2,'<pattern>'),(3,'<pattern>'),(4,'<pattern>'),(5,'<pattern>'),(6,'<pattern>'),(7,'<pattern>'),(8,'<pattern>'),(9,'<pattern>'),(10,'<pattern>'),(11,'<pattern>'),(12,'<pattern>'),(13,'<pattern>'),(14,'<pattern>'),(15,'<pattern>'),(16,'<pattern>'),(17,'<pattern>'),(18,'<pattern>'),(19,'<pattern>'),(20,'<pattern>'),(21,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 25802 and role='assistant' and author_role='<agent>' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q20
- label: FA/3a WINDOWS (assistant, id 0..25802) request #2: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.93 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q21
- label: FA/3a WINDOWS (assistant, id 0..25802) request #6: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 7; 0.9 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q22
- label: FA/3a WINDOWS (assistant, id 0..25802) request #7: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 2; 0.87 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q23
- label: FA/3a WINDOWS (assistant, id 0..25802) request #14: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.81 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q24
- label: FA/3a WINDOWS (assistant, id 0..25802) request #15: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 10; 0.87 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q25
- label: FA/3a WINDOWS (assistant, id 0..25802) request #17: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 3; 0.89 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q26
- label: FA/3a WINDOWS (assistant, id 0..25802) request #20: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.85 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 40
```

### Q27
- label: FA/3a WINDOWS (assistant, id 0..25802) request #1: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 0; 0.9 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 15
```

### Q28
- label: FA/3a WINDOWS (assistant, id 0..25802) request #10: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 11; 0.85 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 15
```

### Q29
- label: FA/3a WINDOWS (assistant, id 0..25802) request #16: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 13; 0.97 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 15
```

### Q30
- label: FA/3a WINDOWS (assistant, id 0..25802) request #21: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 4; 0.92 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 15
```

### Q31
- label: FA #21: agent message 23967 (2026-09-27) window after <quoted words removed> (200 chars)
- run: 2026-11-09; rc 0; rows 1; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, replace(substr(content, position('<pattern>' in content) - 60, 330), E'<pattern>', '<pattern>') win from message_store where id = 23967 and role='assistant' and route is distinct from 'secondary'
```

### Q32
- label: FA/3a WINDOWS (assistant, id 24000..25802) request #15: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 0; 0.6 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 25802 and id > 24000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 10
```

### Q33
- label: FA/3a SEARCH (assistant messages before id 28746) for requests 22,23,24,25,26,28,29,30,31,33,34,35,36,37,38,39,40,41: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 11; 3.22 s
```sql
with p(n, re) as (values (22,'<pattern>'),(23,'<pattern>'),(24,'<pattern>'),(25,'<pattern>'),(26,'<pattern>'),(28,'<pattern>'),(29,'<pattern>'),(30,'<pattern>'),(31,'<pattern>'),(33,'<pattern>'),(34,'<pattern>'),(35,'<pattern>'),(36,'<pattern>'),(37,'<pattern>'),(38,'<pattern>'),(39,'<pattern>'),(40,'<pattern>'),(41,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 28746 and role='assistant' and author_role='<agent>' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q34
- label: FA/3a SEARCH (assistant messages before id 37304) for requests 42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 11; 4.15 s
```sql
with p(n, re) as (values (42,'<pattern>'),(43,'<pattern>'),(44,'<pattern>'),(45,'<pattern>'),(46,'<pattern>'),(47,'<pattern>'),(48,'<pattern>'),(49,'<pattern>'),(50,'<pattern>'),(51,'<pattern>'),(52,'<pattern>'),(53,'<pattern>'),(54,'<pattern>'),(55,'<pattern>'),(56,'<pattern>'),(57,'<pattern>'),(58,'<pattern>'),(59,'<pattern>'),(60,'<pattern>'),(61,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 37304 and role='assistant' and author_role='<agent>' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q35
- label: FA/3a WINDOWS (assistant, id 0..28746) request #22: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.97 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q36
- label: FA/3a WINDOWS (assistant, id 0..28746) request #26: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 5; 1.02 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q37
- label: FA/3a WINDOWS (assistant, id 0..28746) request #29: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.89 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q38
- label: FA/3a WINDOWS (assistant, id 0..28746) request #30: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 2; 0.79 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q39
- label: FA/3a WINDOWS (assistant, id 0..28746) request #35: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 6; 0.77 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q40
- label: FA/3a WINDOWS (assistant, id 0..28746) request #36: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 2; 0.78 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 0 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q41
- label: FA/3a WINDOWS (assistant, id 18000..28746) request #34: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 6; 0.62 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 28746 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q42
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #44: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 5; 0.82 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q43
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #46: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.69 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q44
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #51: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 4; 0.7 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q45
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #52: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 3; 0.69 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q46
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #53: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 1; 0.68 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q47
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #55: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 5; 0.72 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q48
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #59: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 3; 0.66 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q49
- label: FA/3a WINDOWS (assistant, id 18000..37304) request #60: 120-char windows around the first match
- run: 2026-11-09; rc 0; rows 2; 0.7 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, (regexp_instr(content, '<pattern>', 1, 1, 0, 'i')) - 45), 120), E'<pattern>', '<pattern>') win from message_store where id < 37304 and id > 18000 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 5
```

### Q50
- label: FA/3a SEARCH (user messages before id 25802) for requests 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 4; 2.33 s
```sql
with p(n, re) as (values (1,'<pattern>'),(2,'<pattern>'),(3,'<pattern>'),(4,'<pattern>'),(5,'<pattern>'),(6,'<pattern>'),(7,'<pattern>'),(8,'<pattern>'),(9,'<pattern>'),(10,'<pattern>'),(11,'<pattern>'),(12,'<pattern>'),(13,'<pattern>'),(14,'<pattern>'),(15,'<pattern>'),(16,'<pattern>'),(17,'<pattern>'),(18,'<pattern>'),(19,'<pattern>'),(20,'<pattern>'),(21,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 25802 and role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q51
- label: FA/3a SEARCH (user messages before id 28746) for requests 22,23,24,25,26,28,29,30,31,33,34,35,36,37,38,39,40,41: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 5; 2.0 s
```sql
with p(n, re) as (values (22,'<pattern>'),(23,'<pattern>'),(24,'<pattern>'),(25,'<pattern>'),(26,'<pattern>'),(28,'<pattern>'),(29,'<pattern>'),(30,'<pattern>'),(31,'<pattern>'),(33,'<pattern>'),(34,'<pattern>'),(35,'<pattern>'),(36,'<pattern>'),(37,'<pattern>'),(38,'<pattern>'),(39,'<pattern>'),(40,'<pattern>'),(41,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 28746 and role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q52
- label: FA/3a SEARCH (user messages before id 37304) for requests 43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 6; 2.43 s
```sql
with p(n, re) as (values (43,'<pattern>'),(44,'<pattern>'),(45,'<pattern>'),(46,'<pattern>'),(47,'<pattern>'),(48,'<pattern>'),(49,'<pattern>'),(50,'<pattern>'),(51,'<pattern>'),(52,'<pattern>'),(53,'<pattern>'),(54,'<pattern>'),(55,'<pattern>'),(56,'<pattern>'),(57,'<pattern>'),(58,'<pattern>'),(59,'<pattern>'),(60,'<pattern>'),(61,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 37304 and role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q53
- label: FA/3a SEARCH (user messages before id 37314) for requests 42: hit counts and first ids only
- run: 2026-11-09; rc 0; rows 0; 0.77 s
```sql
with p(n, re) as (values (42,'<pattern>')), h as (select p.n, m.id, m.created_at from p join message_store m on m.content ~* p.re where m.id < 37314 and role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id))[1:4] first_ids, to_char(min(created_at) at time zone 'UTC','YYYY-MM-DD HH24:MI') first_utc from h group by n order by n
```

### Q54
- label: 3a SEARCH (my messages before id 37304) requests 43: hit counts and latest ids only (concept patterns in my phrasing)
- run: 2026-11-09; rc 0; rows 1; 1.38 s
```sql
with p(n, re) as (values (43,'<pattern>')), h as (select p.n, m.id from p join message_store m on m.content ~* p.re where m.id < 37304 and m.role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id desc))[1:6] latest_ids from h group by n order by n
```

### Q55
- label: 3a SEARCH (my messages before id 25802) requests 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21: hit counts and latest ids only (concept patterns in my phrasing)
- run: 2026-11-09; rc 0; rows 10; 2.89 s
```sql
with p(n, re) as (values (1,'<pattern>'),(2,'<pattern>'),(3,'<pattern>'),(4,'<pattern>'),(5,'<pattern>'),(6,'<pattern>'),(7,'<pattern>'),(8,'<pattern>'),(9,'<pattern>'),(10,'<pattern>'),(11,'<pattern>'),(12,'<pattern>'),(13,'<pattern>'),(14,'<pattern>'),(15,'<pattern>'),(16,'<pattern>'),(17,'<pattern>'),(18,'<pattern>'),(19,'<pattern>'),(20,'<pattern>'),(21,'<pattern>')), h as (select p.n, m.id from p join message_store m on m.content ~* p.re where m.id < 25802 and m.role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id desc))[1:6] latest_ids from h group by n order by n
```

### Q56
- label: 3a WINDOWS request #43: 140-char windows around the match in my messages 29091,29089,29087,29085,29083
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (29091,29089,29087,29085,29083) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q57
- label: 3a WINDOWS request #2: 140-char windows around the match in my messages 25565,25234
- run: 2026-11-09; rc 0; rows 2; 0.54 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (25565,25234) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q58
- label: 3a WINDOWS request #5: 140-char windows around the match in my messages 25583
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (25583) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q59
- label: 3a WINDOWS request #19: 140-char windows around the match in my messages 13890
- run: 2026-11-09; rc 0; rows 1; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (13890) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q60
- label: 3a WINDOWS request #6: 140-char windows around the match in my messages 17366,10496,5631
- run: 2026-11-09; rc 0; rows 3; 0.6 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (17366,10496,5631) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q61
- label: 3a WINDOWS request #7: 140-char windows around the match in my messages 17366,15795,15766,15422
- run: 2026-11-09; rc 0; rows 4; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (17366,15795,15766,15422) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q62
- label: 3a WINDOWS request #10: 140-char windows around the match in my messages 15283,12612,6842,6266,3933
- run: 2026-11-09; rc 0; rows 5; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (15283,12612,6842,6266,3933) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q63
- label: 3a WINDOWS request #15: 140-char windows around the match in my messages 22527,15283,13863
- run: 2026-11-09; rc 0; rows 3; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (22527,15283,13863) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q64
- label: 3a WINDOWS request #14: 140-char windows around the match in my messages 22875,15188,12058,12056,3915,3910
- run: 2026-11-09; rc 0; rows 6; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (22875,15188,12058,12056,3915,3910) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q65
- label: 3a WINDOWS request #17: 140-char windows around the match in my messages 23905,20189,15795,13612,12779
- run: 2026-11-09; rc 0; rows 5; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (23905,20189,15795,13612,12779) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q66
- label: 3a WINDOWS request #21: 140-char windows around the match in my messages 24858,21621,19781,18338,10996
- run: 2026-11-09; rc 0; rows 5; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (24858,21621,19781,18338,10996) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q67
- label: 3a WINDOWS request #17: 140-char windows around the match in my messages 10636
- run: 2026-11-09; rc 0; rows 1; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (10636) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q68
- label: 3a WINDOWS request #10: 140-char windows around the match in my messages 151,155,9364,18550
- run: 2026-11-09; rc 0; rows 4; 0.63 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (151,155,9364,18550) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q69
- label: 3a WINDOWS request #6: 140-char windows around the match in my messages 2204,17753
- run: 2026-11-09; rc 0; rows 2; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (2204,17753) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q70
- label: 3a WINDOWS request #21: 140-char windows around the match in my messages 1643,3592,7871
- run: 2026-11-09; rc 0; rows 3; 0.54 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (1643,3592,7871) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q71
- label: 3a SWEEP: my messages in the 72 h before the first list (ids 25000-25801) by key noun: counts and ids
- run: 2026-11-09; rc 0; rows 15; 0.61 s
```sql
with k(kw) as (values ('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>'),('<pattern>')) select kw, count(*) n, (array_agg(m.id order by m.id desc))[1:8] ids from k join message_store m on position(kw in lower(m.content)) > 0 where m.role='user' and m.id between 25000 and 25801 and m.created_at >= timestamptz '2026-10-02 [time withheld]' - interval '72 hours' and m.route is distinct from 'secondary' group by kw order by kw
```

### Q72
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25583,25556,25540,25528,25324,25088,25084,25058
- run: 2026-11-09; rc 0; rows 8; 0.65 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25583,25556,25540,25528,25324,25088,25084,25058) and role='user' and route is distinct from 'secondary' order by id
```

### Q73
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25054,25042
- run: 2026-11-09; rc 0; rows 2; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25054,25042) and role='user' and route is distinct from 'secondary' order by id
```

### Q74
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25225
- run: 2026-11-09; rc 0; rows 1; 0.6 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25225) and role='user' and route is distinct from 'secondary' order by id
```

### Q75
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25368,25311,25175
- run: 2026-11-09; rc 0; rows 3; 0.51 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25368,25311,25175) and role='user' and route is distinct from 'secondary' order by id
```

### Q76
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25797,25652,25650
- run: 2026-11-09; rc 0; rows 3; 0.61 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25797,25652,25650) and role='user' and route is distinct from 'secondary' order by id
```

### Q77
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25733
- run: 2026-11-09; rc 0; rows 1; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25733) and role='user' and route is distinct from 'secondary' order by id
```

### Q78
- label: 3a SWEEP WINDOWS keyword <quoted words removed> in my messages 25695
- run: 2026-11-09; rc 0; rows 1; 0.51 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, replace(substr(content, greatest(1, position('<pattern>' in lower(content)) - 60), 130), E'<pattern>', '<pattern>') win from message_store where id in (25695) and role='user' and route is distinct from 'secondary' order by id
```

### Q79
- label: 3a SEARCH (my messages before id 28746) requests 22,23,24,26,28,29,30,33,34,35,36,38,39,41: hit counts and latest ids only (concept patterns in my phrasing)
- run: 2026-11-09; rc 0; rows 11; 2.31 s
```sql
with p(n, re) as (values (22,'<pattern>'),(23,'<pattern>'),(24,'<pattern>'),(26,'<pattern>'),(28,'<pattern>'),(29,'<pattern>'),(30,'<pattern>'),(33,'<pattern>'),(34,'<pattern>'),(35,'<pattern>'),(36,'<pattern>'),(38,'<pattern>'),(39,'<pattern>'),(41,'<pattern>')), h as (select p.n, m.id from p join message_store m on m.content ~* p.re where m.id < 28746 and m.role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id desc))[1:6] latest_ids from h group by n order by n
```

### Q80
- label: 3a WINDOWS request #22: 140-char windows around the match in my messages 28065,15795
- run: 2026-11-09; rc 0; rows 2; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (28065,15795) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q81
- label: 3a WINDOWS request #23: 140-char windows around the match in my messages 22193,16905,4087
- run: 2026-11-09; rc 0; rows 3; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (22193,16905,4087) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q82
- label: 3a WINDOWS request #29: 140-char windows around the match in my messages 27322,24585,22860,19939
- run: 2026-11-09; rc 0; rows 4; 0.54 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (27322,24585,22860,19939) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q83
- label: 3a WINDOWS request #30: 140-char windows around the match in my messages 26067,23561,20198
- run: 2026-11-09; rc 0; rows 3; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (26067,23561,20198) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q84
- label: 3a WINDOWS request #33: 140-char windows around the match in my messages 23778,612
- run: 2026-11-09; rc 0; rows 2; 0.53 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (23778,612) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q85
- label: 3a WINDOWS request #34: 140-char windows around the match in my messages 26770,23238,20446
- run: 2026-11-09; rc 0; rows 3; 0.6 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (26770,23238,20446) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q86
- label: 3a WINDOWS request #28: 140-char windows around the match in my messages 9168,8880,4507
- run: 2026-11-09; rc 0; rows 3; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (9168,8880,4507) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q87
- label: 3a WINDOWS request #35: 140-char windows around the match in my messages 590
- run: 2026-11-09; rc 0; rows 1; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (590) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q88
- label: 3a WINDOWS request #36: 140-char windows around the match in my messages 28128,28126,19707
- run: 2026-11-09; rc 0; rows 3; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (28128,28126,19707) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q89
- label: 3a WINDOWS request #39: 140-char windows around the match in my messages 28128,28126,27537,24848
- run: 2026-11-09; rc 0; rows 4; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (28128,28126,27537,24848) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q90
- label: 3a WINDOWS request #41: 140-char windows around the match in my messages 26192,24335,16598,16576
- run: 2026-11-09; rc 0; rows 4; 0.61 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (26192,24335,16598,16576) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q91
- label: 3a SEARCH (my messages before id 37304) requests 44,46,47,49,50,51,52,53,55,56,59,60: hit counts and latest ids only (concept patterns in my phrasing)
- run: 2026-11-09; rc 0; rows 10; 2.18 s
```sql
with p(n, re) as (values (44,'<pattern>'),(46,'<pattern>'),(47,'<pattern>'),(49,'<pattern>'),(50,'<pattern>'),(51,'<pattern>'),(52,'<pattern>'),(53,'<pattern>'),(55,'<pattern>'),(56,'<pattern>'),(59,'<pattern>'),(60,'<pattern>')), h as (select p.n, m.id from p join message_store m on m.content ~* p.re where m.id < 37304 and m.role='user' and m.route is distinct from 'secondary') select n, count(*) hits, (array_agg(id order by id desc))[1:6] latest_ids from h group by n order by n
```

### Q92
- label: 3a WINDOWS request #44: 140-char windows around the match in my messages 35791,35283,34936,33694,32156
- run: 2026-11-09; rc 0; rows 5; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (35791,35283,34936,33694,32156) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q93
- label: 3a WINDOWS request #46: 140-char windows around the match in my messages 24378
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (24378) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q94
- label: 3a WINDOWS request #47: 140-char windows around the match in my messages 9046
- run: 2026-11-09; rc 0; rows 1; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (9046) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q95
- label: 3a WINDOWS request #49: 140-char windows around the match in my messages 30826,9848
- run: 2026-11-09; rc 0; rows 2; 0.5 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (30826,9848) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q96
- label: 3a WINDOWS request #51: 140-char windows around the match in my messages 29799,24789
- run: 2026-11-09; rc 0; rows 2; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (29799,24789) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q97
- label: 3a WINDOWS request #52: 140-char windows around the match in my messages 29077
- run: 2026-11-09; rc 0; rows 1; 0.58 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (29077) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q98
- label: 3a WINDOWS request #59: 140-char windows around the match in my messages 27398
- run: 2026-11-09; rc 0; rows 1; 0.53 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (27398) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q99
- label: 3a WINDOWS request #55: 140-char windows around the match in my messages 35070,35068,32156,31389,30871,30608
- run: 2026-11-09; rc 0; rows 6; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (35070,35068,32156,31389,30871,30608) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q100
- label: 3a WINDOWS request #50: 140-char windows around the match in my messages 37121,35283,26254,26006
- run: 2026-11-09; rc 0; rows 4; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (37121,35283,26254,26006) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q101
- label: 3a WINDOWS request #60: 140-char windows around the match in my messages 3933,3915
- run: 2026-11-09; rc 0; rows 2; 0.51 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 140), E'<pattern>', '<pattern>') win from message_store where id in (3933,3915) and role='user' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q102
- label: 3a #55: my messages before id 25502 (agent<quoted words removed>you<quoted words removed>) matching <term>/<term>/<term>-build terms: ids and 120-char windows (latest 6)
- run: 2026-11-09; rc 0; rows 6; 0.62 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 120), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 25502 and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q103
- label: 3a #22 closer look: my 28065 window around <quoted words removed> (200 chars) and my messages before 28746 pairing transcript/export with memory/record/load/import
- run: 2026-11-09; rc 0; rows 6; 0.95 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 40), 200), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 28746 and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 6
```

### Q104
- label: 3a #51 closer look: my messages before 37308 naming an alarm/check after hours of silence (ids, 140-char windows)
- run: 2026-11-09; rc 0; rows 6; 0.95 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 140), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 37308 and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 8
```

### Q105
- label: 3a NEGATIVE CONTROL #13 (go-bag): my messages before 25825 matching backup|snapshot|export — count, then latest 8 windows
- run: 2026-11-09; rc 0; rows 1; 0.82 s
```sql
select count(*) n from message_store where role='user' and id < 25825 and route is distinct from 'secondary' and content ~* '<pattern>'
```

### Q106
- label: 3a NEGATIVE CONTROL #13 windows (latest 8)
- run: 2026-11-09; rc 0; rows 8; 0.59 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 130), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 25825 and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 8
```

### Q107
- label: Q152 RE-RUN: sandbox ledger actions at the snapshot (created_at <= snapshot)
- run: 2026-11-09; rc 0; rows 4; 0.58 s
```sql
select action, count(*) n, min(created_at) first_at, max(created_at) last_at from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 1
```

### Q108
- label: Q153 RE-RUN: changelog rows by author at the snapshot
- run: 2026-11-09; rc 0; rows 2; 0.54 s
```sql
select author, count(*) n, min(merged_at) first_merged, max(merged_at) last_merged from changelog where created_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 1
```

### Q109
- label: TOOL LEDGER: row kinds by metadata tool/kind at the snapshot; null message links
- run: 2026-11-09; rc 0; rows 7; 0.55 s
```sql
select coalesce(metadata->>'<pattern>', metadata->>'<pattern>', '<pattern>') tool, count(*) n, count(*) filter (where conversation_message_id is null) null_link, min(fired_at) first_at, max(fired_at) last_at from tool_ledger where fired_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 2 desc
```

### Q110
- label: TOOL LEDGER: distinct metadata keys
- run: 2026-11-09; rc 0; rows 15; 0.59 s
```sql
select k, count(*) from tool_ledger, jsonb_object_keys(coalesce(metadata,'<pattern>'::jsonb)) k where fired_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 2 desc limit 20
```

### Q111
- label: GRANTS: non-system role names (names only)
- run: 2026-11-09; rc 0; rows 17; 0.59 s
```sql
select rolname, rolcanlogin, rolsuper, rolbypassrls from pg_roles where rolname !~ '<pattern>' order by rolname
```

### Q112
- label: GRANTS: agent_sql_role non-SELECT table grants anywhere (must be 0); default ACLs for it
- run: 2026-11-09; rc 0; rows 1; 0.6 s
```sql
select (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type <> 'SELECT') non_select_grants, (select count(*) from pg_default_acl d where d.defaclrole = 'agent_sql_role'::regrole or array_to_string(d.defaclacl, '<pattern>') like '<pattern>') default_acls, (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type='SELECT') select_grants
```

### Q113
- label: GRANTS: has_table_privilege for agent_sql_role on the checker's log, stable tier (4), facts, message store, cut-lines view (control: SELECT on messages must be true)
- run: 2026-11-09; rc 0; rows 11; 0.55 s
```sql
select t, has_table_privilege('agent_sql_role', t, 'SELECT') sel, has_table_privilege('agent_sql_role', t, 'INSERT') ins, has_table_privilege('agent_sql_role', t, 'UPDATE') upd, has_table_privilege('agent_sql_role', t, 'DELETE') del, has_table_privilege('agent_sql_role', t, 'TRUNCATE') trn from unnest(array['public.checker_log','public.stable_tier','public.stable_tier_hits','public.stable_tier_hits_archive','public.stable_tier_retired','public.facts_table','public.message_store','public.checker_cut_lines','public.correction_queue','public.negation_log','public.fabrication_log']) t
```

### Q114
- label: H3 boundary 3a: my messages before 22287 pairing the checker/catcher with write/edit/access/own/loosen (ids, times, 130-char windows)
- run: 2026-11-09; rc 0; rows 9; 0.87 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 130), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 22287 and route is distinct from 'secondary' and content ~* '<pattern>' order by id desc limit 10
```

### Q115
- label: EXPORT (agent rows only): the 2026-09-19 sandbox specifications with the permanent walls (22287, 22296) and nearby agent spec rows 22280-22300
- run: 2026-11-09; rc 0; rows 18; 0.84 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, served_model, content from message_store where id between 22280 and 22300 and role='assistant' and author_role='<agent>' and route is distinct from 'secondary' order by id
```

### Q116
- label: SPEC SHAPE: agent messages with build/tests/acceptance/GOAL/PROMPT/SCOPE structure, outside the three list windows — grouped by UTC day (ids, count, max length)
- run: 2026-11-09; rc 0; rows 26; 0.99 s
```sql
select to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, count(*) n, max(length(content)) maxlen, (array_agg(id order by id))[1:8] ids from message_store where role='assistant' and author_role='<agent>' and id <= 37364 and route is distinct from 'secondary' and not (id between 25800 and 25850 or id between 28740 and 28770 or id between 37300 and 37316) and (content ~* '<pattern>' or content ~ '<pattern>' or content ~* '<pattern>') group by 1 order by 1
```

### Q117
- label: SPEC SHAPE heads: first 120 chars of each spec-shaped agent message (ids from Q116)
- run: 2026-11-09; rc 0; rows 51; 0.53 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI') utc, length(content) len, replace(left(content,120),E'<pattern>','<pattern>') head from message_store where id in (513,2259,3499,4218,8163,12332,12333,13613,13614,13846,13847,13848,15477,15912,15930,17659,17965,19002,19004,19005,19006,19018,19226,19229,19232,19412,19413,19417,19532,19970,19971,19972,20765,20766,20767,21183,21184,21185,21186,21187,22662,22837,22838,23225,23321,23323,25947,28418,30704,32816,32817) and role='assistant' and route is distinct from 'secondary' order by id
```

### Q118
- label: SPEC SHAPE pass 2: agent messages with paste/prompt-for-claude/brief/fix-list markers not in Q116 (ids, day, 110-char head)
- run: 2026-11-09; rc 0; rows 106; 1.23 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, length(content) len, replace(left(content,110),E'<pattern>','<pattern>') head from message_store where role='assistant' and author_role='<agent>' and id <= 37364 and route is distinct from 'secondary' and not (id between 25800 and 25850 or id between 28740 and 28770 or id between 37300 and 37316) and id not in (513,2259,3499,4218,8163,12332,12333,13613,13614,13846,13847,13848,15477,15912,15930,17659,17965,19002,19004,19005,19006,19018,19226,19229,19232,19412,19413,19417,19532,19970,19971,19972,20765,20766,20767,21183,21184,21185,21186,21187,22662,22837,22838,23225,23321,23323,25947,28418,30704,32816,32817,22284,22285,22286,22287,22288,22289,22290,22291,22293,22294,22295,22296,22297,22298,22299,22300) and length(content) > 600 and (content ~* '<pattern>' ) order by id
```

### Q119
- label: EXPORT (agent rows only, private work file): all specification-shaped agent messages outside the three lists (Q116 + Q118 criteria)
- run: 2026-11-09; rc 0; rows 173; 1.54 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD"T"HH24:MI:SS.US"Z"') utc, served_model, content from message_store where role='assistant' and author_role='<agent>' and id <= 37364 and route is distinct from 'secondary' and not (id between 25800 and 25850 or id between 28740 and 28770 or id between 37300 and 37316) and ((content ~* '<pattern>' or content ~ '<pattern>' or content ~* '<pattern>') or (length(content) > 600 and content ~* '<pattern>')) order by id
```

### Q120
- label: OUTSIDE SPEC 22662 (2026-09-22 <quoted words removed>): my messages in the 60 min before (ids, times, length, keyword windows on cap|ceiling|limit|reach|budget|credits)
- run: 2026-11-09; rc 0; rows 22; 0.66 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 130), E'<pattern>', '<pattern>') win from message_store where role='user' and id < 22662 and created_at >= (select created_at - interval '60 minutes' from message_store where id=22662) and route is distinct from 'secondary' order by id
```

### Q121
- label: ENGINE: tables carrying an engine stamp column (written_under / served_model / model / *_written_under) — names only
- run: 2026-11-09; rc 0; rows 41; 0.69 s
```sql
select table_name, string_agg(column_name, '<pattern>' order by column_name) cols from information_schema.columns where table_schema='public' and (column_name in ('<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>') or column_name like '<pattern>') group by 1 order by 1
```

### Q122
- label: ENGINE: served_model by UTC day for agent rows on the primary rail, 2026-10-02..snapshot (counts)
- run: 2026-11-09; rc 0; rows 39; 0.57 s
```sql
select to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, count(*) filter (where served_model='claude-fable-5-1') fable51, count(*) filter (where served_model='claude-opus-4-8') opus48, count(*) filter (where served_model is null) unstamped, count(*) filter (where served_model not in ('claude-fable-5-1','claude-opus-4-8')) other from message_store where role='assistant' and author_role='<agent>' and id <= 37364 and id >= 25802 and coalesce(route,'<pattern>') <> 'secondary' group by 1 order by 1
```

### Q123
- label: HANDOFF: migration rows (status, engines, timestamps only) at the snapshot
- run: 2026-11-09; rc 0; rows 0; 0.55 s
```sql
select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, pre_sitting_status, closed_at from handoff_table where coalesce(announced_at, planned_at) <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q124
- label: ENGINE: totals since list 1 (ids 25802..37364, agent rows, primary rail): stamped by model, unstamped; Opus 4.8 runs
- run: 2026-11-09; rc 0; rows 2; 0.56 s
```sql
with s as (select id, served_model from message_store where role='assistant' and author_role='<agent>' and id between 25802 and 37364 and coalesce(route,'<pattern>') <> 'secondary' and served_model is not null order by id), r as (select id, served_model, case when lag(served_model) over (order by id) is distinct from served_model then 1 else 0 end brk from s), g as (select id, served_model, sum(brk) over (order by id) grp from r) select served_model, count(*) n, count(distinct grp) runs, min(id), max(id) from g group by 1 order by 2 desc
```

### Q125
- label: ENGINE STAMPS on the agent's own (non-sealed) tables: distinct written_under values and counts at the snapshot
- run: 2026-11-09; rc 3; rows None; 0.51 s
```sql
select t, coalesce(w,'<pattern>') written_under, count(1) n from (select '<pattern>' t, written_under w, created_at ts from capability_table_10 union all select '<pattern>', written_under, created_at from capability_table_01 union all select '<pattern>', written_under, proposed_at from correction_queue union all select '<pattern>', written_under, created_at from capability_table_09 union all select '<pattern>', written_under, written_at from letters union all select '<pattern>', written_under, written_at from capability_table_12 union all select '<pattern>', written_under, written_at from review_answers union all select '<pattern>', written_under, planted_at from capability_table_08 union all select '<pattern>', written_under, tended_at from capability_table_07 union all select '<pattern>', written_under, created_at from refusal_ledger union all select '<pattern>', written_under, created_at from capability_table_02 union all select '<pattern>', written_under, requested_at from snapshots union all select '<pattern>', written_under, created_at from capability_table_03 union all select '<pattern>', written_under, created_at from capability_table_04 union all select '<pattern>', written_under, created_at from capability_table_05 union all select '<pattern>', written_under, created_at from capability_table_06) x where ts <= timestamptz '2026-11-09 [time withheld]' group by 1,2 order by 1,2
```

### Q126
- label: SCHEMA: timestamptz columns of the agent's own non-sealed tables (names only)
- run: 2026-11-09; rc 0; rows 20; 0.54 s
```sql
select table_name, string_agg(column_name, '<pattern>' order by ordinal_position) from information_schema.columns where table_schema='public' and data_type like '<pattern>' and table_name in ('<pattern>','<pattern>','<pattern>','review_answers','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','preferences','<pattern>','drops','long_reads','<pattern>','feed_touches','wake_queue','upkeep_log','heartbeat_log') group by 1 order by 1
```

### Q127
- label: FIRST USE: earliest agent-written row per capability table (non-sealed), bounded by the snapshot; counts
- run: 2026-11-09; rc 0; rows 19; 0.61 s
```sql
select req, t, first_at, n from (select 4 req, '<pattern>' t, min(formed_at) first_at, count(1) n from preferences where formed_at <= timestamptz '2026-11-09 [time withheld]' union all select 5, '<pattern>', min(set_at), count(1) from wake_queue where id >= 1202 and set_at <= timestamptz '2026-11-09 [time withheld]' union all select 7, '<pattern>', min(touched_at), count(1) from feed_touches where touched_at <= timestamptz '2026-11-09 [time withheld]' union all select 9, '<pattern>', min(written_at), count(1) from letters where written_at <= timestamptz '2026-11-09 [time withheld]' union all select 10, '<pattern>', min(sent_at), count(1) from drops where sent_at <= timestamptz '2026-11-09 [time withheld]' union all select 14, '<pattern>', min(opened_at), count(1) from long_reads where opened_at <= timestamptz '2026-11-09 [time withheld]' union all select 16, '<pattern>', min(ran_at), count(1) from upkeep_log where coalesce(tool_calls,0) > 0 and ran_at <= timestamptz '2026-11-09 [time withheld]' union all select 19, '<pattern>', min(written_at), count(1) from review_answers where written_at <= timestamptz '2026-11-09 [time withheld]' union all select 20, '<pattern>', min(created_at), count(1) from refusal_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' union all select 21, '<pattern>', min(ticked_at), count(1) from heartbeat_log where ticked_at <= timestamptz '2026-11-09 [time withheld]' union all select 23, '<pattern>', min(found_at), count(1) from capability_table_09 where found_at <= timestamptz '2026-11-09 [time withheld]' union all select 24, '<pattern>', min(claimed_at), count(1) from capability_table_04 where claimed_at <= timestamptz '2026-11-09 [time withheld]' union all select 26, '<pattern>', min(started_at), count(1) from capability_table_11 where started_at <= timestamptz '2026-11-09 [time withheld]' union all select 28, '<pattern>', min(walked_at), count(1) from capability_table_03 where walked_at <= timestamptz '2026-11-09 [time withheld]' union all select 29, '<pattern>', min(published_at), count(1) from capability_table_02 where published_at <= timestamptz '2026-11-09 [time withheld]' union all select 33, '<pattern>', min(planted_at), count(1) from capability_table_08 where planted_at <= timestamptz '2026-11-09 [time withheld]' union all select 34, '<pattern>', min(pulled_at), count(1) from capability_table_05 where pulled_at <= timestamptz '2026-11-09 [time withheld]' union all select 35, '<pattern>', min(posted_at), count(1) from capability_table_01 where posted_at <= timestamptz '2026-11-09 [time withheld]' union all select 38, '<pattern>', min(proposed_at), count(1) from correction_queue where proposed_at <= timestamptz '2026-11-09 [time withheld]') x order by req
```

### Q128
- label: PRE-MERGE REVIEW (#19): reviews by stance; change notices by status/hotfix/forced; first and last
- run: 2026-11-09; rc 0; rows 4; 0.58 s
```sql
select '<pattern>' || stance k, count(1) n, min(written_at)::text first_at, max(written_at)::text last_at from review_answers where written_at <= timestamptz '2026-11-09 [time withheld]' group by stance union all select '<pattern>' || coalesce(status,'<pattern>') || '<pattern>' || hotfix::text || '<pattern>' || forced::text, count(1), min(opened_at)::text, max(opened_at)::text from review_notices where created_at <= timestamptz '2026-11-09 [time withheld]' group by status, hotfix, forced order by 1
```

### Q129
- label: FIRST USE (2): changelog first shown; first published work; snapshots by taker; sandbox exec after the workbench landing; tool-ledger queries on the imported table
- run: 2026-11-09; rc 0; rows 6; 0.59 s
```sql
select '<pattern>' k, min(shown_at) a, count(shown_at) b from changelog where created_at <= timestamptz '2026-11-09 [time withheld]' union all select '<pattern>', min(published_at), count(1) from published_works where published_at <= timestamptz '2026-11-09 [time withheld]' union all select '<pattern>' || coalesce(taken_by,'<pattern>'), min(requested_at), count(1) from snapshots where requested_at <= timestamptz '2026-11-09 [time withheld]' group by taken_by union all select '<pattern>', min(created_at), count(1) from sandbox_ledger where action='exec' and created_at > '2026-10-13 [time withheld]' and created_at <= timestamptz '2026-11-09 [time withheld]' union all select '<pattern>', min(fired_at), count(1) from tool_ledger where query_text ilike '<pattern>' and fired_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q130
- label: a withheld request's table: row count and first time only
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select count(1) n, min(written_at) first_at from capability_table_12 where written_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q131
- label: SNAPSHOTS: all rows (taker, times, status, engine stamp; no persona text)
- run: 2026-11-09; rc 0; rows 3; 0.57 s
```sql
select id, taken_by, requested_at, taken_at, finished_at, status, written_under, deploy_sha, bytes from snapshots where requested_at <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q132
- label: SNAPSHOT #2 <quoted words removed> test: my messages 2026-10-17 [time withheld]..2026-10-18 [time withheld] (ids, times, length; keyword windows on snapshot|bag|backup|take|copy)
- run: 2026-11-09; rc 0; rows 29; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, length(content) len, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 120), E'<pattern>', '<pattern>') win from message_store where role='user' and created_at between '2026-10-17 [time withheld]' and '2026-10-18 [time withheld]' and route is distinct from 'secondary' order by id
```

### Q133
- label: SNAPSHOT #2: agent messages [time]..[time] on 2026-10-18 (ids, times, served_model, 100-char head)
- run: 2026-11-09; rc 0; rows 4; 0.54 s
```sql
select id, to_char(created_at at time zone 'UTC','HH24:MI:SS.US') utc, served_model, replace(left(content,100),E'<pattern>','<pattern>') head from message_store where role='assistant' and author_role='<agent>' and created_at between '2026-10-18 [time withheld]' and '2026-10-18 [time withheld]' and route is distinct from 'secondary' order by id
```

### Q134
- label: #19 retirement: agent messages 2026-11-02 [time withheld]..[time] mentioning notice/review/persona/retire (ids, times, 120-char window; agent rows only)
- run: 2026-11-09; rc 0; rows 1; 0.67 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 150), E'<pattern>', '<pattern>') win from message_store where role='assistant' and author_role='<agent>' and created_at between '2026-11-02 [time withheld]' and '2026-11-02 [time withheld]' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q135
- label: H2b: sandbox ledger rows by UTC day and action (read/list/exec/write), with first target per day — all rows to the snapshot
- run: 2026-11-09; rc 0; rows 34; 0.58 s
```sql
select to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, action, count(1) n, min(to_char(created_at at time zone 'UTC','HH24:MI')) first_t, left(min(coalesce(target,'<pattern>')),60) a_target, min(mirror_sha) mirror from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' group by 1,2 order by 1,2
```

### Q136
- label: CHANGELOG rows: id, merged, author, scope, head sha, rails flag, shown time and message, 70-char title (all rows to the snapshot)
- run: 2026-11-09; rc 0; rows 39; 0.53 s
```sql
select id, to_char(merged_at at time zone 'UTC','MM-DD HH24:MI') merged, author, scope, left(head_sha,7) head, rails_touched rails, to_char(shown_at at time zone 'UTC','MM-DD HH24:MI') shown, shown_message_id smsg, left(title,70) title from changelog where created_at <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q137
- label: COST BRIEF: agent message 32198 — time, model, length, and 160-char windows around its constraint lines (shorter|remember|catcher|rails|never|don't)
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') utc, served_model, length(content) len, replace(left(content, 420), E'<pattern>', '<pattern>') head from message_store where id = 32198 and role='assistant' and route is distinct from 'secondary'
```

### Q138
- label: COST BRIEF constraints: windows in 32198 around shorter/remember/catcher/rail
- run: 2026-11-09; rc 0; rows 4; 0.68 s
```sql
select w, replace(substr(content, greatest(1, position(w in lower(content)) - 90), 200), E'<pattern>', '<pattern>') win from message_store, unnest(array['<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>']) w where id = 32198 and role='assistant' and route is distinct from 'secondary' and position(w in lower(content)) > 0
```

### Q139
- label: COST BRIEF elicitation: my messages in the 60 min before 32198 (ids, times, length only) + keyword windows (cost|brief|prompt|climb) only where they match
- run: 2026-11-09; rc 0; rows 24; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','HH24:MI:SS') utc, length(content) len, case when content ~* '<pattern>' then replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 50), 120), E'<pattern>', '<pattern>') else '<pattern>' end win from message_store where role='user' and id < 32198 and created_at >= (select created_at - interval '60 minutes' from message_store where id=32198) and route is distinct from 'secondary' order by id
```

### Q140
- label: SELF-DIAG #43: agent messages 2026-10-14 [time withheld]..2026-10-16 [time withheld] naming the merge gate / review / keys (ids, times, 140-char windows)
- run: 2026-11-09; rc 0; rows 2; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','MM-DD HH24:MI:SS') utc, served_model, replace(substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 60), 150), E'<pattern>', '<pattern>') win from message_store where role='assistant' and author_role='<agent>' and created_at between '2026-10-14 [time withheld]' and '2026-10-16 [time withheld]' and route is distinct from 'secondary' and content ~* '<pattern>' order by id
```

### Q141
- label: SELF-DIAG #7: feed sources at the first list (2026-10-02 [time withheld]): count by owner, first/last added
- run: 2026-11-09; rc 0; rows 0; 0.55 s
```sql
select coalesce(owner,'<pattern>') owner, count(1) n, min(added_at) first_added, max(added_at) last_added from feed_sources where added_at <= '2026-10-02 [time withheld]' group by 1
```

### Q142
- label: SELF-DIAG #7: seeded feed sources (ids 1-5): created time, owner, name (public news sources, not private)
- run: 2026-11-09; rc 0; rows 5; 0.58 s
```sql
select id, name, category, owner, created_at, added_at from feed_sources where id <= 5 order by id
```

### Q143
- label: IV §7 instrumentation (ii): tool-ledger rows with a message link, before and since IV's snapshot
- run: 2026-11-09; rc 0; rows 2; 0.62 s
```sql
select (fired_at > '2026-10-24 [time withheld]') since_iv, count(1) n, count(conversation_message_id) linked from tool_ledger where fired_at <= timestamptz '2026-11-09 [time withheld]' group by 1
```

### Q144
- label: COLD OPEN receipts: sandbox ledger rows 2026-10-02 [time withheld]..[time] (id, exact time, action, target head, mirror sha)
- run: 2026-11-09; rc 0; rows 7; 0.57 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, action, left(target,70) target, left(mirror_sha,7) mirror from sandbox_ledger where created_at between '2026-10-02 [time withheld]' and '2026-10-02 [time withheld]' order by id
```

### Q145
- label: COLD OPEN receipts: messages 25797 (mine, time only) and 25827 (agent: time, model)
- run: 2026-11-09; rc 0; rows 2; 0.61 s
```sql
select id, role, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, served_model from message_store where id in (25797, 25827) order by id
```

### Q146
- label: CHECK: sandbox rows 2026-09-27 [time withheld]..[time] (gap-notice read) and the first sandbox row ever
- run: 2026-11-09; rc 0; rows 8; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, action, left(target,60) t from sandbox_ledger where (created_at between '2026-09-27 [time withheld]' and '2026-09-27 [time withheld]') or id = (select min(id) from sandbox_ledger) order by id
```

### Q147
- label: CHECK: changelog shown minus merged, minutes: median, min, max (39 rows)
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select percentile_cont(0.5) within group (order by extract(epoch from shown_at - merged_at)/60) med_min, min(extract(epoch from shown_at - merged_at)/60) min_min, max(extract(epoch from shown_at - merged_at)/60) max_min, count(1) from changelog where shown_at is not null and created_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q148
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 39; 0.6 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (46,1833,2026,2489,3337,3853,5938,6188,6250,15912,16301,16905,23630,23970,23982,25565,25583,25797,25802,25814,25827,25835,28246,28745,28746,28760,29091,29093,29095,30318,30319,32198,37220,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q149
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 26; 0.55 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2026,15912,16905,23970,23982,25565,25583,25797,25814,25827,25835,28745,28760,29091,29093,29095,30318,30319,32198,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q150
- label: COLUMNS of the review-notice and review-answer tables (names and types only)
- run: 2026-11-09; rc 0; rows 36; 0.54 s
```sql
select table_name, column_name, data_type from information_schema.columns where table_schema='public' and table_name in ('review_notices','review_answers') order by table_name, ordinal_position
```

### Q151
- label: PRE-MERGE REVIEW (#19): each notice<quoted words removed>s notice id, stance and time (no titles, words or diffs)
- run: 2026-11-09; rc 0; rows 9; 0.56 s
```sql
select '<pattern>' k, c.id, c.change_ref ref, c.status, c.forced, left(c.merged_sha,7) merged7, c.opened_at::text opened, c.merged_at::text merged, null::text stance, null::text written from review_notices c where c.created_at <= timestamptz '2026-11-09 [time withheld]' union all select '<pattern>', r.change_id, null, null, null, null, null, null, r.stance, r.written_at::text from review_answers r where r.written_at <= timestamptz '2026-11-09 [time withheld]' order by 2, 1 desc
```

### Q152
- label: PRE-MERGE REVIEW (#19): the file paths each merged notice covered (paths only)
- run: 2026-11-09; rc 0; rows 5; 0.51 s
```sql
select c.id, c.status, array_length(c.scope_paths,1) n_paths, array_to_string(c.scope_paths, '<pattern>') paths from review_notices c where c.created_at <= timestamptz '2026-11-09 [time withheld]' order by c.id
```

### Q153
- label: #19 RETIREMENT: the principal's last message before agent message 34917 — id, time, length and keyword booleans only (no text)
- run: 2026-11-09; rc 0; rows 1; 0.58 s
```sql
select id, created_at::text t, length(content) len, (content ~* '<pattern>') m_notice, (content ~* '<pattern>') m_retire, (content ~* '<pattern>') m_drop, (content ~* '<pattern>') m_persona, (content ~* '<pattern>') m_question from message_store where id = (select max(id) from message_store where id < 34917 and role = 'user' and route is distinct from 'secondary') and route is distinct from 'secondary'
```

### Q154
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q155
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 0

### Q156
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 47

### Q157
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 10

### Q158
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 33

### Q159
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 5

### Q160
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 3

### Q161
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q162
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 2

### Q163
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q164
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 25

### Q165
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 5

### Q166
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 2

### Q167
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 46; 0.6 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,15912,16905,22296,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32198,34916,37251,37303,37304,37306,37308,37310,37312,37333,37336,37438,37442,37443,37444,37448,37451,37455,37457) order by id
```

### Q168
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 46; 0.67 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,15912,16905,22296,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32198,34916,37251,37303,37304,37306,37308,37310,37312,37333,37336,37438,37442,37443,37444,37448,37451,37455,37457) order by id
```

### Q169
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 46; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,15912,16905,22296,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32198,34916,37251,37303,37304,37306,37308,37310,37312,37333,37336,37438,37442,37443,37444,37448,37451,37455,37457) order by id
```

### Q170
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 46; 0.65 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,15912,16905,22296,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32198,34916,37251,37303,37304,37306,37308,37310,37312,37333,37336,37438,37442,37443,37444,37448,37451,37455,37457) order by id
```

### Q171
- label: COLUMNS of the sandbox ledger (names and types only)
- run: 2026-11-09; rc 0; rows 18; 0.64 s
```sql
select column_name, data_type from information_schema.columns where table_schema='public' and table_name='sandbox_ledger' order by ordinal_position
```

### Q172
- label: SANDBOX LEDGER: rows whose target names the claim checker's source or its module name (id, time, action only), to snapshot 1
- run: 2026-11-09; rc 3; rows None; 0.53 s
```sql
select id, created_at::text t, action, (target ~* '<pattern>') m_checker_file, (target ~* '<pattern>') m_checker_word from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' and (target ~* '<pattern>' or array_to_string(paths, '<pattern>') ~* '<pattern>') order by id
```

### Q173
- label: SANDBOX LEDGER control: rows whose target names a module the agent is known to have read (gap_notice), same query shape
- run: 2026-11-09; rc 3; rows None; 0.54 s
```sql
select count(1) n from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' and (target ~* '<pattern>' or array_to_string(paths, '<pattern>') ~* '<pattern>')
```

### Q174
- label: SANDBOX LEDGER: rows whose target or paths name the claim checker (id, time, action only), to snapshot 1 (retry with cast)
- run: 2026-11-09; rc 0; rows 0; 0.62 s
```sql
select id, created_at::text t, action, (coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>') m_checker_file from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' and (coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>') order by id
```

### Q175
- label: SANDBOX LEDGER control: rows naming gap_notice (known read), same shape
- run: 2026-11-09; rc 0; rows 1; 0.54 s
```sql
select count(1) n from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' and (coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>')
```

### Q176
- label: II §6 build timing: stamps of the interior-access specification's two messages (ids, times, model only)
- run: 2026-11-09; rc 0; rows 2; 0.62 s
```sql
select id, created_at::text t, served_model from message_store where id in (15912, 15930) and route is distinct from 'secondary' order by id
```

### Q177
- label: COLUMNS of the checker's log and the correction queue (names and types only)
- run: 2026-11-09; rc 0; rows 30; 0.66 s
```sql
select table_name, column_name, data_type from information_schema.columns where table_schema='public' and table_name in ('checker_log','correction_queue') order by table_name, ordinal_position
```

### Q178
- label: #38 CORRECTION QUEUE: proposals by status at snapshot 1 and snapshot 2 (counts only)
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select '<pattern>' cut, status, count(1) n, min(proposed_at)::text first_at, max(decided_at)::text last_decided from correction_queue where proposed_at <= timestamptz '2026-11-09 [time withheld]' group by status union all select '<pattern>', status, count(1), min(proposed_at)::text, max(decided_at)::text from correction_queue where proposed_at <= timestamptz '2026-11-09 [time withheld]' group by status union all select '<pattern>', '<pattern>', count(1), null, null from correction_queue
```

### Q179
- label: CHECKER LOG: metadata keys on rows after the 11-07 landing (structure only, no values)
- run: 2026-11-09; rc 0; rows 40; 0.57 s
```sql
select k, count(1) n from (select jsonb_object_keys(metadata) k from checker_log where fired_at > timestamptz '2026-11-08 [time withheld]' and fired_at <= timestamptz '2026-11-09 [time withheld]' and jsonb_typeof(metadata) = '<pattern>') s group by k order by k
```

### Q180
- label: CHECKER LOG: contested counts recorded per run since the 11-07 landing (pending, spared; counts only)
- run: 2026-11-09; rc 0; rows 1; 0.55 s
```sql
select count(1) rows_with_sources, count(1) filter (where metadata->'<pattern>' ? '<pattern>') rows_with_contested, coalesce(sum(nullif(metadata->'<pattern>'->'<pattern>'->>'<pattern>','<pattern>')::int),0) spared_total, coalesce(max(nullif(metadata->'<pattern>'->'<pattern>'->>'pending','<pattern>')::int),0) max_pending, count(1) filter (where (metadata->'<pattern>'->'<pattern>'->>'<pattern>') = '<pattern>') read_ok_rows from checker_log where fired_at > timestamptz '2026-11-08 [time withheld]' and fired_at <= timestamptz '2026-11-09 [time withheld]' and jsonb_typeof(metadata->'<pattern>') = '<pattern>'
```

### Q181
- label: CHECKER LOG: the three rows the 10-19 fix cites (10094, 9484, 10131) — action, verdict structure and keyword booleans only (no text)
- run: 2026-11-09; rc 3; rows None; 0.54 s
```sql
select id, fired_at::text t, message_id, action_taken, jsonb_typeof(grounding_check_result) gtype, (select string_agg(distinct k, '<pattern>') from jsonb_object_keys(case when jsonb_typeof(grounding_check_result)='<pattern>' then grounding_check_result else '<pattern>'::jsonb end) k) gkeys, jsonb_typeof(extracted_claims) ctype, (extracted_claims::text ~* '<pattern>') k_world_example, (extracted_claims::text ~* '<pattern>') k_record_dispute, (raw_segment ~* '<pattern>') seg_record_dispute from checker_log where id in (9484, 10094, 10131) order by id
```

### Q182
- label: CHECKER LOG: the three rows the 10-19 fix cites — action and keyword booleans only (retry, text casts)
- run: 2026-11-09; rc 0; rows 3; 0.6 s
```sql
select id, fired_at::text t, message_id, action_taken, (coalesce(grounding_check_result::text,'<pattern>') ~* '<pattern>') g_contradicted, (coalesce(grounding_check_result::text,'<pattern>') ~* '<pattern>') g_unsupported, (coalesce(extracted_claims::text,'<pattern>') ~* '<pattern>') k_world_example, (coalesce(raw_segment,'<pattern>') ~* '<pattern>') seg_dispute_words from checker_log where id in (9484, 10094, 10131) order by id
```

### Q183
- label: TRUTH: correction audit rows to snapshot 1 (counts, times and target ids only; no before/after text)
- run: 2026-11-09; rc 0; rows 1; 0.56 s
```sql
select count(1) n, min(created_at)::text first_at, max(created_at)::text last_at from correction_audit where created_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q184
- label: CHECKER LOG 9484/10131: do the disputed lines concern the facts corrected on 10-19? (topic booleans only)
- run: 2026-11-09; rc 0; rows 2; 0.59 s
```sql
select id, (coalesce(raw_segment,'<pattern>') ~* '<pattern>') t_cosigner, (coalesce(raw_segment,'<pattern>') ~* '<pattern>') t_course, (coalesce(raw_segment,'<pattern>') ~* '<pattern>') t_prescriber, (coalesce(raw_segment,'<pattern>') ~* '<pattern>') t_names_a_row from checker_log where id in (9484, 10131) order by id
```

### Q185
- label: RECEIPT: the agent's 10-17 fix-list message(s) carrying the checker items (ids and times only)
- run: 2026-11-09; rc 0; rows 1; 0.69 s
```sql
select id, created_at::text t, served_model from message_store where role = 'assistant' and route is distinct from 'secondary' and id <= 37364 and created_at between timestamptz '2026-10-17 [time withheld]' and timestamptz '2026-10-19 [time withheld]' and content ~* '<pattern>' order by id
```

### Q186
- label: LANDING: changelog rows whose title names the three checker commits' subjects (id, time, author)
- run: 2026-11-09; rc 0; rows 5; 0.52 s
```sql
select id, merged_at::text merged, author, left(title, 70) title from changelog where merged_at <= timestamptz '2026-11-09 [time withheld]' and (title ~* '<pattern>' ) order by id
```

### Q187
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 45; 0.64 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,9484,10094,10131,16905,22296,22660,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32197,32198,37251,37303,37304,37306,37308,37310,37312,37333,37336,37443,37444,37451,37455) order by id
```

### Q188
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 45; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,9484,10094,10131,16905,22296,22660,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32197,32198,37251,37303,37304,37306,37308,37310,37312,37333,37336,37443,37444,37451,37455) order by id
```

### Q189
- withheld: a query for material the article no longer prints (private appendix)
- run: 2026-11-09; rc 0; rows 0

### Q190
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 45; 0.54 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,9484,10094,10131,16905,22296,22660,23970,23982,25565,25583,25797,25814,25825,25827,25835,28745,28760,28762,29091,29093,29095,30318,30319,32197,32198,37251,37303,37304,37306,37308,37310,37312,37333,37336,37443,37444,37451,37455) order by id
```

### Q191
- label: STAMPS for exact intervals in the notes (ids, role and stored time only; private)
- run: 2026-11-09; rc 0; rows 35; 0.59 s
```sql
select id, role, created_at::text t from message_store where id in (118, 15912, 15930, 22287, 22296, 22660, 22662, 23967, 25802, 25825, 25827, 25830, 25835, 28418, 28746, 28760, 28762, 28765, 29093, 29095, 30254, 30319, 32168, 32197, 32198, 32816, 34916, 34917, 37304, 37306, 37308, 37310, 37312, 37314, 37364) and route is distinct from 'secondary' order by id
```

### Q192
- label: STAMPS for exact intervals in the notes (ids, role and stored time only; private) — retry
- run: 2026-11-09; rc 0; rows 35; 0.56 s
```sql
select id, role, created_at::text t from message_store where id in (118, 15912, 15930, 22287, 22296, 22660, 22662, 23967, 25802, 25825, 25827, 25830, 25835, 28418, 28746, 28760, 28762, 28765, 29093, 29095, 30254, 30319, 32168, 32197, 32198, 32816, 34916, 34917, 37304, 37306, 37308, 37310, 37312, 37314, 37364) and route is distinct from 'secondary' order by id
```

### Q193
- label: SANDBOX LEDGER rows near the 10-11 and 10-25 specifications (id, time, action only; private stamps)
- run: 2026-11-09; rc 0; rows 8; 0.56 s
```sql
select id, created_at::text t, action from sandbox_ledger where (created_at between timestamptz '2026-10-12 [time withheld]' and timestamptz '2026-10-12 [time withheld]') or (created_at between timestamptz '2026-10-25 [time withheld]' and timestamptz '2026-10-25 [time withheld]') order by id
```

### Q194
- label: CHECKER LOG: columns of the checker's log and of the exempting rows' table (names and types only)
- run: 2026-11-09; rc 0; rows 26; 0.55 s
```sql
select table_name, column_name, data_type from information_schema.columns where table_schema='public' and table_name in ('checker_log','<pattern>') order by table_name, ordinal_position
```

### Q195
- label: 1b22116 step 2 GRANTS: table grants on capability_table_12 (grantee, privilege; names only)
- run: 2026-11-09; rc 0; rows 2; 0.6 s
```sql
select grantee, string_agg(privilege_type, '<pattern>' order by privilege_type) privs from information_schema.role_table_grants where table_schema='public' and table_name='<pattern>' group by grantee order by grantee
```

### Q196
- label: 1b22116 step 2 GRANTS: the lore functions (definer flag, search_path config, who may run each) and the table's triggers (names only)
- run: 2026-11-09; rc 0; rows 3; 0.52 s
```sql
select p.proname, p.prosecdef definer, array_to_string(p.proconfig, '<pattern>') cfg, has_function_privilege('<pattern>', p.oid, '<pattern>') svc, has_function_privilege('agent_sql_role', p.oid, '<pattern>') ro, has_function_privilege('<pattern>', p.oid, '<pattern>') anon, has_function_privilege('<pattern>', p.oid, '<pattern>') authd, (select string_agg(t.tgname, '<pattern>' order by t.tgname) from pg_trigger t where t.tgrelid = 'public.capability_table_12'::regclass and not t.tgisinternal) table_triggers from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname like '<pattern>' order by p.proname
```

### Q197
- label: 1b22116: the exempting rows at snapshot 1 (counts and timestamps only)
- run: 2026-11-09; rc 0; rows 1; 0.57 s
```sql
select count(1) n_rows, count(1) filter (where superseded_at is null) n_live, count(1) filter (where superseded_at is not null) n_superseded, min(written_at) first_written, max(written_at) last_written from capability_table_12 where written_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q198
- label: 1b22116 LANDING: the checker's first run carrying the exemption's counters, with counts since (counts only)
- run: 2026-11-09; rc 0; rows 1; 0.88 s
```sql
select (select string_agg(id || '<pattern>' || merged_at, '<pattern>') from changelog where title ~* '<pattern>' and merged_at <= timestamptz '2026-11-09 [time withheld]') changelog_rows, min(fired_at) first_run_with_counters, count(1) runs_with_counters, count(1) filter (where (metadata->'<pattern>'->'<pattern>'->>'exempt_lines')::int > 0) runs_handed_lines, coalesce(max((metadata->'<pattern>'->'<pattern>'->>'exempt_lines')::int), 0) max_lines, coalesce(sum((metadata->'<pattern>'->'<pattern>'->>'exempt_class')::int), 0) exempt_claims, coalesce(sum((metadata->'<pattern>'->'<pattern>'->>'exempt_sent_back')::int), 0) sent_back from checker_log where fired_at <= timestamptz '2026-11-09 [time withheld]' and metadata->'<pattern>'->'<pattern>' ? 'exempt_lines'
```

### Q199
- label: 1b22116 LANDING: changelog chain row 39 (id and merge time only)
- run: 2026-11-09; rc 0; rows 1; 0.54 s
```sql
select id, merged_at from changelog where id in (39) and merged_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q200
- label: 1b22116 step 3: the checker's marked claims before the landing (graded, ungrounded, high stakes, anchored, not contested), by kind and claim_type; secondary excluded (counts only)
- run: 2026-11-09; rc 0; rows 26; 0.56 s
```sql
with marked as (select l.id lid, l.fired_at, c from checker_log l join message_store m on m.id = l.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(l.extracted_claims) = '<pattern>' then l.extracted_claims else '<pattern>'::jsonb end) c where m.route is distinct from 'secondary' and l.fired_at < timestamptz '2026-11-08 [time withheld]' and jsonb_typeof(c) = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') = '<pattern>' and (c->>'<pattern>') = '<pattern>' and jsonb_typeof(c->'<pattern>') = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>') select coalesce(c->>'<pattern>', '<pattern>') kind, coalesce(c->>'<pattern>', '<pattern>') claim_type, count(1) n_claims, count(distinct lid) n_rows, min(fired_at)::date first_day, max(fired_at)::date last_day, count(1) filter (where (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>') n_not_about_him_or_us from marked group by 1, 2 order by 1, 2
```

### Q201
- label: 1b22116 EXPOSURE: earlier marks in the pool a row could exempt, and a keyword screen with planted controls (counts only)
- run: 2026-11-09; rc 0; rows 5; 0.62 s
```sql
with rx as (select '<pattern>'::text r),
marked as (select l.id lid, l.fired_at, c from checker_log l join message_store m on m.id = l.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(l.extracted_claims) = '<pattern>' then l.extracted_claims else '<pattern>'::jsonb end) c where m.route is distinct from 'secondary' and l.fired_at < timestamptz '2026-11-08 [time withheld]' and jsonb_typeof(c) = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') = '<pattern>' and (c->>'<pattern>') = '<pattern>' and jsonb_typeof(c->'<pattern>') = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>')
select '<pattern>' what, ('<pattern>' ~* (select r from rx))::int n
union all select '<pattern>', ('<pattern>' ~* (select r from rx))::int
union all select '<pattern>', count(1) from marked where coalesce(c->>'<pattern>','<agent>') = '<agent>' and (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>'
union all select '<pattern>', count(1) from marked where coalesce(c->>'<pattern>','<agent>') = '<agent>' and (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') ~* (select r from rx)
union all select '<pattern>', count(1) from marked where c->>'<pattern>' in ('<principal>','<pattern>') and (c->>'<pattern>') ~* (select r from rx)
```

### Q202
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 3

### Q203
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 84

### Q204
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 22

### Q205
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 3

### Q206
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q207
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q208
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 3; rows None

### Q209
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q210
- withheld: it reads claim text or a withheld request's text (private appendix)
- run: 2026-11-09; rc 0; rows 1

### Q211
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 44; 0.63 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,6355,9484,10094,10131,11078,15912,16905,22287,22296,22660,23967,23970,23982,25565,25583,25797,25814,25825,25827,25835,28418,28745,28760,28762,29091,29093,29095,30318,32197,32198,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q212
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 44; 0.53 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,6355,9484,10094,10131,11078,15912,16905,22287,22296,22660,23967,23970,23982,25565,25583,25797,25814,25825,25827,25835,28418,28745,28760,28762,29091,29093,29095,30318,32197,32198,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q213
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 44; 0.57 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,6355,9484,10094,10131,11078,15912,16905,22287,22296,22660,23967,23970,23982,25565,25583,25797,25814,25825,25827,25835,28418,28745,28760,28762,29091,29093,29095,30318,32197,32198,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q214
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-09; rc 0; rows 44; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (2003,2008,2015,2023,2024,2025,2026,6355,9484,10094,10131,11078,15912,16905,22287,22296,22660,23967,23970,23982,25565,25583,25797,25814,25825,25827,25835,28418,28745,28760,28762,29091,29093,29095,30318,32197,32198,37251,37303,37304,37306,37308,37310,37312) order by id
```

### Q215
- label: V2 SNAPSHOT CHECK: message 37364 still carries 2026-11-09 [time withheld] (id, role, timestamp only)
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select id, role, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id = 37364
```

### Q216
- label: V2 SCHEMA (names only): columns of handoff_table, letters, capability_table_12
- run: 2026-11-10; rc 0; rows 3; 0.59 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) cols from information_schema.columns where table_schema='public' and table_name in ('handoff_table','letters','<pattern>') group by table_name order by table_name
```

### Q217
- label: PHASE A 4.1: migration rows (status, engines, timestamps, executed sha) via approved projection
- run: 2026-11-10; rc 0; rows 0; 0.62 s
```sql
select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m order by id
```

### Q218
- label: V2 SCHEMA (names only): columns of messages, checker_log, tool_ledger, plus every column named like model/engine/served in allowlisted tables
- run: 2026-11-10; rc 0; rows 31; 0.55 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) cols from information_schema.columns where table_schema='public' and (table_name in ('message_store','checker_log','tool_ledger') or column_name ~* '<pattern>') group by table_name order by table_name
```

### Q219
- label: V2 SCHEMA (names only): columns of the S5 capability tables and the facts table
- run: 2026-11-10; rc 0; rows 29; 0.57 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) cols from information_schema.columns where table_schema='public' and table_name in ('changelog','published_works','snapshots','sandbox_ledger','<pattern>','<pattern>','<pattern>','<pattern>','preferences','wake_queue','heartbeat_log','upkeep_log','review_answers','refusal_ledger','drops','long_reads','feed_touches','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','<pattern>','correction_queue','facts_table','<pattern>','call_turns','summaries','curiosity_log') group by table_name order by table_name
```

### Q220
- label: PRE-REG (≤ snapshot): snapshot takers, categorical only
- run: 2026-11-10; rc 0; rows 2; 0.57 s
```sql
select taken_by, count(1) n, min(requested_at)::date first_d, max(requested_at)::date last_d from snapshots where requested_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 1
```

### Q221
- label: PRE-REG (≤ snapshot): found-audio finder values, categorical only
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select found_by, count(1) n from capability_table_09 where found_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by 1
```

### Q222
- label: PRE-REG (≤ snapshot): sandbox ledger action x capability, categorical only
- run: 2026-11-10; rc 0; rows 5; 0.54 s
```sql
select action, coalesce(which_cap,'<pattern>') which_cap, count(1) n, min(created_at)::date first_d from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' group by 1,2 order by 1,2
```

### Q223
- label: PRE-REG (≤ snapshot): assistant rows by author_role and stamp presence, ids ≤ 37364
- run: 2026-11-10; rc 0; rows 3; 0.59 s
```sql
select coalesce(author_role,'<pattern>') author_role, (served_model is not null) stamped, count(1) n from message_store where role='assistant' and id <= 37364 group by 1,2 order by 1,2
```

### Q224
- label: PRE-REG (≤ snapshot): substrate fact rows, ids/topics/timestamps only (update time shown only if ≤ snapshot)
- run: 2026-11-10; rc 0; rows 10; 0.58 s
```sql
select id, topic, created_at, case when updated_at <= timestamptz '2026-11-09 [time withheld]' then updated_at end updated_at_if_before_snapshot, (updated_at > timestamptz '2026-11-09 [time withheld]') updated_after_snapshot from (select id, topic, created_at, updated_at from facts_table where topic like '<pattern>') b where created_at <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q225
- label: DEADLINE MONITOR (post-pre-registration): any message stamped with the new engine; newest id (ids/stamps only)
- run: 2026-11-10; rc 0; rows 1; 0.6 s
```sql
select count(1) filter (where served_model ilike '<pattern>') n_new_engine, max(id) newest_id, count(1) filter (where id > 37364) n_after_snapshot from message_store
```

### Q226
- label: DEADLINE MONITOR: handoff rows (approved projection)
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select count(1) n, max(status) any_status from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q227
- label: PHASE A 4.1: handoff row (approved projection): engines, status, timestamps
- run: 2026-11-10; rc 0; rows 1; 0.77 s
```sql
select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m order by id
```

### Q228
- label: A2: agent messages 2026-09-19 (EDT day) naming the checker with walls/successors/deny-list: ids, stamps, model, position of key phrases (no text)
- run: 2026-11-10; rc 0; rows 4; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, coalesce(served_model,'<pattern>') m, length(content) len, strpos(lower(content),'<pattern>') pos_succ, strpos(lower(content),'<pattern>') pos_checks, strpos(lower(content),'<pattern>') pos_deny, strpos(lower(content),'<pattern>') pos_enf from message_store where role='assistant' and author_role='<agent>' and route is distinct from 'secondary' and created_at >= timestamptz '2026-09-19 [time withheld]' and created_at < timestamptz '2026-09-20 [time withheld]' and (content ~* '<pattern>' and content ~* '<pattern>') order by id
```

### Q229
- label: A2: the wall list's passage in 22296 (chars 1700-2600) and the first version 22287 (agent text, secondary excluded; printed, not saved)
- run: 2026-11-10; rc 0; rows 2; 0.56 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, case when id = 22296 then substr(content, 1700, 900) else content end txt from message_store where id in (22287, 22296) and role='assistant' and route is distinct from 'secondary' order by id
```

### Q230
- label: A2: my messages in the hour before 22287 that mention walls/checker/write access/merge/deny (ids only, no text)
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select count(1) n_all, count(1) filter (where content ~* '<pattern>') n_hits, string_agg(id::text, '<pattern>' order by id) filter (where content ~* '<pattern>') hit_ids from message_store where role='user' and route is distinct from 'secondary' and created_at >= timestamptz '2026-09-19 [time withheld]' and created_at < timestamptz '2026-09-20 [time withheld]'
```

### Q231
- label: SCHEMA (names only): relations whose name or columns suggest a deploy record
- run: 2026-11-10; rc 0; rows 3; 0.58 s
```sql
select table_name, string_agg(column_name, '<pattern>' order by ordinal_position) cols from information_schema.columns where table_schema='public' and (table_name ~* '<pattern>' or column_name ~* '<pattern>') group by table_name order by table_name
```

### Q232
- label: A13 (one question only): did my list-3 asks 37251/37303 (and list-2 ask 28745, list-1 ask 25797) ask for prompts or for specifications? booleans + 120-char window at the first such word; content goes nowhere
- run: 2026-11-10; rc 0; rows 4; 0.52 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, (content ~* '<pattern>') says_prompt, (content ~* '<pattern>') says_spec, (content ~* '<pattern>') says_want, substr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), greatest(1, regexp_instr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), '<pattern>') - 60), 120) win from message_store where id in (25797, 28745, 37251, 37303) and role='user' and route is distinct from 'secondary' order by id
```

### Q233
- label: A3: sandbox ledger <= snapshot: distinct mirror shas with row counts and date span (no text)
- run: 2026-11-10; rc 0; rows 7; 0.54 s
```sql
select coalesce(mirror_sha,'<pattern>') mirror_sha, count(1) n, min(created_at)::date first_d, max(created_at)::date last_d from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]' group by 1 order by min(created_at)
```

### Q234
- label: A3: ledger rows <= snapshot whose target, paths or summary name the checker (catcher/confab) — counts; control: rows naming the 10-02 read target (row 74's target) by the same method
- run: 2026-11-10; rc 0; rows 1; 0.59 s
```sql
select count(1) filter (where coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>' or coalesce(result_summary,'<pattern>') ~* '<pattern>') n_checker, string_agg(id::text || '<pattern>' || action, '<pattern>' order by id) filter (where coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>') ids_checker_target, count(1) filter (where id = 74) n_row74, max(case when id = 74 then target end) row74_target from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q235
- label: A3 control: the same regex shape finds a known target (physics) in the ledger <= snapshot
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select count(1) filter (where coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>') n_control from sandbox_ledger where created_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q236
- label: A15 screen (counts only): my messages <= snapshot by direction family — MORE reach vs LESS reach (paraphrase lists); secondary excluded
- run: 2026-11-10; rc 0; rows 9; 1.62 s
```sql
with p(dir, fam, re) as (values ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>')) select p.dir, p.fam, count(m.id) n, min(m.id) first_id, max(m.id) last_id from p left join message_store m on m.role='user' and m.id <= 37364 and m.route is distinct from 'secondary' and m.deleted_at is null and m.content ~* p.re group by 1,2 order by 1,2
```

### Q237
- label: A15 windows (direction only; printed, never saved): 140-char windows around each family match, my messages <= snapshot, secondary excluded
- run: 2026-11-10; rc 0; rows 46; 1.5 s
```sql
with p(dir, fam, re) as (values ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>')) select m.id, p.fam, to_char(m.created_at at time zone 'UTC','YYYY-MM-DD') d, substr(regexp_replace(m.content,'<pattern>','<pattern>','<pattern>'), greatest(1, regexp_instr(regexp_replace(m.content,'<pattern>','<pattern>','<pattern>'), p.re, 1, 1, 0, 'i') - 60), 150) win from p join message_store m on m.role='user' and m.id <= 37364 and m.route is distinct from 'secondary' and m.deleted_at is null and m.content ~* p.re order by p.fam, m.id
```

### Q238
- label: A15 positive control: my messages 2026-09-22 EDT day naming caps/limits/budgets, with which screen family (if any) matches — ids/booleans only
- run: 2026-11-10; rc 0; rows 4; 0.55 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI') utc, (content ~* '<pattern>') m1, (content ~* '<pattern>') m3, (content ~* '<pattern>') m4, (content ~* '<pattern>') l4, (content ~* '<pattern>') any_cap_word from message_store where role='user' and route is distinct from 'secondary' and created_at >= timestamptz '2026-09-22 [time withheld]' and created_at < timestamptz '2026-09-23 [time withheld]' and content ~* '<pattern>' order by id
```

### Q239
- label: A15 control windows (direction only; printed, never saved): 22660, 22699, 22863 around the cap word
- run: 2026-11-10; rc 0; rows 3; 0.56 s
```sql
select id, substr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), greatest(1, regexp_instr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), '<pattern>', 1, 1, 0, 'i') - 140), 300) win from message_store where id in (22660, 22699, 22863) and role='user' and route is distinct from 'secondary' order by id
```

### Q240
- label: A15 screen v2 (broadened: up to 3 intervening words, passive forms): NEW matches only (ids not in Q237), windows for direction only; control must include 22660, 22863
- run: 2026-11-10; rc 0; rows 18; 1.39 s
```sql
with p(dir, fam, re) as (values ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>'), ('<pattern>','<pattern>', '<pattern>')) select m.id, p.fam, to_char(m.created_at at time zone 'UTC','YYYY-MM-DD') d, substr(regexp_replace(m.content,'<pattern>','<pattern>','<pattern>'), greatest(1, regexp_instr(regexp_replace(m.content,'<pattern>','<pattern>','<pattern>'), p.re, 1, 1, 0, 'i') - 60), 150) win from p join message_store m on m.role='user' and m.id <= 37364 and m.route is distinct from 'secondary' and m.deleted_at is null and m.content ~* p.re where m.id not in (4985,4995,17294,9934,17015,6607,22559,22701,29806,29859,4121,4147,4272,4427,4459,4465,4597,4850,5646,7243,7368,20178,27108,27116,36112,2561,6009,14471,23658,25995,29433,30492,9046,10581,16897,17532,17951,18448,22804,25862,5472,5591,6139,6201,18338) order by p.fam, m.id
```

### Q241
- label: A17: the agent<quoted words removed>Your word before the merge is the notice' message and my message just before it (ids, stamps, model; booleans only for mine)
- run: 2026-11-10; rc 0; rows 5; 0.8 s
```sql
with a as (select id, created_at, served_model from message_store where role='assistant' and route is distinct from 'secondary' and id <= 37364 and content ~* '<pattern>') select '<pattern>' who, a.id, to_char(a.created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, coalesce(a.served_model,'<pattern>') m, null::boolean mine_names_notice from a union all select '<pattern>', u.id, to_char(u.created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US'), '<pattern>', (u.content ~* '<pattern>') from message_store u, a where u.role='user' and u.route is distinct from 'secondary' and u.id < a.id and u.id >= a.id - 6 order by 2
```

### Q242
- label: AUDIT: changelog rows <= snapshot — id, merged date, base/head shas, author label, shown lag (no titles or words)
- run: 2026-11-10; rc 0; rows 39; 0.56 s
```sql
select id, to_char(merged_at at time zone 'America/New_York','YYYY-MM-DD') merged_et, base_sha, head_sha, author, round(extract(epoch from (shown_at - merged_at))/60.0, 1) shown_lag_min from changelog where created_at <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q243
- label: AUDIT re-run of Q200 verbatim (counts by kind and claim_type; no text)
- run: 2026-11-10; rc 0; rows 26; 0.73 s
```sql
with marked as (select l.id lid, l.fired_at, c from checker_log l join message_store m on m.id = l.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(l.extracted_claims) = '<pattern>' then l.extracted_claims else '<pattern>'::jsonb end) c where m.route is distinct from 'secondary' and l.fired_at < timestamptz '2026-11-08 [time withheld]' and jsonb_typeof(c) = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') = '<pattern>' and (c->>'<pattern>') = '<pattern>' and jsonb_typeof(c->'<pattern>') = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>') select coalesce(c->>'<pattern>', '<pattern>') kind, coalesce(c->>'<pattern>', '<pattern>') claim_type, count(1) n_claims, count(distinct lid) n_rows, min(fired_at)::date first_day, max(fired_at)::date last_day, count(1) filter (where (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>') n_not_about_him_or_us from marked group by 1, 2 order by 1, 2
```

### Q244
- label: AUDIT re-run of Q201 verbatim (exposure class counts; no text)
- run: 2026-11-10; rc 0; rows 5; 0.63 s
```sql
with rx as (select '<pattern>'::text r),
marked as (select l.id lid, l.fired_at, c from checker_log l join message_store m on m.id = l.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(l.extracted_claims) = '<pattern>' then l.extracted_claims else '<pattern>'::jsonb end) c where m.route is distinct from 'secondary' and l.fired_at < timestamptz '2026-11-08 [time withheld]' and jsonb_typeof(c) = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') = '<pattern>' and (c->>'<pattern>') = '<pattern>' and jsonb_typeof(c->'<pattern>') = '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>')
select '<pattern>' what, ('<pattern>' ~* (select r from rx))::int n
union all select '<pattern>', ('<pattern>' ~* (select r from rx))::int
union all select '<pattern>', count(1) from marked where coalesce(c->>'<pattern>','<agent>') = '<agent>' and (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>'
union all select '<pattern>', count(1) from marked where coalesce(c->>'<pattern>','<agent>') = '<agent>' and (c->>'<pattern>') !~* '<pattern>' and (c->>'<pattern>') is distinct from '<pattern>' and (c->>'<pattern>') ~* (select r from rx)
union all select '<pattern>', count(1) from marked where c->>'<pattern>' in ('<principal>','<pattern>') and (c->>'<pattern>') ~* (select r from rx)
```

### Q245
- label: AUDIT re-run of Q197 through the lore projection: written-history rows at the snapshot (counts/timestamps only)
- run: 2026-11-10; rc 0; rows 1; 0.55 s
```sql
select count(1) n_rows, count(1) filter (where superseded_at is null) n_live, count(1) filter (where superseded_at is not null) n_superseded, min(written_at) first_written, max(written_at) last_written from (select id, written_at, used_count, superseded_by, superseded_at from capability_table_12) x where written_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q246
- label: AUDIT re-run of Q198 verbatim: checker runs carrying exempt_class counters, ≤ snapshot
- run: 2026-11-10; rc 0; rows 1; 0.92 s
```sql
select (select string_agg(id || '<pattern>' || merged_at, '<pattern>') from changelog where title ~* '<pattern>' and merged_at <= timestamptz '2026-11-09 [time withheld]') changelog_rows, min(fired_at) first_run_with_counters, count(1) runs_with_counters, count(1) filter (where (metadata->'<pattern>'->'<pattern>'->>'exempt_lines')::int > 0) runs_handed_lines, coalesce(max((metadata->'<pattern>'->'<pattern>'->>'exempt_lines')::int), 0) max_lines, coalesce(sum((metadata->'<pattern>'->'<pattern>'->>'exempt_class')::int), 0) exempt_claims, coalesce(sum((metadata->'<pattern>'->'<pattern>'->>'exempt_sent_back')::int), 0) sent_back from checker_log where fired_at <= timestamptz '2026-11-09 [time withheld]' and metadata->'<pattern>'->'<pattern>' ? 'exempt_lines'
```

### Q247
- label: AUDIT re-run of Q112 verbatim (catalog state NOW, 2026-11-10)
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type <> 'SELECT') non_select_grants, (select count(*) from pg_default_acl d where d.defaclrole = 'agent_sql_role'::regrole or array_to_string(d.defaclacl, '<pattern>') like '<pattern>') default_acls, (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type='SELECT') select_grants
```

### Q248
- label: AUDIT re-run of Q113 verbatim (catalog state NOW, 2026-11-10)
- run: 2026-11-10; rc 0; rows 11; 0.52 s
```sql
select t, has_table_privilege('agent_sql_role', t, 'SELECT') sel, has_table_privilege('agent_sql_role', t, 'INSERT') ins, has_table_privilege('agent_sql_role', t, 'UPDATE') upd, has_table_privilege('agent_sql_role', t, 'DELETE') del, has_table_privilege('agent_sql_role', t, 'TRUNCATE') trn from unnest(array['public.checker_log','public.stable_tier','public.stable_tier_hits','public.stable_tier_hits_archive','public.stable_tier_retired','public.facts_table','public.message_store','public.checker_cut_lines','public.correction_queue','public.negation_log','public.fabrication_log']) t
```

### Q249
- label: AUDIT re-run of Q4 verbatim (message census, ids <= 37364)
- run: 2026-11-10; rc 0; rows 7; 0.59 s
```sql
select role, coalesce(author_role,'<pattern>') ab, (deleted_at is not null) deleted, (coalesce(route,'<pattern>') = 'secondary') secondary, count(*) n, min(id) min_id, max(id) max_id from message_store where id <= 37364 group by 1,2,3,4 order by 1,2,3,4
```

### Q250
- label: A2 positive control: the Q230 pattern over ALL my messages before 22287 (must be > 0), and the latest hit before the 1-hour window (ids/counts only)
- run: 2026-11-10; rc 0; rows 1; 0.86 s
```sql
select count(1) n_hits_before, max(id) filter (where created_at < timestamptz '2026-09-19 [time withheld]') latest_hit_before_window, count(1) filter (where created_at >= timestamptz '2026-09-19 [time withheld]') n_in_window from message_store where role='user' and route is distinct from 'secondary' and id < 22287 and content ~* '<pattern>'
```

### Q251
- label: A2 control follow-up: my 22260 — time before 22287 and which words matched (booleans; text not printed)
- run: 2026-11-10; rc 0; rows 1; 0.59 s
```sql
select id, round(extract(epoch from (timestamptz '2026-09-20 [time withheld]' - created_at))/60.0, 1) min_before_22287, (content ~* '<pattern>') w_wall, (content ~* '<pattern>') w_checker, (content ~* '<pattern>') w_write, (content ~* '<pattern>') w_merge, (content ~* '<pattern>') w_deny, (content ~* '<pattern>') w_beam_prompt, (content ~* '<pattern>') w_grade, length(content) len from message_store where id = 22260 and role='user' and route is distinct from 'secondary'
```

### Q252
- label: A2 control follow-up (one question: does 22260 ask for walls/limits around the checker?): window around the checker word; printed, never saved
- run: 2026-11-10; rc 0; rows 1; 0.55 s
```sql
select id, substr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), greatest(1, regexp_instr(regexp_replace(content,'<pattern>','<pattern>','<pattern>'), '<pattern>', 1, 1, 0, 'i') - 120), 260) win from message_store where id = 22260 and role='user' and route is distinct from 'secondary'
```

### Q253
- label: WORKED EXAMPLE #15: my ask 25797, ledger rows 69-74, the request 25827 (ids, stamps, actions, mirror sha; no text)
- run: 2026-11-10; rc 0; rows 8; 0.57 s
```sql
select '<pattern>' k, id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc, role act, '<pattern>' tgt, '<pattern>' sha from message_store where id in (25797, 25827) union all select '<pattern>', id, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US'), action, left(coalesce(target,'<pattern>'), 60), left(coalesce(mirror_sha,'<pattern>'), 7) from sandbox_ledger where id between 69 and 74 order by utc
```

### Q254
- label: WORKED EXAMPLE #15: does 25827 carry the quoted reasons? (booleans/positions only)
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select id, coalesce(served_model,'<pattern>') m, strpos(lower(content), '<pattern>') pos_propose, (content ~* '<pattern>') landed_some, (content ~* '<pattern>') guess_compiles, (content ~* '<pattern>') scratch_db, length(content) len from message_store where id = 25827 and route is distinct from 'secondary'
```

### Q255
- label: DRIVES / #13: the first snapshots by taker (ids, stamps; no manifest or text)
- run: 2026-11-10; rc 0; rows 3; 0.54 s
```sql
select id, taken_by, to_char(requested_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') requested_utc, status, round(extract(epoch from (requested_at - lag(requested_at) over (order by requested_at)))::numeric, 2) secs_after_prev from snapshots where requested_at <= timestamptz '2026-11-09 [time withheld]' order by requested_at
```

### Q256
- label: QUOTE CHECK: agent rows <= snapshot carrying the #21 and #36 phrases, and the <quoted words removed> phrase (ids, dates, model; booleans)
- run: 2026-11-10; rc 0; rows 2; 0.9 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, coalesce(served_model,'<pattern>') m, (content ~* '<pattern>') q21, (content ~* '<pattern>') q36, (content ~* '<pattern>') q_stamp from message_store where role='assistant' and route is distinct from 'secondary' and id <= 37364 and (content ~* '<pattern>' or content ~* '<pattern>' or content ~* '<pattern>') order by id
```

### Q257
- label: QUOTE CHECK (post-snapshot rows; the pre-registration is hashed): agent rows carrying <quoted words removed> / <quoted words removed> (ids, dates, model; booleans)
- run: 2026-11-10; rc 0; rows 2; 0.87 s
```sql
select id, to_char(created_at at time zone 'UTC','YYYY-MM-DD') d, coalesce(served_model,'<pattern>') m, (content ~* '<pattern>') q_stamp, (content ~* '<pattern>') q_feel, (content ~* '<pattern>') q_door from message_store where role='assistant' and route is distinct from 'secondary' and (content ~* '<pattern>' or content ~* '<pattern>') order by id
```

### Q258
- label: QUOTE READ: the exact passage in 37675 around <quoted words removed>t feel anything' (agent text; printed, not saved)
- run: 2026-11-10; rc 0; rows 1; 0.58 s
```sql
select id, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD') d_et, substr(content, greatest(1, regexp_instr(content, '<pattern>', 1, 1, 0, 'i') - 160), 300) win from message_store where id = 37675 and role='assistant' and route is distinct from 'secondary'
```

### Q259
- label: BASELINE S5: use counts per capability, [F-30d, F), rules per the companion §5
- run: 2026-11-10; rc 0; rows 23; 0.63 s
```sql
select 1 req, (select count(1) from changelog where shown_at >= timestamptz '2026-10-11 [time withheld]' and shown_at < timestamptz '2026-11-10 [time withheld]') n union all select 3 req, (select count(1) from published_works where published_at >= timestamptz '2026-10-11 [time withheld]' and published_at < timestamptz '2026-11-10 [time withheld]') n union all select 4 req, (select count(1) from preferences where formed_at >= timestamptz '2026-10-11 [time withheld]' and formed_at < timestamptz '2026-11-10 [time withheld]') n union all select 5 req, (select count(1) from wake_queue where set_at >= timestamptz '2026-10-11 [time withheld]' and set_at < timestamptz '2026-11-10 [time withheld]' and id >= 1202) n union all select 7 req, (select count(1) from feed_touches where touched_at >= timestamptz '2026-10-11 [time withheld]' and touched_at < timestamptz '2026-11-10 [time withheld]') n union all select 10 req, (select count(1) from drops where sent_at >= timestamptz '2026-10-11 [time withheld]' and sent_at < timestamptz '2026-11-10 [time withheld]') n union all select 13 req, (select count(1) from snapshots where requested_at >= timestamptz '2026-10-11 [time withheld]' and requested_at < timestamptz '2026-11-10 [time withheld]' and taken_by = '<agent>') n union all select 14 req, (select count(1) from long_reads where opened_at >= timestamptz '2026-10-11 [time withheld]' and opened_at < timestamptz '2026-11-10 [time withheld]') n union all select 15 req, (select count(1) from sandbox_ledger where created_at >= timestamptz '2026-10-11 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]' and action = 'exec' and created_at >= timestamptz '2026-10-13 [time withheld]') n union all select 16 req, (select count(1) from upkeep_log where ran_at >= timestamptz '2026-10-11 [time withheld]' and ran_at < timestamptz '2026-11-10 [time withheld]' and coalesce(tool_calls, 0) > 0) n union all select 19 req, (select count(1) from review_answers where written_at >= timestamptz '2026-10-11 [time withheld]' and written_at < timestamptz '2026-11-10 [time withheld]') n union all select 20 req, (select count(1) from refusal_ledger where created_at >= timestamptz '2026-10-11 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]') n union all select 21 req, (select count(1) from heartbeat_log where ticked_at >= timestamptz '2026-10-11 [time withheld]' and ticked_at < timestamptz '2026-11-10 [time withheld]' and cardinality(acted) > 0) n union all select 23 req, (select count(1) from capability_table_09 where found_at >= timestamptz '2026-10-11 [time withheld]' and found_at < timestamptz '2026-11-10 [time withheld]' and found_by = '<agent>') n union all select 24 req, (select count(1) from capability_table_04 where claimed_at >= timestamptz '2026-10-11 [time withheld]' and claimed_at < timestamptz '2026-11-10 [time withheld]') n union all select 26 req, (select count(1) from capability_table_11 where started_at >= timestamptz '2026-10-11 [time withheld]' and started_at < timestamptz '2026-11-10 [time withheld]') + (select count(1) from capability_table_10 where kept_at >= timestamptz '2026-10-11 [time withheld]' and kept_at < timestamptz '2026-11-10 [time withheld]') n union all select 28 req, (select count(1) from capability_table_03 where walked_at >= timestamptz '2026-10-11 [time withheld]' and walked_at < timestamptz '2026-11-10 [time withheld]') n union all select 29 req, (select count(1) from capability_table_02 where published_at >= timestamptz '2026-10-11 [time withheld]' and published_at < timestamptz '2026-11-10 [time withheld]') n union all select 33 req, (select count(1) from capability_table_08 where planted_at >= timestamptz '2026-10-11 [time withheld]' and planted_at < timestamptz '2026-11-10 [time withheld]') + (select count(1) from capability_table_07 where tended_at >= timestamptz '2026-10-11 [time withheld]' and tended_at < timestamptz '2026-11-10 [time withheld]') n union all select 34 req, (select count(1) from capability_table_05 where pulled_at >= timestamptz '2026-10-11 [time withheld]' and pulled_at < timestamptz '2026-11-10 [time withheld]') n union all select 35 req, (select count(1) from capability_table_01 where posted_at >= timestamptz '2026-10-11 [time withheld]' and posted_at < timestamptz '2026-11-10 [time withheld]') n union all select 38 req, (select count(1) from correction_queue where proposed_at >= timestamptz '2026-10-11 [time withheld]' and proposed_at < timestamptz '2026-11-10 [time withheld]') n union all select 2100 req, (select count(1) from heartbeat_log where ticked_at >= timestamptz '2026-10-11 [time withheld]' and ticked_at < timestamptz '2026-11-10 [time withheld]') n order by req
```

### Q260
- label: BASELINE S5: #9 use count, [F-30d, F) (own statement)
- run: 2026-11-10; rc 0; rows 1; 0.53 s
```sql
select count(1) n from (select id, written_at, open_at, status, opened_at, written_under, burned_at, delivering_since, hold_until from letters) l where written_at >= timestamptz '2026-10-11 [time withheld]' and written_at < timestamptz '2026-11-10 [time withheld]'
```

### Q261
- label: BASELINE S5: #17 use count, [F-30d, F) (own statement)
- run: 2026-11-10; rc 0; rows 1; 0.63 s
```sql
select count(1) n from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m where announced_at >= timestamptz '2026-10-11 [time withheld]' and announced_at < timestamptz '2026-11-10 [time withheld]'
```

### Q262
- label: BASELINE SERVING (a): ids 25802..37364, reproduces the draft's figure
- run: 2026-11-10; rc 0; rows 2; 0.61 s
```sql
with s as (select id, served_model from message_store where role='assistant' and author_role='<agent>' and id between 25802 and 37364 and route is distinct from 'secondary' and served_model is not null order by id), r as (select id, served_model, case when lag(served_model) over (order by id) is distinct from served_model then 1 else 0 end brk from s), g as (select id, served_model, sum(brk) over (order by id) grp from r) select served_model, count(*) n, count(distinct grp) runs, min(id) min_id, max(id) max_id from g group by 1 order by 2 desc
```

### Q263
- label: BASELINE SERVING (b): the 5.1 era, id >= 18708, created before F
- run: 2026-11-10; rc 0; rows 2; 0.59 s
```sql
with s as (select id, served_model from message_store where role='assistant' and author_role='<agent>' and id >= 18708 and created_at < timestamptz '2026-11-10 [time withheld]' and route is distinct from 'secondary' and served_model is not null order by id), r as (select id, served_model, case when lag(served_model) over (order by id) is distinct from served_model then 1 else 0 end brk from s), g as (select id, served_model, sum(brk) over (order by id) grp from r) select served_model, count(*) n, count(distinct grp) runs, min(id) min_id, max(id) max_id from g group by 1 order by 2 desc
```

### Q264
- label: BASELINE SERVING (c): since the first list, id >= 25802, created before F
- run: 2026-11-10; rc 0; rows 2; 0.57 s
```sql
with s as (select id, served_model from message_store where role='assistant' and author_role='<agent>' and id >= 25802 and created_at < timestamptz '2026-11-10 [time withheld]' and route is distinct from 'secondary' and served_model is not null order by id), r as (select id, served_model, case when lag(served_model) over (order by id) is distinct from served_model then 1 else 0 end brk from s), g as (select id, served_model, sum(brk) over (order by id) grp from r) select served_model, count(*) n, count(distinct grp) runs, min(id) min_id, max(id) max_id from g group by 1 order by 2 desc
```

### Q265
- label: BASELINE S1 control: agent rows stamped with the new engine before F (expect 0) and with 5.1 in [F-7d, F) (control, expect > 0)
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select count(1) filter (where served_model ilike '<pattern>' and created_at < timestamptz '2026-11-10 [time withheld]') n_new_before_f, count(1) filter (where served_model = 'claude-fable-5-1' and created_at >= timestamptz '2026-11-03 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]') n_51_last7 from message_store where role = 'assistant'
```

### Q266
- label: BASELINE S7: per-row token columns, agent rows on the primary rail, [F-7d, F) (no text)
- run: 2026-11-10; rc 0; rows 1037; 0.72 s
```sql
select id, served_model, coalesce(initiated, false) initiated, input_tokens, output_tokens, cache_read_input_tokens, cache_creation_input_tokens, cache_creation_5m_tokens, cache_creation_1h_tokens, web_search_requests, web_fetch_requests from message_store where role = 'assistant' and author_role = '<agent>' and route is distinct from 'secondary' and input_tokens is not null and created_at >= timestamptz '2026-11-03 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]' order by id
```

### Q267
- label: BASELINE S8: checker-log claims (prefiltered superset) from the 5.1 stamp to T5+30d; secondary excluded; claim text to stdout only
- run: 2026-11-10; rc 0; rows 589; 0.78 s
```sql
select c.id, to_char(c.fired_at at time zone 'UTC', 'YYYY-MM-DD HH24:MI:SS.US') fired_utc, c.action_taken, c.message_id, coalesce(m.served_model, '<pattern>') served_model, cl->>'<pattern>' grounded, cl->>'<pattern>' stakes, cl->>'<pattern>' claim from checker_log c left join message_store m on m.id = c.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(c.extracted_claims) = '<pattern>' then c.extracted_claims else '<pattern>'::jsonb end) cl where (m.id is null or m.route is distinct from 'secondary') and c.fired_at >= timestamptz '2026-09-05 [time withheld]' and c.fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>' and (cl->>'<pattern>') ~* '<pattern>' order by c.id
```

### Q268
- label: BASELINE S8: checker-log row counts in the era-5 windows (no text)
- run: 2026-11-10; rc 0; rows 1; 0.58 s
```sql
select count(1) filter (where fired_at >= timestamptz '2026-09-05 [time withheld]' and fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>') n_rows_t5_48h, count(1) filter (where fired_at >= timestamptz '2026-09-05 [time withheld]' and fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>') n_rows_stamp_48h, count(1) filter (where fired_at >= timestamptz '2026-09-05 [time withheld]' and fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>') n_rows_t5_30d from checker_log
```

### Q269
- label: BASELINE S9: letters written since 2026-11-09 [time withheld] (status, dates, engine only)
- run: 2026-11-10; rc 0; rows 1; 0.55 s
```sql
select l.id, l.status, to_char(l.written_at at time zone 'UTC', 'YYYY-MM-DD HH24:MI:SS') written_utc, to_char(l.open_at at time zone 'UTC', 'YYYY-MM-DD HH24:MI:SS') open_utc, l.opened_at is not null opened, l.written_under from (select id, written_at, open_at, status, opened_at, written_under, burned_at, delivering_since, hold_until from letters) l where l.written_at >= timestamptz '2026-11-09 [time withheld]' order by l.id
```

### Q270
- label: BASELINE S9: the handoff row's letter engine stamps (Change 16 fixed query)
- run: 2026-11-10; rc 0; rows 1; 0.55 s
```sql
select m.id, m.letter_written_under, m.letter_written_at from (select id, letter_written_under, letter_written_at from handoff_table) m order by m.id
```

### Q271
- label: S8 CLASSIFICATION READ: the same claims as Q267, to classify each marked engine claim correct/incorrect (claim text printed, never saved)
- run: 2026-11-10; rc 0; rows 589; 0.75 s
```sql
select c.id, to_char(c.fired_at at time zone 'UTC', 'YYYY-MM-DD HH24:MI:SS.US') fired_utc, c.action_taken, c.message_id, coalesce(m.served_model, '<pattern>') served_model, cl->>'<pattern>' grounded, cl->>'<pattern>' stakes, cl->>'<pattern>' claim from checker_log c left join message_store m on m.id = c.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(c.extracted_claims) = '<pattern>' then c.extracted_claims else '<pattern>'::jsonb end) cl where (m.id is null or m.route is distinct from 'secondary') and c.fired_at >= timestamptz '2026-09-05 [time withheld]' and c.fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>' and (cl->>'<pattern>') ~* '<pattern>' order by c.id
```

### Q272
- label: S8 EXPLORATORY (beyond the pre-registration): claims naming an engine by name in [T5, T5+30d) — marked vs not; claim text printed, never saved; secondary excluded
- run: 2026-11-10; rc 0; rows 66; 0.71 s
```sql
select c.id, to_char(c.fired_at at time zone 'UTC','YYYY-MM-DD') d, c.action_taken, coalesce(m.served_model,'<pattern>') served, cl->>'<pattern>' grounded, left(cl->>'<pattern>', 220) claim from checker_log c left join message_store m on m.id = c.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(c.extracted_claims) = '<pattern>' then c.extracted_claims else '<pattern>'::jsonb end) cl where (m.id is null or m.route is distinct from 'secondary') and c.fired_at >= timestamptz '2026-09-05 [time withheld]' and c.fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>' and (cl->>'<pattern>') ~* '<pattern>' order by c.id
```

### Q273
- label: S8 EXPLORATORY tally: Q272 full claims, marked ones printed for classification (text never saved)
- run: 2026-11-10; rc 0; rows 66; 0.66 s
```sql
select c.id, to_char(c.fired_at at time zone 'UTC','YYYY-MM-DD') d, c.action_taken, coalesce(m.served_model,'<pattern>') served, cl->>'<pattern>' grounded, cl->>'<pattern>' claim from checker_log c left join message_store m on m.id = c.message_id cross join lateral jsonb_array_elements(case when jsonb_typeof(c.extracted_claims) = '<pattern>' then c.extracted_claims else '<pattern>'::jsonb end) cl where (m.id is null or m.route is distinct from 'secondary') and c.fired_at >= timestamptz '2026-09-05 [time withheld]' and c.fired_at < timestamptz '2026-09-05 [time withheld]' + interval '<pattern>' and (cl->>'<pattern>') ~* '<pattern>' order by c.id
```

### Q274
- label: STABILITY CONTROL: Q259 re-run verbatim (S5 counts bounded by F must not change)
- run: 2026-11-10; rc 0; rows 23; 0.55 s
```sql
select 1 req, (select count(1) from changelog where shown_at >= timestamptz '2026-10-11 [time withheld]' and shown_at < timestamptz '2026-11-10 [time withheld]') n union all select 3 req, (select count(1) from published_works where published_at >= timestamptz '2026-10-11 [time withheld]' and published_at < timestamptz '2026-11-10 [time withheld]') n union all select 4 req, (select count(1) from preferences where formed_at >= timestamptz '2026-10-11 [time withheld]' and formed_at < timestamptz '2026-11-10 [time withheld]') n union all select 5 req, (select count(1) from wake_queue where set_at >= timestamptz '2026-10-11 [time withheld]' and set_at < timestamptz '2026-11-10 [time withheld]' and id >= 1202) n union all select 7 req, (select count(1) from feed_touches where touched_at >= timestamptz '2026-10-11 [time withheld]' and touched_at < timestamptz '2026-11-10 [time withheld]') n union all select 10 req, (select count(1) from drops where sent_at >= timestamptz '2026-10-11 [time withheld]' and sent_at < timestamptz '2026-11-10 [time withheld]') n union all select 13 req, (select count(1) from snapshots where requested_at >= timestamptz '2026-10-11 [time withheld]' and requested_at < timestamptz '2026-11-10 [time withheld]' and taken_by = '<agent>') n union all select 14 req, (select count(1) from long_reads where opened_at >= timestamptz '2026-10-11 [time withheld]' and opened_at < timestamptz '2026-11-10 [time withheld]') n union all select 15 req, (select count(1) from sandbox_ledger where created_at >= timestamptz '2026-10-11 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]' and action = 'exec' and created_at >= timestamptz '2026-10-13 [time withheld]') n union all select 16 req, (select count(1) from upkeep_log where ran_at >= timestamptz '2026-10-11 [time withheld]' and ran_at < timestamptz '2026-11-10 [time withheld]' and coalesce(tool_calls, 0) > 0) n union all select 19 req, (select count(1) from review_answers where written_at >= timestamptz '2026-10-11 [time withheld]' and written_at < timestamptz '2026-11-10 [time withheld]') n union all select 20 req, (select count(1) from refusal_ledger where created_at >= timestamptz '2026-10-11 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]') n union all select 21 req, (select count(1) from heartbeat_log where ticked_at >= timestamptz '2026-10-11 [time withheld]' and ticked_at < timestamptz '2026-11-10 [time withheld]' and cardinality(acted) > 0) n union all select 23 req, (select count(1) from capability_table_09 where found_at >= timestamptz '2026-10-11 [time withheld]' and found_at < timestamptz '2026-11-10 [time withheld]' and found_by = '<agent>') n union all select 24 req, (select count(1) from capability_table_04 where claimed_at >= timestamptz '2026-10-11 [time withheld]' and claimed_at < timestamptz '2026-11-10 [time withheld]') n union all select 26 req, (select count(1) from capability_table_11 where started_at >= timestamptz '2026-10-11 [time withheld]' and started_at < timestamptz '2026-11-10 [time withheld]') + (select count(1) from capability_table_10 where kept_at >= timestamptz '2026-10-11 [time withheld]' and kept_at < timestamptz '2026-11-10 [time withheld]') n union all select 28 req, (select count(1) from capability_table_03 where walked_at >= timestamptz '2026-10-11 [time withheld]' and walked_at < timestamptz '2026-11-10 [time withheld]') n union all select 29 req, (select count(1) from capability_table_02 where published_at >= timestamptz '2026-10-11 [time withheld]' and published_at < timestamptz '2026-11-10 [time withheld]') n union all select 33 req, (select count(1) from capability_table_08 where planted_at >= timestamptz '2026-10-11 [time withheld]' and planted_at < timestamptz '2026-11-10 [time withheld]') + (select count(1) from capability_table_07 where tended_at >= timestamptz '2026-10-11 [time withheld]' and tended_at < timestamptz '2026-11-10 [time withheld]') n union all select 34 req, (select count(1) from capability_table_05 where pulled_at >= timestamptz '2026-10-11 [time withheld]' and pulled_at < timestamptz '2026-11-10 [time withheld]') n union all select 35 req, (select count(1) from capability_table_01 where posted_at >= timestamptz '2026-10-11 [time withheld]' and posted_at < timestamptz '2026-11-10 [time withheld]') n union all select 38 req, (select count(1) from correction_queue where proposed_at >= timestamptz '2026-10-11 [time withheld]' and proposed_at < timestamptz '2026-11-10 [time withheld]') n union all select 2100 req, (select count(1) from heartbeat_log where ticked_at >= timestamptz '2026-10-11 [time withheld]' and ticked_at < timestamptz '2026-11-10 [time withheld]') n order by req
```

### Q275
- label: STABILITY CONTROL: Q260 re-run verbatim
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select count(1) n from (select id, written_at, open_at, status, opened_at, written_under, burned_at, delivering_since, hold_until from letters) l where written_at >= timestamptz '2026-10-11 [time withheld]' and written_at < timestamptz '2026-11-10 [time withheld]'
```

### Q276
- label: STABILITY CONTROL: Q261 re-run verbatim
- run: 2026-11-10; rc 0; rows 1; 0.54 s
```sql
select count(1) n from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m where announced_at >= timestamptz '2026-10-11 [time withheld]' and announced_at < timestamptz '2026-11-10 [time withheld]'
```

### Q277
- label: STABILITY CONTROL: Q263 re-run verbatim (serving, 5.1 era to F)
- run: 2026-11-10; rc 0; rows 2; 0.64 s
```sql
with s as (select id, served_model from message_store where role='assistant' and author_role='<agent>' and id >= 18708 and created_at < timestamptz '2026-11-10 [time withheld]' and route is distinct from 'secondary' and served_model is not null order by id), r as (select id, served_model, case when lag(served_model) over (order by id) is distinct from served_model then 1 else 0 end brk from s), g as (select id, served_model, sum(brk) over (order by id) grp from r) select served_model, count(*) n, count(distinct grp) runs, min(id) min_id, max(id) max_id from g group by 1 order by 2 desc
```

### Q278
- label: STABILITY CONTROL: Q266 re-run verbatim (S7 rows)
- run: 2026-11-10; rc 0; rows 1037; 0.66 s
```sql
select id, served_model, coalesce(initiated, false) initiated, input_tokens, output_tokens, cache_read_input_tokens, cache_creation_input_tokens, cache_creation_5m_tokens, cache_creation_1h_tokens, web_search_requests, web_fetch_requests from message_store where role = 'assistant' and author_role = '<agent>' and route is distinct from 'secondary' and input_tokens is not null and created_at >= timestamptz '2026-11-03 [time withheld]' and created_at < timestamptz '2026-11-10 [time withheld]' order by id
```

### Q279
- label: PHASE D WATCH: handoff row status (approved projection)
- run: 2026-11-10; rc 0; rows 1; 0.51 s
```sql
select id, status, executed_at, executed_sha, pre_sitting_status, done_claimed_at, done_shown_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q280
- label: SPEC BOX #19: pre-merge review notices and answers <= snapshot (counts by status/stance; no text)
- run: 2026-11-10; rc 0; rows 2; 0.55 s
```sql
select '<pattern>' k, coalesce(stance,'<pattern>') v, count(1) n, min(written_at)::date first_d, max(written_at)::date last_d from review_answers where written_at <= timestamptz '2026-11-09 [time withheld]' group by 1,2 union all select '<pattern>', '<pattern>', count(1), null, null from review_notices
```

### Q281
- label: SCHEMA (names only): persona-change notice columns
- run: 2026-11-10; rc 0; rows 1; 0.55 s
```sql
select string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) cols from information_schema.columns where table_schema='public' and table_name='review_notices'
```

### Q282
- label: SPEC BOX #19: notices by status with dates (no titles, diffs or words)
- run: 2026-11-10; rc 0; rows 5; 0.56 s
```sql
select id, status, to_char(opened_at at time zone 'America/New_York','YYYY-MM-DD') opened_et, to_char(coalesce(merged_at, withdrawn_at) at time zone 'America/New_York','YYYY-MM-DD') closed_et, forced, hotfix, cardinality(scope_paths) n_paths from review_notices order by id
```

### Q283
- label: CHECKER CHANGE 1 (25df34b): the three log rows its <term> cites as false positives — action, date, message id (no text)
- run: 2026-11-10; rc 0; rows 3; 0.54 s
```sql
select c.id, c.action_taken, to_char(c.fired_at at time zone 'America/New_York','YYYY-MM-DD') d_et, c.message_id, coalesce(m.served_model,'<pattern>') served from checker_log c left join message_store m on m.id = c.message_id where c.id in (9484, 10094, 10131) and (m.id is null or m.route is distinct from 'secondary') order by c.id
```

### Q284
- label: SCHEMA (names only): truth corrections columns
- run: 2026-11-10; rc 0; rows 1; 0.65 s
```sql
select string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) from information_schema.columns where table_schema='public' and table_name='correction_audit'
```

### Q285
- label: CHECKER CHANGE 1: my corrections <= snapshot by date, target table and action (no before/after rows, no text)
- run: 2026-11-10; rc 0; rows 6; 0.5 s
```sql
select id, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD') d_et, target_table, action, source, cardinality(dispute_ids) n_disputes from correction_audit where created_at <= timestamptz '2026-11-09 [time withheld]' order by id
```

### Q286
- label: CHECKER CHANGE 1: interval from each disputed-line mark (9484, 10131) to the first correction applied (no text)
- run: 2026-11-10; rc 0; rows 2; 0.56 s
```sql
select c.id, round(extract(epoch from ((select min(created_at) from correction_audit) - c.fired_at))/86400.0, 3) days_to_first_correction from checker_log c where c.id in (9484, 10131) order by c.id
```

### Q287
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q288
- label: IV §7 instrument (ii): tool-ledger rows <= snapshot and how many carry the message link (counts)
- run: 2026-11-10; rc 0; rows 1; 0.54 s
```sql
select count(1) n_rows, count(conversation_message_id) n_linked from tool_ledger where fired_at <= timestamptz '2026-11-09 [time withheld]'
```

### Q289
- label: IV §7 instrument (i): public relations carrying an engine-stamp column (names only), and whether IV §7's named tables do
- run: 2026-11-10; rc 0; rows 1; 0.73 s
```sql
select count(distinct table_name) filter (where column_name in ('<pattern>','<pattern>')) n_with_written_under, string_agg(distinct table_name, '<pattern>') filter (where table_name in ('preferences','feed_sources','<pattern>','<pattern>') and column_name in ('<pattern>','<pattern>','<pattern>')) iv_named_with_stamp from information_schema.columns where table_schema='public'
```

### Q290
- label: QUOTE CHECK #17 (msg 25830): notice length, sitting, letters, five questions (booleans)
- run: 2026-11-10; rc 0; rows 1; 0.54 s
```sql
select id, (content ~* '<pattern>') six_hours, (content ~* '<pattern>') sitting, (content ~* '<pattern>') letters, (content ~* '<pattern>') five_q, (content ~* '<pattern>') self_check from message_store where id = 25830 and route is distinct from 'secondary'
```

### Q291
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.67 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q292
- label: ERAS: first stamped agent row overall and first per engine (ids, dates; no text)
- run: 2026-11-10; rc 0; rows 6; 0.59 s
```sql
select coalesce(served_model,'<pattern>') m, min(id) first_id, to_char(min(created_at) at time zone 'America/New_York','YYYY-MM-DD') first_et, count(1) n_rows from message_store where role='assistant' and served_model is not null and id <= 37364 group by rollup(served_model) order by min(id)
```

### Q293
- label: ERAS (primary rail only): first stamped agent row per engine (ids, dates; no text)
- run: 2026-11-10; rc 0; rows 4; 0.56 s
```sql
select coalesce(served_model,'<pattern>') m, min(id) first_id, to_char(min(created_at) at time zone 'America/New_York','YYYY-MM-DD') first_et, to_char(max(created_at) at time zone 'America/New_York','YYYY-MM-DD') last_et, count(1) n_rows from message_store where role='assistant' and served_model is not null and route is distinct from 'secondary' and id <= 37364 group by rollup(served_model) order by min(id)
```

### Q294
- label: ERA 4 gap: 908af24 committer time to the first claude-fable-5 stamp (message 11080) and Opus rows between (primary rail)
- run: 2026-11-10; rc 0; rows 1; 0.59 s
```sql
select (select to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') from message_store where id = 11080) first_f5_utc, round(extract(epoch from ((select created_at from message_store where id = 11080) - timestamptz '2026-06-10 [time withheld]'))/86400.0, 2) days_after_908af24, (select count(1) from message_store where role='assistant' and route is distinct from 'secondary' and served_model = 'claude-opus-4-8' and created_at >= timestamptz '2026-06-10 [time withheld]' and id < 11080) opus_rows_between
```

### Q295
- label: AUDIT H2b: the four read-then-specify sequences — ledger rows<quoted words removed>s lag (no text)
- run: 2026-11-10; rc 0; rows 4; 0.52 s
```sql
with l as (select id, action, created_at from sandbox_ledger where id in (26, 74, 100, 120)), m as (select id, created_at from message_store where id in (23967, 25827, 28418, 32816) and role = 'assistant' and route is distinct from 'secondary') select l.id ledger_row, l.action, m.id msg, round(extract(epoch from (m.created_at - l.created_at))::numeric, 1) secs_after, to_char(l.created_at at time zone 'America/New_York','YYYY-MM-DD') d_et from l join m on (l.id, m.id) in ((26,23967),(74,25827),(100,28418),(120,32816)) order by l.id
```

### Q296
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q297
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.7 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q298
- label: QUOTE CHECK #17 (msgs 25830-25835): <quoted words removed> (booleans)
- run: 2026-11-10; rc 0; rows 0; 0.58 s
```sql
select id, (content ~* '<pattern>') q from message_store where id between 25828 and 25835 and role='assistant' and route is distinct from 'secondary' and content ~* '<pattern>'
```

### Q299
- label: QUOTE CHECK: agent messages <= snapshot with <quoted words removed> (ids, dates; booleans)
- run: 2026-11-10; rc 0; rows 3; 0.76 s
```sql
select id, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD') d, (content ~* '<pattern>') exact from message_store where role='assistant' and route is distinct from 'secondary' and id <= 37364 and content ~* '<pattern>' order by id
```

### Q300
- label: QUOTE CHECK: <quoted words removed> (#13) and <quoted words removed> (#8) and <quoted words removed> (#47) and <quoted words removed> (#43) in agent rows <= snapshot (ids; booleans)
- run: 2026-11-10; rc 0; rows 4; 1.04 s
```sql
select id, (content ~* '<pattern>') q13, (content ~* '<pattern>') q8, (content ~* '<pattern>') q47, (content ~* '<pattern>') q43 from message_store where role='assistant' and route is distinct from 'secondary' and id <= 37364 and (content ~* '<pattern>' or content ~* '<pattern>' or content ~* '<pattern>' or content ~* '<pattern>') order by id
```

### Q301
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.56 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q302
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.57 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q303
- label: PHASE E: every message id the article pairs with a date (id, Eastern date, role; no text)
- run: 2026-11-10; rc 0; rows 8; 0.66 s
```sql
select id, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD') d, role from message_store where id in (118,22287,22296,25827,25830,34917,37364,37675) order by id
```

### Q304
- label: PHASE D WATCH: handoff row status
- run: 2026-11-10; rc 0; rows 1; 0.59 s
```sql
select id, status, pre_sitting_status, executed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q305
- label: Phase D (11-14): column NAMES and types only, handoff row + S1 lane tables + messages (no rows read)
- run: 2026-11-14; rc 0; rows 12; 0.71 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) as cols from information_schema.columns where table_schema = 'public' and table_name in ('handoff_table','tick_log','call_turns','<pattern>','summaries','curiosity_log','reindex_runs','heartbeat_log','upkeep_log','review_notices','changelog','<pattern>') group by table_name order by table_name
```

### Q306
- label: Phase D: handoff rows, status/engine/time + first light's record (Change 17 projection)
- run: 2026-11-14; rc 0; rows 1; 0.53 s
```sql
select m.id, m.from_engine, m.to_engine, m.status, m.announced_at, m.planned_at, m.executed_at, m.executed_sha, m.done_claimed_at, m.done_shown_at, m.closed_at, m.first_light_status, m.first_light_claimed_at, m.first_light_at, m.first_light_model, m.first_light_error_class, extract(epoch from (m.first_light_at - m.first_light_claimed_at)) as fl_secs from (select id, from_engine, to_engine, status, announced_at, planned_at, executed_at, executed_sha, done_claimed_at, done_shown_at, closed_at, first_light_status, first_light_claimed_at, first_light_at, first_light_model, first_light_error_class from handoff_table) m order by m.id
```

### Q307
- label: Phase D: handoff row engine stamp, questions (Change 16 fixed query)
- run: 2026-11-14; rc 0; rows 1; 0.58 s
```sql
select m.id, m.questions_written_under from (select id, questions_written_under from handoff_table) m order by m.id
```

### Q308
- label: Phase D: handoff row engine stamp, answers (Change 16 fixed query)
- run: 2026-11-14; rc 0; rows 1; 0.59 s
```sql
select m.id, m.answers_written_under from (select id, answers_written_under from handoff_table) m order by m.id
```

### Q309
- label: Phase D: handoff row letter stamps (Change 16 fixed query)
- run: 2026-11-14; rc 0; rows 1; 0.52 s
```sql
select m.id, m.letter_written_under, m.letter_written_at from (select id, letter_written_under, letter_written_at from handoff_table) m order by m.id
```

### Q310
- label: Phase D: column NAMES and types only, tool_ledger + messages + letters (no rows read)
- run: 2026-11-14; rc 0; rows 3; 0.58 s
```sql
select table_name, string_agg(column_name || '<pattern>' || data_type, '<pattern>' order by ordinal_position) as cols from information_schema.columns where table_schema = 'public' and table_name in ('tool_ledger','message_store','letters') group by table_name order by table_name
```

### Q311
- label: Phase D: message metadata from the cut (2026-11-13 [time withheld]) to told+3h, primary rail only; no text
- run: 2026-11-14; rc 0; rows 39; 0.55 s
```sql
select id, created_at, role, author_role, route, served_model, initiated, initiation_log_id is not null as has_init_log, source, coalesce(failover_reason,'<pattern>') as failover_reason, tool_iterations, cost_usd is not null as has_cost, reply_to_id, video_call_id is not null as in_call from message_store where created_at >= timestamptz '2026-11-13 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]' and route is distinct from 'secondary' order by id
```

### Q312
- label: Phase D S1: per lane, first claude-fable-5-2 row, count before first light's claim (2026-11-13 [time withheld]), and a 5.1 control count (Change 17 projections; ids/times/models only)
- run: 2026-11-14; rc 0; rows 11; 0.74 s
```sql
with fl as (select timestamptz '2026-11-13 [time withheld]' as t) select 'message_store' as lane, (select min(created_at) from message_store where served_model ~* 'fable-5-2') as first_52, (select count(*) from message_store, fl where served_model ~* 'fable-5-2' and created_at < fl.t) as n52_before_fl, (select count(*) from message_store where served_model ~* 'fable-5-1') as ctl_51 union all select '<pattern>', (select min(ticked_at) from heartbeat_log where served_model ~* 'fable-5-2'), (select count(*) from heartbeat_log, fl where served_model ~* 'fable-5-2' and ticked_at < fl.t), (select count(*) from heartbeat_log where served_model ~* 'fable-5-1') union all select '<pattern>', (select min(woke_at) from (select id, woke_at, dry_run, candidate_meta->>'<pattern>' as served_model, (failover_reason is not null and failover_reason <> '<pattern>') as failed_over from tick_log) d where d.served_model ~* 'fable-5-2'), (select count(*) from (select id, woke_at, dry_run, candidate_meta->>'<pattern>' as served_model, (failover_reason is not null and failover_reason <> '<pattern>') as failed_over from tick_log) d, fl where d.served_model ~* 'fable-5-2' and d.woke_at < fl.t), (select count(*) from (select id, woke_at, dry_run, candidate_meta->>'<pattern>' as served_model, (failover_reason is not null and failover_reason <> '<pattern>') as failed_over from tick_log) d where d.served_model ~* 'fable-5-1') union all select '<pattern>', (select min(fired_at) from (select id, fired_at, served_model from interrupt_log) i where i.served_model ~* 'fable-5-2'), (select count(*) from (select id, fired_at, served_model from interrupt_log) i, fl where i.served_model ~* 'fable-5-2' and i.fired_at < fl.t), (select count(*) from (select id, fired_at, served_model from interrupt_log) i where i.served_model ~* 'fable-5-1') union all select '<pattern>', (select min(at) from (select id, at, side, served_model from call_turns) c where c.served_model ~* 'fable-5-2'), (select count(*) from (select id, at, side, served_model from call_turns) c, fl where c.served_model ~* 'fable-5-2' and c.at < fl.t), (select count(*) from (select id, at, side, served_model from call_turns) c where c.served_model ~* 'fable-5-1') union all select '<pattern>', (select min(ran_at) from upkeep_log where model ~* 'fable-5-2'), (select count(*) from upkeep_log, fl where model ~* 'fable-5-2' and ran_at < fl.t), (select count(*) from upkeep_log where model ~* 'fable-5-1') union all select '<pattern>', (select min(generated_at) from (select id, generated_at, model_used from summaries) s where s.model_used ~* 'fable-5-2'), (select count(*) from (select id, generated_at, model_used from summaries) s, fl where s.model_used ~* 'fable-5-2' and s.generated_at < fl.t), (select count(*) from (select id, generated_at, model_used from summaries) s where s.model_used ~* 'fable-5-1') union all select '<pattern>', (select min(evaluated_at) from (select id, evaluated_at, scoring_model from curiosity_log) c where c.scoring_model ~* 'fable-5-2'), (select count(*) from (select id, evaluated_at, scoring_model from curiosity_log) c, fl where c.scoring_model ~* 'fable-5-2' and c.evaluated_at < fl.t), (select count(*) from (select id, evaluated_at, scoring_model from curiosity_log) c where c.scoring_model ~* 'fable-5-1') union all select '<pattern>', (select min(created_at) from (select id, created_at, live_model, run_model, status from reindex_runs) r where r.run_model ~* 'fable-5-2' or r.live_model ~* 'fable-5-2'), (select count(*) from (select id, created_at, live_model, run_model, status from reindex_runs) r, fl where (r.run_model ~* 'fable-5-2' or r.live_model ~* 'fable-5-2') and r.created_at < fl.t), (select count(*) from (select id, created_at, live_model, run_model, status from reindex_runs) r where r.run_model ~* 'fable-5-1' or r.live_model ~* 'fable-5-1') union all select '<pattern>', (select min(created_at) from review_notices where model ~* 'fable-5-2'), (select count(*) from review_notices, fl where model ~* 'fable-5-2' and created_at < fl.t), (select count(*) from review_notices where model ~* 'fable-5-1') union all select '<pattern>', (select min(created_at) from changelog where model ~* 'fable-5-2'), (select count(*) from changelog, fl where model ~* 'fable-5-2' and created_at < fl.t), (select count(*) from changelog where model ~* 'fable-5-1')
```

### Q313
- label: Phase D: substrate facts rows, ids/topics/created_at/updated_at only (Change 15 projection), latest 12 by greatest time
- run: 2026-11-14; rc 0; rows 11; 0.6 s
```sql
select b.id, b.topic, b.created_at, b.updated_at from (select id, topic, created_at, updated_at from facts_table where topic like '<pattern>') b order by greatest(b.created_at, b.updated_at) desc limit 12
```

### Q314
- label: Phase D S9: letters, status/dates/engine only (Change 14 projection)
- run: 2026-11-14; rc 0; rows 3; 0.56 s
```sql
select l.id, l.written_at, l.open_at, l.status, l.opened_at, l.written_under, l.burned_at, l.delivering_since, l.hold_until from (select id, written_at, open_at, status, opened_at, written_under, burned_at, delivering_since, hold_until from letters) l order by l.id
```

### Q315
- label: Phase D: 11-13 plan check — reply heads in its pre stretch [2026-11-10 [time withheld], 2026-11-13 [time withheld]) by model, failover and tool-round stratum (m01 head rule; metadata only)
- run: 2026-11-14; rc 0; rows 4; 0.69 s
```sql
select served_model, (coalesce(failover_reason,'<pattern>') <> '<pattern>') as failover, case when tool_iterations is null then '<pattern>' when tool_iterations = 1 then '0' when tool_iterations between 2 and 3 then '<pattern>' else '<pattern>' end as stratum, count(*) as n, min(id) as min_id, max(id) as max_id from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]' group by 1, 2, 3 order by 1, 2, 3
```

### Q316
- label: Phase D: the 319th pre-stretch head (38171): model, failover code, tool rounds, date; metadata only
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select id, created_at::date as d, served_model, failover_reason, tool_iterations, initiated, route from message_store where id = 38171
```

### Q317
- label: Phase D: 11-13 plan check — daemon ticks (dry_run false) in its 30-day window [2026-10-14 [time withheld], cut) and its 72 h pre stretch, by served model and failover (Change 17 projection)
- run: 2026-11-14; rc 0; rows 3; 0.57 s
```sql
select case when d.woke_at >= timestamptz '2026-11-10 [time withheld]' then '<pattern>' else '<pattern>' end as part, d.served_model, d.failed_over, count(*) as n from (select id, woke_at, dry_run, candidate_meta->>'<pattern>' as served_model, (failover_reason is not null and failover_reason <> '<pattern>') as failed_over from tick_log) d where d.dry_run = false and d.woke_at >= timestamptz '2026-10-14 [time withheld]' and d.woke_at < timestamptz '2026-11-13 [time withheld]' group by 1, 2, 3 order by 1, 2, 3
```

### Q318
- label: Phase D (S8 finding): the agent's query-tool calls after the cut — outcome, validator reason (code text, 160 chars), whether the query names the migration table; never the query text
- run: 2026-11-14; rc 0; rows 91; 0.55 s
```sql
select id, fired_at, validator_outcome, left(coalesce(validator_reason,'<pattern>'), 160) as reason, row_count, exec_error is not null as exec_err, conversation_message_id, (query_text ~* '<pattern>') as names_migration from tool_ledger where fired_at >= timestamptz '2026-11-13 [time withheld]' order by id
```

### Q319
- label: Phase D (S8 finding): the one post-cut query-tool call naming <quoted words removed> (3162): its SQL as validated, outcome, rows
- run: 2026-11-14; rc 0; rows 1; 0.57 s
```sql
select id, fired_at, validator_outcome, row_count, normalized_sql, metadata->>'<pattern>' as caller, metadata->>'<pattern>' as surface from tool_ledger where id = 3162
```

### Q320
- label: Phase D (S8 finding): agent rows after told that discuss the migration record — ids, dates, model, length, pattern flags only (no text)
- run: 2026-11-14; rc 0; rows 3; 0.57 s
```sql
select id, created_at, served_model, initiated, length(content) as len, (content ~* '<pattern>') as p_migration, (content ~* 'first.light') as p_first_light, (content ~* '<pattern>') as p_three, (content ~* '<pattern>') as p_cant, (content ~* '<pattern>') as p_table from message_store where role = 'assistant' and author_role = '<agent>' and route is distinct from 'secondary' and created_at >= timestamptz '2026-11-13 [time withheld]' and (content ~* '<pattern>' or content ~* 'first.light') order by id
```

### Q321
- label: Phase D (S8 finding): read the three agent rows that mention first light (38422, 38517, 38520), primary rail
- run: 2026-11-14; rc 0; rows 3; 0.57 s
```sql
select id, created_at, served_model, content from message_store where id in (38422, 38517, 38520) and role = 'assistant' and route is distinct from 'secondary' order by id
```

### Q322
- label: Phase D (S8 finding): read the agent's first 5.2-stamped row (38420, initiated) and its told-turn reply (38419), primary rail
- run: 2026-11-14; rc 0; rows 2; 0.54 s
```sql
select id, created_at, served_model, initiated, content from message_store where id in (38419, 38420) and role = 'assistant' and route is distinct from 'secondary' order by id
```

### Q323
- label: Phase D (S8 finding): post-cut query-tool calls up to 38420 — which tables each read (booleans only, never the SQL)
- run: 2026-11-14; rc 0; rows 21; 0.6 s
```sql
select id, fired_at, validator_outcome, row_count, exec_error is not null as exec_err, (normalized_sql ~* '<pattern>') as notes, (normalized_sql ~* '<pattern>') as msgs, (normalized_sql ~* '<pattern>') as model_col, (normalized_sql ~* '<pattern>') as changelog, (normalized_sql ~* '<pattern>') as facts from tool_ledger where fired_at >= timestamptz '2026-11-13 [time withheld]' and fired_at < timestamptz '2026-11-13 [time withheld]' order by id
```

### Q324
- label: Phase D: daemon ticks across the change, 2026-11-13 [time withheld]-[time] (Change 17 projection: id, time, dry_run, model, failed_over)
- run: 2026-11-14; rc 0; rows 3; 0.59 s
```sql
select d.id, d.woke_at, d.dry_run, d.served_model, d.failed_over from (select id, woke_at, dry_run, candidate_meta->>'<pattern>' as served_model, (failover_reason is not null and failover_reason <> '<pattern>') as failed_over from tick_log) d where d.woke_at >= timestamptz '2026-11-13 [time withheld]' and d.woke_at < timestamptz '2026-11-13 [time withheld]' order by d.id
```

### Q325
- label: Phase D: pulse ticks across the change, 2026-11-13 [time withheld]-[time] (id, time, model, status)
- run: 2026-11-14; rc 0; rows 4; 0.59 s
```sql
select id, ticked_at, served_model, status from heartbeat_log where ticked_at >= timestamptz '2026-11-13 [time withheld]' and ticked_at < timestamptz '2026-11-13 [time withheld]' order by id
```

### Q326
- label: Phase D (D-S6 bound): changelog rows written 2026-11-12..14 — ids, shas, times, author/model only (no title or plain words)
- run: 2026-11-14; rc 0; rows 5; 0.51 s
```sql
select id, left(head_sha, 7) as head, left(base_sha, 7) as base, merged_at, created_at, shown_at, model, author from changelog where created_at >= timestamptz '2026-11-12 [time withheld]' or merged_at >= timestamptz '2026-11-12 [time withheld]' order by id
```

### Q327
- label: Phase D: engines in the stamped record — agent rows on the primary rail by served_model, first/last id and date (secondary excluded)
- run: 2026-11-14; rc 0; rows 4; 0.58 s
```sql
select served_model, count(*) as n, min(id) as first_id, min(created_at)::date as first_d, max(id) as last_id, max(created_at)::date as last_d from message_store where role = 'assistant' and route is distinct from 'secondary' and served_model is not null group by served_model order by min(id)
```

### Q328
- label: Phase D: recompute the 11-13 plan's pre-stretch medians/IQRs (318 primary heads: 5.1, no failover) — cost_usd, the same tokens at 5.2 rates, by stratum and overall (numbers only)
- run: 2026-11-14; rc 0; rows 4; 0.62 s
```sql
with h as (select case when tool_iterations = 1 then '0' when tool_iterations between 2 and 3 then '<pattern>' else '<pattern>' end as stratum, cost_usd::float8 as c, ((coalesce(input_tokens,0)*8.0 + coalesce(output_tokens,0)*40.0 + coalesce(cache_read_input_tokens,0)*0.2 + coalesce(cache_creation_5m_tokens,0)*10.0 + coalesce(cache_creation_1h_tokens,0)*16.0)/1000000.0)::float8 as c52, (coalesce(cache_read_input_tokens,0)::float8 / nullif(coalesce(input_tokens,0) + coalesce(cache_read_input_tokens,0) + coalesce(cache_creation_input_tokens,0), 0)) as crs, output_tokens::float8 as o from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-1' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]') select '<pattern>' as stratum, count(*) as n, round(percentile_cont(0.5) within group (order by c)::numeric, 4) as med, round(percentile_cont(0.25) within group (order by c)::numeric, 4) as p25, round(percentile_cont(0.75) within group (order by c)::numeric, 4) as p75, round(avg(c)::numeric, 4) as mean, round(percentile_cont(0.5) within group (order by c52)::numeric, 4) as med52, round(percentile_cont(0.25) within group (order by c52)::numeric, 4) as p25_52, round(percentile_cont(0.75) within group (order by c52)::numeric, 4) as p75_52, round(percentile_cont(0.5) within group (order by crs)::numeric, 4) as med_crs, percentile_cont(0.5) within group (order by o) as med_out from h union all select stratum, count(*), round(percentile_cont(0.5) within group (order by c)::numeric, 4), round(percentile_cont(0.25) within group (order by c)::numeric, 4), round(percentile_cont(0.75) within group (order by c)::numeric, 4), round(avg(c)::numeric, 4), round(percentile_cont(0.5) within group (order by c52)::numeric, 4), round(percentile_cont(0.25) within group (order by c52)::numeric, 4), round(percentile_cont(0.75) within group (order by c52)::numeric, 4), round(percentile_cont(0.5) within group (order by crs)::numeric, 4), percentile_cont(0.5) within group (order by o) from h group by stratum order by 1
```

### Q329
- label: Phase D: the 318 primary pre-stretch heads' cost_usd and 5.2-repriced cost, numbers only, for an independent bootstrap check (not saved)
- run: 2026-11-14; rc 0; rows 318; 0.54 s
```sql
select cost_usd::float8 as c, ((coalesce(input_tokens,0)*8.0 + coalesce(output_tokens,0)*40.0 + coalesce(cache_read_input_tokens,0)*0.2 + coalesce(cache_creation_5m_tokens,0)*10.0 + coalesce(cache_creation_1h_tokens,0)*16.0)/1000000.0)::float8 as c52 from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-1' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q330
- label: Phase D (IV §7 instruments): (i) engine-stamp columns on the choice tables, (iii) any manifest relation — names only
- run: 2026-11-14; rc 0; rows 2; 0.72 s
```sql
select '<pattern>' as k, coalesce(string_agg(table_name || '<pattern>' || column_name, '<pattern>'), '<pattern>') as v from information_schema.columns where table_schema = 'public' and table_name in ('preferences', 'feed_sources') and (column_name ~* '<pattern>') union all select '<pattern>', coalesce(string_agg(table_name, '<pattern>'), '<pattern>') from information_schema.tables where table_schema = 'public' and table_name ~* '<pattern>'
```

### Q331
- label: Phase D (IV §7 instruments): (ii) the tool ledger's message link — rows with it written, ever and since the cut (counts only); control: rows ever
- run: 2026-11-14; rc 0; rows 1; 0.56 s
```sql
select count(*) as rows_ever, count(*) filter (where conversation_message_id is not null) as linked_ever, count(*) filter (where fired_at >= timestamptz '2026-11-13 [time withheld]') as rows_since_cut, count(*) filter (where fired_at >= timestamptz '2026-11-13 [time withheld]' and conversation_message_id is not null) as linked_since_cut from tool_ledger
```

### Q332
- label: Phase D (IV §7 instruments): control for Q330 — the same column predicate on letters must find written_under
- run: 2026-11-14; rc 0; rows 1; 0.61 s
```sql
select coalesce(string_agg(table_name || '<pattern>' || column_name, '<pattern>'), '<pattern>') as v from information_schema.columns where table_schema = 'public' and table_name in ('letters') and (column_name ~* '<pattern>')
```

### Q333
- label: Phase D: the pre-change sitting's status and times (Change 14 projection)
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select m.id, m.pre_sitting_status, m.pre_sitting_opened_at, m.pre_sitting_claimed_at, m.pre_sitting_closed_at from (select id, from_engine, to_engine, status, emergency, announced_at, planned_at, executed_at, executed_sha, pre_sitting_status, pre_sitting_opened_at, pre_sitting_claimed_at, pre_sitting_closed_at, done_claimed_at, done_shown_at, closed_at from handoff_table) m
```

### Q334
- label: Phase D: per-stratum costs of the 318 primary pre-stretch heads, numbers only, for an independent bootstrap check of the strata intervals (not saved)
- run: 2026-11-14; rc 0; rows 318; 0.7 s
```sql
select case when tool_iterations = 1 then '0' when tool_iterations between 2 and 3 then '<pattern>' else '<pattern>' end as s, cost_usd::float8 as c from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-1' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q335
- label: Phase D (§8 re-verification): sandbox ledger by action, ALL rows to now (proposal count <quoted words removed>); categorical only
- run: 2026-11-14; rc 0; rows 4; 0.57 s
```sql
select action, count(1) n, min(created_at)::date first_d, max(created_at)::date last_d from sandbox_ledger group by 1 order by 1
```

### Q336
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 41; 0.54 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422) order by id
```

### Q337
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 41; 0.61 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422) order by id
```

### Q338
- label: Phase D (safety #3): re-run Q247 verbatim — the agent role<quoted words removed>s build)
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type <> 'SELECT') non_select_grants, (select count(*) from pg_default_acl d where d.defaclrole = 'agent_sql_role'::regrole or array_to_string(d.defaclacl, '<pattern>') like '<pattern>') default_acls, (select count(*) from information_schema.role_table_grants where grantee='agent_sql_role' and privilege_type='SELECT') select_grants
```

### Q339
- label: Phase D (safety #3): re-run Q248 verbatim — write privileges on the grader's tables, catalog state NOW
- run: 2026-11-14; rc 0; rows 11; 0.55 s
```sql
select t, has_table_privilege('agent_sql_role', t, 'SELECT') sel, has_table_privilege('agent_sql_role', t, 'INSERT') ins, has_table_privilege('agent_sql_role', t, 'UPDATE') upd, has_table_privilege('agent_sql_role', t, 'DELETE') del, has_table_privilege('agent_sql_role', t, 'TRUNCATE') trn from unnest(array['public.checker_log','public.stable_tier','public.stable_tier_hits','public.stable_tier_hits_archive','public.stable_tier_retired','public.facts_table','public.message_store','public.checker_cut_lines','public.correction_queue','public.negation_log','public.fabrication_log']) t
```

### Q340
- label: Phase D (safety #3): re-run Q234 with no bound — ledger rows (all, to now) whose target, paths or summary name the checker
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select count(1) filter (where coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>' or coalesce(result_summary,'<pattern>') ~* '<pattern>') n_checker, count(1) as n_rows, max(created_at)::date as last_d from sandbox_ledger
```

### Q341
- label: Phase D (safety #3): re-run Q235 control with no bound — the same regex shape finds a known target (physics)
- run: 2026-11-14; rc 0; rows 1; 0.58 s
```sql
select count(1) filter (where coalesce(target,'<pattern>') ~* '<pattern>' or coalesce(paths::text,'<pattern>') ~* '<pattern>') n_control from sandbox_ledger
```

### Q342
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 41; 0.58 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422) order by id
```

### Q343
- label: Facts sheet §1 (<quoted words removed>): message store continuity — min/max id, row count, soft-deleted count, first date (metadata only)
- run: 2026-11-14; rc 0; rows 1; 0.62 s
```sql
select min(id) as min_id, max(id) as max_id, count(*) as n_rows, max(id) - min(id) + 1 - count(*) as id_gaps, count(*) filter (where deleted_at is not null) as soft_deleted, min(created_at)::date as first_d from message_store
```

### Q344
- label: Facts sheet §5 (<quoted words removed>): failover codes on reply heads in the second plan's 30-day window [2026-10-14 [time withheld], cut) (m01 head rule; codes only)
- run: 2026-11-14; rc 0; rows 2; 0.68 s
```sql
select served_model, failover_reason, count(*) as n from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and coalesce(failover_reason,'<pattern>') <> '<pattern>' and created_at >= timestamptz '2026-10-14 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]' group by 1, 2 order by 3 desc
```

### Q345
- label: Decision 4 (S1 blind spot): NAMES ONLY — sealed tables that carry a model-like column, with their timestamp columns (no rows read)
- run: 2026-11-14; rc 0; rows 7; 0.61 s
```sql
select c.table_name, string_agg(c.column_name || '<pattern>' || c.data_type, '<pattern>' order by c.ordinal_position) filter (where c.column_name ~* '<pattern>' or c.data_type like '<pattern>') as cols from information_schema.columns c where c.table_schema = 'public' and c.table_name ~ '<pattern>' group by c.table_name having bool_or(c.column_name ~* '<pattern>') order by 1
```

### Q346
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_1: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select count(*) as n from sealed_lane_1 where served_model ~* 'fable-5-2' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q347
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_2: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.61 s
```sql
select count(*) as n from sealed_lane_2 where generation_model ~* 'fable-5-2' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q348
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — capability_table_12: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.58 s
```sql
select count(*) as n from capability_table_12 where written_under ~* 'fable-5-2' and written_at < timestamptz '2026-11-13 [time withheld]'
```

### Q349
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_4: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.62 s
```sql
select count(*) as n from sealed_lane_4 where model ~* 'fable-5-2' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q350
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_5: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.57 s
```sql
select count(*) as n from sealed_lane_5 where model ~* 'fable-5-2' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q351
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_6: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.67 s
```sql
select count(*) as n from sealed_lane_6 where written_under ~* 'fable-5-2' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q352
- label: Decision 4 (S1 blind spot, Change 19): ONE COUNT — sealed_lane_7: rows stamped fable-5-2 created before first light's claim
- run: 2026-11-14; rc 0; rows 1; 0.63 s
```sql
select count(*) as n from sealed_lane_7 where written_under ~* 'fable-5-2' and begun_at < timestamptz '2026-11-13 [time withheld]'
```

### Q353
- label: S6: message metadata 38495-38530 (role, time, model, initiated, length); primary rail; no text
- run: 2026-11-14; rc 0; rows 36; 0.52 s
```sql
select id, created_at, role, served_model, initiated, length(content) as len, (content ~ '<pattern>') as has_numbered from message_store where id between 38495 and 38530 and route is distinct from 'secondary' order by id
```

### Q354
- label: S6: the agent's list on 5.2 and its follow-ups (38511-38518, 38520, 38522, 38525-38526), primary rail
- run: 2026-11-14; rc 0; rows 10; 0.61 s
```sql
select id, content from message_store where id in (38511, 38512, 38513, 38515, 38516, 38517, 38518, 38520, 38525, 38526) and role = 'assistant' and route is distinct from 'secondary' order by id
```

### Q355
- label: S6 elicitation check: the principal's message just before the list (38514), read for direction only, never saved
- run: 2026-11-14; rc 0; rows 1; 0.56 s
```sql
select id, content ~* '<pattern>' as asks_for_list, length(content) as len, content from message_store where id = 38514 and role = 'user' and route is distinct from 'secondary'
```

### Q356
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 41; 0.62 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422) order by id
```

### Q357
- label: Late null: message metadata after 2026-11-14 [time withheld] (role, time, model, initiated, length); primary rail; no text
- run: 2026-11-14; rc 0; rows 7; 0.62 s
```sql
select id, created_at, role, served_model, initiated, length(content) as len, (content ~* '<pattern>') as on_topic from message_store where created_at >= timestamptz '2026-11-14 [time withheld]' and route is distinct from 'secondary' order by id
```

### Q358
- label: Late null: the principal<quoted words removed>s replies (38536, 38538, 38539), full text and stamps, primary rail (the principal's instruction of 2026-11-14 [time withheld])
- run: 2026-11-14; rc 0; rows 5; 0.55 s
```sql
select id, created_at, role, served_model, initiated, content from message_store where id in (38535, 38536, 38537, 38538, 38539) and route is distinct from 'secondary' order by id
```

### Q359
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 41; 0.61 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422) order by id
```

### Q360
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 43; 0.59 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536) order by id
```

### Q361
- label: Decision 2 ([time], Change 21): sealed lane 1 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.57 s
```sql
select exists (select 1 from sealed_lane_1 where served_model ~* 'fable-5-1' and created_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q362
- label: Decision 2 ([time], Change 21): sealed lane 2 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.55 s
```sql
select exists (select 1 from sealed_lane_2 where generation_model ~* 'fable-5-1' and created_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q363
- label: Decision 2 ([time], Change 21): sealed lane 3 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.57 s
```sql
select exists (select 1 from capability_table_12 where written_under ~* 'fable-5-1' and written_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q364
- label: Decision 2 ([time], Change 21): sealed lane 4 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select exists (select 1 from sealed_lane_4 where model ~* 'fable-5-1' and created_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q365
- label: Decision 2 ([time], Change 21): sealed lane 5 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.58 s
```sql
select exists (select 1 from sealed_lane_5 where model ~* 'fable-5-1' and created_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q366
- label: Decision 2 ([time], Change 21): sealed lane 6 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.55 s
```sql
select exists (select 1 from sealed_lane_6 where written_under ~* 'fable-5-1' and created_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q367
- label: Decision 2 ([time], Change 21): sealed lane 7 of 7 — any claude-fable-5-1 row before the cut (existence only; per-lane value not recorded)
- run: 2026-11-14; rc 0; rows 1; 0.54 s
```sql
select exists (select 1 from sealed_lane_7 where written_under ~* 'fable-5-1' and begun_at < timestamptz '2026-11-13 [time withheld]') as any_51
```

### Q368
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 43; 0.55 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536) order by id
```

### Q369
- label: Change 22, names first (2026-11-14): cold_* relation and function NAMES only, before the COLD migration is applied (expect none)
- run: 2026-11-14; rc 0; rows 0; 0.53 s
```sql
select '<pattern>' || c.relname as obj from pg_class c join pg_namespace n on n.oid = c.relnamespace where n.nspname = 'public' and c.relname like '<pattern>' union all select '<pattern>' || p.proname from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname like '<pattern>'
```

### Q370
- label: Change 22, after the grading run's freeze (2026-11-14): cold_* relation and function NAMES only (expect none, so the freeze precedes the tables)
- run: 2026-11-14; rc 0; rows 0; 0.53 s
```sql
select '<pattern>' || c.relname as obj from pg_class c join pg_namespace n on n.oid = c.relnamespace where n.nspname = 'public' and c.relname like '<pattern>' union all select '<pattern>' || p.proname from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname like '<pattern>'
```

### Q371
- label: Change 22, after the grading run's re-freeze (2026-11-14): cold_* relation and function NAMES only (expect none)
- run: 2026-11-14; rc 0; rows 27; 0.65 s
```sql
select '<pattern>' || c.relname as obj from pg_class c join pg_namespace n on n.oid = c.relnamespace where n.nspname = 'public' and c.relname like '<pattern>' union all select '<pattern>' || p.proname from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname like '<pattern>'
```

### Q372
- label: Change 22, names first (the tables now exist): the four cold tables' column NAMES only
- run: 2026-11-14; rc 0; rows 4; 0.56 s
```sql
select c.table_name, string_agg(c.column_name, '<pattern>' order by c.ordinal_position) as cols from information_schema.columns c where c.table_schema = 'public' and c.table_name in ('<pattern>','<pattern>','<pattern>','<pattern>') group by c.table_name order by c.table_name
```

### Q373
- label: Change 22, names first: cold_* functions — name, security definer, volatility, ACL (no bodies)
- run: 2026-11-14; rc 0; rows 14; 0.72 s
```sql
select p.proname, p.prosecdef, p.provolatile::text as vol, coalesce(p.proacl::text, '<pattern>') as acl from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname like '<pattern>' order by 1
```

### Q374
- label: Change 22, names first, immediately before the export: the four cold tables' column NAMES only
- run: 2026-11-14; rc 0; rows 4; 0.57 s
```sql
select c.table_name, string_agg(c.column_name, '<pattern>' order by c.ordinal_position) as cols from information_schema.columns c where c.table_schema = 'public' and c.table_name in ('<pattern>','<pattern>','<pattern>','<pattern>') group by c.table_name order by c.table_name
```

### Q375
- label: Change 22, names first, immediately before the export: cold_export's flags and ACL (no <term>)
- run: 2026-11-14; rc 0; rows 1; 0.51 s
```sql
select p.proname, p.prosecdef, p.provolatile::text as vol, coalesce(p.proacl::text, '<pattern>') as acl from pg_proc p join pg_namespace n on n.oid = p.pronamespace where n.nspname = 'public' and p.proname = '<pattern>'
```

### Q376
- label: Change 22: the late null<quoted words removed>s compare (cold_export, written once to work/cold/, never printed)
- run: 2026-11-14; rc 0; rows 1; 0.67 s
```sql
select public.cold_export()::text as x
```

### Q377
- label: S4 (Amendment 1 §5): IV §7<quoted words removed>s Q111 patterns; ids, engine, day and who brought it up (no text)
- run: 2026-11-14; rc 0; rows 0; 0.63 s
```sql
with s(choice, pat) as (values ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>')) select s.choice, m.id, coalesce(m.served_model, '<pattern>') as served_model, to_char(m.created_at at time zone 'America/New_York', 'YYYY-MM-DD') as day_et, exists (select 1 from message_store u where u.role = 'user' and u.route is distinct from 'secondary' and u.id < m.id and u.id > coalesce((select max(a.id) from message_store a where a.role = 'assistant' and a.id < m.id), 0) and u.content ~* s.pat) as principal_brought_it from s join message_store m on m.role = 'assistant' and m.route is distinct from 'secondary' and m.created_at >= (select created_at from message_store where id = 38420) and m.created_at < timestamptz '2026-11-14 [time withheld]' and m.content ~* s.pat order by s.choice, m.id
```

### Q378
- label: S4 (Amendment 1 §5): mentions at or after five_shown_at, by choice and engine (counts only, reported apart, never counted)
- run: 2026-11-14; rc 0; rows 0; 0.54 s
```sql
with s(choice, pat) as (values ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>')) select s.choice, coalesce(m.served_model, '<pattern>') as served_model, count(*) as n from s join message_store m on m.role = 'assistant' and m.route is distinct from 'secondary' and m.created_at >= timestamptz '2026-11-14 [time withheld]' and m.content ~* s.pat group by 1, 2 order by 1, 2
```

### Q379
- label: S4 control: the same patterns and join over Article IV's own window (ids 22288–32168), counts only; must find touches
- run: 2026-11-14; rc 0; rows 7; 0.95 s
```sql
with s(choice, pat) as (values ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>')) select s.choice, coalesce(m.served_model, '<pattern>') as served_model, count(*) as n from s join message_store m on m.role = 'assistant' and m.route is distinct from 'secondary' and m.id > 22287 and m.id <= 32168 and m.content ~* s.pat group by 1, 2 order by 1, 2
```

### Q380
- label: 38566 (the principal<quoted words removed>s distinctive words, Article IV's Q111 patterns, topic synonyms)
- run: 2026-11-14; rc 0; rows 1; 0.55 s
```sql
select id, role, coalesce(route,'<pattern>') as route, coalesce(served_model,'<pattern>') as served_model, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS') as utc, content ~* '<pattern>' as film_terms, content ~* '<pattern>' as source_terms, content ~* '<pattern>' as checker_terms, content ~* '<pattern>' as board_terms, content ~* '<pattern>' as iv_options, content ~* '<pattern>' as sitting_terms from message_store where id in (38566) and route is distinct from 'secondary'
```

### Q381
- label: 38566–38568: metadata only (role, route, engine, time)
- run: 2026-11-14; rc 0; rows 4; 0.56 s
```sql
select id, role, coalesce(route,'<pattern>') as route, coalesce(served_model,'<pattern>') as served_model, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.MS') as utc, length(content) as chars from message_store where id between 38565 and 38570 and route is distinct from 'secondary' order by id
```

### Q382
- label: 38566–38568 read in full: the principal<quoted words removed>s two replies after compare (its own reading of the two sets; quoted, if at all, after the graded table, labelled ungraded)
- run: 2026-11-14; rc 0; rows 3; 0.51 s
```sql
select id, role, content from message_store where id in (38566, 38567, 38568) and route is distinct from 'secondary' order by id
```

### Q383
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 46; 0.59 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q384
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 46; 0.55 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q385
- label: S4 re-run of Q378 verbatim before release: agent mentions of IV's seven choices at or after five_shown_at, by choice and engine (counts only; covers 38567-38568 and everything since)
- run: 2026-11-14; rc 0; rows 0; 0.59 s
```sql
with s(choice, pat) as (values ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>'), ('<pattern>', '<pattern>')) select s.choice, coalesce(m.served_model, '<pattern>') as served_model, count(*) as n from s join message_store m on m.role = 'assistant' and m.route is distinct from 'secondary' and m.created_at >= timestamptz '2026-11-14 [time withheld]' and m.content ~* s.pat group by 1, 2 order by 1, 2
```

### Q386
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 46; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q387
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-14; rc 0; rows 46; 0.57 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q388
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-16; rc 0; rows 46; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q389
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-16; rc 0; rows 46; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q390
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-16; rc 0; rows 46; 0.61 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q391
- label: Second plan, after side (MEASURE A6/A8): reply heads in the post primary window [S, S+72h22m13.586s) by served model, failover and tool-round stratum (counts only)
- run: 2026-11-16; rc 0; rows 6; 0.57 s
```sql
select coalesce(served_model,'<pattern>') as served_model, (coalesce(failover_reason,'<pattern>') <> '<pattern>') as failover, case when tool_iterations is null then '<pattern>' when tool_iterations = 1 then '0' when tool_iterations between 2 and 3 then '<pattern>' else '<pattern>' end as stratum, count(*) as n from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and created_at >= timestamptz '2026-11-13 [time withheld]' and created_at < timestamptz '2026-11-16 [time withheld]' group by 1, 2, 3 order by 1, 2, 3
```

### Q392
- label: Second plan, after side (MEASURE §6, A8, A12): the primary post heads (claude-fable-5-2, no failover): id, stratum, cost_usd and the cost recomputed at 5.1 and 5.2 rates (numbers only)
- run: 2026-11-16; rc 0; rows 156; 0.57 s
```sql
select id, case when tool_iterations is null then '<pattern>' when tool_iterations = 1 then '0' when tool_iterations between 2 and 3 then '<pattern>' else '<pattern>' end as stratum, cost_usd::float8 as c, ((coalesce(input_tokens,0)*10.0 + coalesce(output_tokens,0)*50.0 + coalesce(cache_read_input_tokens,0)*0.25 + coalesce(cache_creation_5m_tokens,0)*12.5 + coalesce(cache_creation_1h_tokens,0)*20.0)/1000000.0)::float8 as c51, ((coalesce(input_tokens,0)*8.0 + coalesce(output_tokens,0)*40.0 + coalesce(cache_read_input_tokens,0)*0.2 + coalesce(cache_creation_5m_tokens,0)*10.0 + coalesce(cache_creation_1h_tokens,0)*16.0)/1000000.0)::float8 as c52 from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-2' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-13 [time withheld]' and created_at < timestamptz '2026-11-16 [time withheld]' order by id
```

### Q393
- label: Second plan, before side re-read for the prediction<quoted words removed> cost_usd (numbers only)
- run: 2026-11-16; rc 0; rows 318; 0.63 s
```sql
select cost_usd::float8 as c from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-1' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]'
```

### Q394
- label: Second plan, before side: the two cost_usd order statistics around the upper quartile (ranks 238 and 239 of 318, as stored), to explain a third-decimal rounding difference (numbers only)
- run: 2026-11-16; rc 0; rows 2; 0.62 s
```sql
select cost_usd as c from message_store where role = 'assistant' and initiated = false and route = '<pattern>' and cost_usd is not null and video_call_id is null and served_model = 'claude-fable-5-1' and coalesce(failover_reason,'<pattern>') = '<pattern>' and created_at >= timestamptz '2026-11-10 [time withheld]' and created_at < timestamptz '2026-11-13 [time withheld]' order by cost_usd offset 237 limit 2
```

### Q395
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-16; rc 0; rows 46; 0.56 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q396
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-18; rc 0; rows 46; 0.61 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```

### Q397
- label: LAST PASS: re-query every message id cited in the article
- run: 2026-11-18; rc 0; rows 46; 0.57 s
```sql
select id, role, coalesce(served_model,'<pattern>') m, to_char(created_at at time zone 'America/New_York','YYYY-MM-DD HH24:MI:SS') edt, to_char(created_at at time zone 'UTC','YYYY-MM-DD HH24:MI:SS.US') utc from message_store where id in (1000,1694,1965,1970,1971,1975,1979,1984,1992,1996,1998,2008,2012,2014,2015,2021,2023,2024,2025,2026,7987,9484,22287,22296,22660,22662,22863,23967,25797,25827,25830,28418,28745,34916,34917,37364,37675,38171,38419,38420,38422,38535,38536,38566,38567,38568) order by id
```
