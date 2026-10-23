# Security Policy

At **ZebraLearn**, security is a top priority for our base infrastructure and container runtimes. Because `laravel-base` serves as the foundational container image for production Laravel applications, we take proactive steps to continuously audit, patch, and remediate vulnerabilities in our images.

---

## 🛡️ Supported Versions

We provide active security patches and updates for the latest major release branch and supported PHP runtimes. Older major releases are deprecated when a new major version is released.

### Image Release Branches

| Release Series | Status | Security Support |
|:---|:---|:---|
| **2.x** (`latest`, `2.0.x`) | **Current / Active** | :white_check_mark: Supported (Active security patches & updates) |
| **1.x** (`1.0.x`) | **Deprecated** | :x: Unsupported (Users are strongly advised to upgrade to 2.x) |

### Supported PHP Runtimes

| PHP Version | Alpine Base | Status |
|:---|:---|:---|
| **8.5** *(Default)* | Alpine Linux | :white_check_mark: Supported |
| **8.4** | Alpine Linux | :white_check_mark: Supported |
| **8.3** | Alpine Linux | :white_check_mark: Supported |
| **8.2** | Alpine Linux 3.20 | :white_check_mark: Supported |
| **< 8.2** | Legacy | :x: Unsupported / End-of-Life upstream |

---

## 🔍 Proactive Security Measures

This repository implements multiple automated security and supply-chain controls:

1. **Automated Vulnerability Scanning**: Every container build published to GitHub Container Registry (`ghcr.io`) undergoes automated scanning using [Aqua Security Trivy](https://github.com/aquasecurity/trivy) for OS packages (`apk`) and bundled runtime binaries.
2. **GitHub Code Scanning & SARIF**: Scan findings are automatically uploaded to the repository's **GitHub Security tab** as SARIF reports for real-time tracking and triage.
3. **Weekly Automated Updates**: [Dependabot](https://github.com/dependabot) is configured to monitor upstream Docker parent images weekly for security updates.
4. **Supply Chain Integrity**: Multi-architecture container builds are published with cryptographic digests, Software Bill of Materials (SBOM), and provenance attestations (`mode=max`).
5. **Runtime Protection**: Utilizes `tini` as the container init process to ensure proper signal forwarding and zombie process reaping, avoiding process leaks and denial of service.

---

## 🚨 Reporting a Vulnerability

If you discover a security vulnerability or suspect an exploitable flaw within this repository or container images, **please do not disclose it publicly** through public GitHub issues, discussions, or pull requests.

### Preferred Method: GitHub Private Vulnerability Reporting

Please submit a report confidentially through GitHub's Private Vulnerability Reporting interface:
👉 **[Report a Security Advisory](https://github.com/zebralearn/laravel-base/security/advisories/new)**

### Alternative Method: Direct Security Contact

If you are unable to use GitHub Security Advisories, email our security team directly:
- **Email**: `stputekuldip@gmail.com`
- **Subject Line**: `[SECURITY VULNERABILITY] zebralearn/laravel-base`

### Information to Include in Your Report

To help us investigate and remediate the issue quickly, please include:
- A descriptive summary of the vulnerability.
- Affected image version(s) and PHP version tag(s) (e.g., `ghcr.io/zebralearn/laravel-base:2.0.1-php8.4`).
- Steps to reproduce or proof-of-concept (PoC) code/commands.
- Expected impact, potential attack vectors, or CVSS score (if known).
- Any suggested fixes or remediation steps.

---

## ⏱️ Response & Remediation Timeline

Our maintainers commit to the following response timeline for security issues:

| Milestone | Target Response Time |
|:---|:---|
| **Initial Acknowledgment** | Within **48 hours** |
| **Triage & Validation** | Within **3 business days** |
| **Status Update & Remediation Plan** | Within **5 business days** |
| **Patch Release (Critical)** | Within **48 to 72 hours** after verification |
| **Patch Release (High / Medium)** | In the next scheduled release (typically within 7–14 days) |

Once a vulnerability has been fixed and a patched release is published to GitHub Container Registry, we will publish a GitHub Security Advisory crediting the finder (if requested).

---

## 🔒 Consumer Best Practices

When building production applications using `zebralearn/laravel-base`, we recommend the following security practices:

- **Run as Non-Root**: When deploying in production, run application processes as an unprivileged user (e.g., `www-data` or a dedicated app user) whenever possible.
- **Pin Specific Versions**: Avoid using the floating `latest` tag in production environments; pin to specific SemVer release tags (e.g., `2.0.1-php8.4`) to ensure reproducible builds.
- **Scan Downstream Images**: Incorporate container scanning into your application's CI/CD pipeline to catch application-level vulnerabilities in Composer dependencies or custom packages.
- **Secrets Management**: Never bake secrets, private keys, or `.env` files into container layers. Inject sensitive values at runtime using environment variables or Kubernetes/Docker secrets.
