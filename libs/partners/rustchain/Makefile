.PHONY: all format lint type test tests integration_tests help check_imports check_version

# Default target executed when no arguments are given to make.
all: help

.EXPORT_ALL_VARIABLES:
UV_FROZEN = true

# Define a variable for the test file path.
TEST_FILE ?= tests/unit_tests/
PYTEST_EXTRA ?=

integration_test integration_tests: TEST_FILE=tests/integration_tests/

test:
	uv run --group test --group test_integration pytest $(PYTEST_EXTRA) $(TEST_FILE)

integration_test integration_tests:
	uv run --group test --group test_integration pytest -v --tb=short -n auto --benchmark-disable $(PYTEST_EXTRA) $(TEST_FILE)

tests:
	uv run --group test pytest $(PYTEST_EXTRA) $(TEST_FILE)


######################
# LINTING AND FORMATTING
######################

# Define a variable for Python and notebook files.
PYTHON_FILES=.
MYPY_CACHE=.mypy_cache
lint: PYTHON_FILES=.
lint format: PYTHON_FILES=langchain_rustchain
lint_diff format_diff: PYTHON_FILES=$(shell git diff --relative=libs/partners/rustchain --name-only --diff-filter=d master | grep -E '\.py$$|\.ipynb$$')
UV_RUN_LINT = uv run --group lint

lint:
	./scripts/lint_imports.sh
	[ "$(PYTHON_FILES)" = "" ] || $(UV_RUN_LINT) ruff check $(PYTHON_FILES)
	[ "$(PYTHON_FILES)" = "" ] || $(UV_RUN_LINT) ruff format $(PYTHON_FILES) --diff

format:
	[ "$(PYTHON_FILES)" = "" ] || $(UV_RUN_LINT) ruff format $(PYTHON_FILES)
	[ "$(PYTHON_FILES)" = "" ] || $(UV_RUN_LINT) ruff check --fix $(PYTHON_FILES)

check_imports: $(shell find langchain_rustchain -name '*.py')
	$(UV_RUN_LINT) python ./scripts/check_imports.py $^

check_version:
	uv run python ./scripts/check_version.py

######################
# HELP
######################

help:
	@echo '----'
	@echo 'check_imports			- check imports'
	@echo 'check_version                - validate version consistency'
	@echo 'format                       - run code formatters'
	@echo 'lint                         - run linters'
	@echo 'test                         - run unit tests'
	@echo 'tests                        - run unit tests'
	@echo 'test TEST_FILE=<test_file>   - run all tests in file'