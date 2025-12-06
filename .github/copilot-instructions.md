# AI Coding Agent Instructions - Universidades Database Project

## Project Overview

This workspace contains **educational database projects** for a graduate-level Data Science program, focused on SQL design, data manipulation, and analytical workflows. The primary projects are:

- **`Examen.ipynb`**: SQL exam covering DML operations (INSERT/UPDATE/DELETE), complex queries (JOINs, GROUP BY, subqueries), and Pandas-based analytics on a university database
- **`Examen 2.md`**: Comprehensive database design project for a veterinary clinic, involving schema design, normalization, and transactional controls

The database file `Datos/universidad.db` is a SQLite database connected in notebooks via `sqlite3` module.

---

## Critical Architecture & Data Flow

### Database Connection Pattern
All notebooks follow this standardized connection pattern:
```python
import sqlite3
import pandas as pd

conn = sqlite3.connect('C:/Users/Sonia/Universidades/Datos/universidad.db')
cursor = conn.cursor()
cursor.execute("PRAGMA foreign_keys = ON;")  # ALWAYS enable foreign keys
```

**Key Pattern:**
- Enable `PRAGMA foreign_keys = ON` immediately after connection (enforces referential integrity)
- Always call `conn.commit()` after INSERT/UPDATE/DELETE operations
- Use `pd.read_sql_query()` for SELECT queries, `cursor.execute()` + `fetchone()/fetchall()` for checks

### Table Structure (Universidad Database)
Based on the notebook context, the schema includes:
- **Cursos**: `id_curso` (PK), `nombre_curso`, `creditos`, `id_profesor` (FK)
- **Estudiantes**: `id_estudiante` (PK), `nombre`, `apellido`, `email`, `fecha_ingreso`
- **Inscripciones**: `id_estudiante` (FK), `id_curso` (FK), `nota_final`
- **Profesores**: `id_profesor` (PK), `nombre`, `apellido` (implied by exam questions)

---

## Critical Developer Workflows

### Exam-Based Workflow (Examen.ipynb)
The notebook is structured in **3 sections** with specific patterns:

1. **Section 1 - DML Operations (5 points)**
   - Pattern: Check existence → Insert if missing → Commit
   - Example: `SELECT id_curso ... WHERE nombre_curso = ?` → if None → INSERT
   - Always use parameterized queries to prevent SQL injection
   - Retrieve auto-generated IDs after INSERT for foreign key references

2. **Section 2 - SELECT Queries (6 points)**
   - Use `ejecutar_sql(query)` helper function for clean pandas DataFrame display
   - Implement aggregations (COUNT, AVG), JOINs (LEFT, INNER), subqueries, GROUP BY/HAVING
   - Example: `SELECT nombre_curso, COUNT(*) FROM Inscripciones JOIN Cursos ... GROUP BY id_curso`

3. **Section 3 - Pandas Analytics (4 points)**
   - Load result sets into DataFrames: `df_notas = pd.read_sql_query(query, conn)`
   - Use DataFrame filtering: `df_riesgo = df_notas[df_notas['nota_final'] < 3.0]`
   - Create pivot tables: `df.pivot_table(values='nota_final', index='nombre_curso', columns='nombre_profesor')`
   - Generate visualizations with matplotlib/seaborn

### Schema Design Workflow (Examen 2.md)
This project requires:
1. **Conceptual modeling**: Chen/Crow's Foot notation for ER diagrams
2. **Normalization justification**: Explain 3NF compliance (no transitive dependencies)
3. **SQL DDL implementation**: CREATE TABLE scripts with PK, FK, and NOT NULL constraints
4. **Transactional safety**: Implement database-level constraints to prevent data anomalies (e.g., overbooking prevention)

---

## Project-Specific Patterns & Conventions

### SQL Code Standards
- **Always check for existence before INSERT**: Prevent duplicate entries in academic systems
  ```python
  cursor.execute("SELECT id FROM Table WHERE unique_field = ?", (value,))
  if cursor.fetchone() is None:
      cursor.execute("INSERT INTO Table ...")
  ```
- **Use parameterized queries** (`?` placeholders): Required for all user-influenced inputs
- **Commit after mutations**: `conn.commit()` is mandatory after INSERT/UPDATE/DELETE

### Python/Pandas Patterns
- Use `display()` function in notebooks for formatted table output (Jupyter-compatible)
- DataFrame column names come from SQL AS aliases or table column names directly
- Filter operations: `df[df['column'] > threshold]` for analytical filtering
- Always check DataFrame `.info()` for type validation after SQL load

### Naming Conventions
- SQL identifiers use snake_case: `nombre_curso`, `id_estudiante`, `fecha_ingreso`
- English module names (sqlite3, pandas) contrast with Spanish database design (exam context)
- File naming: `01_esquema_basico.sql` for schema files (numbered for execution order)

---

## Integration Points & Dependencies

### External Dependencies
- **sqlite3**: Built-in Python module for database operations
- **pandas**: DataFrames for query results and analytical work
- **matplotlib/seaborn** (implied): For visualization in Section 3 of exam

### Critical Integration Pattern: ID Retrieval After INSERT
After inserting a course or student, retrieve the auto-generated ID for subsequent references:
```python
cursor.execute("INSERT INTO Cursos (nombre_curso, creditos, id_profesor) VALUES (?, ?, ?)", (...))
conn.commit()
cursor.execute("SELECT id_curso FROM Cursos WHERE nombre_curso = ?", (name,))
id_curso = cursor.fetchone()[0]
```

This pattern is essential because:
- Examen.ipynb requires matriculating a student in a newly created course
- The exam tests understanding of referential integrity across tables

---

## Key Files & Examples

| File | Purpose | Key Pattern |
|------|---------|-------------|
| `Examen.ipynb` | Main SQL exam implementation | Check → Insert → Retrieve ID → Use in FK |
| `Examen 2.md` | Database design specification | Schema DDL + normalization justification |
| `Datos/universidad.db` | SQLite database with course/student data | Enable foreign keys; maintain referential integrity |

---

## When Assisting with This Project, Focus On:

1. **Correctness of SQL logic**: Verify JOINs, subqueries, and aggregations match academic requirements
2. **Data integrity checks**: Ensure PRAGMA foreign_keys is enabled and referenced IDs exist
3. **Pandas transformation accuracy**: Filter, pivot, and aggregate operations must match exam intent
4. **Parameterized queries**: All dynamic inputs must use `?` placeholders
5. **Commit consistency**: Every mutation must be followed by `conn.commit()`

---

## Common Pitfalls to Avoid

- ❌ Forgetting `conn.commit()` after INSERT/UPDATE/DELETE
- ❌ Using direct string concatenation instead of parameterized queries
- ❌ Attempting to insert records without verifying ID foreign keys exist
- ❌ Running exam cells out of order (IDs generated in earlier cells needed in later ones)
- ❌ Omitting `PRAGMA foreign_keys = ON` after connection
