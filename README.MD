# JMeter Performance Tests

**Student:** Umair Ali  
**Registration Number:** FA22-BSE-137  
**Professor:** Yella Mehroze

## Overview

This project contains automated performance testing using Apache JMeter with GitHub Actions CI/CD integration.

## GitHub Actions Workflow

The workflow is configured to run JMeter performance tests on push and pull requests to the master branch.

### Workflow Configuration

```yaml
name: JMeter Performance Tests

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  jmeter-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Download Apache JMeter
        run: |
          wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.tgz
          tar -xzf apache-jmeter-5.6.3.tgz

      - name: Run JMeter Tests
        run: |
          mkdir -p results reports
          apache-jmeter-5.6.3/bin/jmeter.sh \
            -n \
            -t test_plan.jmx \
            -l results/results.jtl \
            -e \
            -o reports/

      - name: Upload HTML Report
        uses: actions/upload-artifact@v4
        with:
          name: jmeter-html-report
          path: reports/

      - name: Upload JTL Results
        uses: actions/upload-artifact@v4
        with:
          name: jmeter-jtl-results
          path: results/results.jtl
```

## Workflow Explanation

| Step | Description |
|------|-------------|
| **Trigger Events** | Workflow runs automatically on `push` and `pull_request` events to the `master` branch |
| **Environment** | Executes on `ubuntu-latest` runner with latest Linux environment |
| **Checkout** | Clones the repository code using `actions/checkout@v4` |
| **Java Setup** | Installs Java 17 with Temurin distribution using `actions/setup-java@v4` |
| **Download JMeter** | Downloads Apache JMeter 5.6.3 binary and extracts it with tar |
| **Run Tests** | Executes JMeter test plan (`test_plan.jmx`) in non-GUI mode (`-n` flag) and generates reports |
| **Generate Reports** | Creates HTML reports (`-e` flag) and outputs to `reports/` directory |
| **Upload HTML Report** | Stores the generated HTML report as an artifact named `jmeter-html-report` |
| **Upload JTL Results** | Stores the raw test results (`results.jtl`) as an artifact named `jmeter-jtl-results` |