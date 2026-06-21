# Economics Code Notes

Reusable Stata and Python notes and templates for empirical economics research.

## Structure

The repository is organized into two parts.

**The Python part** records my current understanding of Python-based research workflows and newer computational tools for empirical economics. It is organized around three purposes:

1. To clarify the role of Python in empirical research, including when Python is useful, how to organize Python projects, and how Python differs from Stata.
2. To present my understanding of how LLM-based tools work, drawing on selected references to explain key terms, agent workflows, and ways to customize skills for a researcher’s own writing and research style, while also emphasizing which steps require manual checking rather than relying on the agent.
3. To leave space for later notes on machine learning methods, data collection, APIs and other Python-related tools.

This part is not only a collection of external resources, but also a record of how I organize, interpret, and adapt these tools for my own empirical research workflow.

**The Stata part** organizes the empirical workflow in a more traditional way. It includes project setup notes and do-file templates, a raw-data-to-analysis-data workflow, a reference file for commonly used commands, a regression workflow, and method notes grouped into separate folders.

```text
economics-code-notes/
│
├── README.md
│
├── python/
│   ├── 00-python-project-template/
│   │   └── python-project-template.md
│   │
│   ├── 01-agent-assisted-research/
│   │   └── agent-assisted-research.md
│   │
│   ├── 02-machine-learning-notes/
│   │   └── machine-learning-notes.md
│   │
│   └── ...
│
├── stata/
│   ├── 00-project-setup/
│   │   ├── project-setup.md
│   │   ├── from-raw-data-to-analysis-data.md
│   │   └── do-file-template.do
│   │
│   ├── 01-common-commands/
│   │   └── common-commands.md
│   │
│   ├── 02-regression-workflow/
│   │   └── regression-workflow.md
│   │
│   ├── 03-method-notes/
│   │   └── method-notes.md
│   │
│   └── ...
│
└── docs/
```

> Each folder contains a `.md` note; some include a runnable `.do` / `.py` template.

## More

*To be added.*
