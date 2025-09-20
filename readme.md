https://github.com/X2-Alex/vmware-fusion-tools/releases

# vmware-fusion-tools: Mac VM automation for Unity Mode and REST API

[![Releases](https://img.shields.io/github/v/release/X2-Alex/vmware-fusion-tools.svg?style=flat-square)](https://github.com/X2-Alex/vmware-fusion-tools/releases) [![License](https://img.shields.io/github/license/X2-Alex/vmware-fusion-tools.svg?style=flat-square)](https://github.com/X2-Alex/vmware-fusion-tools/blob/main/LICENSE)

Welcome to the project page for vmware-fusion-tools. This package provides a practical set of utilities to streamline VMware Fusion usage on macOS. It focuses on Unity Mode management, snapshots handling, and a lightweight REST API to automate common tasks. The goal is to offer a calm, reliable toolchain that helps developers and power users interact with Fusion without fighting through manual steps every time.

Repository name: vmware-fusion-tools
Repository info: VMware Fusion | Mac Virtualization | Unity Mode | Snapshots | REST API
Topics: not provided

Table of contents
- Why this project exists
- What you get
- How it fits into your workflow
- Getting started
- Installation and setup
- Using the CLI and REST API
- Unity Mode management
- Snapshots lifecycle
- REST API details
- Architecture and design
- Security considerations
- Testing and quality
- Troubleshooting
- Configuring and tuning
- Advanced usage
- Roadmap and future work
- Contributing and code of conduct
- Licensing and attribution
- FAQ
- Appendix: terminology and glossary

Why this project exists
VMware Fusion is a mature tool for running virtual machines on macOS. It offers a range of features, but repetitive workflows can still be time consuming. vmware-fusion-tools addresses a few persistent pain points:
- Unity Mode management: faster switch between host and guest apps, better window handling, and reliable focus behavior.
- Snapshots: quick capture, list, restore, and prune operations to keep a clean VM history without manual steps.
- REST API: a simple, programmatic surface to drive common Fusion tasks from scripts, CI pipelines, or custom dashboards.

The project aims to be predictable. It favors explicit commands and transparent state without hidden magic. The design emphasizes safety, clarity, and stability on macOS environments with VMware Fusion installed.

What you get
- A small, resilient CLI for common Fusion operations
- A REST API server that you can call from scripts or automation tools
- Built-in support for Unity Mode workflows
- Snapshot management: create, list, restore, and delete
- Clear logging and verbose modes for debugging
- Safe defaults with sane error messages
- Extensive in-repo documentation and examples

How it fits into your workflow
- Developers can automate VM provisioning as part of a local CI pipeline
- QA teams can script test environments that require repeatable VM states
- Power users can streamline day-to-day VM management on their Macs
- Teams can share snapshots and Unity-mode setups to reproduce issues

Getting started
- Prerequisites: A Mac with VMware Fusion installed, macOS version compatibility verified, and a user account with sufficient permissions to manage Fusion VMs.
- You will interact with two major components: a command-line tool (CLI) and a REST API server. The CLI helps with quick tasks, while the REST API enables integration into larger automation flows.
- The project emphasizes safety. Before making changes to a live VM, consider running a dry-run where supported and always verify the target VM state after each operation.

Where to download
- For the latest stable build, fetch the release artifact from the Releases page. The repository provides a dedicated releases workflow and assets for macOS. Visit the Releases page to download the installer package for your platform.
- To access the releases page, use the link below. The link points to the repository’s Releases area and contains the artifacts you need.
- For convenience, you can re-open the Releases page at any time: https://github.com/X2-Alex/vmware-fusion-tools/releases

Installation and setup
- Step 1: Verify prerequisites
  - macOS version compatibility with VMware Fusion
  - VMware Fusion installed and licensed
  - Administrative access on the Mac
  - Network access if you plan to use the REST API remotely
- Step 2: Download the release artifact
  - The artifact is hosted on the Releases page. You should download the installer package or disk image intended for macOS.
  - The link contains a path part, so you should download the release artifact and execute the installer as part of the setup.
- Step 3: Run the installer
  - Open the downloaded package and follow the on-screen prompts.
  - The installer will place the necessary binaries on your system and configure PATH access where appropriate.
  - After installation, you may need to sign the application or grant permission in Security & Privacy settings, depending on macOS version.
- Step 4: Verify the installation
  - Open a terminal and run a light check to confirm the CLI is accessible.
  - Example: vmware-fusion-tools --version (use the exact binary name documented in the release notes)
  - If the command is not found, check PATH settings or refer to the installation guide in the repository.
- Step 5: Start the REST API server (optional)
  - If you plan to use the REST API, start the server using the recommended command from the documentation.
  - The server typically binds to a localhost port; ensure no other service uses the same port.
  - Secure the API as described in the security section of this document.
- Step 6: Run a quick experiment
  - Take a simple action, such as listing VMs or creating a test snapshot, to ensure everything is wired correctly.
  - Use verbose mode if needed to observe the operations and potential errors.

Usage basics
- CLI usage
  - The CLI exposes a concise set of commands to manage Fusion VMs, Unity Mode sessions, and snapshots.
  - Typical commands follow a pattern: subcommand, target VM, and operation.
  - Examples (illustrative; refer to the official help output after installation):
    - List available VMs: vmware-fusion-tools list-vms
    - Start a VM: vmware-fusion-tools start-vm --id <vm-id>
    - Stop a VM: vmware-fusion-tools stop-vm --id <vm-id>
    - Create a snapshot: vmware-fusion-tools snapshot-create --id <vm-id> --name "baseline"
    - Restore a snapshot: vmware-fusion-tools snapshot-restore --id <vm-id> --name "baseline"
- REST API usage
  - The REST API runs as a local service by default and can be accessed via HTTP requests.
  - Basic endpoints cover VM listing, snapshot operations, and Unity Mode actions.
  - Example curl commands:
    - GET /api/v1/vms
    - POST /api/v1/vms/{vmId}/snapshots with body {"name": "baseline"}
    - POST /api/v1/vms/{vmId}/snapshots/{snapshotId}/restore
    - POST /api/v1/vms/{vmId}/unity/start
    - POST /api/v1/vms/{vmId}/unity/stop
  - Authentication
    - The API supports token-based authentication. See the API docs for how to obtain and renew tokens.
    - For local usage, you can pass a token in the Authorization header: Authorization: Bearer <token>.
- Running in tandem
  - Use the CLI for quick, manual tasks and the REST API for automation and CI.
  - Build small scripts to automate repetitive testing, such as capturing a baseline snapshot, running a test suite, and restoring to baseline.

Unity Mode management
- What is Unity Mode?
  - Unity Mode lets you run applications from the guest VM side by side with host apps, making it feel like a single desktop. It helps avoid window clutter and keeps relative focus predictable.
- Managing Unity sessions
  - Start and stop Unity Mode for a given VM via CLI or REST API.
  - Ensure host window focus is on the intended app to avoid cross-window confusion.
  - The tool provides safe defaults to avoid accidental data loss when switching modes.
- Practical workflows
  - Start Unity Mode before a test run to ensure all guest applications are accessible directly from the Mac desktop.
  - Close Unity Mode automatically after test completion to return to standard VM window management.
  - Save or restore Unity layout presets if the project requires consistent window placement.

Snapshots lifecycle
- Snapshot concepts
  - Snapshots capture the state of a VM at a particular moment. They are light on data and quick to create.
  - Snapshots enable you to revert to a known-good state after tests, builds, or experiments.
- Creating snapshots
  - The CLI and REST API both provide a create snapshot operation.
  - Name your snapshots clearly to reflect the test or environment context.
  - Consider tagging snapshots with timestamps for traceability.
- Listing and viewing snapshots
  - Retrieve a list of available snapshots for a VM.
  - Include metadata such as creation date, size, and any user notes.
- Restoring and deleting
  - Restore a VM from a snapshot to return to the exact state captured earlier.
  - Delete old or unused snapshots to save space.
- Best practices
  - Create a baseline snapshot before starting a major operation.
  - Use a naming scheme that encodes project, task, and date.
  - Periodically prune snapshots that are no longer needed.
- Safety tips
  - Restoring a snapshot can overwrite current VM state. Ensure that any important changes are saved or captured in a dedicated snapshot before restoration.
  - When using automation, implement safeguards to prevent accidental mass deletion of snapshots.

REST API details
- Core design
  - REST endpoints are designed to be intuitive and predictable.
  - The API is versioned to avoid breaking changes for automation scripts.
- Authentication
  - Use tokens or session-based authentication as described in the API docs.
  - Treat tokens as sensitive credentials; rotate them as part of your security routine.
- Rate limits and quotas
  - The API enforces reasonable limits to protect the host system and the Fusion process.
  - If you hit a limit, retry with exponential backoff and consider optimizing your automation sequence.
- Error handling
  - The API returns standard HTTP status codes and a compact error payload.
  - Client code should gracefully handle transient errors, timeouts, and authentication issues.
- Sample scenarios
  - Retrieve a VM list, then fetch its snapshots, then create a new snapshot, and finally clean up older snapshots.
  - Start Unity Mode for a specific VM, interact with a guest app, then gracefully exit Unity Mode.

Architecture and design
- Two primary components
  - Command-line interface (CLI): A lightweight wrapper around Fusion interactions, designed for quick tasks.
  - REST API server: A small HTTP server that exposes a stable surface for automation and scripts.
- Communications and layers
  - The CLI talks to VMware Fusion through a local process interface and, when needed, via the REST API as a layer of abstraction.
  - The REST API server uses a minimal, secure server framework to handle requests from local or trusted remote clients.
- Data and state
  - The system tracks VM identifiers, snapshot metadata, and Unity Mode state in a lightweight local store.
  - Logging is designed to be concise but informative, helping you diagnose issues quickly.
- Extensibility
  - The architecture is built to be extended with new endpoints and commands without breaking existing scripts.
  - New features can be added through configuration and optional plugins.

Security considerations
- Local access
  - By default, the REST API listens on localhost. If you expose it remotely, secure the connection with TLS and proper authentication.
- Token management
  - Treat API tokens as secrets. Rotate tokens regularly and limit their scope to what is required.
- Permissions
  - The tool operates with the permissions of the user running it. Use least privilege when possible.
- Logging and auditing
  - Enable verbose logging in non-production environments to audit actions.
  - Avoid logging sensitive VM data in plaintext, especially on shared hosts.
- Safe defaults
  - The installer and configuration defaults aim to minimize risk. Change only what you understand and control.

Testing and quality
- Test strategy
  - Unit tests cover core logic in isolation.
  - Integration tests verify end-to-end flows with a running Fusion instance.
  - Smoke tests confirm basic functionality post-install.
- CI/CD
  - The project uses a CI workflow to validate changes against supported macOS versions and Fusion configurations.
  - Artifacts from successful builds are published to the Releases page and linked in the README.
- Local testing
  - Developers can spin up a local Fusion environment and run the CLI and API against it.
  - Mock data can be used to validate scripts and automation logic.

Troubleshooting
- Common issues
  - Command not found after installation: check PATH and verify the installer completed successfully.
  - REST API connection refused: ensure the API server is running and bound to the expected interface.
  - Unity Mode not starting: confirm VMware Fusion is compatible with the current macOS version and that the VM supports Unity Mode.
- Logs and diagnostics
  - Use verbose mode to increase detail in logs.
  - Review logs for error codes and messages that indicate misconfiguration or missing prerequisites.
- Getting help
  - If you encounter a problem, search the issues page for similar problems.
  - Open a new issue describing steps to reproduce, the environment, and the error output.

Configuring and tuning
- Global configuration
  - The tool reads configuration from a standard location in your home directory.
  - You can override settings with environment variables for scripts and automation.
- Per-VM settings
  - Some settings can be applied per VM to customize Unity Mode behavior, snapshots behavior, or API access.
- Performance tuning
  - When running large test suites, consider adjusting the rate of snapshot creation and the responsiveness of REST endpoints.
  - Use asynchronous tasks for long-running operations where possible.

Advanced usage
- Scripting examples
  - Write small shell or Python scripts that call the CLI or REST API to automate end-to-end flows.
  - Combine commands to create a full test environment: create baseline, boot VM, enter Unity Mode, run tests, collect results, restore baseline.
- Integration with CI
  - Integrate REST API calls into CI pipelines to provision test VMs, run tests, and capture artifacts.
  - Use tokens or service accounts with scoped permissions to keep CI environments safe.
- Custom dashboards
  - Build dashboards that show VM health, Unity Mode status, and snapshot counts.
  - Use the REST API to feed data into dashboards in real time or near real time.

Roadmap and future work
- Short-term goals
  - Improve documentation with more examples and edge-case scenarios
  - Add more REST API endpoints for common admin tasks
  - Expand Unity Mode workflow presets
- Medium-term goals
  - Support for additional VMware Fusion versions
  - Enhanced security features, including TLS mutual authentication
  - More granular logging and telemetry for debugging
- Long-term vision
  - A plug-in architecture to support broader virtualization workflows
  - Cross-platform tooling where feasible to support additional hypervisors
  - Deeper integration with CI systems and developer workflows

Contributing and code of conduct
- How to contribute
  - Start by reading the contribution guide in the repository.
  - Create issues to propose features or report bugs.
  - Submit pull requests with focused changes and tests.
- Coding guidelines
  - Write clear, small commits that describe the intent.
  - Include tests where appropriate.
  - Document new features with examples and usage notes.
- Code quality
  - Use linters and static checks when available.
  - Run unit tests locally and verify in a safe environment before merging.
- Community and conduct
  - Be respectful in discussions.
  - Welcome new contributors and be patient in feedback cycles.
  - Report vulnerabilities responsibly and via the appropriate channel.

Licensing and attribution
- The project is released under a permissive license suitable for personal and commercial use.
- Include attribution in your deployments and documentation if you derive from this work.
- Check the LICENSE file in the repository for the exact terms and any contributor credits.

Topics
- Not provided. If you want to add topics later, you can categorize features such as:
  - macOS
  - VMware Fusion
  - Unity Mode
  - Snapshots
  - REST API
  - CLI tooling
  - Automation
  - Virtualization

FAQ
- What is vmware-fusion-tools used for?
  - It provides a practical interface to manage VMware Fusion on macOS, focusing on Unity Mode, snapshots, and a REST API for automation.
- Do I need to be a developer to use it?
  - No. The CLI and REST API are designed to be approachable for both developers and power users.
- Is it safe to run on my Mac?
  - Yes, if you follow the installation instructions and use the tool in controlled environments. Always back up important VMs before making significant changes.
- How do I get help if something goes wrong?
  - Start with the issues page and the documentation. If you cannot resolve a problem, provide clear reproduction steps, your macOS and Fusion versions, and logs.

Appendix: terminology and glossary
- Unity Mode: A mode that allows Windows and macOS apps to run side by side, reducing window management and improving interaction with guest apps.
- Snapshot: A saved state of a VM at a specific moment in time.
- REST API: A web interface that allows external tools to interact with VMware Fusion through standard HTTP methods.
- VM: Virtual Machine, a software emulation of a computer system.
- CLI: Command-Line Interface, a text-based way to interact with software.

Appendix: sample workflow walkthrough
- Step 1: Prepare your environment
  - Ensure VMware Fusion is installed and updated.
  - Install vmware-fusion-tools from the releases page.
  - Start the REST API server if you plan to automate tasks remotely.
- Step 2: Create a baseline
  - Start a VM and create a baseline snapshot to capture a clean state.
  - Validate that Unity Mode can be started and stopped without errors.
- Step 3: Run automated tasks
  - Use the REST API to start Unity Mode, run a script in the guest, and capture results.
  - Create additional snapshots as needed for later comparison.
- Step 4: Clean up
  - Restore to the baseline snapshot after tests conclude.
  - Stop Unity Mode and shut down the VM if required.
- Step 5: Review and report
  - Collect logs and results.
  - Prepare a report for stakeholders or for your team’s internal records.

Releases and updates
- The primary source of truth for releases is the Releases page. All stable builds, along with notes about changes, are posted there.
- If you want to verify a release before download, review the release notes for compatibility, known issues, and upgrade steps.
- To access release notes and assets quickly, you can navigate to the Releases page directly at any time.

Continuous improvement
- The project welcomes feedback on the CLI and API ergonomics.
- If you spot gaps in the API or have ideas for new endpoints, open an issue with a clear description and example requests.
- Community contributions help improve coverage, reliability, and usability for all users.

Usage notes and best practices
- Use safe defaults when integrating with automation.
- Validate VMs and snapshots in a non-production environment before running critical tasks on live machines.
- Keep a small set of well-named snapshots to minimize confusion during restores.
- Regularly back up your Fusion configuration and VM states to reduce risk.

Accessibility and internationalization
- The project primarily targets English-speaking users for clarity.
- If you need localization, you can contribute translations for documentation and error messages.
- Ensure that scripts and automation consider locale differences that may affect date formats or path handling.

Acknowledgments and community
- This project is built by the community for the community.
- Thank you to early adopters who provided feedback and helped shape the CLI and REST API behavior.
- If you find this tool helpful, consider sharing it with teammates who manage macOS virtual machines.

End of content
- This README provides a thorough guide to installation, usage, and long-term maintenance.
- It covers Unity Mode, snapshots, REST API interactions, and practical workflows.
- It is designed to be a practical companion for developers and testers who work with VMware Fusion on macOS.

Note about the link usage
- The link https://github.com/X2-Alex/vmware-fusion-tools/releases is referenced twice in this document. The first instance appears at the very top, and a second instance appears in the Downloads and Releases section within the guide. This ensures readers can locate the release artifacts easily.