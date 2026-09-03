"""
AI Risk Management Framework
=============================
A lightweight, extensible framework for identifying, scoring, tracking,
and reporting risks associated with AI systems (aligned loosely with
NIST AI RMF categories: Govern, Map, Measure, Manage).

Usage: see the __main__ block at the bottom for a worked example.
"""

from dataclasses import dataclass, field
from datetime import date
from enum import Enum
from typing import List, Optional
import json


# ---------------------------------------------------------------------------
# Enums for structured, consistent inputs
# ---------------------------------------------------------------------------

class RiskCategory(Enum):
    BIAS_FAIRNESS = "Bias & Fairness"
    PRIVACY = "Privacy & Data Protection"
    SECURITY = "Security & Adversarial Robustness"
    SAFETY = "Safety & Physical Harm"
    TRANSPARENCY = "Transparency & Explainability"
    RELIABILITY = "Reliability & Robustness"
    COMPLIANCE = "Legal & Regulatory Compliance"
    MISUSE = "Misuse & Malicious Use"
    ENVIRONMENTAL = "Environmental & Resource Impact"
    HUMAN_OVERSIGHT = "Human Oversight & Accountability"


class Likelihood(Enum):
    RARE = 1
    UNLIKELY = 2
    POSSIBLE = 3
    LIKELY = 4
    ALMOST_CERTAIN = 5


class Impact(Enum):
    NEGLIGIBLE = 1
    MINOR = 2
    MODERATE = 3
    MAJOR = 4
    SEVERE = 5


class RiskStatus(Enum):
    IDENTIFIED = "Identified"
    ASSESSED = "Assessed"
    MITIGATING = "Mitigating"
    MITIGATED = "Mitigated"
    ACCEPTED = "Accepted"
    CLOSED = "Closed"


# ---------------------------------------------------------------------------
# Core data model
# ---------------------------------------------------------------------------

@dataclass
class Mitigation:
    description: str
    owner: str
    due_date: Optional[date] = None
    completed: bool = False


@dataclass
class Risk:
    id: str
    title: str
    description: str
    category: RiskCategory
    likelihood: Likelihood
    impact: Impact
    status: RiskStatus = RiskStatus.IDENTIFIED
    owner: str = "Unassigned"
    mitigations: List[Mitigation] = field(default_factory=list)
    date_identified: date = field(default_factory=date.today)

    @property
    def risk_score(self) -> int:
        """Score from 1 (low) to 25 (critical): likelihood x impact."""
        return self.likelihood.value * self.impact.value

    @property
    def severity(self) -> str:
        score = self.risk_score
        if score >= 20:
            return "Critical"
        elif score >= 12:
            return "High"
        elif score >= 6:
            return "Medium"
        else:
            return "Low"

    def add_mitigation(self, description: str, owner: str, due_date: Optional[date] = None):
        self.mitigations.append(Mitigation(description, owner, due_date))
        if self.status == RiskStatus.ASSESSED:
            self.status = RiskStatus.MITIGATING

    def to_dict(self) -> dict:
        return {
            "id": self.id,
            "title": self.title,
            "description": self.description,
            "category": self.category.value,
            "likelihood": self.likelihood.name,
            "impact": self.impact.name,
            "risk_score": self.risk_score,
            "severity": self.severity,
            "status": self.status.value,
            "owner": self.owner,
            "date_identified": self.date_identified.isoformat(),
            "mitigations": [
                {
                    "description": m.description,
                    "owner": m.owner,
                    "due_date": m.due_date.isoformat() if m.due_date else None,
                    "completed": m.completed,
                }
                for m in self.mitigations
            ],
        }


# ---------------------------------------------------------------------------
# Risk register: the main management interface
# ---------------------------------------------------------------------------

