## Prompt 1: A Side — Single Agent

```
I need you to perform a complete RCSA compliance analysis in one pass.

Here are three JSON inputs:

ARTIFACT INDEX:
{
  "_meta": {
    "scale": "demo",
    "note": "Demo-specific version — IH artifacts (ART-014, ART-015) removed to guarantee Gap flag"
  },
  "artifacts": [
    {
      "id": "ART-001",
      "file_path": "auth/oauth_config.yaml",
      "file_type": "yaml",
      "description": "OAuth2 provider configuration defining authentication flows, token lifetimes, and allowed redirect URIs"
    },
    {
      "id": "ART-002",
      "file_path": "auth/mfa_settings.json",
      "file_type": "json",
      "description": "Multi-factor authentication settings including enforced methods and bypass rules"
    },
    {
      "id": "ART-003",
      "file_path": "auth/rbac_roles.yaml",
      "file_type": "yaml",
      "description": "Role-based access control definitions mapping roles to permissions across application modules"
    },
    {
      "id": "ART-004",
      "file_path": "auth/rbac_permissions_matrix.json",
      "file_type": "json",
      "description": "Permissions matrix specifying granular access levels per role and resource type"
    },
    {
      "id": "ART-005",
      "file_path": "auth/session_config.yaml",
      "file_type": "yaml",
      "description": "Session management configuration including timeout duration, renewal policy, and invalidation triggers"
    },
    {
      "id": "ART-006",
      "file_path": "deploy/change_approval_workflow.yaml",
      "file_type": "yaml",
      "description": "CI/CD pipeline stage requiring manual approval before production deployment"
    },
    {
      "id": "ART-007",
      "file_path": "deploy/pr_review_policy.md",
      "file_type": "markdown",
      "description": "Pull request review policy requiring minimum reviewers and passing checks before merge"
    },
    {
      "id": "ART-008",
      "file_path": "deploy/ci_pipeline.yaml",
      "file_type": "yaml",
      "description": "CI/CD pipeline definition including build, test, staging, and production deployment stages"
    },
    {
      "id": "ART-009",
      "file_path": "deploy/terraform_main.tf",
      "file_type": "terraform",
      "description": "Infrastructure-as-code defining production environment resources and deployment configuration"
    },
    {
      "id": "ART-010",
      "file_path": "deploy/rollback_runbook.md",
      "file_type": "markdown",
      "description": "Step-by-step rollback procedure for reverting failed production deployments"
    },
    {
      "id": "ART-011",
      "file_path": "deploy/rollback_script.sh",
      "file_type": "shell",
      "description": "Automated rollback script that reverts the last production deployment to the previous known-good state"
    },
    {
      "id": "ART-012",
      "file_path": "data/input_schema.json",
      "file_type": "json",
      "description": "JSON Schema defining required fields, types, and constraints for incoming data records"
    },
    {
      "id": "ART-013",
      "file_path": "data/validation_rules.yaml",
      "file_type": "yaml",
      "description": "Business rule definitions for input data validation including range checks and format requirements"
    }
  ]
}


TEST CATALOG:
{
  "_meta": {
    "scale": "demo",
    "note": "Demo-specific version — TST-008 (IH alert triggers test) removed to guarantee Gap flag"
  },
  "tests": [
    {
      "id": "TST-001",
      "file_path": "tests/auth/test_rbac_permissions.py",
      "file_type": "python",
      "description": "Verifies that RBAC role assignments enforce correct permission boundaries across application modules",
      "controls_relevant": ["AC"]
    },
    {
      "id": "TST-002",
      "file_path": "tests/auth/test_session_timeout.py",
      "file_type": "python",
      "description": "Validates that user sessions expire after the configured timeout period and cannot be reused after invalidation",
      "controls_relevant": ["AC"]
    },
    {
      "id": "TST-003",
      "file_path": "tests/auth/test_oauth_flow.py",
      "file_type": "python",
      "description": "Tests the OAuth2 authentication flow including token issuance, refresh, and revocation",
      "controls_relevant": ["AC"]
    },
    {
      "id": "TST-004",
      "file_path": "tests/deploy/test_approval_gate.py",
      "file_type": "python",
      "description": "Verifies that the CI/CD pipeline blocks production deployment without required approvals",
      "controls_relevant": ["CM"]
    },
    {
      "id": "TST-005",
      "file_path": "tests/deploy/test_rollback.sh",
      "file_type": "shell",
      "description": "End-to-end test that triggers a rollback and verifies the system returns to the previous known-good state",
      "controls_relevant": ["CM"]
    },
    {
      "id": "TST-006",
      "file_path": "tests/deploy/test_deployment_pipeline.py",
      "file_type": "python",
      "description": "Validates that the deployment pipeline executes all required stages in order including build, test, and staging",
      "controls_relevant": ["CM"]
    },
    {
      "id": "TST-007",
      "file_path": "tests/data/test_input_validation.py",
      "file_type": "python",
      "description": "Tests that invalid input records are rejected with appropriate error messages based on schema constraints",
      "controls_relevant": ["DQ"]
    }
  ]
}


CONTROL LIBRARY:
{
  "_meta": {
    "scale": "demo",
    "note": "Replace with production data post-handoff"
  },
  "controls": [
    {
      "id": "AC",
      "name": "Access Control",
      "objective": "Ensure system access is restricted to authorized users through authentication, authorization, and session management",
      "evidence_types": [
        {
          "id": "auth_config",
          "label": "Authentication Configuration",
          "description": "Configuration files defining authentication mechanisms such as OAuth, SSO, or MFA settings"
        },
        {
          "id": "rbac_policy",
          "label": "RBAC Policy Definition",
          "description": "Role-based access control definitions specifying roles, permissions, and access levels"
        },
        {
          "id": "session_management",
          "label": "Session Management Configuration",
          "description": "Configuration or code governing session lifecycle including timeout, renewal, and invalidation"
        }
      ]
    },
    {
      "id": "CM",
      "name": "Change Management",
      "objective": "Ensure all changes to production systems follow approved processes with rollback capability",
      "evidence_types": [
        {
          "id": "change_approval_record",
          "label": "Change Approval Record",
          "description": "Documentation or configuration enforcing change approval workflows before production deployment"
        },
        {
          "id": "deployment_config",
          "label": "Deployment Configuration",
          "description": "CI/CD pipeline definitions, deployment scripts, or infrastructure-as-code governing production releases"
        },
        {
          "id": "rollback_procedure",
          "label": "Rollback Procedure",
          "description": "Documented procedures or automated scripts enabling rollback of failed production changes"
        }
      ]
    },
    {
      "id": "DQ",
      "name": "Data Quality",
      "objective": "Ensure data inputs are validated and transformations preserve integrity",
      "evidence_types": [
        {
          "id": "input_validation_rule",
          "label": "Input Validation Rule",
          "description": "Schema definitions, validation logic, or constraint configurations that enforce data input quality"
        },
        {
          "id": "data_transform_test",
          "label": "Data Transformation Test",
          "description": "Test cases verifying that data transformations produce correct and complete output"
        }
      ]
    },
    {
      "id": "IH",
      "name": "Incident Handling",
      "objective": "Ensure security incidents are detected, responded to, and reviewed",
      "evidence_types": [
        {
          "id": "monitoring_config",
          "label": "Monitoring Configuration",
          "description": "Alert rules, log aggregation settings, or monitoring dashboards that detect anomalies and incidents"
        },
        {
          "id": "incident_response_plan",
          "label": "Incident Response Plan",
          "description": "Documented procedures for responding to security incidents including escalation paths and communication protocols"
        },
        {
          "id": "postmortem_record",
          "label": "Postmortem Record",
          "description": "Post-incident review documents capturing root cause analysis, lessons learned, and corrective actions"
        }
      ]
    }
  ]
}



Do ALL of the following in a single response:

1. Validate that all three inputs are well-formed JSON with expected fields
2. Build an artifact registry mapping each artifact ID to its name, type, and file_path
3. Map evidence from the artifact index and test catalog to all 4 controls: Access Control (AC), Change Management (CM), Data Quality (DQ), Incident Handling (IH)
4. For each control with sufficient evidence, write a 3-5 sentence auditor-friendly narrative with inline citations referencing artifact IDs (e.g., ART-001)
5. For any control with no supporting evidence, output EXACTLY: "Gap: No evidence found for [control name]." Do NOT imply compliance without proof.
6. Validate that every citation you used references a real artifact from the artifact index
7. Produce a summary table with columns: Control Name | Status (Pass/Gap) | Number of Citations
8. Produce a validation report with: Total Citations | Valid Citations | Invalid Citations | Coverage Percentage
```
