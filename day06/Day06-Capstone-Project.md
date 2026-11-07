# TerraWeek Day 6: Advanced Terraform and Capstone Project

## Overview
This document outlines the completion of TerraWeek Day 06. The project focuses on environment isolation, native testing, static security analysis, and CI/CD automation using Terraform and GitHub Actions. All tasks were executed sequentially with verified terminal outputs.

## Task 1: Workspaces and Environments
**Objective:** Manage multiple environments using Terraform Workspaces and dynamic configurations.

*   Created and verified `staging` and `prod` workspaces.
*   Configured `main.tf` to evaluate instance types dynamically using the local block: `instance_type = terraform.workspace == "prod" ? "t3.medium" : "t3.micro"`.
*   **Terminal Output:** Executing `terraform plan` in the `staging` workspace successfully evaluated the condition, generating the output: `deployment_info = "Deployment terraweek-staging with instance type: t3.micro"`.

**Screenshot Reference:** [Task 1 - Workspaces Deployment](screenshots/task-01-workspaces.png)

## Task 2: Quality Gates (fmt, validate, test)
**Objective:** Enforce code formatting, syntax validation, and infrastructure logic verification.

*   Executed `terraform fmt -recursive` to standardize code style across the project.
*   Executed `terraform validate` which confirmed the configuration syntax is valid.
*   Executed `terraform test` using the native framework (Terraform 1.5.0+).
*   **Terminal Output:** The test execution for `tests/basic.tftest.hcl` completed with `Success! 4 passed, 0 failed`. The passing runs included:
    *   `dev_uses_micro` (plan-based)
    *   `prod_uses_medium` (plan-based)
    *   `name_has_prefix` (apply-based)
    *   `rejects_bad_environment` (plan-based)

**Screenshot Reference:** [Task 2 - Native Testing Success](screenshots/task-02-testing.png)

## Task 3: Security Scanning
**Objective:** Perform Static Application Security Testing (SAST) on the Infrastructure as Code prior to deployment.

*   Installed Aqua Security's Trivy via Homebrew.
*   Executed `trivy config .` to scan the root module for potential vulnerabilities and misconfigurations.
*   **Terminal Output:** The scan analyzed the terraform configuration files, resulting in a clean report with `0` misconfigurations detected.

**Screenshot Reference:** [Task 3 - Trivy Scan Report](screenshots/task-03-security-scan.png)

## Task 4: CI/CD with GitHub Actions
**Objective:** Automate formatting, validation, testing, and security scanning on code push.

*   Created `.github/workflows/terraform.yml` located at the repository root.
*   Configured the pipeline to trigger on pushes and pull requests to the `main` branch.
*   Integrated `terraform init -backend=false` to optimize the pipeline by bypassing remote state initialization during the validation phases.
*   **Output:** The GitHub Actions workflow successfully triggered and passed all jobs sequentially: Checkout Repository, Setup Terraform, Terraform Format Check, Terraform Init, Terraform Validate, Terraform Test, and Security Scan (Trivy).

**Screenshot Reference:** [Task 4 - GitHub Actions Pipeline](screenshots/task-04-github-actions.png)

## Task 5: Capstone Best Practices Checklist
To fulfill the production-grade Capstone requirements, the following architectural standards were implemented in the codebase:

*   **Remote State with Locking:** Added `backend.tf` configuring an S3 backend (`terraweek-state-bucket-rk`) mapped to the `ap-south-1` region and a DynamoDB table (`terraform-locks`) for state locking operations.
*   **No Hardcoded Secrets:** Utilized standard variable blocks and workspace references instead of static credentials.
*   **Automated Pipeline:** Fully wired CI workflow preventing unverified code from merging.