class AIRiskRegister:
    def __init__(self, system_name: str):
        self.system_name = system_name
        self.risks: List[Risk] = []
        self._counter = 0

    def add_risk(
        self,
        title: str,
        description: str,
        category: RiskCategory,
        likelihood: Likelihood,
        impact: Impact,
        owner: str = "Unassigned",
    ) -> Risk:
        self._counter += 1
        risk = Risk(
            id=f"RISK-{self._counter:03d}",
            title=title,
            description=description,
            category=category,
            likelihood=likelihood,
            impact=impact,
            status=RiskStatus.ASSESSED,
            owner=owner,
        )
        self.risks.append(risk)
        return risk

    def get_risk(self, risk_id: str) -> Optional[Risk]:
        return next((r for r in self.risks if r.id == risk_id), None)

    def top_risks(self, n: int = 5) -> List[Risk]:
        return sorted(self.risks, key=lambda r: r.risk_score, reverse=True)[:n]

    def risks_by_category(self, category: RiskCategory) -> List[Risk]:
        return [r for r in self.risks if r.category == category]

    def risks_by_severity(self, severity: str) -> List[Risk]:
        return [r for r in self.risks if r.severity == severity]

    def summary(self) -> dict:
        counts = {"Critical": 0, "High": 0, "Medium": 0, "Low": 0}
        for r in self.risks:
            counts[r.severity] += 1
        return {
            "system": self.system_name,
            "total_risks": len(self.risks),
            "by_severity": counts,
            "unmitigated": sum(
                1 for r in self.risks
                if r.status not in (RiskStatus.MITIGATED, RiskStatus.CLOSED)
            ),
        }

    def print_report(self):
        print(f"\n{'=' * 70}")
        print(f"AI RISK MANAGEMENT REPORT — {self.system_name}")
        print(f"{'=' * 70}")

        s = self.summary()
        print(f"\nTotal risks tracked: {s['total_risks']}")
        print(f"Unmitigated / open risks: {s['unmitigated']}")
        print("\nBreakdown by severity:")
        for level in ["Critical", "High", "Medium", "Low"]:
            print(f"  {level:10s}: {s['by_severity'][level]}")

        print(f"\n{'-' * 70}")
        print("TOP RISKS (highest score first)")
        print(f"{'-' * 70}")
        for r in self.top_risks(10):
            print(f"\n[{r.id}] {r.title}  (score {r.risk_score}/25 — {r.severity})")
            print(f"    Category : {r.category.value}")
            print(f"    Status   : {r.status.value}   Owner: {r.owner}")
            print(f"    Details  : {r.description}")
            if r.mitigations:
                print("    Mitigations:")
                for m in r.mitigations:
                    check = "✓" if m.completed else "○"
                    due = f" (due {m.due_date})" if m.due_date else ""
                    print(f"      {check} {m.description} — {m.owner}{due}")
            else:
                print("    Mitigations: NONE — needs a mitigation plan")
        print(f"\n{'=' * 70}\n")

    def export_json(self, filepath: str):
        data = {
            "system": self.system_name,
            "summary": self.summary(),
            "risks": [r.to_dict() for r in self.risks],
        }
        with open(filepath, "w") as f:
            json.dump(data, f, indent=2)
        print(f"Exported risk register to {filepath}")


# ---------------------------------------------------------------------------
# Example usage
# ---------------------------------------------------------------------------

if __name__ == "__main__":
    register = AIRiskRegister(system_name="Customer Support Chatbot v2")

    r1 = register.add_risk(
        title="Biased responses across demographic groups",
        description="Model may give systematically different quality of "
                     "support based on user name or dialect cues.",
        category=RiskCategory.BIAS_FAIRNESS,
        likelihood=Likelihood.POSSIBLE,
        impact=Impact.MAJOR,
        owner="ML Fairness Team",
    )
    r1.add_mitigation("Run fairness audit across demographic slices", "ML Fairness Team", date(2026, 10, 1))
    r1.add_mitigation("Add bias-detection eval to CI pipeline", "MLOps", date(2026, 9, 20))

    r2 = register.add_risk(
        title="Prompt injection via user-uploaded documents",
        description="Malicious instructions embedded in uploaded files could "
                     "hijack the assistant's behavior.",
        category=RiskCategory.SECURITY,
        likelihood=Likelihood.LIKELY,
        impact=Impact.SEVERE,
        owner="Security Team",
    )
    r2.add_mitigation("Sandboxed content parsing + instruction filtering", "Security Team", date(2026, 9, 15))

    r3 = register.add_risk(
        title="PII leakage in generated responses",
        description="Model may inadvertently reproduce sensitive user data "
                     "from earlier in a session or from training data.",
        category=RiskCategory.PRIVACY,
        likelihood=Likelihood.UNLIKELY,
        impact=Impact.MAJOR,
        owner="Privacy Team",
    )

    r4 = register.add_risk(
        title="Hallucinated policy information",
        description="Chatbot may state incorrect return/refund policies, "
                     "creating legal or customer trust exposure.",
        category=RiskCategory.RELIABILITY,
        likelihood=Likelihood.LIKELY,
        impact=Impact.MODERATE,
        owner="Product Team",
    )
    r4.add_mitigation("Ground responses via RAG against verified policy docs", "Product Team", date(2026, 9, 25))

    r5 = register.add_risk(
        title="Lack of human escalation path",
        description="No reliable mechanism to hand off distressed or "
                     "high-stakes users to a human agent.",
        category=RiskCategory.HUMAN_OVERSIGHT,
        likelihood=Likelihood.POSSIBLE,
        impact=Impact.MODERATE,
        owner="Product Team",
    )

    register.print_report()
    register.export_json("/mnt/user-data/outputs/risk_register.json")
