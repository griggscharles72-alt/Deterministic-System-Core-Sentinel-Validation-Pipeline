Deterministic System — Sentinel Pipeline

A deterministic state pipeline for collecting raw inputs, normalizing them into canonical structures, validating them through Sentinel rules, and committing trusted state to persistent memory.

The system is designed to eliminate ambiguity in data processing by enforcing a strict pipeline:

Raw Intake → Normalization → Waffle Layer → Sentinel Validation → Persistent Memory → Deterministic Outputs

The architecture guarantees that identical inputs and configuration produce identical system state.

⸻

Project Purpose

The Deterministic System provides a framework for building observability, diagnostics, and system-state pipelines where:
	•	Raw tool outputs and environment signals are collected safely
	•	Data is normalized into canonical structures
	•	Intermediate transformations remain deterministic
	•	Sentinel rules validate state transitions
	•	Only verified records become persistent state
	•	Reports and outputs derive solely from committed state

The goal is to create a reproducible, auditable system where no hidden heuristics or uncontrolled state mutations occur.

⸻

Deterministic Guarantee

Given identical inputs and configuration:

F(R, C) → S

Where:
	•	R = raw inputs
	•	C = configuration and rules
	•	S = resulting system state

The system guarantees:
	•	identical outputs
	•	identical object identities
	•	identical database state
	•	identical reports

No randomness exists anywhere in the pipeline.

⸻

System Pipeline

The system processes all information through a strict deterministic pipeline:

R → N → W → S → M

Where:

R = Raw data collected from tools, sensors, or system inputs
N = Normalized canonical records
W = Waffle-layer shaped data structures
S = Sentinel-approved records
M = Persistent memory state

Operator outputs are derived from persistent memory:

O = h(M)

Meaning reports are always computed from committed state, never raw inputs.

⸻

Layer Responsibilities

Raw Intake

Untrusted inputs collected from tools or system signals.

Examples:
	•	system command output
	•	diagnostic data
	•	telemetry streams
	•	file artifacts
	•	network observations
	•	logs

Raw data is never written directly to persistent state.

⸻

Normalization Layer

Transforms raw inputs into canonical structures.

Responsibilities:
	•	schema validation
	•	timestamp normalization
	•	canonical field names
	•	canonical identity extraction
	•	deterministic ordering
	•	provenance tagging

Normalization ensures that equivalent data always produces identical normalized records.

⸻

Waffle Layer

The Waffle layer prepares normalized records for Sentinel evaluation.

Responsibilities:
	•	merge related observations
	•	attach derived metrics
	•	enrich records with deterministic calculations
	•	prepare staging records
	•	resolve aliases
	•	compute intermediate scores

This layer does not commit state.

It produces candidate records for validation.

⸻

Sentinel Layer

Sentinel acts as the system gatekeeper.

Responsibilities:
	•	rule evaluation
	•	schema verification
	•	state transition validation
	•	identity consistency checks
	•	policy enforcement
	•	decision classification

Sentinel produces one of three outcomes:

ACCEPT
REJECT
PENDING

Only ACCEPTED records may enter persistent memory.

⸻

Memory Layer

Persistent system state.

Implemented using SQLite and append-only logs.

Responsibilities:
	•	storing verified objects
	•	storing observation history
	•	recording artifacts
	•	maintaining run history
	•	storing tags and metadata
	•	audit logging

All committed records must pass Sentinel.

⸻

Output Layer

Operator views derived strictly from persistent state.

Examples:
	•	summaries
	•	historical comparisons
	•	artifact reports
	•	system health reports
	•	delta analysis

Outputs are deterministic functions of stored state.

⸻

Canonical Object Identity

Every object in the system must have a stable deterministic identifier.

Identity is derived from canonical fields:

ID(o) = H(type || canonical_fields)

Example implementation:

SHA256(object_type + canonical_key)

Examples of canonical keys:

Access point: BSSID
Device: MAC address
Artifact: file hash
Run: timestamp + host id

This prevents identity drift across runs.

⸻

State Transition Model

Records move through strict states.

RAW → NORMALIZED → STAGED → APPROVED

Alternative paths:

STAGED → REJECTED
REJECTED → QUARANTINED

Illegal transitions are rejected.

For example:

APPROVED → STAGED is not allowed.

State immutability guarantees auditability.

⸻

Run Model

Each execution of the system is a Run.

Every run produces metadata:
	•	run_id
	•	host_id
	•	start_time
	•	end_time
	•	profile
	•	tool_versions
	•	schema_version
	•	system_fingerprint

All records must reference the run that produced them.

⸻

Artifact Integrity

All files are content-addressed.

File identity:

hash(file) = SHA256(file_bytes)

Artifacts store:
	•	file path
	•	file hash
	•	file size
	•	creation time
	•	run id
	•	tool source

Artifacts cannot be modified after recording.

⸻

Repository Structure

deterministic_system/

bin/
    dsys
    dsys-reset

config/
    system.yaml
    profiles/
    sentinel/
    symbols/

data/
    db/
    state/
    artifacts/
    logs/

docs/
    architecture.md
    sentinel_model.md
    waffle_layer.md
    runbooks/

src/
    deterministic_system/

        core/
        intake/
        normalize/
        waffle/
        sentinel/
        memory/
        symbols/
        reports/
        console/
        utils/

tests/

scripts/


⸻

Core Modules

intake

Responsible for collecting raw system data.

normalize

Canonicalizes all records and attaches provenance.

waffle

Intermediate shaping and enrichment layer.

sentinel

Validation engine enforcing deterministic rules.

memory

SQLite persistence, snapshots, audit logs.

symbols

Registry of allowed system tags, states, and object types.

reports

Generates summaries and analysis.

console

CLI interface for system operators.

⸻

SQLite Data Model

Core tables include:

runs
objects
observations
staged_records
sentinel_decisions
artifacts
tags
snapshots
tool_runs
audit_events

The runs table anchors all historical analysis.

⸻

CLI Commands

Example operator commands:

dsys doctor
dsys ingest
dsys verify
dsys report
dsys history
dsys reset

Command responsibilities:

doctor – validate environment
ingest – collect raw inputs
verify – run sentinel validation
report – generate summaries
history – inspect past runs
reset – clear working state

⸻

Deterministic Principles

The system enforces several core principles.

No randomness
No implicit state mutation
No raw data commits
Canonical identities only
Immutable transitions
Reproducible outputs

All system behavior must be explainable through rules and inputs.

⸻

Anti-Goals

The system intentionally avoids:
	•	heuristic AI decision making
	•	probabilistic inference
	•	uncontrolled background state mutation
	•	undocumented state transitions
	•	direct writes from raw input to database

The design prioritizes deterministic traceability over speculative automation.
