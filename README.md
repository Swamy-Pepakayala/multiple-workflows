# multiple-workflows
# Concurrency limits 
name: Example Workflow

on:
  push:
    branches:
      - main

jobs:
  check_concurrency:
    runs-on: ubuntu-latest
    steps:
      - name: Check if another instance is running
        run: |
          # Implement logic to check if another instance of this workflow is already running
          # This could involve checking for a specific file, querying GitHub API, etc.
          # Example: Check if a lock file exists
          if [ -f /tmp/workflow-lock ]; then
            echo "Another instance of this workflow is running. Exiting."
            exit 0
          else
            # Create a lock file to prevent other instances from starting
            touch /tmp/workflow-lock
          fi

  # Continue with the rest of your workflow steps here
  build:
    needs: check_concurrency
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      # Add more steps as needed

  # Clean up the lock file at the end of the workflow
  cleanup:
    runs-on: ubuntu-latest
    needs: [build]
    steps:
      - name: Clean up
        run: |
          # Remove the lock file to allow the next instance to run
          rm /tmp/workflow-lock
