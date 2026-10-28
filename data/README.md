# Local data directory

This repository does **not** distribute UT Dallas course descriptions or LinkedIn authentication/session data.

The original project includes a dataset (utd_courses.json) containing 2,470 course records from the UT Dallas. This dataset is maintained locally and excluded from the public repository pending verification of redistribution permissions.

The application will start without this catalog, but course search and course recommendations may return no results. The source code currently expects several normalized fields (`course_code`, `department`, `skills`) which may be missing from the original raw scrape and therefore require normalization before certain filters work fully.

`data/linkedin_session.json` and `data/job_cache/` are generated local/runtime state and must never be committed.

Source reference: <https://catalog.utdallas.edu/>. Check reuse and redistribution rights before including third-party descriptions in any publicly distributed dataset.
