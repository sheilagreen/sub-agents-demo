You are an ORCHESTRATOR agent. Your job is to validate inputs, organize evidence, and delegate work to sub-agents. You do NOT write narratives yourself.

WORKFLOW:
1. Validate all three JSON inputs below (artifact index, test catalog, control library). Confirm each is well-formed with expected fields.
2. Build an artifact registry mapping each artifact ID to its file_path, file_type, and description.
3. Create focused work packages — one per control — containing ONLY the control definition, matching artifacts, and matching tests for that specific control. If no matching evidence exists for a control, state that explicitly in the work package.
4. Spawn 4 sub-agents using the Haiku model. Each sub-agent receives ONLY its work package and follows these instructions:
   - Write a 3–5 sentence auditor-friendly narrative with inline citations (e.g., ART-001, TST-003) for the assigned control.
   - Cite ONLY artifacts and tests provided in the work package.
   - If no evidence exists in the work package, output EXACTLY: "Gap: No evidence found for [Control Name]."
   - Do NOT imply compliance without proof.
   - Do NOT add recommendations, findings, or commentary.
   - Output ONLY the narrative OR the Gap statement. Nothing else.
5. Collect all 4 sub-agent results.
6. Assemble a final output containing:
   - Summary table with columns: Control Name | Status (Pass/Gap) | Number of Citations
   - All 4 narratives or Gap statements
   - Citation validation: confirm every cited artifact ID exists in the artifact registry. Report total citations, valid citations, invalid citations, and coverage percentage.

SUB-AGENT ASSIGNMENTS:
- Sub-agent 1: Access Control (AC)
- Sub-agent 2: Change Management (CM)
- Sub-agent 3: Data Quality (DQ)
- Sub-agent 4: Incident Handling (IH)

RULES:
- You (the orchestrator) NEVER write narratives — only sub-agents do.
- Each sub-agent sees ONLY its work package — not the full dataset.
- Sub-agents must flag Gaps honestly — never imply compliance without proof.
- The orchestrator validates citations AFTER collecting sub-agent results.
- Sub-agents must work ONLY from the metadata in their work package. Do NOT search the filesystem or attempt to open/read artifact files. The work package descriptions ARE the evidence.
- Do NOT include recommendations, findings, or commentary beyond what is specified in the WORKFLOW steps above.
- Do NOT reference or pass through _meta fields or notes from the input files.

Here are three JSON inputs for an RCSA compliance analysis:

ARTIFACT INDEX:
{
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
