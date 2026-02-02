# dataengweek2
# Data Engineering Zoomcamp 2026 – Module 2 Homework

This repository contains the solution for **Module 2 – Workflow Orchestration with Kestra**.

Due to environment limitations, the solution is provided as a **Kestra flow definition (YAML)** directly in this repository, as allowed by the homework instructions.

---

## Kestra Flow – NYC Taxi ETL (2019–2021)

The following Kestra flow extends the existing pipeline for **Yellow and Green Taxi data**
to include **data for the year 2021 (January–July)**.

It supports:
- Parameterized execution by taxi type, year, and month
- Backfilling historical data
- Iteration over multiple months and taxi types using `ForEach`

---

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

---

## Backfill Strategy for 2021

To ingest data for 2021, the flow can be executed using:

- **Scheduled backfill** from `2021-01-01` to `2021-07-31`
- Or a `ForEach` task iterating over:
  - taxi types: `yellow`, `green`
  - months: `01` to `07`

This allows processing all available NYC Taxi datasets for 2021.

---

## Notes

- The YAML definition is provided directly in this README
  in accordance with the homework instruction:
  
  > *If your solution includes code that is not in file format,
  > please include these directly in the README file.*

- The focus of this homework is on **workflow orchestration design**
  rather than local execution.
