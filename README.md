# VibeCheck - AI-Powered Visual QA for Generated Code

## Overview

VibeCheck is a visual quality assurance tool designed to validate AI-generated code by comparing rendered output against intended specifications. Developers spend an average of 11.4 hours per week reviewing AI-generated code, yet functional tests and code reviews consistently miss visual rendering issues—misaligned elements, incorrect colors, missing components, and broken spacing that impact user experience.

VibeCheck solves this by automatically extracting rendered elements from AI-generated HTML/CSS/JavaScript, comparing them against a checklist of expected elements derived from the original prompt, and generating a structured mismatch report with pixel-level precision.

## Problem Statement

AI-assisted code generation has become standard practice in modern development workflows. However, the current validation approach has a critical gap:

- Functional tests pass but code may render incorrectly
- Code review examines source code, not rendered output
- Manual QA is time-consuming and subjective
- Developers cannot quickly determine if AI output "looks right"

Result: 66% of developers report "almost right, but not quite" as their top frustration with AI code, and debugging rendering issues takes longer than writing code from scratch.

## Solution

VibeCheck bridges this gap by treating rendered output as the source of truth. The validation workflow consists of four deterministic steps:

1. **Intent Capture** - User provides a text description of the intended UI
2. **Expectation Generation** - Claude API converts the description into a structured checklist of expected elements
3. **Rendering and Extraction** - Playwright headless browser renders the AI-generated code and extracts every visible element's position and styling
4. **Structural Diff** - Deterministic comparison identifies mismatches with defined tolerances

The tool returns a structured report highlighting exactly what rendered incorrectly and how.

## Live Demo

Visit the live deployment: https://vibecheck-demo.vercel.app

**Quick start:**
1. Enter a UI description (e.g., "Login page with email input, password input, and blue submit button")
2. Paste HTML/CSS code
3. Click "Run VibeCheck"
4. Review the side-by-side comparison and mismatch report

## Technical Architecture

The system operates across three independent layers:

**Frontend Layer** - Deployed on Vercel, built with Next.js and React. Users interact with two screens: an upload interface for submitting prompts and code, and a results interface displaying expected vs. actual elements with highlighted mismatches.

**API Layer** - Next.js API routes handle checklist generation through Claude API integration and manage the diff engine logic for structural comparison.

**Rendering Layer** - A separate Node.js service deployed on Render.com accepts HTML submissions, renders them in a headless Chromium browser, and returns extracted element data.

The architecture separates concerns: rendering is computationally intensive and stateful, so it runs as a dedicated service. Checklist generation and diffing are lightweight and stateless, so they run as serverless API routes. This design optimizes cost and scalability.

## Technology Stack

**Frontend Framework** - Next.js 14 with React 18 for server-side rendering and API routes.

**Styling** - Tailwind CSS for utility-first styling with minimal bundle size.

**Deployment** - Vercel for the frontend and API routes, with native Next.js support and automatic deployments on push.

**Checklist Generation** - Claude API (Sonnet 4.6) for converting user intent to structured expectations.

**Browser Automation** - Playwright for headless Chromium rendering and element extraction.

**Render Service Deployment** - Render.com for Docker-based Node.js service hosting Chromium.

**Data Validation** - AJV for JSON Schema validation to ensure data integrity across system boundaries.

## Data Contract

All JSON flowing through VibeCheck conforms to a shared schema that defines the structure of element data. The schema includes element identifiers, semantic types, text content, pixel-perfect positioning, computed styling information, and metadata about the data source and timestamp.

This schema serves as the contract between all system components and enables type-safe integration between the three architectural layers.

## Installation

### Prerequisites

Requires Node.js 18 or higher and npm 9 or higher. Git is needed to clone the repository.

### Local Setup

Clone the repository and install dependencies using npm. Create an environment configuration file by copying the provided template and add your Anthropic API key. The Render service URL should be configured once the rendering service is deployed.

