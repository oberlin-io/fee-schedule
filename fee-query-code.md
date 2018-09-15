# Code overview

The code queries a unique ID based on order factors, intersects the unique ID with the client name, and returns the fee.

# Code Explanation

A QUERY() function selects a unique ID using:
  1) SQL-like greater than/less than on a date range
  2) Embedded IF() functions to test types (eg, form type)

One MATCH() function takes the unique ID (which is a composite of pertinent order factors). Another takes the client name. An INDEX() function intersects both to locate the fee in the matrix.
```
Where...

  For order data table...
  C2 = Form type
  E2 = Ordered date
  N2 = Client
  Q2 = Area (county)
  R2 = FHA type
  
  For fee schedule table...
  CF = Fee schedule table
  CF!A = Starting active date of fee
  CF!B = Ending active date of fee
  CF!C = FHA type
  CF!D = County (area)
  CF!E = Appraisal form type
  CF!F = Unique ID
  CF!G:O = Client

At row 2...

=
IF
  (ISBLANK(C2),
  "",
  INDEX(
    CF!$F$2:$O,
    MATCH(
      QUERY(
        CF!$A$3:$F,
        "SELECT F WHERE A <= DATE '"&
          TEXT(
            DATEVALUE(E2),
            "YYY-MM-DD")&
          "' AND B > DATE '"&
          TEXT(
            DATEVALUE(E2),
            "YYY-MM-DD")&
          "' AND C = '"&
          R2&
          "' AND D = '"&
          IF(
            Q2="30",
            "Other",
            IF(
              Q2="35",
              "Holmes/Tusc", "Stark"
            )
          )&
          "' AND E = '"&
          C2&
          "'"
      ),
      CF!$F$2:$F,
      0
    ),
    MATCH(N2, CF!$F$2:$2,
    0
    )
  )
)
```
