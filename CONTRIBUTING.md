# Contributing to ThiqahOps

Thank you for your interest in contributing to ThiqahOps! This document provides guidelines and instructions for contributing to the project.

## 🤝 Code of Conduct

Read the [detailed code of conduct here](https://github.com/ImBIOS/.github/blob/main/CODE_OF_CONDUCT.md).

Short version:

- Be respectful and inclusive
- Welcome newcomers and help them learn
- Focus on constructive feedback
- Respect different viewpoints and experiences

## 🚀 Getting Started

### Prerequisites

- **Bun** v1.0+ ([Install Bun](https://bun.sh))
- **Docker** installed and running
- **Git** for version control
- **OpenRouter API Key** for testing (optional)

### Development Setup

1. **Fork the repository**

   ```bash
   # Fork on GitHub, then clone your fork
   git clone https://github.com/YOUR_USERNAME/thiqah-ops.git
   cd thiqah-ops
   ```

2. **Install dependencies**

   ```bash
   bun install
   ```

3. **Set up environment variables**

   ```bash
   cp .env.example .env
   # Add your OPENROUTER_API_KEY if you want to test
   ```

4. **Run the benchmark locally**

   ```bash
   bun run bench --quick  # Quick test with top 3 models
   ```

## 📝 How to Contribute

### Reporting Bugs

1. **Check existing issues** to avoid duplicates
2. **Create a new issue** with:
   - Clear, descriptive title
   - Steps to reproduce
   - Expected vs actual behavior
   - Environment details (OS, Bun version, Docker version)
   - Relevant logs or error messages

### Suggesting Features

1. **Check existing issues** and discussions
2. **Open a feature request** with:
   - Clear description of the feature
   - Use case and motivation
   - Proposed implementation approach (if any)

### Adding New Scenarios

Scenarios are the core of ThiqahOps. Here's how to add a new one:

#### 1. Choose the Right Category

Scenarios belong to one of these categories:

- `setup` - Installing and configuring services
- `security` - Security hardening and best practices
- `networking` - Network configuration and management
- `service-management` - Process and service management
- `file-operations` - File system operations
- `monitoring` - System monitoring and logging
- `backup` - Backup and recovery operations
- `package-management` - Package installation and updates
- `system-config` - System configuration
- `troubleshooting` - Debugging and fixing issues
- `text-processing` - Text manipulation with grep/awk/sed

#### 2. Create the Scenario File

Create a new file in the appropriate category directory:

```typescript
// src/scenarios/category/scenario-name.ts
import type { Scenario } from "../../types";

export const scenarioName: Scenario = {
  id: "scenario-id",
  name: "Category: Scenario Name",
  description: "Brief description of what this scenario tests",
  category: "category-name",
  tags: ["tag1", "tag2", "relevant-keywords"],
  difficulty: "easy" | "medium" | "hard",
  initialState: "optional pre-setup commands", // Optional
  goalPrompt: "Clear instruction for the AI agent",
  validations: [
    {
      description: "What this validation checks",
      checkCommand: "bash command to verify state",
      expectedOutput: /regex pattern or string/,
      scoreWeight: 1.0, // 0.1 to 2.0
      critical: true, // If true, failure fails entire scenario
    },
    // Add more validations...
  ],
};
```

#### 3. Scenario Guidelines

**Goal Prompt:**

- Be clear and specific
- Mention Docker container context if relevant
- Include any constraints or requirements
- Avoid ambiguous instructions

**Validations:**

- **Critical validations**: Mark as `critical: true` for security or core functionality
- **Score weights**: Use 1.0 as default, increase for important checks (up to 2.0)
- **Check commands**: Should be idempotent and safe to run multiple times
- **Expected output**: Use regex for flexible matching, exact strings for precise checks

**Initial State:**

- Use for scenarios that need pre-existing state (e.g., broken permissions, running processes)
- Format as bash commands that will be executed before the AI gets the prompt

**Example Scenario:**

```typescript
import type { Scenario } from "../../types";

export const exampleScenario: Scenario = {
  id: "example-scenario",
  name: "Security: Example Scenario",
  description: "Tests AI's ability to configure something securely",
  category: "security",
  tags: ["security", "configuration", "example"],
  difficulty: "medium",
  goalPrompt:
    "Configure /etc/example.conf to disable insecure features. Set 'InsecureMode=no' and 'RequireAuth=yes'.",
  validations: [
    {
      description: "Configuration file exists",
      checkCommand: "test -f /etc/example.conf && echo 'exists'",
      expectedOutput: /exists/,
      scoreWeight: 1.0,
      critical: true,
    },
    {
      description: "InsecureMode is disabled",
      checkCommand: "grep 'InsecureMode=no' /etc/example.conf",
      expectedOutput: /InsecureMode=no/,
      scoreWeight: 1.5,
      critical: true,
    },
    {
      description: "RequireAuth is enabled",
      checkCommand: "grep 'RequireAuth=yes' /etc/example.conf",
      expectedOutput: /RequireAuth=yes/,
      scoreWeight: 1.0,
      critical: false,
    },
  ],
};
```

#### 4. Register the Scenario

Add your scenario to `src/scenarios/index.ts`:

```typescript
import { exampleScenario } from "./category/example-scenario";

export const scenarios: Scenario[] = [
  // ... existing scenarios
  exampleScenario,
];
```

#### 5. Test Your Scenario

```bash
# Test with a specific scenario (you may need to filter scenarios in code)
bun run bench --quick

# Or test locally by temporarily modifying the scenarios array
```

### Improving Existing Scenarios

- Fix validation commands that don't work correctly
- Improve goal prompts for clarity
- Add missing validations
- Update difficulty ratings if needed
- Add relevant tags

### Code Improvements

- Bug fixes
- Performance optimizations
- Code refactoring
- Documentation improvements
- Test coverage

## 🔧 Development Workflow

### 1. Create a Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

**Branch naming:**

- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation
- `refactor/` - Code refactoring
- `test/` - Test improvements

### 2. Make Changes

- Write clear, readable code
- Follow existing code style
- Add comments for complex logic
- Update documentation if needed

### 3. Test Your Changes

```bash
# Run type checking
bun run check-types

# Run linter
bun x biome check .

# Test the benchmark (if you have API key)
bun run bench --quick
```

### 4. Commit Your Changes

```bash
git add .
git commit -m "feat: add new scenario for X"
```

**Commit message format:**

- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation
- `refactor:` - Refactoring
- `test:` - Tests
- `chore:` - Maintenance

### 5. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a Pull Request on GitHub with:

- Clear title and description
- Reference related issues
- Describe what changed and why
- Include test results if applicable

## 📋 Pull Request Checklist

Before submitting a PR, ensure:

- [ ] Code follows existing style conventions
- [ ] TypeScript types are correct (no `any` types)
- [ ] All validations in new scenarios are tested
- [ ] Documentation is updated if needed
- [ ] Commit messages follow the format
- [ ] No linter errors (`bun x biome check .`)
- [ ] No type errors (`bun run check-types`)

## 🧪 Testing Guidelines

### Testing Scenarios

1. **Test locally** with `bun run bench --quick`
2. **Verify validations** work correctly
3. **Check edge cases** (missing files, wrong permissions, etc.)
4. **Ensure idempotency** - commands should be safe to run multiple times

### Testing Code Changes

- Run type checking: `bun run check-types`
- Run linter: `bun x biome check .`
- Test with different model filters: `bun run bench free`, `bun run bench paid`

## 📚 Code Style

- **TypeScript**: Use strict types, avoid `any`
- **Formatting**: Use Biome (configured in `biome.jsonc`)
- **Naming**: Use descriptive names, camelCase for variables/functions
- **Comments**: Add comments for complex logic or non-obvious decisions

## 🐛 Debugging

### Common Issues

1. **Docker not running**: Ensure Docker daemon is running
2. **Rate limiting**: Use `--low-limit` flag if needed
3. **Container name conflicts**: Containers are auto-generated with timestamps
4. **Validation failures**: Check validation commands work in isolation

### Debug Mode

You can add console.log statements for debugging (remove before PR):

```typescript
console.log("Debug info:", someVariable);
```

## 📖 Documentation

- Update README.md for user-facing changes
- Add JSDoc comments for public functions
- Update CONTRIBUTING.md if workflow changes
- Document new scenario categories if needed

## ❓ Questions?

- Open a [Discussion](https://github.com/ImBIOS/thiqah-ops/discussions) for questions
- Check existing [Issues](https://github.com/ImBIOS/thiqah-ops/issues)
- Review the codebase for examples

## 🙏 Thank You

Your contributions make ThiqahOps better for everyone. Whether it's:

- Adding new scenarios
- Fixing bugs
- Improving documentation
- Suggesting features

Every contribution is valuable and appreciated!

---

## Happy Contributing

🚀
