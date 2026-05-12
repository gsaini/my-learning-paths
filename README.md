# My Learning Paths

Welcome to my personal learning repository! This repo is dedicated to documenting my journey through various learning paths, courses, and certifications across different domains, with a focus on cloud technologies, AI, and professional development.

## Purpose

The main goal of this repository is to:

- Organize and track my progress through different learning paths
- Store resource links, references, and study materials
- Document achievements, milestones, and certifications earned
- Capture notes, key takeaways, and reflections from each module or course
- Serve as a portfolio of my continuous learning and growth

## Structure

Each directory in this repository represents a specific learning path or course. Inside each, you will find:

- A dedicated README with an overview of the path
- Notes and summaries for each module or topic
- Links to official resources and additional references
- Records of achievements, badges, and certificates

## Current Learning Paths

- [Post Graduate Program in Artificial Intelligence & Machine Learning: Business Applications (The University of Texas)](https://www.mygreatlearning.com/pg-program-online-artificial-intelligence-machine-learning?utm_source=eportfolio&gl_source=Linkedin&gl_campaign=Eportfolio)
- [Generative AI Leader (Google Cloud)](./Generative%20AI%20Leader/README.md)

## Achievements & Certifications

- [**AWS Certified Machine Learning - Associate**](https://www.credly.com/badges/6143e4de-470b-427b-a623-94e9431ec648)
  *Issued: March 2025*

  <img src="https://images.credly.com/size/240x240/images/1a634b4e-3d6b-4a74-b118-c0dcb429e8d2/image.png" alt="AWS Certified Machine Learning - Associate Badge" width="120"/>

This section will be updated as I complete courses and earn certifications.

## How to Use

Feel free to explore the directories and use the resources or notes for your own learning. If you have suggestions or want to collaborate, feel free to open an issue or pull request.

## Local Setup (Markdown Linting)

This repo lints staged markdown files on commit using [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) + [lint-staged](https://github.com/lint-staged/lint-staged), wired up as a native git hook in [`.githooks/`](./.githooks/) (no husky). After cloning, run:

```sh
pnpm install
```

`pnpm install` triggers the `prepare` script, which points git at the versioned hooks directory (`git config core.hooksPath .githooks`). From then on, every commit auto-fixes and validates any staged `*.md` files.

Useful scripts:

- `pnpm run lint:md` — lint every markdown file
- `pnpm run lint:md:fix` — auto-fix every markdown file

Rules live in [`.markdownlint.json`](./.markdownlint.json) — tuned for prose-heavy learning content (e.g. line-length and code-fence-language checks are disabled).

---

Happy learning!
