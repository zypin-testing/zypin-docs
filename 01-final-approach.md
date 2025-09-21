# Zypin Testing Framework - Final Approach Overview

## 🏗️ Architecture

### Plugin-Based Architecture
```
@zypin/core/                       # Main framework package
├── package.json                   # Core framework dependencies
├── cli.js                         # Main CLI entry point
├── README.md                      # Project documentation
│
├── core/
│   ├── plugin-loader.js           # Discovers & loads @zypin/* packages
│   ├── template-scanner.js        # Scans plugins for templates
│   ├── package-installer.js       # Shows installation instructions
│   ├── process-manager.js         # Start/health check processes
│   ├── template-manager.js        # Copy templates to user projects
│   ├── server.js                  # Built-in HTTP server
│   └── config.js                  # Global framework configuration
│
└── bin/
    └── zypin                      # Executable symlink to cli.js

@zypin/selenium/                   # Selenium plugin package
├── package.json                   # Selenium plugin dependencies
├── index.js                       # Plugin interface (start, run, health)
├── start.js                       # Spawns: java -jar lib/selenium-server.jar
├── run.js                         # Reads user's package.json, loads template runner
├── health.js                      # Checks if Grid is responding
├── lib/
│   ├── webdriver.js               # WebDriver utilities
│   └── cucumber-bdd/              # Cucumber BDD support files
│       ├── step-definitions/
│       └── support/
├── config/
│   └── defaults.json              # Default selenium settings
└── templates/
    ├── basic-webdriver/
    │   ├── runner.js              # Template-specific execution logic
    │   ├── test.js                # Simple WebDriver test example
    │   ├── package.json           # Test project dependencies
    │   └── README.md              # How to run this template
    └── cucumber-bdd/
        ├── runner.js              # Cucumber execution logic
        ├── features/
        │   └── demo.feature       # Sample BDD scenario
        ├── step-definitions/
        │   └── index.js           # Step implementations
        ├── support/
        │   └── index.js           # Cucumber world setup
        ├── package.json           # Cucumber dependencies
        └── README.md

@zypin/playwright/                 # Playwright plugin package (Future)
├── package.json                   # Playwright plugin dependencies
├── index.js                       # Plugin interface
├── start.js                       # Spawns: npx playwright serve
├── run.js                         # Playwright execution logic
├── health.js                      # Checks if Playwright server is up
├── config/
│   └── defaults.json              # Default playwright settings
└── templates/
    ├── basic-playwright/
    │   ├── runner.js              # Playwright execution logic
    │   ├── tests/
    │   │   └── example.spec.js    # Basic Playwright test
    │   ├── playwright.config.js   # Playwright configuration
    │   ├── package.json           # Project dependencies
    │   └── README.md
    └── visual-testing/
        ├── runner.js              # Visual testing execution logic
        ├── tests/
        │   └── visual.spec.js     # Screenshot comparison tests
        ├── screenshots/           # Expected screenshots
        ├── package.json
        └── README.md
```

### Core Framework Components

