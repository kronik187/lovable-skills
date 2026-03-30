# SQL Verification Templates

Reusable SQL queries for Layer 3 (SQL Editor) verification. Always run schema discovery first before using any of these templates — column names vary between projects.

## Schema Discovery

### List all columns in a table
```sql
SELECT column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_schema = 'public'
  AND table_name = '<TABLE_NAME>'
ORDER BY ordinal_position;
```

### List all tables in public schema
```sql
SELECT table_name, table_type
FROM information_schema.tables
WHERE table_schema = 'public'
ORDER BY table_name;
```

### List all edge functions (database functions)
```sql
SELECT routine_name, routine_type, data_type as return_type
FROM information_schema.routines
WHERE routine_schema = 'public'
ORDER BY routine_name;
```

## Data Integrity

### Row counts with null analysis
```sql
SELECT
  COUNT(*) as total_rows,
  COUNT(CASE WHEN <column> IS NOT NULL THEN 1 END) as populated,
  COUNT(CASE WHEN <column> IS NULL THEN 1 END) as nulls,
  ROUND(COUNT(CASE WHEN <column> IS NOT NULL THEN 1 END)::decimal / COUNT(*) * 100, 1) as pct_populated
FROM <table>;
```

### Value distribution
```sql
SELECT <column>, COUNT(*) as count
FROM <table>
GROUP BY <column>
ORDER BY count DESC
LIMIT 20;
```

### Recent records (check if sync is working)
```sql
SELECT *
FROM <table>
WHERE updated_at > NOW() - INTERVAL '1 hour'
ORDER BY updated_at DESC
LIMIT 10;
```

## Security

### RLS policies on a table
```sql
SELECT schemaname, tablename, policyname, permissive, roles, cmd, qual, with_check
FROM pg_policies
WHERE tablename = '<TABLE_NAME>';
```

### Check if RLS is enabled
```sql
SELECT relname, relrowsecurity, relforcerowsecurity
FROM pg_class
WHERE relname = '<TABLE_NAME>';
```

## Indexes

### List indexes on a table
```sql
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = '<TABLE_NAME>';
```

## Data Integrity — Race Conditions & Duplicates

### Detect duplicate rows from concurrent syncs
```sql
SELECT wallet_id, symbol, COUNT(*) as dupes
FROM portfolio_token_balances
GROUP BY wallet_id, symbol
HAVING COUNT(*) > 1
ORDER BY dupes DESC;
```

### Clean up duplicates (keep most recent)
```sql
DELETE FROM portfolio_token_balances
WHERE id IN (
  SELECT id FROM (
    SELECT id, ROW_NUMBER() OVER (
      PARTITION BY wallet_id, symbol
      ORDER BY updated_at DESC
    ) as rn
    FROM portfolio_token_balances
  ) ranked
  WHERE rn > 1
);
```

### Add unique constraint to prevent future duplicates
```sql
ALTER TABLE portfolio_token_balances
ADD CONSTRAINT uq_wallet_token UNIQUE (wallet_id, symbol);
```

## Multi-Chain Verification

### Per-chain sync status
```sql
SELECT chain, COUNT(*) as records,
       COUNT(CASE WHEN value_usd > 0 THEN 1 END) as non_zero,
       COUNT(CASE WHEN value_usd IS NULL THEN 1 END) as nulls,
       MAX(updated_at) as last_sync
FROM portfolio_token_balances
GROUP BY chain
ORDER BY chain;
```

### Detect stale prices from API failures
```sql
SELECT COUNT(*) as stale_prices
FROM portfolio_token_balances
WHERE (price_usd IS NULL OR price_usd = 0)
  AND updated_at > NOW() - INTERVAL '1 hour';
```

### Check data type issues (TEXT columns that should be NUMERIC)
```sql
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = '<TABLE_NAME>'
  AND data_type = 'text'
  AND column_name LIKE '%fee%' OR column_name LIKE '%price%' OR column_name LIKE '%balance%' OR column_name LIKE '%value%';
```

## Migration Verification

### Check if migration was executed
```sql
SELECT * FROM _supabase_migrations
ORDER BY executed_at DESC
LIMIT 10;
```

### Verify new table exists
```sql
SELECT table_name, table_type
FROM information_schema.tables
WHERE table_schema = 'public'
  AND table_name = '<EXPECTED_TABLE>';
```

## CryptoFolio-Specific Queries

These are specific to the CryptoFolio/Mooncatcher project but serve as examples of domain-specific verification queries.

### Gap analysis summary
```sql
SELECT
  gap_status,
  COUNT(*) as wallet_count,
  ROUND(AVG(gap_percent)::decimal, 2) as avg_gap_pct,
  ROUND(SUM(gap_usd)::decimal, 2) as total_gap_usd
FROM portfolio_wallet_gap_checks
GROUP BY gap_status;
```

### Token balance verification
```sql
SELECT
  w.name as wallet_name,
  tb.symbol,
  tb.balance_formatted,
  tb.value_usd,
  tb.price_usd
FROM portfolio_token_balances tb
JOIN portfolio_wallets w ON w.id = tb.wallet_id
WHERE tb.value_usd > 0
ORDER BY tb.value_usd DESC
LIMIT 20;
```
