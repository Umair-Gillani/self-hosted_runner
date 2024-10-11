# How to Create Your Own Self-Hosted Runner for GitHub Actions

A self-hosted runner allows you to run GitHub Actions workflows on your own infrastructure. This gives you more control over the hardware, software, and network settings. Below is a comprehensive, step-by-step guide to setting up your own self-hosted runner.

---

## Prerequisites

Before you begin, ensure you have the following:

- **A GitHub Account**: You need access to a GitHub repository where you have administrative rights.
- **A Machine for the Runner**: This can be a physical or virtual machine with internet access.
  - **Operating System**: Windows, macOS, or Linux (Ubuntu is commonly used).
  - **Hardware Requirements**: At least 2 cores and 2 GB of RAM.
- **Administrative Access**: You need to have admin rights on the machine to install software and configure services.
- **Network Access**: The runner must be able to communicate with `github.com` over the internet.

---

## Step 1: Prepare the Machine

### 1.1 Update the System

Ensure your system is up to date.

For **Ubuntu/Linux**:
```bash
sudo apt update
sudo apt upgrade -y
```

For **Windows**:

- Use Windows Update to install all available updates.

### 1.2 Install Required Software

Some workflows might require additional software like Git, Docker, or specific language runtimes.

For **Ubuntu/Linux**:
```bash
sudo apt install -y git
```

For **Windows**:

- Install Git for Windows from [git-scm.com](https://git-scm.com/download/win).

---

## Step 2: Create a Directory for the Runner

Choose a directory where the runner will reside.

```bash
mkdir actions-runner && cd actions-runner
```

---

## Step 3: Download the Latest Runner Package

Go to the [GitHub Actions runner releases](https://github.com/actions/runner/releases) page and download the latest runner package suitable for your operating system.

For **Linux** (64-bit), run:

```bash
# Replace X.Y.Z with the latest version number
curl -o actions-runner-linux-x64-X.Y.Z.tar.gz -L https://github.com/actions/runner/releases/download/vX.Y.Z/actions-runner-linux-x64-X.Y.Z.tar.gz

# Extract the installer
tar xzf ./actions-runner-linux-x64-X.Y.Z.tar.gz
```

---

## Step 4: Configure the Runner

### 4.1 Obtain a Runner Token

You need a token to authenticate the runner with GitHub.

1. Navigate to your GitHub repository.
2. Click on **Settings**.
3. In the left sidebar, click **Actions**.
4. Click **Runners**.
5. Click **Add Runner**.
6. Select your operating system.
7. Copy the `config` command provided; it contains the token.

### 4.2 Run the Configuration Command

In your terminal, paste the `config` command you copied.

Example:

```bash
./config.sh --url https://github.com/yourusername/yourrepository --token YOUR_TOKEN  --labels  self-hosted,oracle-vm-runner
```

During configuration, you'll be prompted to:

- Enter the runner name (press Enter to accept the default).
- Enter the runner group (press Enter to accept the default).
- Enter additional labels (you can add custom labels or press Enter to skip).
- Configure the runner as a service (optional).

---

## Step 5: Install Runner Dependencies

Depending on your workflows, install any additional dependencies.

For example, to install Node.js and npm:

```bash
sudo apt install -y nodejs npm
```

---

## Step 6: Start the Runner

You can start the runner in two ways:

### 6.1 Run Interactively

```bash
./run.sh
```

This will start the runner in the foreground. To stop it, press `Ctrl+C`.

### 6.2 Run as a Service

To run the runner in the background and start automatically on boot:

#### For **Linux**:

1. Install the service:

   ```bash
   sudo ./svc.sh install
   ```

2. Start the service:

   ```bash
   sudo ./svc.sh start
   ```

#### For **Windows**:

- Use the provided PowerShell scripts to install and start the service.

---

## Step 7: Verify the Runner is Online

1. Go back to your GitHub repository.
2. Navigate to **Settings** > **Actions** > **Runners**.
3. You should see your runner listed and its status as **Online**.

---

## Step 8: Test the Runner

Create a simple workflow to test the runner.

### 8.1 Create a Workflow File

In your repository, create a `.github/workflows/self-hosted-test.yml` file with the following content:

```yaml
name: Self-Hosted Runner Test

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: self-hosted
    steps:
      - name: Run a command
        run: echo "Self-hosted runner is working!"
```

### 8.2 Commit and Push

Commit the workflow file and push it to the repository.

```bash
git add .github/workflows/self-hosted-test.yml
git commit -m "Add self-hosted runner test workflow"
git push origin main
```

### 8.3 Check the Workflow Run

1. Go to the **Actions** tab in your repository.
2. You should see the **Self-Hosted Runner Test** workflow running.
3. Verify that it completes successfully.

---

## Step 9: Securing Your Runner

### 9.1 Limit Access

- Ensure that only trusted workflows are run on your self-hosted runner.
- Avoid using `runs-on: self-hosted` without additional labels.

### 9.2 Use Labels for Control

Assign labels to your runner during configuration and specify them in your workflows.

Example during configuration:

```bash
./config.sh --labels my-runner
```

Workflow specifying the label:

```yaml
jobs:
  test:
    runs-on: self-hosted, my-runner
```

---

## Step 10: Maintenance and Updates

### 10.1 Update the Runner Software

Periodically check for updates to the runner software.

```bash
./svc.sh stop
sudo ./bin/installdependencies.sh
sudo ./svc.sh start
```

### 10.2 Monitor Runner Health

- Regularly monitor the runner machine for disk space, CPU usage, and memory.
- Keep the operating system and software dependencies up to date.

---

## Additional Considerations

- **Scaling**: For multiple runners, repeat the setup on different machines or use containerization.
- **Containerized Runners**: You can run runners inside Docker containers for isolation.
- **Automation**: Use scripts or configuration management tools (like Ansible) to automate the setup for multiple runners.

---

## Conclusion

You've successfully set up a self-hosted runner for GitHub Actions. This runner will now execute workflows that specify it in the `runs-on` field. Remember to keep your runner secure and up to date to ensure smooth operation.
