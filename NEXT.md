# NEXT

Work queue for this repo. Each item was found by a check, listed under it.

1. Add a Maven `pom.xml` so server and client build from source.
   - Check: `javac` on `src/` alone gives 37 classes and no errors. `src-server/` and `src-client/` import Gson (grep), and no build file lists it. `git ls-files` shows no pom or gradle file.
2. Add unit tests for `LmsrCalculator.cost` and `LmsrCalculator.price`.
   - Check: both are public, and `git ls-files` shows no test file. Cases: price sums to 1, equal quantities give 0.5, huge quantities do not overflow (the class comment claims this).
3. Add unit tests for `OrderBookMatcher.submit`.
   - Check: it is the public entry point for matching, and no test file exists. Cases: no match rests in the book, a full match, a partial match.
