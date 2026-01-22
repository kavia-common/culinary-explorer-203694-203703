# Food Recipe Database — Schema + Seed (Step 02.01)

This database container uses PostgreSQL and provides a CLI connection command in:

- `db_connection.txt`

Example:

```bash
psql postgresql://appuser:dbuser123@localhost:5000/myapp
```

## What was applied

All SQL was executed **one statement at a time** via:

```bash
CONN=$(cat db_connection.txt)
$CONN -c "SQL_STATEMENT_HERE"
```

### Tables

#### `recipes`
- Stores core recipe metadata.

Columns:
- `id BIGSERIAL PRIMARY KEY`
- `title TEXT NOT NULL UNIQUE`
- `description TEXT NOT NULL`
- `cuisine TEXT NOT NULL`
- `servings INTEGER NOT NULL CHECK (servings > 0)`
- `prep_time_minutes INTEGER NOT NULL CHECK (prep_time_minutes >= 0)`
- `cook_time_minutes INTEGER NOT NULL CHECK (cook_time_minutes >= 0)`
- `image_url TEXT NULL`
- `created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`
- `updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`

#### `ingredients`
- Ingredient rows per recipe.

Columns:
- `id BIGSERIAL PRIMARY KEY`
- `recipe_id BIGINT NOT NULL REFERENCES recipes(id) ON DELETE CASCADE`
- `name TEXT NOT NULL`
- `quantity TEXT NOT NULL`
- `sort_order INTEGER NOT NULL DEFAULT 0`
- `created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`

Indexes:
- `idx_ingredients_recipe_id` on `(recipe_id)`

#### `preparation_steps`
- Step-by-step instructions per recipe.

Columns:
- `id BIGSERIAL PRIMARY KEY`
- `recipe_id BIGINT NOT NULL REFERENCES recipes(id) ON DELETE CASCADE`
- `step_number INTEGER NOT NULL CHECK (step_number > 0)`
- `instruction TEXT NOT NULL`
- `created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`
- `UNIQUE (recipe_id, step_number)` ensures a stable step order per recipe

Indexes:
- `idx_steps_recipe_id` on `(recipe_id)`
- `idx_steps_recipe_id_step_number` on `(recipe_id, step_number)`

## Seed dataset

Seeded 3 complete recipes (each with ingredients + preparation steps):
1. Spaghetti Aglio e Olio
2. Chicken Tikka Masala
3. Classic Pancakes

Verification query used:

```sql
SELECT
  (SELECT COUNT(*) FROM recipes) AS recipes_count,
  (SELECT COUNT(*) FROM ingredients) AS ingredients_count,
  (SELECT COUNT(*) FROM preparation_steps) AS steps_count;
```

Result:

- recipes: **3**
- ingredients: **19**
- steps: **11**
