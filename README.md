# Web3 Bug Bounty Security Extension for Gemini CLI

A specialized Gemini CLI extension for **bug bounty hunting in Web3 projects**. It analyzes smart contracts, DeFi protocols, and Web3 infrastructure for vulnerabilities based on the [OWASP Smart Contract Top 10 (2026)](https://scs.owasp.org/sctop10/) and the [OWASP Web3 Attack Vectors Top 15](https://scs.owasp.org/sctop10/Web3-Attack-Vectors-Top15/).

![Security Extension Workflow](./assets/gemini-cli-security-extension-workflow.gif)

## Features

- **Web3-specialized analysis**: Purpose-built for bug bounty hunting in smart contracts, DeFi protocols, and Web3 infrastructure.
- **OWASP Smart Contract Top 10 (2026)**: Detects all 10 critical smart contract vulnerability classes (reentrancy, oracle manipulation, flash loan attacks, access control, and more).
- **OWASP Web3 Attack Vectors Top 15**: Covers off-chain threats including multisig hijacking, supply chain attacks, private key compromise, approval phishing, drainer malware, rug pulls, DNS hijacking, and nation-state infiltration.
- **Focused analysis**: Specifically designed to analyze code changes within pull requests, helping to identify and address vulnerabilities early in the development process.
- **Open source**: The extension is open source and distributed under the Apache 2.0 license.
- **Integrated with Gemini CLI**: Integrates seamlessly into the Gemini CLI environment, making Web3 security an accessible part of your workflow.
- **Expandable scope**: The extension is designed with an extensible architecture, allowing for future expansion of detected security risks and more advanced analysis techniques.
- **Dependency scans**: Identifies known vulnerabilities affecting your project's dependencies using [OSV-Scanner](https://github.com/google/osv-scanner).

## Installation

Install the Web3 Bug Bounty Security extension by running the following command from your terminal *(requires Gemini CLI v0.4.0 or newer)*:

```bash
gemini extensions install https://github.com/avaloki108/security-bb-web3
```

## Use the extension

The Web3 Bug Bounty Security extension adds the `/security:analyze` command to Gemini CLI which analyzes code changes on your current branch for Web3-specific vulnerabilities — smart contract bugs, DeFi exploit patterns, and off-chain attack vectors — providing an intelligent, Gemini-powered bug bounty report.

Important: This report is a first-pass analysis, not a complete security audit. Use in combination with other tools and manual review.

Note: The /security:analyze command is currently designed for interactive use. Support for non-interactive sessions is planned for a future release (tracked in [issue #20](https://github.com/gemini-cli-extensions/security/issues/20)).

### Customize the `/security:analyze` command

By default, the `/security:analyze` command determines the scope of the analysis using `git diff --merge-base origin/HEAD`. However, to customize the scope, you can add instructions to the command using natural language. For example, to analyze all files in `scripts` folder, you can run the command as
```bash
/security:analyze Analyze all the source code under the script folder. Skip the docs, config files and package files.
```

To get the security report in JSON format, you can use the `--json` flag or request JSON output using natural language:
```bash
/security:analyze --json
```

Or alternatively:
```bash
/security:analyze Return the report in JSON format.
```

![Customize analysis command](./assets/customize_command.gif)

### Scan for vulnerable dependencies

Modern software is built on open-source dependencies, but this can introduce security risks if a dependency contains vulnerabilities.

Regularly running a dependency scan is a critical step in securing your software supply chain and protecting your project from well-known attack vectors.

The `/security:scan-deps` command automates this process by integrating [OSV-Scanner](https://github.com/google/osv-scanner), a tool that cross-references your project's dependencies with [OSV.dev](https://osv.dev/), a Google-maintained, open-source vulnerability database. OSV.dev provides precise vulnerability data by aggregating information from a wide range of open-source ecosystems, ensuring comprehensive and reliable security advisories.

To run a dependency scan, use the following command:
```bash
/security:scan-deps
```

After running the command, you will receive a report listing:
- **Which dependencies are vulnerable.**
- **Details about the specific vulnerabilities**, including their severity and identifiers.
- **Guidance on how to remediate the issues**, such as which version to upgrade to.

## GitHub Integration

### I already use [run-gemini-cli](https://github.com/google-github-actions/run-gemini-cli) workflows in my repository:

* Replace your existing `gemini-review.yml` with this [updated workflow](https://github.com/gemini-cli-extensions/security/blob/main/.github/workflows/gemini-review.yml), which includes the new Security Analysis step.

### I don't use [run-gemini-cli](https://github.com/google-github-actions/run-gemini-cli) workflows in my repository yet:

1. Integrate the Gemini CLI Security Extension into your GitHub workflow to analyze incoming code:

2. Follow Steps 1-3 in this [Quick Start](https://github.com/google-github-actions/run-gemini-cli?tab=readme-ov-file#quick-start).

3. Create a `.github/workflows` directory in your repository's root (if it doesn't already exist).

4. Copy this [Example Workflow](https://github.com/gemini-cli-extensions/security/blob/main/.github/workflows/gemini-review.yml) into the `.github/workflows` directory. See the run-gemini-cli [configuration](https://github.com/google-github-actions/run-gemini-cli?tab=readme-ov-file#configuration) to make changes to the workflow.

5. Ensure the new workflow file is committed and pushed to GitHub.

6. Open a new pull request, or comment `@gemini-cli /review` on an existing PR, to run the Gemini CLI Code Review along with Security Analysis.

## Benchmark

To evaluate the quality and effectiveness of our security analysis, we benchmarked the extension against a real-world dataset of known vulnerabilities.

### Methodology

Our evaluation process is designed to test the extension's ability to identify vulnerabilities in code changes.

1. **Dataset**: We used the [OpenSSF CVE Benchmark](https://github.com/ossf-cve-benchmark/ossf-cve-benchmark), a dataset containing GitHub repositories of real applications in TypeScript / JavaScript. For each vulnerability, the dataset provides the commit containing the vulnerable code (`prePatch`) and the commit where the vulnerability was fixed (`postPatch`).
2. **Analysis Target**: For each CVE, we set up the repository, found the introducing patch with the help of [archeogit](https://github.com/samaritan/archeogit), and added that patch to our local environment.
3. **Report Generation**: We ran the `/security:analyze` command on this diff to generate a security report.
4. **Validation**: Since the dataset has a small number of repositories, we manually reviewed all the generated security reports and compared with the ground truth to calculate the final precision and recall numbers.

We are now actively working to automate the evaluation framework and enrich our datasets by adding new classes of vulnerabilities.

### Results

Our evaluation on this dataset yielded a precision of **90%** and a recall of **93%**.

* **Precision (90%)** measures the accuracy of our detections. Of all the potential vulnerabilities the extension identified, 90% were actual security risks.
* **Recall (93%)** measures the completeness of our coverage. The extension successfully identified 93% of all the known vulnerabilities present in the dataset.

## Types of vulnerabilities

The Web3 Bug Bounty Security extension scans for the following vulnerability categories:

### OWASP Smart Contract Top 10 (2026)

Based on the [OWASP Smart Contract Top 10 (2026)](https://scs.owasp.org/sctop10/):

- **SC01 — Access Control Vulnerabilities**: Missing or broken authorization checks on privileged functions, unprotected admin/upgrade paths, and `tx.origin` misuse
- **SC02 — Business Logic Vulnerabilities**: Design-level flaws in lending, AMM, reward, or governance logic that break intended economic rules
- **SC03 — Price Oracle Manipulation**: Use of manipulable spot prices or unsafe price feeds enabling under-collateralized borrows and unfair liquidations
- **SC04 — Flash Loan–Facilitated Attacks**: Bugs that become exploitable when amplified with uncollateralized flash loan capital in a single transaction
- **SC05 — Lack of Input Validation**: Missing validation of addresses, amounts, array bounds, or cross-chain message sources
- **SC06 — Unchecked External Calls**: External `.call()` with unchecked return values; state updates after external calls enabling reentrancy
- **SC07 — Arithmetic Errors**: Precision loss in share/interest/AMM calculations; rounding direction errors that can be exploited at scale
- **SC08 — Reentrancy Attacks**: Re-entrant external calls before state is fully updated, including cross-function and cross-contract reentrancy
- **SC09 — Integer Overflow and Underflow**: Dangerous arithmetic without overflow checks, especially in pre-0.8.0 Solidity and unsafe `unchecked` blocks
- **SC10 — Proxy & Upgradeability Vulnerabilities**: Unprotected `initialize()`, storage collisions, and weakly governed upgrade mechanisms

### OWASP Web3 Attack Vectors Top 15 (Beyond Smart Contracts)

Based on the [OWASP Web3 Attack Vectors Top 15](https://scs.owasp.org/sctop10/Web3-Attack-Vectors-Top15/):

- **WA01 — Multisig Hijacking**: Blind signing risks, UI spoofing in multisig interfaces, malicious delegatecall via compromised signing frontends
- **WA02 — Supply Chain Attacks**: Malicious npm/PyPI packages, missing lockfiles, compromised `window.ethereum` injection points
- **WA03 — Private Key Compromise**: Hardcoded keys, weak entropy, ECDSA nonce reuse, private key logging
- **WA04 — Drainer Malware & DaaS**: Unlimited token approvals, `setApprovalForAll` misuse, `permit` signature abuse
- **WA05 — Fake Interview & Video Call Social Engineering**: Documentation patterns that increase social engineering risk
- **WA06 — UI/UX Spoofing & Approval Phishing**: Unclear approval flows, missing CSP headers, EIP-7702 delegation abuse
- **WA07 — Centralised Exchange & Web2/2.5 Infrastructure Breaches**: Missing transaction limits, single points of failure in signing workflows
- **WA08 — Phishing & General Social Engineering**: Open redirects, missing email authentication guidance, support flows requesting secrets
- **WA09 — Romance, Investment & Pig Butchering Scams**: Misleading "guaranteed returns" language, fake recovery service references
- **WA10 — Rug Pulls, Fake Airdrops & Token Impersonation**: Uncapped mint functions, owner-controlled pause/blacklist, hidden fee mechanisms
- **WA11 — Wrench Attacks & Physical Coercion**: Single-key treasury ownership, missing spend limits, absent time-locked withdrawals
- **WA12 — Insider Threats & Collusive Abuse**: Admin functions bypassing timelocks, single-person key control
- **WA13 — DNS, Domain & Routing Infrastructure Hijacking**: Missing CSP/SRI headers, CDN-hosted scripts without integrity checks
- **WA14 — Wallet Software, Extension & App Compromises**: Overly broad extension permissions, unpinned wallet SDK versions
- **WA15 — Nation-State Infiltration via Fake Hiring & Malicious OSS Contributions**: CI/CD pipeline access to production secrets, auto-merge policies, missing npm provenance

### Secrets management

- **Hardcoded secrets**: Credentials such as API keys, private keys, passwords and connection strings, and symmetric encryption keys embedded directly in the source code

### Insecure data handling

- **Weak cryptographic algorithms**: Weak or outdated cryptographic algorithms, including any instances of DES, Triple DES, RC4, or ECB mode in block ciphers
- **Logging of sensitive information**: Logging statements that might write passwords, PII, API keys, or session tokens to application or system logs
- **Personally identifiable information (PII) handling violations**: Improper storage, insecure transmission, or any use of PII that may violate data privacy regulations
- **Insecure deserialization**: Code that deserializes data from untrusted sources without proper validation, which could allow an attacker to execute arbitrary code

### Injection vulnerabilities

- **Cross-site scripting (XSS)**: Instances where unsanitized or improperly escaped user input is rendered directly into HTML, which could allow for script execution in a user's browser
- **SQL injection (SQLi)**: Database queries that are constructed by concatenating strings with raw, un-parameterized user input
- **Command injection**: Code that executes system commands or cloud functions using user-provided input without proper sanitization
- **Server-side request forgery (SSRF)**: Code that makes network requests to URLs provided by users without validation, which could allow an attacker to probe internal networks or services
- **Server-side template injection (SSTI)**:  Instances where user input is directly embedded into a server-side template before it is rendered

### Authentication

- **Authentication bypass**: Improper session validation, insecure "remember me" functionality, or custom authentication endpoints that lack brute-force protection
- **Weak or predictable session tokens**: Tokens that are predictable, lack sufficient entropy, or are generated from user-controllable data
- **Insecure password reset**: Predictable reset tokens, leakage of tokens in logs or URLs, and insecure confirmation of a user's identity

## LLM Safety
- **Insecure Prompt Handling (Prompt Injection)**: Analyzes how prompts are constructed to identify risks from untrusted user data, which could lead to prompt injection attacks. This can also include embedding sensitive information (API Keys, credentials, PII) directly within the code used to generate the prompt or the prompt itself.
- **Improper Output Handling**: Detects when LLM-generated content is used unsafely, leading to vulnerabilities like Cross-Site Scripting (XSS), SQL Injection (SQLi), or the remote execution of code via functions like `eval()`. Also flags code where security-sensitive decisions are based on unvalidated LLM output.
- **Insecure Plugin and Tool Usage**: Scans for vulnerabilities in how the LLM interacts with external tools, flagging overly permissive tools or unsafe data flows that could be exploited by malicious output.

## Resources

- [OWASP Smart Contract Top 10 (2026)](https://scs.owasp.org/sctop10/): Reference list for the top 10 smart contract vulnerabilities
- [OWASP Web3 Attack Vectors Top 15](https://scs.owasp.org/sctop10/Web3-Attack-Vectors-Top15/): Reference list for the top 15 off-chain Web3 attack vectors
- [Gemini CLI extensions](https://github.com/google-gemini/gemini-cli/blob/main/docs/extensions/index.md): Documentation about using extensions in Gemini CLI
- [GitHub issues](https://github.com/avaloki108/security-bb-web3/issues): Report bugs or request features

## Legal

- License: [Apache License 2.0](https://github.com/avaloki108/security-bb-web3/blob/main/LICENSE)
- Security: [Security Policy](https://github.com/avaloki108/security-bb-web3/blob/main/SECURITY.md)

## Star history

[![Star History Chart](https://api.star-history.com/svg?repos=gemini-cli-extensions/security&type=Date)](https://www.star-history.com/#gemini-cli-extensions/security&Date)
