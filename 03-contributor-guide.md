# Zypin Testing Framework - Creating New Packages

## 🎯 Overview

This guide provides step-by-step instructions for creating new `@zypin/*` packages. Using the current `@zypin/selenium` package as reference, you'll learn how to build plugins that integrate seamlessly with the Zypin framework.

## 📋 Prerequisites

Before creating a new package, ensure you have:

- **Node.js 16+** installed
- **npm** or **yarn** package manager
- Understanding of the testing tool you want to integrate
- Access to the Zypin repository for testing integration

## 🏗️ Current Package Structure (Based on @zypin/selenium)

Every `@zypin/*` package must follow this structure:

```
@zypin/your-package/
├── package.json                   # Package metadata and dependencies
├── index.js                       # Plugin interface exports (REQUIRED)
├── start.js                       # Server startup logic (optional)
├── run.js                         # Test execution logic (optional)
├── health.js                      # Health check logic (optional)
├── config/
│   └── defaults.json              # Default configuration
├── lib/                           # Shared utilities and libraries
│   ├── your-utilities.js
│   └── your-tool-specific/        # Tool-specific libraries
│       ├── step-definitions/      # For BDD frameworks
│       └── support/               # Support files
├── templates/                     # Test project templates
│   ├── basic-template/
│   │   ├── runner.js              # Template execution logic (REQUIRED)
│   │   ├── test.js                # Sample test file
│   │   ├── package.json           # Template dependencies with zypin metadata
│   │   └── README.md              # Template instructions
│   └── advanced-template/
│       ├── runner.js
│       ├── tests/
│       ├── package.json
│       └── README.md
└── utils/                         # Package-specific utilities
    └── input-handler.js           # Input file processing
```

## 🔌 Plugin Interface Requirements

### Required Exports

Every plugin must export a standard interface in `index.js`:

```javascript
// @zypin/your-package/index.js
const startModule = require('./start.js');
const runModule = require('./run.js');
const healthModule = require('./health.js');

module.exports = {
  name: 'your-package',                    // Plugin name (REQUIRED)
  version: '0.1.0',                       // Plugin version (REQUIRED)
  description: 'Your testing tool integration', // Plugin description (REQUIRED)
  start: startModule.start,               // Server startup function (optional)
  run: runModule.run,                     // Test execution function (optional)
  health: healthModule.checkHealth,       // Health check function (optional)
  templates: ['basic-template', 'advanced-template'] // Available templates (REQUIRED)
};
```

**Current Example from @zypin/selenium:**
```javascript
// @zypin/selenium/index.js
const startModule = require('./start.js');
const runModule = require('./run.js');
const healthModule = require('./health.js');

module.exports = {
  name: 'selenium',
  version: '0.1.0',
  description: 'Selenium Grid integration for web testing',
  start: startModule.start,
  run: runModule.run,
  health: healthModule.checkHealth,
  templates: ['basic-webdriver', 'cucumber-bdd']
};
```

### Interface Functions

#### Start Function (Optional)
```javascript
// start.js
async function start(processManager, config = {}) {
  // Load default config
  const defaultConfig = require('./config/defaults.json');
  const finalConfig = { ...defaultConfig, ...config };

  // Start your testing server/service
  // Return process handle for process manager
  return processHandle;
}

module.exports = { start };
```

**Current Example from @zypin/selenium:**
- Checks Java version (>= 11)
- Downloads Selenium JAR if needed
- Spawns `java -jar selenium-server.jar standalone`
- Returns process handle for management

#### Run Function (Optional)
```javascript
// run.js
async function run(inputFiles, cliParams = {}) {
  // Read user's package.json to detect template
  const packageJsonPath = path.join(process.cwd(), 'package.json');
  const userPackageJson = JSON.parse(fs.readFileSync(packageJsonPath, 'utf8'));
  const templateType = userPackageJson.zypin?.template;

  // Load template runner
  const templateRunner = require(`./templates/${templateType}/runner.js`);

  // Build 3-tier configuration
  const defaults = require('./config/defaults.json');
  const userConfig = userPackageJson.zypin?.config || {};
  const finalConfig = { ...defaults, ...userConfig, ...cliParams };

  // Execute template runner
  const result = await templateRunner.execute(inputFiles, finalConfig);
  return result;
}

module.exports = { run };
```

