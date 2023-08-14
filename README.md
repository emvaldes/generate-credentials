# DevOps (DaaS) - Generate Credentials
GitHub Actions : DevOps As A Service (DaaS) - Generate Credentials

![GitHub Actions - Generate Credentials](https://github.com/emvaldes/generate-credentials/workflows/GitHub%20Actions%20-%20Generate%20Credentials/badge.svg)

---
### GitHub Actions (Required):

[System Requirements](https://github.com/emvaldes/system-requirements) -
[Marketplace](https://github.com/marketplace/actions/system-requirements)

---
### GitHub Secrets (Required):

```console
AWS_ACCESS_KEY_ID             Service-Account AWS Access Key-Id (e.g.: AKIA2...VT7DU).
AWS_SECRET_ACCESS_KEY         Service-Account AWS Secret Access Key (e.g.: zBqDU...mSCpe)
```
```console
AWS_DEFAULT_ACCOUNT           The AWS Account number (e.g.: 123456789012).
```
---
### GitHub Variables (Required):

```console
AWSCLI_CLI                    Install Amazon WebServices CLI (false)
```
```console
AWS_DEFAULT_PROFILE           The AWS Credentials Default User (e.g.: default).
AWS_DEFAULT_REGION            The AWS Default Region (e.g.: us-east-1)
```

```console
CUSTOM_TOOLS                  Install packages from custom list (default: null)
DEFAULT_TOOLS                 Install packages from default list (default: null)

DEVOPS_ACCESS_ROLE            Defines the AWS IAM Role: DevOps--Custom-Access.Role
DEVOPS_ACCOUNT_NAME           A Deployment Service Account name (devops).

PYTHON_REQUIREMENTS           Listing Python packages (default: null)

SANITIZE_OUTPUT               Sanitize Credentials Output (false)
```
```console
PRIVATE_KEYPAIR_FILE          Terraform AWS KeyPair (default: id_rsa)
```
```console
UPDATE_PIP                    Update Python package management (true)
UPDATE_PYTHON                 Update Python to the latest version (true)
```
```console
UPDATE_SYSTEM                 Updating Operating System (false)
UPGRADE_SYSTEM                Upgrading Operating System (false)
```
```console
DEBUGER_MODE                  Enable/Disable Shell Debugger
VERBOSE_MODE                  Identify verbosity level (false)
```
```console
ACTIONS_RUNNER_DEBUG          Enable runner diagnostic logging (true)
ACTIONS_STEP_DEBUG            Enable step debug logging (true)
```