#### Core Modules
- **`cli.js`** - Main CLI entry point, handles all zypin commands and routes to plugins
- **`plugin-loader.js`** - Discovers and loads @zypin/* packages from node_modules
- **`template-scanner.js`** - Scans loaded plugins for available templates
- **`package-installer.js`** - Shows installation instructions for missing packages
- **`process-manager.js`** - Manages spawning and tracking of server processes
- **`template-manager.js`** - Handles copying templates to user projects
- **`server.js`** - Built-in HTTP server for remote operations
- **`config.js`** - Global framework configuration and plugin settings

#### Plugin System
- **Scoped packages** using `@zypin/*` namespace
- Each plugin is a **separate npm package** with standard interface
- **Plugin discovery** from node_modules/@zypin/* directories
- **No Docker** - direct process spawning (`java -jar selenium-server.jar`)
- Each template has its own **runner.js** for execution logic

#### Plugin Interface
Each plugin package (@zypin/selenium, @zypin/playwright, etc.) must export:
- **`name`** - Plugin name (e.g., 'selenium')
- **`version`** - Plugin version
- **`start`** - Server startup function (optional)
- **`run`** - Test execution function (optional)
- **`health`** - Health check function (optional)
- **`templates`** - Array of available template names
- **`description`** - Plugin description

#### Plugin Structure
Each plugin package contains:
- **`index.js`** - Plugin interface exports
- **`start.js`** - Package-specific server startup logic
- **`run.js`** - Reads user's package.json, loads appropriate template runner
- **`health.js`** - Package-specific health check implementation
- **`config/defaults.json`** - Default package configuration
- **`templates/`** - Ready-to-use test project templates, each with runner.js

#### Template Structure
Templates contain two types of files:

**Framework Files (stay in template):**
- **`runner.js`** - Template-specific execution logic (not copied to user projects)

**User Files (copied to user projects):**
- **Test code examples** - Working test implementations  
- **Configuration files** - Pre-configured to connect to local servers
- **Dependencies** - package.json with required testing libraries
- **Documentation** - README with setup and run instructions

**Template Examples:**
- **basic-webdriver** - Simple WebDriver test with single test.js file
- **cucumber-bdd** - Full BDD setup with features, step definitions, and support files

**When users run `zypin create-project`:**
1. **Test files are copied** to user project directory
2. **runner.js stays in framework** template directory  
3. **Framework loads runner.js** from template when executing tests

## 💻 CLI Commands

```bash
# Start server packages (auto-starts built-in server)
zypin start --packages selenium,playwright

# Create new project from template
zypin create-project my-tests --template selenium/cucumber-bdd

# Run tests (auto-detects template from package.json)
zypin run --input features/login.feature    # Run specific test file
zypin run --input features/                 # Run test directory
zypin run --input tests/

# Run with configuration overrides (CLI parameters have highest priority)
zypin run --input features/ --browser firefox --headless --parallel 2
zypin run --input tests/ --browser edge --timeout 60000 --retries 3

# Check health of running packages (requires server URL)
zypin health --server http://localhost:8421

# Help commands (auto-show help when no args provided)
zypin                              # Same as zypin --help
zypin --help                      # Show global help
zypin start                       # Same as zypin start --help  
zypin start --help               # Show start command help
zypin run                        # Same as zypin run --help
zypin run --help                 # Show run command help
```

### CLI Behavior
- **Auto-help**: Commands without required arguments automatically show help instead of throwing errors
- **Consistent pattern**: `zypin command` = `zypin command --help` when no args provided
- **User-friendly**: New users can explore commands by just typing them
- **Server integration**: Built-in HTTP server auto-starts with start command
- **Plugin discovery**: Automatically discovers installed @zypin/* packages

## 📁 Template System

### Template Detection & Execution Flow
1. **CLI reads current directory's `package.json`** to find template
2. **Plugin's `run.js` loads appropriate template `runner.js`** from plugin
3. **`--input` parameter specifies test files/directories** to execute
4. **3-tier config merging**: plugin defaults → package.json config → CLI parameters
5. **Runner executes specified tests** with merged configuration

### 3-Tier Configuration System
**Tier 1: Plugin Defaults** (Base layer)
```json
// @zypin/selenium/config/defaults.json
{
  "gridUrl": "http://localhost:8422",
  "browser": "chrome", 
  "timeout": 30000,
  "headless": false,
  "retries": 0,
  "parallel": 1
}
```

**Tier 2: User Project Config** (Override layer)
```json
// User's package.json zypin.config section
{
  "zypin": {
    "config": {
      "browser": "firefox",     // Overrides default "chrome"
      "headless": true,         // Overrides default false
      "retries": 2             // Overrides default 0
    }
  }
}
```

**Tier 3: CLI Parameters** (Highest priority)
```bash
# CLI parameters override everything
zypin run --input features/ --browser edge --parallel 3 --headless
# Final config: browser="edge", parallel=3, headless=true
```

### Project Creation
- `zypin create-project my-tests --template selenium/cucumber-bdd`
- **Creates new directory** with user's project name
- **Copies template files** (test code, configs, docs) and modifies package.json with project name
- **runner.js stays in plugin** - not copied to user project
- User gets **ready-to-run test project** with execution logic handled by plugin

## 🔄 User Workflow

1. **Install framework**: `npm install -g https://github.com/zypin-testing/zypin-core`
2. **Install plugins**: `npm install -g https://github.com/zypin-testing/zypin-selenium`
3. **Start servers**: `zypin start --packages selenium`
4. **Create project**: `zypin create-project my-tests --template selenium/cucumber-bdd`  
5. **Install deps**: `cd my-tests && npm install`
6. **Run tests**: `zypin run --input features/` or `zypin run --input features/login.feature --browser firefox --headless`
7. **Check status**: `zypin health --server http://localhost:8421`

## 📦 Example User Project Structure
```
my-tests/                          # User's project (created from template)
├── package.json                   # Contains zypin.template = "cucumber-bdd"
├── features/                      # BDD scenarios (copied from template) - THIS IS INPUT
│   ├── login.feature             # Example: zypin run --input features/login.feature
│   └── signup.feature            # Example: zypin run --input features/
├── step-definitions/              # Step implementations (copied from template)
├── support/                       # Test setup (copied from template)
├── README.md                     # Template-specific instructions
└── node_modules/                 # Dependencies (after npm install)

# Note: runner.js stays in plugin at:
# @zypin/selenium/templates/cucumber-bdd/runner.js
```

## ⚙️ Key Features

- ✅ **Simple**: Direct process spawning, no complex abstractions
- ✅ **Plugin-based**: Easy to add new testing tools as @zypin/* packages  
- ✅ **Template-driven**: Users start with working examples
- ✅ **Smart detection**: Auto-detects template from package.json
- ✅ **Flexible config**: 3-tier configuration system
- ✅ **Ready-to-use**: One command creates complete test project
- ✅ **Offline-ready**: Bundled binaries, no network dependencies for core functionality
- ✅ **Scalable**: Plugin architecture allows independent package development

## 🚀 End-to-End Example
```bash
# 1. Install framework and plugins
npm install -g https://github.com/zypin-testing/zypin-core
npm install -g https://github.com/zypin-testing/zypin-selenium

# 2. Start Selenium Grid (auto-starts built-in server)
zypin start --packages selenium

# 3. Create test project
zypin create-project login-tests --template selenium/cucumber-bdd
cd login-tests

# 4. Install dependencies  
npm install

# 5. Run tests
zypin run --input features/login.feature
# ↓ Reads package.json → finds template "cucumber-bdd"
# ↓ Loads @zypin/selenium/templates/cucumber-bdd/runner.js  
# ↓ Executes login.feature against Selenium Grid

# Run with configuration overrides
zypin run --input features/ --browser firefox --headless --parallel 2
# ↓ Same flow but with CLI parameter config overrides
# ↓ Final config: browser="firefox", headless=true, parallel=2

# 6. Check health
zypin health --server http://localhost:8421
```

## 📋 Detailed Implementation

### User Project Configuration
```json
{
  "name": "my-tests",
  "version": "1.0.0",
  "dependencies": {
    "selenium-webdriver": "^4.0.0",
    "@cucumber/cucumber": "^9.0.0"
  },
  "zypin": {
    "package": "selenium",
    "template": "cucumber-bdd",
    "config": {
      "gridUrl": "http://localhost:8422",
      "browser": "chrome",
      "timeout": 30000
    }
  },
  "scripts": {
    "test": "zypin run --input features/",
    "test:login": "zypin run --input features/login.feature",
    "test:smoke": "zypin run --input features/smoke/"
  }
}
```

### Template Runner Implementation
```javascript
// @zypin/selenium/templates/cucumber-bdd/runner.js
module.exports = {
  async execute(inputFiles, config) {
    const { spawn } = require('child_process')
    
    // Handle different input types
    const testFiles = Array.isArray(inputFiles) ? inputFiles : [inputFiles]
    
    return new Promise((resolve, reject) => {
      const cucumber = spawn('npx', [
        'cucumber-js',
        '--require', './step-definitions/**/*.js',
        '--require', './support/**/*.js',
        ...testFiles  // Pass specific test files/directories
      ], {
        env: {
          ...process.env,
          SELENIUM_GRID_URL: config.gridUrl,
          BROWSER: config.browser
        }
      })
      
      cucumber.on('close', (code) => {
        code === 0 ? resolve() : reject(new Error(`Tests failed`))
      })
    })
  }
}
```

**Examples:**
- `zypin run --input features/login.feature` → `inputFiles = "features/login.feature"`
- `zypin run --input features/` → `inputFiles = "features/"`
- CLI can expand directories to specific files before passing to runner

### Plugin Run Logic
```javascript
// @zypin/selenium/run.js
const path = require('path')
const fs = require('fs')

