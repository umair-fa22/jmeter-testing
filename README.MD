# JMeter Performance Testing Suite

> Comprehensive performance and load testing automation using Apache JMeter with GitHub Actions CI/CD integration

**Student:** Umair Ali  
**Registration Number:** FA22-BSE-137  
**Professor:** Yella Mehroze

---

## 📋 Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Running Tests Locally](#running-tests-locally)
- [CI/CD Pipeline](#cicd-pipeline)
- [Reports](#reports)
- [Test Data](#test-data)
- [Contributing](#contributing)

---

## 🎯 Overview

This project provides a comprehensive performance testing framework using **Apache JMeter 5.6.3**. It includes:

- **Automated Performance Tests**: JMeter test plans that simulate realistic user load and behavior
- **CI/CD Integration**: GitHub Actions workflow for continuous testing on every push and pull request
- **HTML Reports**: Interactive dashboard with performance metrics and visualizations
- **JTL Results**: Raw test results in JMeter Test Log format for detailed analysis
- **Test Data Management**: CSV-based test data for parameterized test scenarios

The project is designed to measure response times, throughput, error rates, and other key performance indicators during application load testing.

---

## 📁 Project Structure

```
jmeter-testing/
├── README.md                           # Project documentation
├── test_plan.jmx                       # JMeter test plan (main test configuration)
├── test_data.csv                       # CSV file containing test parameters and data
├── results/
│   └── results.jtl                     # Raw test results (JMeter Test Log format)
└── reports/                            # Generated HTML performance reports
    ├── index.html                      # Main dashboard report
    ├── statistics.json                 # Test statistics in JSON format
    └── content/
        ├── css/                        # Stylesheets for dashboard
        │   ├── dashboard.css
        │   ├── jquery-ui.*.min.css
        │   ├── legends.css
        │   └── theme.blue.css
        ├── js/                         # JavaScript files for interactivity
        │   ├── dashboard.js            # Main dashboard script
        │   ├── customGraph.js
        │   ├── graph.js
        │   └── *.min.js                # jQuery and utility libraries
        └── pages/                      # Report detail pages
            ├── CustomGraphs.html
            ├── OverTime.html
            ├── ResponseTimes.html
            └── Throughput.html
    └── sbadmin2-1.0.7/                 # SBAdmin2 theme files
        ├── less/                       # LESS stylesheets
        └── bower.json
```

---

## 🔧 Prerequisites

Before running the tests, ensure you have the following installed:

- **Java 17 or higher** - JMeter requires Java runtime
  ```bash
  java -version
  ```
  
- **Apache JMeter 5.6.3** - Download from [Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi)
  ```bash
  # Extract and add to PATH
  export PATH=$PATH:/path/to/apache-jmeter-5.6.3/bin
  ```

- **Git** - For cloning the repository

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd jmeter-testing
```

### 2. Verify Test Files

Ensure the following files exist:
- `test_plan.jmx` - Your JMeter test plan configuration
- `test_data.csv` - Test data file with parameters
- `reports/` directory - For storing generated reports

### 3. Prepare Test Data

Edit `test_data.csv` with your test parameters:

```csv
header1,header2,header3
value1,value2,value3
value4,value5,value6
```

---

## 🏃 Running Tests Locally

### Run JMeter Tests with Reporting

```bash
mkdir -p results reports

jmeter -n \
  -t test_plan.jmx \
  -l results/results.jtl \
  -e \
  -o reports/
```

**Flags Explanation:**
- `-n` : Non-GUI mode (headless execution)
- `-t test_plan.jmx` : Test plan file to run
- `-l results/results.jtl` : Output file for test results
- `-e` : Generate HTML report after test completion
- `-o reports/` : Output directory for HTML dashboard

### View Results

Open the generated report in your browser:
```bash
open reports/index.html
```

---

## 🔄 CI/CD Pipeline

### GitHub Actions Workflow

The project includes automated testing through GitHub Actions. The workflow runs on every push and pull request to the master branch.

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

### Pipeline Workflow Explanation

| Step | Description |
|------|-------------|
| **Trigger Events** | Workflow runs automatically on `push` and `pull_request` events to the `master` branch |
| **Environment** | Executes on `ubuntu-latest` runner with latest Linux environment |
| **Checkout** | Clones the repository code using `actions/checkout@v4` |
| **Java Setup** | Installs Java 17 with Temurin distribution using `actions/setup-java@v4` |
| **Download JMeter** | Downloads Apache JMeter 5.6.3 binary and extracts it with tar |
| **Run Tests** | Executes JMeter test plan (`test_plan.jmx`) in non-GUI mode and generates reports |
| **Generate Reports** | Creates HTML reports and outputs to `reports/` directory |
| **Upload HTML Report** | Stores the generated HTML report as an artifact for download |
| **Upload JTL Results** | Stores raw test results (`.jtl`) as an artifact for detailed analysis |

---

## 📊 Reports

### HTML Dashboard (`reports/index.html`)

The auto-generated dashboard includes:

- **Summary Statistics** - Overall test metrics (response time, throughput, errors)
- **Response Times** - Detailed breakdown of response time distribution
- **Throughput** - Requests per second over time
- **Custom Graphs** - User-defined performance visualizations
- **Over Time Charts** - Performance metrics tracked throughout the test execution

### Raw Results (`results/results.jtl`)

Contains JMeter Test Log data with detailed information about each request:
- Response times
- Status codes
- Error messages
- Thread information
- Timestamps

This data can be imported into other tools for advanced analysis.

---

## 📄 Test Data

### Format: CSV

The `test_data.csv` file contains parameterized test data used by JMeter thread groups.

**Example Structure:**
```csv
username,password,endpoint
testuser1,pass123,/api/endpoint1
testuser2,pass456,/api/endpoint2
testuser3,pass789,/api/endpoint3
```

**Usage in JMeter:**
- Use CSV Data Set Config in your test plan
- Reference columns by name: `${username}`, `${password}`, `${endpoint}`
- Data is cycled through thread groups for load simulation

---

## 💡 Best Practices

- **Test Plan Design**: Keep test plans modular and reusable
- **Data Management**: Update `test_data.csv` with realistic test scenarios
- **Report Analysis**: Review both HTML dashboard and raw JTL data for insights
- **Performance Baselines**: Track metrics over time to identify performance trends
- **Load Scenarios**: Gradually increase load to find system capacity limits

---

## 🤝 Contributing

When modifying test plans or adding new tests:

1. Test locally before pushing
2. Ensure test data is valid and comprehensive
3. Document any changes to test scenarios
4. Review generated reports for anomalies
5. Commit with clear messages describing the changes

---

## 📝 Notes

- Test results are stored in `results/` and `reports/` directories
- GitHub Actions artifacts expire after the default retention period (set by your GitHub account)
- For long-term storage, download and archive reports locally
- Monitor CI/CD pipeline logs for any test failures or warnings

---

*Last Updated: May 2026*