**Current Example from @zypin/selenium:**
- Detects template from `package.json` zypin section
- Loads template-specific runner
- Implements 3-tier configuration system
- Validates and normalizes configuration

#### Health Function (Optional)
```javascript
// health.js
async function checkHealth(config = {}) {
  // Check if your service is running and healthy
  // Return health status with display formatting
  return {
    healthy: true,
    status: 'ready',
    message: 'Service is running',
    display: 'Service: ✓ ready\nStatus: Service is running'
  };
}

module.exports = { checkHealth };
```

**Current Example from @zypin/selenium:**
- Checks Java availability and version
- Makes HTTP request to Grid status endpoint
- Returns structured health status with display formatting

## 📦 Package Configuration

### package.json Requirements

```json
{
  "name": "@zypin/your-package",
  "version": "0.1.0",
  "description": "Your testing tool integration for Zypin framework",
  "main": "index.js",
  "keywords": [
    "testing",
    "your-tool",
    "automation",
    "zypin",
    "plugin"
  ],
  "author": "Your Name",
  "license": "MIT",
  "peerDependencies": {
    "@zypin/core": "https://github.com/zypin-testing/zypin-core.git"
  },
  "dependencies": {
    "your-testing-tool": "^1.0.0",
    "chalk": "^4.1.2"
  },
  "scripts": {
    "test": "node health.js"
  }
}
```

**Current Example from @zypin/selenium:**
```json
{
  "name": "@zypin/selenium",
  "version": "0.1.0",
  "description": "Selenium Grid integration plugin for Zypin testing framework",
  "main": "index.js",
  "keywords": [
    "selenium",
    "webdriver",
    "testing",
    "automation",
    "grid",
    "zypin",
    "plugin"
  ],
  "author": "",
  "license": "MIT",
  "peerDependencies": {
    "@zypin/core": "https://github.com/zypin-testing/zypin-core.git"
  },
  "dependencies": {
    "@cucumber/cucumber": "^12.2.0",
    "selenium-webdriver": "^4.35.0"
  },
  "scripts": {
    "test": "node health.js"
  }
}
```

### Configuration Defaults

Create `config/defaults.json` with sensible defaults:

```json
{
  "port": 3000,
  "host": "localhost",
  "timeout": 30000,
  "browser": "chrome",
  "headless": false,
  "parallel": 1,
  "retries": 0,
  "yourToolSpecific": "defaultValue"
}
```

## 🎨 Template System

### Template Structure

Each template must include:

1. **`runner.js`** - Template execution logic (REQUIRED)
2. **`package.json`** - Template dependencies with Zypin metadata
3. **Test files** - Working examples
4. **`README.md`** - Template-specific instructions

### Template package.json

```json
{
  "name": "{{PROJECT_NAME}}",
  "version": "1.0.0",
  "description": "Your tool test project created with Zypin Framework",
  "main": "test.js",
  "scripts": {
    "test": "zypin run --input test.js",
    "test:headless": "zypin run --input test.js --headless"
  },
  "keywords": ["testing", "your-tool", "automation", "zypin"],
  "author": "",
  "license": "MIT",
  "dependencies": {
    "@zypin/your-package": "https://github.com/zypin-testing/zypin-your-package"
  },
  "zypin": {
    "package": "your-package",
    "template": "basic-template",
    "config": {
      "browser": "chrome",
      "headless": false,
      "timeout": 30000,
      "parallel": 1,
      "retries": 0
    },
    "help": {
      "examples": [
        "zypin run --input test.js",
        "zypin run --input tests/",
        "zypin run --input test1.js,test2.js"
      ],
      "nextSteps": [
        "Start servers: zypin start --packages your-package",
        "Run tests: zypin run --input test.js",
        "Run with options: zypin run --input test.js --browser firefox --headless"
      ]
    }
  }
}
```

