# ESEP Grade Calculator

A small Go library that computes a student's final course grade. Grades are
entered in three categories (assignments, exams, essays). The library averages
each category, applies a weight to each average, sums the results, and converts
the total to a letter grade.

## Repository layout

```
.
├── README.MD
└── grade-calculator/              # Go module root (note: hyphen, not underscore)
    ├── go.mod                     # module esep/grade-calculator, go 1.24.4
    ├── grade_calculator.go        # library source (package esepunittests)
    └── grade_calculator_test.go   # unit tests
```

## Requirements

- [Go](https://go.dev/dl/) **1.24 or newer** (`go.mod` declares `go 1.24.4`).
  Check your version with `go version`.
- No third-party dependencies. The tests use only Go's built-in `testing` package.
- Optional: [VS Code](https://code.visualstudio.com/) with the **Go** extension
  (publisher: Go Team at Google).

## Getting started

```bash
git clone https://github.com/audgster/esep-grade-calculator.git
cd esep-grade-calculator/grade-calculator
```

All `go` commands below must be run from the `grade-calculator/` folder, because
that is where `go.mod` lives.

## Usage

```go
gc := esepunittests.NewGradeCalculator()

gc.AddGrade("homework 1", 95, esepunittests.Assignment)
gc.AddGrade("midterm",    88, esepunittests.Exam)
gc.AddGrade("ethics essay", 92, esepunittests.Essay)

letter := gc.GetFinalGrade() // "A", "B", "C", "D" or "F"
```

Import path: `esep/grade-calculator` (the module name in `go.mod`). The package
name declared in the source files is `esepunittests`.

### Public API

| Item | Description |
|------|-------------|
| `NewGradeCalculator() *GradeCalculator` | Creates an empty calculator. |
| `AddGrade(name string, grade int, gradeType GradeType)` | Records a grade in the category given by `gradeType`. |
| `GetFinalGrade() string` | Returns the letter grade for the weighted average. |
| `Assignment`, `Exam`, `Essay` | `GradeType` constants for the three categories. |

### Grading rules

Weights are currently hard-coded:

| Category | Weight |
|----------|--------|
| Assignments | 50% |
| Exams | 35% |
| Essays | 15% |

Letter-grade scale (applied to the weighted numeric grade):

| Numeric grade | Letter |
|---------------|--------|
| 90 and above | A |
| 80 to 89 | B |
| 70 to 79 | C |
| 60 to 69 | D |
| below 60 | F |

There are no plus/minus grades. The final numeric grade is truncated to a whole
number before the letter is chosen.

## Running the tests

From the `grade-calculator/` folder:

```bash
go test ./...            # run all tests
go test -v ./...         # verbose: list each test and its result
go test -count=1 ./...   # bypass Go's test cache and force a fresh run
go test -run TestGetGradeA -v ./...   # run a single test by name
```

### Test coverage

```bash
go test -coverprofile=coverage.txt ./...   # write a coverage profile
go tool cover -func=coverage.txt           # per-function coverage summary
go tool cover -html=coverage.txt           # open a highlighted HTML report
```

### Running tests in VS Code

1. Install the Go extension and accept the prompt to install the Go tools
   (`gopls`, `dlv`, etc.), or run **Go: Install/Update Tools** from the
   Command Palette.
2. Open the `grade-calculator/` folder (the one containing `go.mod`) with
   **File > Open Folder...**.
3. Open `grade_calculator_test.go`, then either:
   - click **run test** / **debug test** above a test function, or
   - open the **Testing** view (beaker icon, or **View: Show Testing**) and
     press the play button next to a test, or
   - use the integrated terminal (`` Ctrl+` ``) and run `go test -v ./...`.
4. For coverage highlighting in the editor, run
   **Go: Toggle Test Coverage In Current Package** from the Command Palette.

## Existing tests

`grade_calculator_test.go` contains three tests. Each builds a calculator, adds
one assignment, one exam and one essay, calls `GetFinalGrade()`, and compares
the result to an expected letter.

| Test | Inputs (assignment, exam, essay) | Expected |
|------|----------------------------------|----------|
| `TestGetGradeA` | 100, 100, 100 | A |
| `TestGetGradeB` | 80, 81, 85 | B |
| `TestGetGradeF` | 50, 55, 45 | F |

## Contributing

- Run `gofmt -w .` and `go vet ./...` before committing.
- Run `go test ./...` and make sure tests pass.
- Keep documentation-only changes in their own commit, separate from code changes.