# Understanding the Insurance Coverage File

This file contains the **coverage history** for your cohort, detailing individual records for every period of insurance coverage each patient had. Each record includes:

- A **start date** (`from`)
- An **end date** (`to`)

---

## Key Concepts

### Overlapping Coverage Periods

- Some patients may have **overlapping coverage periods**, meaning they have more than one active insurance coverage during the same timeframe.
- To determine which coverage is primary, the file includes a **filling order** that prioritizes coverages (e.g., primary vs secondary insurance).

---

### Finding Coverage on a Specific Encounter Date

To find what kind of coverage a patient had on the date of an encounter:

1. **Identify the encounter date**.
2. **Find coverage records where the encounter date falls between the `from` and `to` dates**.

> **Note:**  
> If the `to` date is `NULL`, it means the coverage is ongoing. For practical purposes, treat `NULL` as the current date.

---

## Possible Scenarios When Looking Up Coverage

| Scenario | Description | What to Do |
|----------|-------------|------------|
| **1. Single coverage record found** | Only one coverage record includes the encounter date between `from` and `to`. | This is the primary and only coverage on that date. You are done. |
| **2. Multiple coverage records found** | More than one coverage record includes the encounter date. | Use the **filling order** to determine priority. The lowest filling order number is the primary coverage. You are done. |
| **3. No coverage record found** | No coverage record includes the encounter date. | The patient had **no coverage** at that time. |

---

## Example

| Patient ID | Coverage From | Coverage To | Filling Order | Coverage Type     |
|------------|---------------|-------------|---------------|-------------------|
| 12345      | 2023-01-01    | 2023-06-30  | 1             | Primary Insurance |
| 12345      | 2023-04-01    | NULL        | 2             | Secondary Insurance|

- **Encounter Date:** 2023-05-15
- **Step 1:** Check which records contain 2023-05-15 between `from` and `to`.
- **Step 2:** Both records cover this date.
- **Step 3:** Use filling order: 1 is primary, 2 is secondary.
- **Result:** Primary Insurance is the coverage on 2023-05-15.

---

## Summary

- The coverage file tracks all insurance periods per patient.
- Overlapping coverage is resolved by filling order priority.
- `NULL` `to` dates mean ongoing coverage.
- Use encounter date to find applicable coverage.
- If multiple coverages apply, the one with the lowest filling order is primary.
- No matching coverage means no insurance at that time.

---

If you want me to help you upload this file to your GitHub wiki or make any further adjustments, just let me know!