**Current Example from @zypin/selenium basic-webdriver template:**
```json
{
  "name": "{{PROJECT_NAME}}",
  "version": "1.0.0",
  "description": "Basic WebDriver test project created with Zypin Framework",
  "main": "test.js",
  "scripts": {
    "test": "zypin run --input test.js",
    "test:headless": "zypin run --input test.js --headless",
    "test:firefox": "zypin run --input test.js --browser firefox"
  },
  "keywords": ["testing", "selenium", "webdriver", "automation", "zypin"],
  "author": "",
  "license": "MIT",
  "dependencies": {
    "@zypin/selenium": "https://github.com/zypin-testing/zypin-selenium"
  },
  "zypin": {
    "package": "selenium",
    "template": "basic-webdriver",
    "config": {
      "browser": "chrome",
      "headless": false,
      "timeout": 30000,
      "parallel": 1,
      "retries": 0,
      "windowSize": "1920x1080"
    },
    "help": {
      "examples": [
        "zypin run --input test.js",
        "zypin run --input tests/",
        "zypin run --input test1.js,test2.js"
      ],
      "nextSteps": [
        "Start servers: zypin start --packages selenium",
        "Run tests: zypin run --input test.js",
        "Run with options: zypin run --input test.js --browser firefox --headless"
      ]
    }
  }
}
```

### Template Runner

```javascript
// templates/basic-template/runner.js
const { spawn } = require('child_process');
const path = require('path');
const chalk = require('chalk');
const inputHandler = require('../../utils/input-handler');

async function execute(inputFiles, config) {
  console.log(chalk.blue('Running your tool tests...'));
  console.log(chalk.gray(`Configuration: ${JSON.stringify(config, null, 2)}`));

  // Process input files using input handler
  const expandedFiles = inputHandler.processInput(inputFiles, 'basic-template', {
    verbose: process.env.ZYPIN_DEBUG === 'true',
    maxDepth: 5
  });

  if (expandedFiles.length === 0) {
    throw new Error('No test files found to execute');
  }

  // Set up environment variables
  const env = {
    ...process.env,
    YOUR_TOOL_CONFIG: JSON.stringify(config)
  };
  
  // Execute tests
  let testsRun = 0;
  let testsPassed = 0;
  let testsFailed = 0;

  for (const testFile of expandedFiles) {
    console.log(chalk.gray(`\nRunning: ${testFile}`));
    
    try {
      const result = await runSingleTest(testFile, env, config);
      testsRun++;
      
      if (result.success) {
        testsPassed++;
        console.log(chalk.green(`✓ ${path.basename(testFile)} passed`));
      } else {
        testsFailed++;
        console.log(chalk.red(`✗ ${path.basename(testFile)} failed: ${result.error}`));
      }
    } catch (error) {
      testsRun++;
      testsFailed++;
      console.log(chalk.red(`✗ ${path.basename(testFile)} failed: ${error.message}`));
    }
  }

  return {
    success: testsFailed === 0,
    testsRun,
    testsPassed,
    testsFailed,
    message: testsFailed === 0 ? 'All tests passed' : `${testsFailed} test(s) failed`
  };
}

function runSingleTest(testFile, env, config) {
  return new Promise((resolve) => {
    const timeout = config.timeout || 30000;
    
    const child = spawn('node', [testFile], {
      env,
      stdio: ['ignore', 'pipe', 'pipe'],
      cwd: process.cwd()
    });

    const timeoutId = setTimeout(() => {
      child.kill('SIGTERM');
      resolve({
        success: false,
        error: `Test timeout after ${timeout}ms`
      });
    }, timeout);

    child.on('close', (code) => {
      clearTimeout(timeoutId);
      resolve({
        success: code === 0
      });
    });
  });
}

module.exports = { execute };
```

**Key Features:**
- Uses `inputHandler.processInput()` for consistent file processing
- Supports debug mode with `ZYPIN_DEBUG=true`
- Implements timeout handling
- Provides detailed test result reporting
- Handles multiple test files with individual results

## 🔧 Implementation Checklist