### Running Locally

Start the development server which runs on localhost port 3000. The server includes hot-reload support for development.

### Production Build

Build for production using the provided build script, then start the production server.

## Usage

### Basic Workflow

Navigate to the upload screen, enter a description of the intended UI, paste the HTML/CSS/JavaScript code from an AI tool, and click submit. The application runs the validation pipeline and displays results on the results screen, showing expected elements, actual rendered elements, and a detailed mismatch report with severity levels.

### Example Scenario

A developer asks an AI to generate a login page with specific styling requirements. The AI generates HTML/CSS but renders the submit button in red instead of the requested blue. The developer pastes the code into VibeCheck, which:

1. Generates a checklist expecting a blue button
2. Renders the actual code and finds a red button
3. Flags a color mismatch: expected #2563EB, found #DC2626
4. Displays this mismatch with both values visible for comparison

## Project Structure

The codebase is organized with frontend pages in a pages directory, API routes in a dedicated subdirectory, and shared utilities at the root level. The schema definition and validation helpers are accessible to all components. Example data and test utilities support development and verification.

## Team Composition

This project was built using Agent Orchestrator for distributed code delegation across three team members:

**Render Pipeline Owner** - Responsible for Playwright headless browser implementation, DOM element extraction logic, and service deployment.

**Backend Developer** - Handles Claude API integration for checklist generation and implements the deterministic diff engine for structural comparison.

**Frontend Developer** - Builds the user-facing Next.js interface, manages project tracking, and coordinates demo preparation.

Each team member delegated implementation tasks to AI coding agents through Agent Orchestrator, reviewed generated code against specifications, and merged validated changes. Project progress is tracked in a shared Kanban board.

## Performance

The system is designed for responsive performance. Checklist generation through Claude API completes in 500-800 milliseconds. Rendering in the headless browser takes 1-2 seconds depending on page complexity. The diff engine, being pure logic, completes in under 100 milliseconds. Total pipeline time is typically 2-3 seconds.

Position matching uses a 10-pixel tolerance to account for anti-aliasing and sub-pixel rendering differences. Color matching requires exact hexadecimal matches to catch subtle color discrepancies.

## Limitations

The current version supports text prompts only and does not yet support image-based reference designs. Single render passes are performed without automated retry or regeneration. Element matching relies on explicit ID values and does not include fuzzy matching capabilities.

The rendering service uses viewport dimensions of 1280x720 pixels. Render.com free tier deployments may enter sleep mode after 15 minutes of inactivity. HTML payloads exceeding 10MB may reach API size limits.

## Security

API keys are stored server-side only and are never transmitted to client-side code. HTML and CSS submissions are not persisted to disk and are discarded after processing. User data is not logged for analytics purposes. Cross-origin resource sharing is configured to restrict access to the Vercel deployment domain only.

## Future Roadmap

**Version 2.0** will introduce image-based reference designs allowing users to upload design mockups for comparison against rendered output. Automated retry capabilities will feed mismatch reports back to Claude for code regeneration. Perceptual color distance matching will catch subtle color drift. Multi-viewport testing will cover mobile, tablet, and desktop screen sizes.

**Version 3.0** will integrate with development environments through IDE extensions and CI/CD pipelines. A decentralized accuracy registry will track which AI models consistently produce correctly-rendered output.

## Contributing

The project welcomes contributions from the community. Fork the repository, create a feature branch, make changes with clear commit messages, ensure validation tests pass, and submit a pull request describing the changes.

## License

This project is licensed under the MIT License. See the LICENSE file in the repository for full terms.

## Acknowledgments

Built during the AO Hackathon (The Orchestra), August 12-13, 2026, using Agent Orchestrator for distributed code delegation and review workflows.

## Contact

For issues, questions, or suggestions, please visit the GitHub issues page or contact the development team.

---

**VibeCheck** - Validate AI-generated code by measuring reality, not reading code.