async function run(inputFiles, cliParams = {}) {
  // 1. Read user's package.json to get template type
  const userPackageJson = JSON.parse(fs.readFileSync('./package.json'))
  const templateType = userPackageJson.zypin?.template || 'basic-webdriver'
  
  // 2. Load template-specific runner from plugin (not user project)
  const pluginPath = path.dirname(__dirname) // Get plugin root
  const templateRunner = require(`${pluginPath}/templates/${templateType}/runner.js`)
  
  // 3. Merge configs: 3-tier system (CLI params have highest priority)
  const config = {
    ...require('./config/defaults.json'),        // Tier 1: Package defaults
    ...userPackageJson.zypin?.config,           // Tier 2: User project config  
    ...cliParams                                // Tier 3: CLI parameters (highest priority)
  }
  
  // 4. Validate and normalize merged configuration
  const finalConfig = validateAndNormalizeConfig(config)
  
  // 5. Execute with template runner (runs in user's project directory)
  process.chdir(path.dirname(require.resolve('./package.json'))) // Ensure we're in user project
  return await templateRunner.execute(inputFiles, finalConfig)
}
```

**Key Points:**
- **runner.js stays in plugin** at `@zypin/selenium/templates/{template}/runner.js`
- **Plugin loads runner.js** from template directory, not user project
- **`--input` specifies test files/directories** to execute (not configuration)
- **3-tier configuration system**: plugin defaults → package.json config → CLI parameters (highest priority)
- **CLI parameters override everything** - `zypin run --input tests/ --browser firefox --headless`
- **Execution runs in user's project directory** with their test files
- **Users can't accidentally break execution logic** since runner.js is in plugin
- **Plugin updates can improve execution** without requiring user project changes

## 🎯 Core Principles

### Simplicity First
- Direct process spawning instead of complex containerization
- Minimal abstractions - packages are thin wrappers
- Clear, predictable command structure

### Plugin-Based Design  
- Easy to add new testing tools as @zypin/* packages (playwright, api-testing, mobile, etc.)
- Each plugin is independent and self-contained
- Common interface across all plugins

### Developer Experience
- **Template-driven development** - users start with working examples
- **Smart configuration** - set once in package.json, reuse everywhere
- **Zero-config testing** - works out of the box with sensible defaults
- **Clear feedback** - helpful CLI messages and error handling
- **Auto-help behavior** - running commands without required args shows help instead of errors
- **Protected execution logic** - runner.js stays in plugin, preventing user errors
- **Plugin updates benefit all projects** - execution improvements without user project changes

### Extensibility
- Plugin system for custom @zypin/* packages
- Template system for custom test patterns
- Configuration system for different environments
- Future-proof architecture for new testing tools

This approach provides a **simple, powerful, and extensible** testing framework that grows with your testing needs while maintaining ease of use through its plugin-based architecture.

## 🔌 Plugin Development Guide

### Creating a New Plugin

To create a new `@zypin/*` plugin, follow this structure:

```bash
# Create new plugin package
mkdir @zypin/my-plugin
cd @zypin/my-plugin
npm init -y
```

### Plugin Interface Requirements

Each plugin must export a standard interface in `index.js`:

```javascript
// @zypin/my-plugin/index.js
const startModule = require('./start.js');
const runModule = require('./run.js');
const healthModule = require('./health.js');

module.exports = {
  name: 'my-plugin',                    // Plugin name (required)
  version: '0.1.0',                     // Plugin version (required)
  description: 'My custom testing plugin', // Plugin description (required)
  start: startModule.start,             // Server startup function (optional)
  run: runModule.run,                   // Test execution function (optional)
  health: healthModule.checkHealth,     // Health check function (optional)
  templates: ['basic', 'advanced']      // Available template names (required)
};
```

### Plugin Structure

```
@zypin/my-plugin/
├── package.json                       # Plugin dependencies
├── index.js                          # Plugin interface exports
├── start.js                          # Server startup logic (optional)
├── run.js                            # Test execution logic (optional)
├── health.js                         # Health check logic (optional)
├── config/
│   └── defaults.json                 # Default configuration
└── templates/
    ├── basic/
    │   ├── runner.js                 # Template execution logic
    │   ├── test.js                   # Sample test file
    │   ├── package.json              # Template dependencies
    │   └── README.md                 # Template instructions
    └── advanced/
        ├── runner.js
        ├── tests/
        ├── package.json
        └── README.md
```

### Plugin Functions

#### Start Function (Optional)
```javascript
// start.js
async function start() {
  // Start your testing server/service
  // Return process information or success status
  return { success: true, message: 'Server started' };
}

module.exports = { start };
```

#### Run Function (Optional)
```javascript
// run.js
async function run(inputFiles, cliParams = {}) {
  // Execute tests with given input files and CLI parameters
  // Return execution result
  return { success: true, message: 'Tests completed' };
}

module.exports = { run };
```

#### Health Function (Optional)
```javascript
// health.js
async function checkHealth() {
  // Check if your service is running and healthy
  // Return health status
  return { healthy: true, message: 'Service is running' };
}

module.exports = { checkHealth };
```

### Template Runner

Each template must have a `runner.js` that implements test execution:

```javascript
// templates/basic/runner.js
module.exports = {
  async execute(inputFiles, config) {
    // Execute tests with the given input files and configuration
    // Handle different input types (files, directories)
    // Apply configuration settings
    // Return execution result
  }
};
```

### Installation and Usage

Once your plugin is ready:

1. **Install the plugin**:
   ```bash
   npm install -g /path/to/@zypin/my-plugin
   ```

2. **Use the plugin**:
   ```bash
   zypin start --packages my-plugin
   zypin create-project my-tests --template my-plugin/basic
   zypin run --input tests/
   ```

### Plugin Discovery

The framework automatically discovers plugins by:
1. Scanning `node_modules/@zypin/*` directories
2. Loading each plugin's `index.js` interface
3. Validating the plugin structure
4. Making plugins available to CLI commands

### Best Practices

- **Keep plugins focused** - Each plugin should handle one testing tool or domain
- **Follow naming conventions** - Use `@zypin/` prefix for all plugins
- **Provide clear documentation** - Include README files for templates
- **Handle errors gracefully** - Return meaningful error messages
- **Support configuration** - Use the 3-tier config system
- **Test thoroughly** - Ensure your plugin works with the framework
