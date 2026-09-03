{
  "system": "Customer Support Chatbot v2",
  "summary": {
    "system": "Customer Support Chatbot v2",
    "total_risks": 5,
    "by_severity": {
      "Critical": 1,
      "High": 2,
      "Medium": 2,
      "Low": 0
    },
    "unmitigated": 5
  },
  "risks": [
    {
      "id": "RISK-001",
      "title": "Biased responses across demographic groups",
      "description": "Model may give systematically different quality of support based on user name or dialect cues.",
      "category": "Bias & Fairness",
      "likelihood": "POSSIBLE",
      "impact": "MAJOR",
      "risk_score": 12,
      "severity": "High",
      "status": "Mitigating",
      "owner": "ML Fairness Team",
      "date_identified": "2026-09-03",
      "mitigations": [
        {
          "description": "Run fairness audit across demographic slices",
          "owner": "ML Fairness Team",
          "due_date": "2026-10-01",
          "completed": false
        },
        {
          "description": "Add bias-detection eval to CI pipeline",
          "owner": "MLOps",
          "due_date": "2026-09-20",
          "completed": false
        }
      ]
    },
    {
      "id": "RISK-002",
      "title": "Prompt injection via user-uploaded documents",
      "description": "Malicious instructions embedded in uploaded files could hijack the assistant's behavior.",
      "category": "Security & Adversarial Robustness",
      "likelihood": "LIKELY",
      "impact": "SEVERE",
      "risk_score": 20,
      "severity": "Critical",
      "status": "Mitigating",
      "owner": "Security Team",
      "date_identified": "2026-09-03",
      "mitigations": [
        {
          "description": "Sandboxed content parsing + instruction filtering",
          "owner": "Security Team",
          "due_date": "2026-09-15",
          "completed": false
        }
      ]
    },
    {
      "id": "RISK-003",
      "title": "PII leakage in generated responses",
      "description": "Model may inadvertently reproduce sensitive user data from earlier in a session or from training data.",
      "category": "Privacy & Data Protection",
      "likelihood": "UNLIKELY",
      "impact": "MAJOR",
      "risk_score": 8,
      "severity": "Medium",
      "status": "Assessed",
      "owner": "Privacy Team",
      "date_identified": "2026-09-03",
      "mitigations": []
    },
    {
      "id": "RISK-004",
      "title": "Hallucinated policy information",
      "description": "Chatbot may state incorrect return/refund policies, creating legal or customer trust exposure.",
      "category": "Reliability & Robustness",
      "likelihood": "LIKELY",
      "impact": "MODERATE",
      "risk_score": 12,
      "severity": "High",
      "status": "Mitigating",
      "owner": "Product Team",
      "date_identified": "2026-09-03",
      "mitigations": [
        {
          "description": "Ground responses via RAG against verified policy docs",
          "owner": "Product Team",
          "due_date": "2026-09-25",
          "completed": false
        }
      ]
    },
    {
      "id": "RISK-005",
      "title": "Lack of human escalation path",
      "description": "No reliable mechanism to hand off distressed or high-stakes users to a human agent.",
      "category": "Human Oversight & Accountability",
      "likelihood": "POSSIBLE",
      "impact": "MODERATE",
      "risk_score": 9,
      "severity": "Medium",
      "status": "Assessed",
      "owner": "Product Team",
      "date_identified": "2026-09-03",
      "mitigations": []
    }
  ]
}
