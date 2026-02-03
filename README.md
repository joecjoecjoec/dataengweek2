# Module 2 – Workflow Orchestration with Kestra

This repository contains the solution for **Module 2 – Workflow Orchestration with Kestra**
from the **Data Engineering Zoomcamp**.

The solution is implemented using **Kestra running in a Docker environment**, following
the setup and execution model introduced in the course.

As allowed by the homework instructions, the workflow definition is provided directly
in this repository as a **Kestra flow YAML**, since the main focus of this assignment
is on **workflow orchestration design** rather than application code.

---

## Kestra Flow – NYC Taxi ETL (2019–2021)

The Kestra flow below extends the existing NYC Taxi ETL pipelines for **Yellow** and
**Green** taxi data to include **data for the year 2021 (January–July)**.

The flow is designed to run in a **containerized Kestra setup (Docker)** and supports:

- Parameterized execution by **taxi type**, **year**, and **month**
- Backfilling historical data using Kestra scheduling
- Iteration over multiple months and taxi types using `ForEach`

---

## Kestra Flow Definition

```yaml
id: taxi_etl
namespace: zoomcamp.module2

description: >
  Kestra flow to ingest NYC Taxi data (green and yellow)
  and extend existing pipelines to include data for year 2021.

inputs:
  - id: taxi
    type: STRING
    required: true
    description: Taxi type (yellow or green)

  - id: year
    type: INT
    required: true
    description: Year of the data

  - id: month
    type: STRING
    required: true
    description: Month of the data (MM format)

variables:
  file: "{{ inputs.taxi }}_tripdata_{{ inputs.year }}-{{ inputs.month }}.csv"
  url: "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/{{ inputs.taxi }}/{{ vars.file }}.gz"

tasks:
  - id: extract
    type: io.kestra.plugin.fs.http.Download
    uri: "{{ vars.url }}"
    decompress: true

  - id: log_file
    type: io.kestra.plugin.core.log.Log
    message: "Downloaded {{ vars.file }}"

```
## Backfill Strategy for 2021

To ingest data for 2021, the flow can be executed in the following ways:
	•	Scheduled backfill using Kestra’s backfill functionality
(from 2021-01-01 to 2021-07-31)
	•	Or by using a ForEach task to iterate over:
	•	Taxi types: yellow, green
	•	Months: 01 to 07

This allows processing all available NYC Taxi datasets for 2021 in a reproducible
and automated manner.

⸻

## Notes
	•	The workflow is designed to run on **Kestra deployed via Docker**
	•	The YAML definition is included directly in this README in accordance with the
homework instruction
	•	The emphasis of this homework is on workflow orchestration concepts, including
parameterization, scheduling, and backfilling, rather than on a specific local
execution environment

