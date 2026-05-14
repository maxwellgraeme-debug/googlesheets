# googlesheets

Use this formula to dynamically stack the `ALL COURSES!C2:BO` data from every
non-blank URL in `H3339:H3380`:

```gs
=LET(
  urls, TOCOL(H3339:H3380, 1),
  IF(
    COUNTA(urls)=0,
    ,
    DROP(
      REDUCE(
        MAKEARRAY(1, 65, LAMBDA(r, c, "")),
        urls,
        LAMBDA(
          acc,
          url,
          VSTACK(acc, IMPORTRANGE(url, "'ALL COURSES'!C2:BO"))
        )
      ),
      1
    )
  )
)
```

Why this works:

- `TOCOL(H3339:H3380, 1)` removes blank URL cells before looping.
- `MAKEARRAY(1, 65, ...)` creates a single blank seed row that matches the
  `C:BO` width, so `REDUCE`/`VSTACK` always combines arrays with the same number
  of columns.
- `DROP(..., 1)` removes that temporary seed row from the final result.
- Each `IMPORTRANGE` still starts at `C2`, so headings are not duplicated.

If a source spreadsheet has not been authorised yet, allow access for that
`IMPORTRANGE` source once and then the full stacked formula will recalculate.
