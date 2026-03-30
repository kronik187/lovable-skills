# SQL Verification Templates

Ready-to-run queries for Layer 3 verification. Run via Lovable Cloud SQL Editor or pgcli.

## Schema Discovery

```sql
-- List all columns in a table
SELECT column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_schema = 'public' AND table_name = '<table>'
ORDER BY ordinal_position;

-- List all tables in public schema
SELECT table_name FROM information_schema.tables
WHERE table_schema = 'public' ORDER BY table_name;

-- List all database functions
SELECT routine_name, routine_type
FROM information_schema.routines
WHERE routine_schema = 'public';
```

## Data Integrity

```sql
-- Row counts with null analysis
SELECT COUNT(*) AS total,
       COUNT(<key_column>) AS non_null_key,
       COUNT(*) - COUNT(<key_column>) AS null_key
FROM <table>;

-- Value distribution
SELECT <column>, COUNT(*) AS cnt
FROM <table>
GROUP BY <column>
ORDER BY cnt DESC LIMIT 20;

-- Recent records (check if sync is working)
SELECT * FROM <table>
ORDER BY updated_at DESC LIMIT 10;
```

## Security

```sql
-- Check RLS policies on a table
SELECT policyname, cmd, qual, with_check
FROM pg_policies WHERE tablename = '<table>';

-- Check if RLS is enabled
SELECT tablename, rowsecurity
FROM pg_tables
WHERE schemaname = 'public' AND tablename = '<table>';
```

## Indexes

```sql
-- List indexes on a table
SELECT indexname, indexdef
FROM pg_indexes WHERE tablename = '<table>';
```

## Duplicate Detection (Race Conditions)

```sql
-- Detect duplicate rows from concurrent syncs
SELECT <unique_columns>, COUNT(*)
FROM <table>
GROUP BY <unique_columns>
HAVING COUNT(*) > 1;

-- Clean up duplicates (keep most recent)
DELETE FROM <table> a
USING <table> b
WHERE a.id < b.id
  AND a.<unique_col> = b.<unique_col>;

-- Prevent future duplicates
ALTER TABLE <table>
ADD CONSTRAINT <table>_unique_<col>
UNIQUE (<unique_columns>);
```

## Migration Verification

```sql
-- Check if migration was executed
SELECT * FROM supabase_migrations.schema_migrations
ORDER BY version DESC LIMIT 5;

-- Verify new table exists
SELECT table_name FROM information_schema.tables
WHERE table_schema = 'public' AND table_name = '<expected_table>';
```

## Common Schema Traps

- `balance_usd` vs `value_usd` — naming varies by table
- `source` column may not exist — check before filtering
- `updated_at` vs `last_updated` vs `sync_updated_at` — timestamp columns are inconsistent
- RLS policies can make tables appear empty from the wrong auth context
- NUMERIC not TEXT for prices/balances (common data type mistake)