### Phase 1: Basic Structure
- [ ] Create package directory with `@zypin/` prefix
- [ ] Initialize `package.json` with required metadata
- [ ] Create `index.js` with standard plugin interface
- [ ] Set up basic directory structure (`config/`, `templates/`, `lib/`, `utils/`)

### Phase 2: Core Functions
- [ ] Implement `start.js` (if your tool needs a server)
- [ ] Implement `run.js` with 3-tier configuration system
- [ ] Implement `health.js` (if applicable)
- [ ] Create `config/defaults.json` with sensible defaults
- [ ] Create `utils/input-handler.js` for file processing

### Phase 3: Template System
- [ ] Create at least one template in `templates/` directory
- [ ] Implement template `runner.js` with `execute()` function
- [ ] Create sample test files
- [ ] Add template `package.json` with rich Zypin metadata (including `help` section)
- [ ] Write template `README.md` with instructions
- [ ] Create `DEBUG_GUIDE.md` with debugging help
- [ ] Create `WRITE_GUIDE.md` with writing help

### Phase 4: Integration Testing
- [ ] Test plugin loading with `@zypin/core`
- [ ] Test `zypin start --packages your-package`
- [ ] Test `zypin create-project --template your-package/basic-template`
- [ ] Test `zypin run --input tests/` with created project
- [ ] Test health check functionality

### Phase 5: Documentation & Polish
- [ ] Write comprehensive package README
- [ ] Document all configuration options
- [ ] Add error handling and validation
- [ ] Test on multiple platforms (Windows, macOS, Linux)
- [ ] Add debug logging support

## 🛠️ Required Utilities

### Input Handler (`utils/input-handler.js`)

Every package should include an input handler for consistent file processing:

```javascript
// utils/input-handler.js
const fs = require('fs');
const path = require('path');
const chalk = require('chalk');

class InputHandler {
  constructor() {
    this.supportedExtensions = {
      'basic-template': ['.js'],
      'advanced-template': ['.js', '.ts']
    };
    
    this.excludePatterns = [
      'node_modules',
      '.git',
      '.DS_Store',
      'coverage',
      'screenshots',
      'reports'
    ];
  }

  processInput(inputFiles, templateType, options = {}) {
    // Process and expand input files
    // Return array of valid test files
  }

  validateFiles(files) {
    // Validate file extensions and existence
    // Return validation results
  }
}

module.exports = new InputHandler();
```

**Key Features:**
- Supports multiple file extensions per template
- Excludes common non-test directories
- Recursive directory scanning with depth limits
- File validation and error reporting
- Debug mode support

## 🧪 Testing Your Package

### Local Testing

1. **Install your package locally:**
   ```bash
   cd @zypin/your-package
   npm link
   ```

2. **Test with core framework (Global Mode):**
   ```bash
   # Start your package
   zypin start --packages your-package
   
   # Check health
   zypin health --server http://localhost:8421
   
   # Create a test project
   zypin create-project test-project --template your-package/basic-template
   ```

3. **Test template execution (Template Mode):**
   ```bash
   cd test-project
   npm install
   
   # Run tests (automatically detects template)
   zypin run --input test.js
   zypin run --input tests/
   zypin run --input test1.js,test2.js
   
   # Run with options
   zypin run --input test.js --your-param value --debug
   
   # Test guide commands
   zypin guide --debug    # Should show DEBUG_GUIDE.md
   zypin guide --write    # Should show WRITE_GUIDE.md
   ```

### Integration Testing

1. **Test plugin discovery:**
   ```bash
   zypin --help  # Should show your package in available packages
   ```

2. **Test dual-mode CLI:**
   ```bash
   # Global mode (outside project)
   zypin start --packages your-package
   
   # Template mode (inside project)
   cd test-project
   zypin run --input test.js
   ```

3. **Test configuration merging:**
   ```bash
   # CLI parameters override project config
   zypin run --input test.js --your-param value
   ```

4. **Test error handling:**
   ```bash
   zypin run --input nonexistent-file
   zypin run --input test.js --invalid-param
   ```

