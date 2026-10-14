# IPsec Guardian

An Evidence-Grounded Multi-Agent Cybersecurity Reasoning System for IPsec Security Assessment.

## Overview

IPsec Guardian integrates machine learning flow classification, hybrid RAG retrieval, CrossEncoder reranking, and local Qwen3-14B LLM reasoning across 10 specialized security agents with strict claim grounding to provide robust, evidence-backed security analysis of IPsec configurations and network traffic.

## Architecture

1. **Network Traffic Classification**: Random Forest model predicting flow classes over 8 network traffic features (`source_port`, `destination_port`, `protocol`, `duration`, `packet_count`, `bytes_sent`, `bytes_received`, `bytes_per_packet`).
2. **Hybrid RAG Retrieval V2**: Configuration-aware multi-query FAISS vector search (384-dimensional embeddings via `sentence-transformers/all-MiniLM-L6-v2`) combined with exact phrase lexical matching and configuration parameter scoring.
3. **CrossEncoder Reranking**: Candidate reranking using `cross-encoder/ms-marco-MiniLM-L-6-v2` with section diversity filtering.
4. **10 Modular Security Agents**:
   - `AgentOrchestrator`: Multi-agent pipeline controller and lifecycle coordinator.
   - `IPsecConfigurationAgent`: Rule-based evaluation against CIS & NIST security parameters.
   - `ThreatDetectionAgent`: Random Forest NetFlow anomaly and attack classification.
   - `RAGEvidenceAgent`: FAISS semantic search and CrossEncoder reranking.
   - `VulnerabilityAnalysisAgent`: Weakness identification and severity ranking.
   - `ComplianceAgent`: Benchmark evaluation against NIST SP 800-77, CNSA 1.0, and DoD STIG.
   - `AttackSimulationAgent`: Safe what-if configuration perturbation testing (e.g., PFS disablement).
   - `QwenReasoningAgent`: Evidence-grounded synthesis using Qwen3-14B.
   - `RemediationAgent`: Actionable prioritized mitigation blueprints.
   - `SecurityReportAgent`: Executive summary and technical security audit generation.
5. **Strict Claim Grounding Gate**: Regex-based claim verification preventing ungrounded exact technical parameters, CVEs, or unsupported group numbers from reaching the user.

## Project Structure

```
IPSEC_GUARDIAN/
├── backend/
│   ├── agents/                   # 10 Specialized security agents & orchestrator
│   ├── ai/
│   │   └── Qwen_service.py       # Wrapper service for Qwen3-14B inference
│   ├── ml/
│   │   └── network_traffic_model.joblib # Trained Random Forest model (1.4 MB)
│   ├── Rag/
│   │   ├── ipsec.index           # FAISS index (625 KB, 418 vectors)
│   │   └── metadata.json         # RAG chunk metadata (163 KB)
│   ├── rules/
│   │   ├── compliance_rules.json # NIST, CNSA, STIG compliance rules
│   │   └── ipsec_rules.json      # Cipher suite & configuration policy rules
│   ├── api.py                    # FastAPI web application (/assess, /simulate, /health)
│   └── config.py                 # Centralized system & model configuration
├── frontend/                     # Modern React + Vite Web UI
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── App.jsx
│       ├── components/           # Assessment, ThreatIntel, Simulation, Compliance cards
│       ├── pages/                # CommandCenter, Simulation, Reports, AIAnalyst consoles
│       └── services/api.js       # Backend API client service
├── docs/
│   ├── MERGE_REPORT.md           # Merge & architecture completion report
│   └── rag_knowledge_base/       # 26 markdown knowledge files + statistics
├── scripts/
│   ├── test_multi_agent.py       # 10-Agent pipeline integration test
│   └── test_run.py               # Standalone programmatic assessment verification
├── ipsec_security_assessment.py  # Main pipeline module (Programmatic & CLI mode)
├── requirements.txt              # Unified Python dependencies
└── README.md                     # Project documentation
```

## Running the Application

### 1. Terminal / CLI Mode
```bash
python ipsec_security_assessment.py
```

### 2. Multi-Agent Integration Test
```bash
python scripts/test_multi_agent.py
```

### 3. Backend API
```bash
pip install -r requirements.txt
uvicorn backend.api:app --reload --port 8000
```
Interactive API Documentation will be available at `http://localhost:8000/docs`.

### 4. Frontend Web Application
```bash
cd frontend
npm install
npm run dev
```
Access the application at `http://localhost:3000`.

## Configuration Environment Variables

- `QWEN_MODEL_PATH`: Path to Qwen3-14B model directory (default: `/home/sece2026-student19/models/Qwen3-14B`).
