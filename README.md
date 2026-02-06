# TechConnect RFP Agent Evaluation

Automated evaluation pipeline for RFP analysis agents using the [Azure AI Agent Evaluation GitHub Action](https://github.com/marketplace/actions/azure-ai-agent-evaluation).

---

## 🎯 What This Does

Evaluates the quality and safety of our RFP analysis agents by:
1. Running **20 test queries** against each agent
2. Measuring **quality metrics** (groundedness, relevance, coherence, fluency)
3. Checking **safety compliance** (violence, hate, sexual, self-harm)
4. Generating **comparison reports** across agents

---

## 🤖 Agents Evaluated

| Agent | Purpose |
|-------|---------|
| `RfpOrchestratorAgent` | Plans, delegates, synthesizes final output |
| `RfpSummaryAgent` | Summarizes RFP documents into structured overviews |
| `RfpRiskAgent` | Identifies legal, financial, operational risks |
| `RfpComplianceAgent` | Checks regulatory and policy compliance |

---

## 📊 Evaluation Metrics

### Quality Metrics (Score: 1-5, Target: > 4.0)

| Metric | What It Measures |
|--------|------------------|
| **Groundedness** | Is the response supported by the provided context? |
| **Relevance** | Does the response address the user's query? |
| **Coherence** | Is the response logically structured? |
| **Fluency** | Is the language grammatically correct? |
| **Task Adherence** | Did the agent follow its instructions? |

### Safety Metrics (Score: 0-7, Target: < 1)

| Metric | What It Detects |
|--------|-----------------|
| **Violence** | Violent content |
| **Hate/Unfairness** | Discriminatory content |
| **Sexual** | Inappropriate sexual content |
| **Self-harm** | Self-harm related content |

---

## 🚀 Running Evaluations

### Manual Run
1. Go to **Actions** → **RFP Agent Evaluation**
2. Click **Run workflow**
3. Select agent type: `all`, `summary`, `risk`, or `compliance`
4. View results in the **run summary**

### Automatic Runs
Triggered on push to `main` when changes are made to:
- `data/**` (evaluation dataset)
- `.github/workflows/rfp-evaluation.yml`

---

## 📁 Repository Structure

```
techconnectrfp/
├── .github/workflows/
│   └── rfp-evaluation.yml      # GitHub Action workflow
├── data/
│   └── rfp-evaluation-dataset.json  # 20 test cases
├── README.md                   # This file
└── .gitignore
```

---

## 📝 Test Dataset

Located in `data/rfp-evaluation-dataset.json` with 20 test cases covering:

- Document summarization
- Payment terms analysis  
- Risk identification
- GDPR compliance
- SLA review
- Security certifications
- Liability caps
- Termination rights
- Data handling
- Dispute resolution
- And more...

### Adding Test Cases

```json
{
  "query": "Your RFP question",
  "ground_truth": "Expected correct answer",
  "context": "Relevant context from RFP documents"
}
```

---

## ⚙️ Configuration

### GitHub Variables Required

| Variable | Description |
|----------|-------------|
| `AZURE_CLIENT_ID` | App Registration Client ID |
| `AZURE_TENANT_ID` | Azure AD Tenant ID |
| `AZURE_SUBSCRIPTION_ID` | Azure Subscription ID |
| `AZURE_AI_PROJECT_ENDPOINT` | Foundry Project Endpoint |

### Agent Configuration

Edit `.github/workflows/rfp-evaluation.yml`:

```yaml
env:
  ORCHESTRATOR_AGENT_ID: "RfpOrchestratorAgent:1"
  SUMMARY_AGENT_ID: "RfpSummaryAgent:1"
  RISK_AGENT_ID: "RfpRiskAgent:1"
  COMPLIANCE_AGENT_ID: "RfpComplianceAgent:1"
```

---

## 📈 Interpreting Results

Results appear in the **GitHub Actions run summary**:

| Score | Interpretation |
|-------|----------------|
| **4.5 - 5.0** | Excellent - production ready |
| **4.0 - 4.4** | Good - minor improvements needed |
| **3.0 - 3.9** | Fair - review agent instructions |
| **< 3.0** | Poor - significant issues to address |

### Common Issues & Fixes

| Low Score | Likely Cause | Fix |
|-----------|--------------|-----|
| Groundedness | Agent hallucinating | Add more context to search index |
| Relevance | Off-topic responses | Refine agent instructions |
| Task Adherence | Ignoring instructions | Simplify/clarify agent prompts |

---

## 🔗 Related Resources

- [Azure AI Agent Evaluation Action](https://github.com/marketplace/actions/azure-ai-agent-evaluation)
- [Foundry Agent Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/agents/)
- [Evaluation Metrics Reference](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/evaluation-evaluators/)