5. **Test debug mode:**
   ```bash
   ZYPIN_DEBUG=true zypin run --input test.js
   zypin run --input test.js --debug
   ```

## 📝 Configuration System

### 3-Tier Configuration

Your package must support the 3-tier configuration system:

1. **Tier 1: Package Defaults** (`config/defaults.json`)
2. **Tier 2: User Project Config** (`package.json` zypin section)
3. **Tier 3: CLI Parameters** (highest priority)

```javascript
// In your run.js
const defaults = require('./config/defaults.json');
const userConfig = userPackageJson.zypin?.config || {};
const finalConfig = {
  ...defaults,
  ...userConfig,
  ...cliParams  // CLI parameters override everything
};
```

### CLI Parameter Support

Support common CLI parameters in your templates:

```bash
zypin run --input tests/ --browser firefox --headless --timeout 60000 --parallel 2
```

### Rich Template Metadata

Include comprehensive help information in your template `package.json`:

```json
{
  "zypin": {
    "package": "your-package",
    "template": "basic-template",
    "config": {
      "browser": "chrome",
      "headless": false,
      "timeout": 30000
    },
    "help": {
      "examples": [
        "zypin run --input test.js",
        "zypin run --input tests/",
        "zypin run --input test1.js,test2.js"
      ],
      "nextSteps": [
        "Start servers: zypin start --packages your-package",
        "Run tests: zypin run --input test.js",
        "Run with options: zypin run --input test.js --browser firefox --headless"
      ]
    }
  }
}
```

## 🎯 Quick Start Summary

To create a new `@zypin/*` package:

1. **Create package structure** with `index.js`, `start.js`, `run.js`, `health.js`
2. **Implement plugin interface** with required exports
3. **Create templates** with `runner.js` and rich `package.json` metadata
4. **Add utilities** including `utils/input-handler.js`
5. **Test integration** with dual-mode CLI system

**Reference Implementation:** Study `@zypin/selenium` for complete examples of all components.

## 📚 Template Documentation System

### CLI Guide Commands

Zypin templates support built-in guide commands that provide contextual help to users:

```bash
# Access template-specific guides
zypin guide --debug    # Show debugging guide
zypin guide --write    # Show writing guide
```

### Template Guide Files

Your templates should include guide files in the template directory:

```
templates/your-template/
├── runner.js
├── package.json
├── test.js
├── README.md
├── DEBUG_GUIDE.md      # Debugging help
└── WRITE_GUIDE.md      # Writing help
```

### DEBUG_GUIDE.md

Create a comprehensive debugging guide for your template:

```markdown
# Your Tool Debug Guide

## Quick Debug Checklist
- Common issues and quick fixes
- Essential debug commands
- Pre-test checklist

## Built-in Debug Features
- Automatic screenshots
- Built-in logging
- Timeout management
- Error handling

## Essential Debug Steps
- Screenshot capture
- Information gathering
- JavaScript execution
- Wait strategies

## Advanced Debugging
- Custom debug steps
- Browser dev tools integration
- Performance monitoring
- Environment-specific debugging
```

### WRITE_GUIDE.md

Create a comprehensive writing guide for your template:

```markdown
# Your Tool Writing Guide

## Introduction to Reusable Patterns
- What makes patterns reusable
- Benefits of reusable approaches
- Your tool's unified approach

## Core Reusable Patterns
- Navigation steps
- Interaction steps
- Verification steps
- Wait steps

## Selector Flexibility
- Multiple selector types
- Use cases for each type
- Best practices

## Data-Driven Reusability
- Form filling with data tables
- Scenario outlines
- Dynamic content handling

## Advanced Patterns
- JavaScript execution
- Screenshot capture
- Information gathering
- Browser actions
```

### Guide Integration

The CLI automatically detects and displays these guides:

```javascript
// In your template runner.js
const path = require('path');
const fs = require('fs');

function showGuide(guideType) {
  const guidePath = path.join(__dirname, `${guideType.toUpperCase()}_GUIDE.md`);
  
  if (fs.existsSync(guidePath)) {
    const guideContent = fs.readFileSync(guidePath, 'utf8');
    console.log(guideContent);
  } else {
    console.log(`Guide not found: ${guideType}`);
  }
}
```

