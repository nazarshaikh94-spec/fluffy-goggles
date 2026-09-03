# AI Risk Management Project

Purpose
- Establish an operational program to manage risks from AI/ML systems.

What's included
- templates/ : CSV/MD templates for inventory and risk register
- docs/ : policies and checklists
- tools/ : small scripts to help generate an inventory and run simple checks

How to get started
1. Populate templates/model_inventory.csv with known models.
2. Run tools/model_inventory.py to produce JSON summary.
3. Conduct assessments using templates/assessment_template.md and add findings to templates/risk_register.csv.
4. Implement monitoring for pilot models and iterate.

Contacts
- Program Sponsor: [name@example.com]
- Project Lead: [name@example.com]
- model_id,model_name,owner,version,training_dataset,dataset_version,purpose,production_endpoint,model_type,notes
# Example row:
# m-001,credit-risk-v1,jane.doe,1.0,credit_data_2025,v1.2,credit scoring,/api/models/credit-risk,sklearn-xgboost,used for loan approvals
risk_id,model_id,risk_category,description,likelihood(1-5),impact(1-5),score,priority,mitigation,owner,status,created_at,review_date
# Example row:
# r-001,m-001,Fairness,Model produces disparate false negative rate for group B,4,5,20,High,Re-train with balanced data; fairness constraints,jane.doe,Open,2026-09-03,2026-09-17
#!/usr/bin/env python3
# Minimal script: read model inventory CSV and print JSON summary
import csv, json, sys
from pathlib import Path

def load_inventory(path):
    out = []
    with open(path, newline='') as f:
        reader = csv.DictReader(f)
        for row in reader:
            if not row.get('model_id'): continue
            out.append(row)
    return out

if __name__ == '__main__':
    path = Path('templates/model_inventory.csv')
    if not path.exists():
        print(f'Inventory not found at {path}. Populate templates/model_inventory.csv first.', file=sys.stderr)
        sys.exit(1)
    inv = load_inventory(path)
    print(json.dumps(inv, indent=2))
