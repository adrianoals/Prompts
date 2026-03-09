# Running Dependency Audit

Use the `dependency-auditor` agent to run a dependency audit on the project.

**Project Folder to audit:** `$PROJECT_FOLDER`

If the user did not provide the project folder to audit, run the audit on the entire project. Otherwise, audit only the folder provided by the user.

---

# Follow the Agent specification

Ultrathink about each step in the agent specification (`@dependency-auditor.md`). Then, run the full dependency audit on the project strictly following the instructions defined in the dependency-auditor agent specification.

**DO NOT FORGET!** After producing the full report and if the user has not provided a file path and name yet, explicitly ask:
"Do you want me to save this report to a file? If so, please provide the path and file name."

---