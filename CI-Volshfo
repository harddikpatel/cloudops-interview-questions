# 🧪 CI Role

---

## ✅ Questions & Answers

### . How do you reduce pipeline execution time when tests take too long? 
In one project, regression tests took over 2 hours. I split the suite by category (API, UI, DB), executed them in parallel on Jenkins agents, and cached dependencies in Artifactory. This reduced runtime to 40 minutes.

---

### . What’s your approach if developers complain about frequent false negatives in pipelines?  
I check logs to see if the issue is environmental or test-related. For environment issues, I fix the Docker image or agent config. 
For flaky tests, I isolate them into a nightly pipeline until fixed.

---
### Have you handled a case where pipeline failures blocked urgent hotfixes?
Yes. We had a gating pipeline failure during a production incident. I set up a dedicated “hotfix pipeline” that only ran smoke and lint checks, allowing critical patches while we debugged the regression suite.

---
## 🧩 Jenkins / Zuul / GitLab / Gerrit

### How do you configure Zuul to ensure only tested code gets merged? 
We used Zuul’s “gate” pipeline connected to Gerrit. Code merged only after all Zuul jobs passed in Jenkins, preventing untested code in main branches.

---

### What issues arise when Jenkins agents run out of capacity, and how do you scale them?
Builds got stuck in queues. I configured Jenkins with Kubernetes-based dynamic agents to autoscale by load, cutting queue time drastically.

---
### How do you handle Gerrit-triggered jobs that don’t start in Zuul?
I check Zuul scheduler logs for missed events. Once, RabbitMQ dropped events. Restarting the scheduler and reconnecting Gerrit fixed it. We later added monitoring on event queues.

---

## ⚙️ Ansible

### How do you roll back a failed Ansible deployment in production?
I use `--limit` and `--tags` to revert only affected nodes. For critical systems, we maintain rollback playbooks that reapply the last stable version.

### How do you manage secrets securely in Ansible playbooks?
We use Ansible Vault with HashiCorp Vault integration. Jenkins injects tokens at runtime. No secrets are committed in Git.

### Have you faced a case where an Ansible task failed mid-deployment?
Yes, during a repo outage. I used `ignore_errors: true` for non-critical tasks and added retry logic with exponential backoff for network calls.

---
## 🐍 Python / Pytest

### How do you troubleshoot intermittent Pytest failures in CI?
I use `pytest --lf` and `--reruns` to recheck last failures. Once, a test failed only in Jenkins due to timezone differences. Setting TZ fixed it.

### How do you separate smoke vs. regression tests?
I use markers like `@pytest.mark.smoke` and run them selectively. Smoke runs on each commit, regression nightly.

### Have you built custom fixtures for CI/CD?
Yes. A fixture that spins up a PostgreSQL container before each test ensures clean data for every run.

---
## 🧾 Bash Scripting

### Share a case where you fixed a production issue with Bash.
Jenkins agents filled up with logs. I wrote a Bash script to delete old logs and unused Docker layers. Deployed with Ansible as a cronjob.

### How do you handle Bash portability across distributions?
Use `#!/usr/bin/env bash` and avoid distro-specific binaries. Add conditional checks using `/etc/os-release` when needed.

### How do you make scripts idempotent?
By always checking state before execution. For example, if ! grep -q "line" file; then echo "line" >> file; fi. This avoids duplicate entries.

---

## 📊 Monitoring (Victoria Metrics / Grafana / Zuul Web)

### What metrics do you monitor in production CI/CD?
Job success rate, queue wait time, test duration, agent utilization.

### How do you set up alerts for failing jobs or long queues?
Configured PromQL alerts in Victoria Metrics. If job failure >20% or queue time >10 mins, Grafana sends Slack/PagerDuty alerts.

### Have you implemented auto-scaling from monitoring data?
Yes. Jenkins autoscaled Kubernetes agents when queue length exceeded 10 jobs for 5 minutes.

---

🧠 Git / YAML


### How do you validate YAML configs before pushing?
Added yamllint pre-commit hook and used zuul-client validate. It prevented config-related CI failures.

### Faced a YAML indentation issue in production?
Yes. A single space misalignment disabled Zuul gating. Since then, all YAML validated through CI jobs.

### How do you manage Git branching with frequent hotfixes?
We use Gitflow. Hotfix branches come from main and have lightweight validation. Feature branches go through full checks.

---

🐳 Docker / Artifactory


### How do you manage Docker image bloat?
Multi-stage builds, slim base images, and pruning old layers in Artifactory.

### How do you secure Artifactory access?
Jenkins uses short-lived tokens from a credential manager. No static passwords in code.

### How do you handle corrupted or missing images?
Enabled checksum verification on upload. Also replicate critical images to a secondary registry.

---

🧩 Fault Tracing & Resolution

### Describe a major outage caused by CI/CD.
Zuul jobs halted due to RabbitMQ crash. I restarted the cluster, purged queues, and restored Gerrit connections. Later added HA setup and alerts.

### Pipelines failing only in production but not staging — what was the cause?
Java version mismatch across agents. Fixed by enforcing identical Docker base images.

### How do you balance root cause vs. workaround?
Apply temporary fix if critical (disable flaky test), but always open a post-mortem ticket with SLA.

---

🔍 Merge Request Reviews

### How do you enforce quality before merges?
Automated checks (lint, tests, SonarQube) must pass before Gerrit merge approval.

### Conflicts between automation and reviewer feedback?
Automated checks are non-negotiable for quality gates; reviewers can override stylistic feedback.

### PHow do you speed up review pipelines?
Split fast checks (lint/unit) and heavy checks (security scans). Fast feedback, full coverage.

---
🧪 Test Run Monitoring

### How do you detect and handle hanging tests?
Set Pytest --timeout and Jenkins job timeout. Logs saved automatically on abort.

### How do you track flakiness trends?
Aggregated test data in Grafana. Identified DB timeout as the main cause of 30% of flaky runs, fixed connection pooling.

### What if a gating test blocks releases repeatedly?
Mark test as non-voting until fixed. Critical product failures block release; test issues don’t.