### Current Examples

**@zypin/selenium cucumber-bdd template includes:**
- `DEBUG_GUIDE.md` - Comprehensive debugging help with:
  - Quick debug checklist
  - Built-in debug features (screenshots, logging, timeouts)
  - Essential debug steps
  - Advanced debugging techniques
  - Troubleshooting common issues

- `WRITE_GUIDE.md` - Comprehensive writing help with:
  - Reusable step definitions
  - Core reusable patterns
  - Selector flexibility
  - Data-driven reusability
  - Advanced patterns and best practices

### Guide Best Practices

1. **Make guides actionable** - Include specific commands and examples
2. **Use consistent formatting** - Follow markdown best practices
3. **Include troubleshooting** - Cover common issues and solutions
4. **Provide examples** - Show real usage patterns
5. **Keep guides updated** - Maintain guides as features evolve

### Testing Guide Integration

```bash
# Test guide commands in your template
cd test-project
zypin guide --debug    # Should show DEBUG_GUIDE.md content
zypin guide --write    # Should show WRITE_GUIDE.md content
```

## 🚨 Common Pitfalls

### 1. Missing Plugin Interface
- Ensure `index.js` exports all required fields (`name`, `version`, `description`, `templates`)
- Verify function signatures match expected format
- Test plugin loading with core framework

### 2. Template Structure Issues
- Always include `runner.js` with `execute()` function
- Use `{{PROJECT_NAME}}` placeholder in template package.json
- Include rich Zypin metadata with `help` section in template package.json

### 3. Configuration Problems
- Implement 3-tier configuration system (defaults → user config → CLI params)
- Validate and normalize configuration values
- Handle missing or invalid configuration gracefully

### 4. Missing Utilities
- Include `utils/input-handler.js` for consistent file processing
- Support debug mode with `ZYPIN_DEBUG=true`
- Handle multiple file types and directory scanning

### 5. Dual-Mode CLI Issues
- Test both Global mode (outside project) and Template mode (inside project)
- Ensure template detection works from `package.json` zypin section
- Handle mode-specific command routing

### 6. Missing Guide Files
- Include `DEBUG_GUIDE.md` and `WRITE_GUIDE.md` in templates
- Ensure guide files are comprehensive and actionable
- Test `zypin guide --debug` and `zypin guide --write` commands
- Keep guides updated as features evolve

## 📚 Best Practices

### Code Organization
- Keep functions focused and single-purpose
- Use consistent error handling patterns
- Add comprehensive logging for debugging
- Follow existing code style and patterns from `@zypin/selenium`

### User Experience
- Provide clear error messages with helpful suggestions
- Support debug mode with `ZYPIN_DEBUG=true`
- Include comprehensive help metadata in templates
- Write detailed documentation
- Create comprehensive DEBUG_GUIDE.md and WRITE_GUIDE.md files
- Make guides actionable with specific commands and examples

### Performance
- Implement proper timeouts for long-running operations
- Use efficient file processing for large test suites
- Support parallel execution where possible
- Minimize memory usage in template execution

## 📖 Reference Implementation

Study the `@zypin/selenium` package for a complete reference implementation:

- **Plugin Interface**: `/zypin-selenium/index.js`
- **Start Function**: `/zypin-selenium/start.js`
- **Run Function**: `/zypin-selenium/run.js`
- **Health Check**: `/zypin-selenium/health.js`
- **Templates**: `/zypin-selenium/templates/`
- **Configuration**: `/zypin-selenium/config/defaults.json`
- **Utilities**: `/zypin-selenium/utils/input-handler.js`
- **Guide Files**: `/zypin-selenium/templates/cucumber-bdd/DEBUG_GUIDE.md` and `WRITE_GUIDE.md`

---

**Ready to create your package?** Start with the basic structure and work through the checklist. The `@zypin/selenium` package provides an excellent reference for understanding the current patterns and requirements.

*Happy contributing! 🚀*
