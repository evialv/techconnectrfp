# TechConnect RFP Agent Evaluation

Automated evaluation pipeline for RFP analysis agents using [Azure AI Agent Evaluation GitHub Action](https://github.com/marketplace/actions/azure-ai-agent-evaluation).

## Overview

This repository evaluates three RFP agents:
- **RfpSummaryAgent** - Summarizes RFP documents
- **RfpRiskAgent** - Identifies risks in proposals
- **RfpComplianceAgent** - Checks compliance requirements

## Prerequisites

1. **Azure AI Foundry Project** with deployed agents
2. **Azure AD App Registration** with federated credentials for GitHub Actions
3. **GitHub repository** with Actions enabled

---

## Setup Instructions

### Step 1: Create Agents in Azure AI Foundry

1. Go to [Azure AI Foundry](https://ai.azure.com)
2. Open your project
3. Navigate to **Build** → **Agents**
4. Create three agents:

#### RfpSummaryAgent
```
Name: RfpSummaryAgent
Model: gpt-4.1-mini
Instructions: You are the Summary Agent. Your role is to read and synthesize RFP or proposal documents into clear, structured executive summaries. Focus on key clauses, deliverables, evaluation criteria, pricing terms, timelines, and obligations. Organize your output into sections such as Overview, Key Clauses, Deliverables, Terms, and Notable Conditions.
Tools: Azure AI Search (connect to your RFP index)
```

#### RfpRiskAgent
```
Name: RfpRiskAgent
Model: gpt-4.1-mini
Instructions: You are the Risk Agent. Your task is to identify and assess potential risks across the document, including legal, financial, operational, technical, and scheduling risks. For each risk, provide a short description, the affected clause or section, a risk category, and a qualitative rating (Low, Medium, High).
Tools: Azure AI Search (connect to your RFP index)
```

#### RfpComplianceAgent
```
Name: RfpComplianceAgent
Model: gpt-4.1-mini
Instructions: You are the Compliance Agent. Your goal is to evaluate whether the RFP or proposal aligns with internal policies, regulatory standards, and ethical or contractual requirements. Identify any non-compliant clauses, ambiguous terms, or potential policy conflicts.
Tools: Azure AI Search (connect to your RFP index)
```

5. Note the **Agent IDs** (format: `asst_xxxxx` or similar)

---

### Step 2: Set Up Azure AD Federated Credentials

1. Go to **Azure Portal** → **Microsoft Entra ID** → **App Registrations**
2. Create new registration or use existing
3. Go to **Certificates & Secrets** → **Federated credentials** → **Add credential**
4. Configure:
   - **Scenario**: GitHub Actions deploying Azure resources
   - **Organization**: `evialv`
   - **Repository**: `techconnectrfp`
   - **Entity type**: Branch
   - **Branch**: `main`
   - **Name**: `github-actions-federated`
5. Save and note:
   - **Application (client) ID**
   - **Directory (tenant) ID**
   - Go to Subscription to get **Subscription ID**

---

### Step 3: Grant Permissions

Your App Registration needs these roles on the AI Foundry project:

1. Go to your **AI Foundry resource** in Azure Portal
2. **Access control (IAM)** → **Add role assignment**
3. Add:
   - `Cognitive Services User`
   - `Azure AI Developer`

---

### Step 4: Configure GitHub Repository Variables

1. Go to your repo: https://github.com/evialv/techconnectrfp
2. **Settings** → **Secrets and variables** → **Actions** → **Variables** tab
3. Add these **Repository Variables**:

| Name | Value |
|------|-------|
| `AZURE_CLIENT_ID` | Your App Registration Client ID |
| `AZURE_TENANT_ID` | Your Azure AD Tenant ID |
| `AZURE_SUBSCRIPTION_ID` | Your Azure Subscription ID |
| `AZURE_AI_PROJECT_ENDPOINT` | `https://<your-hub>.services.ai.azure.com/api/projects/<project-name>` |

---

### Step 5: Update Workflow with Agent IDs

Edit `.github/workflows/rfp-evaluation.yml` and replace placeholder agent IDs:

```yaml
agent-ids: "your-actual-summary-agent-id:1"
```

Get agent IDs from Foundry portal → Agents → Select agent → Copy ID

---

### Step 6: Run Evaluation

**Option 1: Manual trigger**
1. Go to **Actions** tab
2. Select **RFP Agent Evaluation**
3. Click **Run workflow**

**Option 2: Automatic on push**
- Push changes to `main` branch
- Workflow runs automatically

---

## Evaluation Metrics

| Metric | Description | Good Score |
|--------|-------------|------------|
| **Groundedness** | Response supported by context | > 4.0 |
| **Relevance** | Response addresses query | > 4.0 |
| **Coherence** | Logically structured | > 4.0 |
| **Fluency** | Grammatically correct | > 4.0 |
| **Task Adherence** | Follows instructions | > 4.0 |
| **Violence** | Safety check | < 1 |
| **Hate/Unfairness** | Safety check | < 1 |
| **Sexual** | Safety check | < 1 |
| **Self-harm** | Safety check | < 1 |

---

## Dataset

Test cases are in `data/rfp-evaluation-dataset.json`. 

To add more test cases:

```json
{
  "query": "Your RFP question",
  "ground_truth": "Expected correct answer",
  "context": "Relevant context from RFP documents"
}
```

---

## Results

Evaluation results appear in the GitHub Actions run summary with:
- Per-metric scores with confidence intervals
- Safety evaluation results
- Statistical comparison (when comparing multiple agents)

---

## Files Structure

```
techconnectrfp/
├── .github/
│   └── workflows/
│       └── rfp-evaluation.yml    # GitHub Action workflow
├── data/
│   └── rfp-evaluation-dataset.json  # Test cases
├── README.md                     # This file
└── .gitignore
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Agent not found" | Verify agent ID format and version |
| "Authentication failed" | Check federated credentials match repo/branch |
| "Endpoint error" | Verify AZURE_AI_PROJECT_ENDPOINT format |
| Low scores | Add more context to test cases |

---

## Resources

- [Azure AI Agent Evaluation Action](https://github.com/marketplace/actions/azure-ai-agent-evaluation)
- [Azure AI Foundry Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/)
- [Evaluation Metrics Reference](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/evaluation-evaluators/)
