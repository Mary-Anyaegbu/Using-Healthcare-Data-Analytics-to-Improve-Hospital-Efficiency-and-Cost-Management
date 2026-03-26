### Note
This is a subset of the DAX measures used in the project, highlighting key calculations for year-over-year analysis and KPIs

### 1. Total Billing Amount
Aggregates total billing from the dataset.
```dax
Total Billing Amount = SUM(healthcare_dataset[Billing Amount])
```
### 2. Total Hospital Visits
Total count of all patient visits
```dax
Total Hospital Visits = COUNT(healthcare_dataset[Name])
```
### 3. Unique Patients
Total count of individual patients who received care in the period; each patient is counted only once regardless of multiple visits.
```dax
Unique Patients = DISTINCTCOUNT(healthcare_dataset[Name])
```
### 4. Avg. Billing per Patient
Calculates average billing per patient using total billing and unique patient count.
```dax
Avg. Billing per Patient =
DIVIDE([Total Billing Amount], [Unique Patients])
```
### 5. Avg. Length of stay
Average number of days patients spend in the hospital per admission
```dax
 Avg. Length of stay = AVERAGE(healthcare_dataset[Length of Stay])
```
### 6. Billing YOY%
Calculates year-over-year percentage change in total billing with conditional text output and directional indicators.
```dax
Billing YOY% =
VAR Curr = [Total Billing Amount]
VAR CurrentYear = MAX(Calendar[Year])
VAR MinYear = MIN(Calendar[Year])
VAR MultiYear = CurrentYear <> MinYear
VAR PrevYear = CurrentYear - 1
VAR Prev =
    CALCULATE(
        [Total Billing Amount],
        ALL(Calendar),
        Calendar[Year] = PrevYear
    )
VAR ChangePct = DIVIDE(Curr - Prev, Prev)
RETURN
IF(
    MultiYear,
    "Select Year",
    IF(
        ISBLANK(Prev) || Prev = 0,
        "No Prior Year",
        IF(
            ChangePct > 0,
            "vs " & PrevYear & " ▲ " & FORMAT(ChangePct, "0.0%"),
            IF(
                ChangePct < 0,
                "vs " & PrevYear & " ▼ " & FORMAT(ABS(ChangePct), "0.0%"),
                "vs " & PrevYear & " ▶ 0%"
            )
        )
    )
)

```
### 7. YOY% Selected Year
Computes YoY percentage change using built-in time intelligence (SAMEPERIODLASTYEAR)
```dax
YOY% Selected Year =
VAR _CurrentBilling = [Total Billing Amount]
VAR _PreviousBilling =
    CALCULATE(
        [Total Billing Amount],
        SAMEPERIODLASTYEAR('Calendar'[Date])
    )
RETURN
DIVIDE(_CurrentBilling - _PreviousBilling, _PreviousBilling)
```

### 8. Hospital Visits YOY%
Calculates YoY percentage change in total visits
```dax
Hospital Visits YOY% =
VAR Curr = [Total Visits]
VAR CurrentYear = MAX(Calendar[Year])
VAR MinYear = MIN(Calendar[Year])
VAR MultiYear = CurrentYear <> MinYear
VAR PrevYear = CurrentYear - 1
VAR Prev =
    CALCULATE(
        [Total Visits],
        ALL(Calendar),
        Calendar[Year] = PrevYear
    )
VAR ChangePct = DIVIDE(Curr - Prev, Prev)
RETURN
IF(
    MultiYear,
    "Select Year",
    IF(
        ISBLANK(Prev) || Prev = 0,
        "No Prior Year",
        IF(
            ChangePct > 0,
            "vs " & PrevYear & " ▲ " & FORMAT(ChangePct, "0.0%"),
            IF(
                ChangePct < 0,
                "vs " & PrevYear & " ▼ " & FORMAT(ABS(ChangePct), "0.0%"),
                "vs " & PrevYear & " ▶ 0%"
            )
        )
    )
)
```
