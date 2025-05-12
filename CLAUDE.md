# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Task Master is a task management system for AI-driven development with Claude, designed to work seamlessly with Cursor AI. It allows you to break down large projects into manageable tasks, track dependencies, and generate implementation plans.

## Common Commands

### Development Commands

```bash
# Install dependencies
npm install

# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage report
npm run test:coverage

# Run end-to-end tests
npm run test:e2e

# Run code formatter to check for style issues
npm run format-check

# Fix formatting issues
npm run format

# Run the MCP server (Model Control Protocol)
npm run mcp-server

# Run the MCP inspector 
npm run inspector
```

### CLI Commands

When referring to the Task Master CLI in your code or documentation, use these commands:

```bash
# Initialize a new project with Task Master
task-master init

# Parse a PRD file and generate tasks
task-master parse-prd <prd-file.txt>

# List all tasks
task-master list

# Show the next task to work on
task-master next

# Show details of a specific task
task-master show <id>

# Generate task files
task-master generate

# Analyze task complexity
task-master analyze-complexity

# Set status of a task
task-master set-status --id=<id> --status=<status>

# Update a single task by ID with new information
task-master update-task --id=<id> --prompt="<prompt>"

# Add a new task
task-master add-task --prompt="Description of new task"

# Expand a task with subtasks
task-master expand --id=<id>
```

## Architecture Overview

Task Master is built with a modular architecture that separates concerns and promotes maintainability:

1. **MCP Server Layer**: Implements the Model Control Protocol for editor integration
   - `mcp-server/server.js`: Entry point for the MCP server
   - `mcp-server/src/index.js`: Main server implementation using FastMCP
   - `mcp-server/src/tools/`: Tool implementations for MCP integration

2. **Core Functionality Layer**: Direct command implementations
   - `mcp-server/src/core/task-master-core.js`: Central module that imports/exports all direct function implementations
   - `mcp-server/src/core/direct-functions/`: Individual command implementations
   - `mcp-server/src/core/utils/`: Shared utility functions

3. **Scripts Layer**: CLI and utility scripts
   - `scripts/`: Contains CLI command implementations and utility modules
   - `scripts/modules/`: Modular components for the CLI
   - `scripts/modules/task-manager/`: Task management functionality

4. **AI Provider Layer**: Integrations with different AI models
   - `src/ai-providers/`: Contains implementations for various AI providers (Anthropic, OpenAI, Google, etc.)

## Configuration

Task Master uses two primary configuration methods:

1. **`.taskmasterconfig` File**: JSON file in project root that stores model selections, parameters, and project defaults.
   - Created via `task-master models --setup`
   - Contains model configurations for main, research, and fallback roles

2. **Environment Variables**: Used exclusively for sensitive API keys, stored in `.env` file or MCP configuration.
   - Required keys depend on configured providers (ANTHROPIC_API_KEY, PERPLEXITY_API_KEY, etc.)

## Key Concepts

- **Tasks**: Main units of work with titles, descriptions, status, and dependencies
- **Subtasks**: Smaller units of work within a task
- **Dependencies**: Relationships between tasks that determine execution order
- **PRD**: Product Requirements Document that can be parsed to generate tasks
- **MCP Integration**: Allows direct interaction with Task Master from compatible editors

## Testing Strategy

The codebase uses Jest for testing with different test types:

- `unit/`: Unit tests for individual functions
- `integration/`: Tests for component interactions
- `e2e/`: End-to-end tests for complete workflows
- `fixtures/`: Test data and mocks

## MCP Server Integration

Task Master implements the Model Control Protocol (MCP) which allows it to be used directly within compatible text editors:

1. Tools are registered in `mcp-server/src/tools/index.js`
2. Each tool wraps a direct function implementation from the core layer
3. The server communicates with editors via the FastMCP library