
# 🚀 Incremental Load Logic (SQL Watermarking)

This pattern shows how to implement **incremental load** using a **watermark table** in SQL Server / Azure SQL DB.  
The watermark table keeps track of the **last processed record**, so each run only loads new data.

---

## 🔹 Step 1. Create a Watermark Table
```sql
CREATE TABLE water_table (
    load_data VARCHAR(2000)
);
````

This table stores the marker (`load_data`) that represents the last processed `date_id`.

---

## 🔹 Step 2. Initialize the Watermark

```sql
-- Find the minimum date_id in source table (example: DT00001)
SELECT MIN(date_id) 
FROM [dbo].[car_sales];

-- Insert a starting marker just before the minimum
INSERT INTO water_table VALUES ('DT00000');
```

By starting at `DT00000` (one step before the minimum), the **first run loads all available data**.

---

## 🔹 Step 3. Query Incremental Data

```sql
-- Pull only records greater than last processed watermark
SELECT *
FROM [dbo].[car_sales]
WHERE Date_ID > (SELECT load_data FROM [dbo].[water_table])
  AND Date_ID <= (SELECT MAX(Date_ID) FROM [dbo].[car_sales]);
```

This ensures:

* Only records newer than the stored watermark are pulled.
* It stops at the maximum available `Date_ID` in source.

---

## 🔹 Step 4. Create Stored Procedure to Update Watermark

```sql
CREATE OR ALTER PROCEDURE update_water_table
    @lastload VARCHAR(2000)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRANSACTION;

    UPDATE water_table
    SET load_data = @lastload;

    COMMIT TRANSACTION;
END;
```

This procedure updates the watermark after each load run.
Example call:

```sql
EXEC update_water_table @lastload = 'DT00025';
```

---

## 🔹 Workflow Summary

1. **Lookup** current watermark from `water_table`.
2. **Extract** data where `Date_ID > watermark`.
3. **Process & load** data into target.
4. **Update watermark** with the latest processed `Date_ID` using `update_water_table`.

---

✅ This logic ensures that every pipeline run only picks up **new data** since the last successful load.


