# Final Project: Tax Calculator Application - Submission and Evaluation (10/10 Points)

This folder contains all required configuration files, code snippets, command outputs, and screenshots ready for submitting your final graded project.

---

## Folder Structure
```text
tax-calculator-devops/
├── Dockerfile
├── tc-pipeline/
│   ├── tasks.yaml
│   ├── pipeline.yaml
│   └── run.yaml
├── screenshots/
│   ├── 01-jasmine-tests-passing.png
│   ├── 02-dockerfile.png
│   ├── 03-docker-build-output.png
│   ├── 04-docker-image-browser.png
│   ├── 05-docker-icr-push.png
│   ├── 06-deployed-on-cloud.png
│   ├── 07-tasks-yaml.png
│   ├── 08-pipeline-changes.png
│   ├── 09-run-yaml.png
│   └── 10-final-output.png
└── README.md
```

---

## Graded Criteria & Answers Guide

### Task 1: Run unit tests using Jasmine (1 point)
- **Action / Command**:
  ```bash
  npx jasmine
  ```
- **Terminal Output**:
  ```text
  Randomized with seed 42429
  Started
  .......

  7 specs, 0 failures
  Finished in 0.007 seconds
  ```
- **Screenshot to Upload**: `screenshots/01-jasmine-tests-passing.png`

---

### Task 2: Create the Dockerfile to deploy in a local Docker container (1 point)
- **File**: `Dockerfile`
- **Code Snippet**:
  ```dockerfile
  FROM nginx
  COPY favicon.ico /usr/share/nginx/html/favicon.ico
  COPY index.html /usr/share/nginx/html/index.html
  COPY script.js /usr/share/nginx/html/script.js
  COPY style.css /usr/share/nginx/html/style.css
  COPY taxCalculator.js /usr/share/nginx/html/taxCalculator.js
  ```
- **Screenshot to Upload**: `screenshots/02-dockerfile.png`

---

### Task 3: Build the Docker image (1 point)
- **Commands**:
  ```bash
  docker build -t tax-calculator .
  docker images
  ```
- **Terminal Output**:
  ```text
  REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
  tax-calculator   latest    3a042b181a6a   12 seconds ago   187MB
  nginx            latest    900dca2a61f5   10 days ago      187MB
  ```
- **Screenshot to Upload**: `screenshots/03-docker-build-output.png`

---

### Task 4: Deploy and test the web application deployed in a Docker container (1 point)
- **Command**:
  ```bash
  docker run -it -d -p 8080:80 tax-calculator
  ```
- **Verification**: Application is accessible in the browser preview via port 8080.
- **Screenshot to Upload**: `screenshots/04-docker-image-browser.png`

---

### Task 5: Tag and push to IBM Cloud Registry (1 point)
- **Commands**:
  ```bash
  docker tag tax-calculator us.icr.io/${SN_ICR_NAMESPACE}/tax-calculator
  docker push us.icr.io/${SN_ICR_NAMESPACE}/tax-calculator
  ```
- **Terminal Output**:
  ```text
  The push refers to repository [us.icr.io/sn-labs-.../tax-calculator]
  latest: digest: sha256:4f3a... size: 1778
  ```
- **Screenshot to Upload**: `screenshots/05-docker-icr-push.png`

---

### Task 6: Deploy the Tax Calculator on IBM Cloud (1 point)
- **Commands**:
  ```bash
  ibmcloud ce application create --name tax-calculator --image us.icr.io/${SN_ICR_NAMESPACE}/tax-calculator
  ibmcloud ce application get --name tax-calculator
  ```
- **Output / Status**:
  ```text
  Status: True
  Status Summary: Application deployed successfully
  URL: https://tax-calculator....us-south.codeengine.appdomain.cloud
  ```
- **Screenshot to Upload**: `screenshots/06-deployed-on-cloud.png`

---

### Task 7: Create the Tekton Pipeline tasks (1 point)
- **File**: `tc-pipeline/tasks.yaml`
- **Code Snippet**:
  ```yaml
  apiVersion: tekton.dev/v1beta1
  kind: Task
  metadata:
    name: npm
  spec:
    workspaces:
      - name: source
    steps:
      - name: npm-install
        image: node:20-buster-slim
        workingDir: $(workspaces.source.path)
        command: ["npm"]
        args: ["install"]
  ---
  apiVersion: tekton.dev/v1beta1
  kind: Task
  metadata:
    name: jasmine
  spec:
    workspaces:
      - name: source
    steps:
      - name: run-tests
        image: node:20-buster-slim
        workingDir: $(workspaces.source.path)
        command: ["npx"]
        args: ["jasmine"]
  ```
- **Screenshot to Upload**: `screenshots/07-tasks-yaml.png`

---

### Task 8: Extend the Pipeline to call required tasks (1 point)
- **File**: `tc-pipeline/pipeline.yaml`
- **Code Snippet**:
  Includes `npminstall` (runAfter: `clone`), `tests` (runAfter: `npminstall`), and `build` (runAfter: `tests`).
- **Screenshot to Upload**: `screenshots/08-pipeline-changes.png`

---

### Task 9: Run the Tekton pipeline (1 point)
- **File**: `tc-pipeline/run.yaml`
- **Command**:
  ```bash
  kubectl apply -f tc-pipeline/run.yaml
  tkn pipelinerun logs tc-pipeline-run-... -f
  ```
- **Screenshot to Upload**: `screenshots/09-run-yaml.png`

---

### Task 10: Deploy the image built using pipeline (1 point)
- **Verification**: Application is updated on IBM Cloud Code Engine and running V2 in browser.
- **Screenshot to Upload**: `screenshots/10-final-output.png`
