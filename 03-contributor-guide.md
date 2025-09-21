# Zypin Testing Framework - Contributor Guide

## 🎯 Overview

This guide provides comprehensive instructions for creating new `@zypin/*` packages for the Zypin Testing Framework. Using the `@zypin/selenium` package as a reference, you'll learn how to build plugins that integrate seamlessly with the core framework.

## 📋 Prerequisites

Before creating a new package, ensure you have:

- **Node.js 16+** installed
- **npm** or **yarn** package manager
- Understanding of the testing tool you want to integrate
- Familiarity with the Zypin framework architecture
- Access to the Zypin repository for testing integration

## 🏗️ Package Architecture Overview

### Standard Package Structure

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
│   └── your-utilities.js
├── templates/                     # Test project templates
│   ├── basic-template/
│   │   ├── runner.js              # Template execution logic (REQUIRED)
│   │   ├── test.js                # Sample test file
│   │   ├── package.json           # Template dependencies
│   │   └── README.md              # Template instructions
│   └── advanced-template/
│       ├── runner.js
│       ├── tests/
│       ├── package.json
│       └── README.md
└── utils/                         # Package-specific utilities
    └── input-handler.js
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

### Interface Functions

#### Start Function (Optional)
```javascript
// start.js
async function start(processManager, config = {}) {
  // Start your testing server/service
  // Return process handle for process manager
  return processHandle;
}

module.exports = { start };
```

#### Run Function (Optional)
```javascript
// run.js
async function run(inputFiles, cliParams = {}) {
  // Execute tests with given input files and CLI parameters
  // Return execution result
  return {
    success: true,
    testsRun: 5,
    testsPassed: 5,
    testsFailed: 0,
    message: 'All tests passed'
  };
}

module.exports = { run };
```

#### Health Function (Optional)
```javascript
// health.js
async function checkHealth(config = {}) {
  // Check if your service is running and healthy
  // Return health status
  return {
    healthy: true,
    status: 'ready',
    message: 'Service is running',
    display: 'Service: ✓ ready\nStatus: Service is running'
  };
}

module.exports = { checkHealth };
```

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
      "timeout": 30000
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

async function execute(inputFiles, config) {
  console.log(chalk.blue('Running your tool tests...'));
  
  // Process input files
  const testFiles = Array.isArray(inputFiles) ? inputFiles : [inputFiles];
  
  // Set up environment variables
  const env = {
    ...process.env,
    YOUR_TOOL_CONFIG: JSON.stringify(config)
  };
  
  // Execute tests
  return new Promise((resolve, reject) => {
    const child = spawn('your-test-command', testFiles, {
      env,
      stdio: ['ignore', 'pipe', 'pipe'],
      cwd: process.cwd()
    });
    
    let output = '';
    child.stdout.on('data', (data) => {
      output += data.toString();
    });
    
    child.on('close', (code) => {
      resolve({
        success: code === 0,
        testsRun: 1,
        testsPassed: code === 0 ? 1 : 0,
        testsFailed: code === 0 ? 0 : 1,
        message: code === 0 ? 'All tests passed' : 'Tests failed'
      });
    });
  });
}

module.exports = { execute };
```

## 🔧 Implementation Checklist

### Phase 1: Basic Structure
- [ ] Create package directory with `@zypin/` prefix
- [ ] Initialize `package.json` with required metadata
- [ ] Create `index.js` with standard plugin interface
- [ ] Set up basic directory structure (`config/`, `templates/`, `lib/`)

### Phase 2: Core Functions
- [ ] Implement `start.js` (if your tool needs a server)
- [ ] Implement `run.js` with 3-tier configuration system
- [ ] Implement `health.js` (if applicable)
- [ ] Create `config/defaults.json` with sensible defaults

### Phase 3: Template System
- [ ] Create at least one template in `templates/` directory
- [ ] Implement template `runner.js` with `execute()` function
- [ ] Create sample test files
- [ ] Add template `package.json` with Zypin metadata
- [ ] Write template `README.md` with instructions

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

## 🧪 Testing Your Package

### Local Testing

1. **Install your package locally:**
   ```bash
   cd @zypin/your-package
   npm link
   ```

2. **Test with core framework:**
   ```bash
   zypin start --packages your-package
   zypin health --server http://localhost:8421
   ```

3. **Test template creation:**
   ```bash
   zypin create-project test-project --template your-package/basic-template
   cd test-project
   npm install
   zypin run --input tests/
   ```

### Integration Testing

1. **Test plugin discovery:**
   ```bash
   zypin --help  # Should show your package in available packages
   ```

2. **Test configuration merging:**
   ```bash
   zypin run --input tests/ --your-param value
   ```

3. **Test error handling:**
   ```bash
   zypin run --input nonexistent-file
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

## 🚨 Common Pitfalls

### 1. Missing Plugin Interface
- Ensure `index.js` exports all required fields
- Verify function signatures match expected format
- Test plugin loading with core framework

### 2. Template Structure Issues
- Always include `runner.js` with `execute()` function
- Use `{{PROJECT_NAME}}` placeholder in template package.json
- Include proper Zypin metadata in template package.json

### 3. Configuration Problems
- Implement 3-tier configuration system
- Validate and normalize configuration values
- Handle missing or invalid configuration gracefully

### 4. Process Management
- Return proper process handles from `start()` function
- Implement proper cleanup on process termination
- Handle process errors and timeouts

### 5. File Path Issues
- Use absolute paths for template loading
- Handle cross-platform path differences
- Validate file existence before operations

## 📚 Best Practices

### Code Organization
- Keep functions focused and single-purpose
- Use consistent error handling patterns
- Add comprehensive logging for debugging
- Follow existing code style and patterns

### User Experience
- Provide clear error messages
- Support debug mode with `ZYPIN_DEBUG=true`
- Include helpful installation instructions
- Write comprehensive documentation

### Performance
- Implement proper timeouts for long-running operations
- Use efficient file processing for large test suites
- Minimize memory usage in template execution
- Support parallel execution where possible

### Security
- Validate all user inputs
- Sanitize file paths and URLs
- Use secure defaults for network operations
- Handle sensitive configuration data properly

## 🔄 Release Process

### Version Management
1. Update version in `package.json`
2. Update version in `index.js` exports
3. Update any version-specific dependencies
4. Test with updated version

### Publishing
1. Ensure all tests pass
2. Update documentation
3. Create release notes
4. Publish to npm (when ready for public release)

### Integration
1. Update `@zypin/core` to include your package as dependency (if needed)
2. Test integration with core framework
3. Update framework documentation
4. Notify users of new package availability

## 📖 Reference Implementation

Study the `@zypin/selenium` package for a complete reference implementation:

- **Plugin Interface**: `/zypin-selenium/index.js`
- **Start Function**: `/zypin-selenium/start.js`
- **Run Function**: `/zypin-selenium/run.js`
- **Health Check**: `/zypin-selenium/health.js`
- **Templates**: `/zypin-selenium/templates/`
- **Configuration**: `/zypin-selenium/config/defaults.json`

## 🤝 Contributing

### Getting Help
- Review existing packages for patterns
- Check the core framework documentation
- Ask questions in project discussions
- Test thoroughly before submitting

### Code Review
- Ensure all checklist items are completed
- Test on multiple platforms
- Verify integration with core framework
- Update documentation as needed

---

**Ready to create your package?** Start with the basic structure and work through the checklist. The `@zypin/selenium` package provides an excellent reference for understanding the patterns and requirements.

*Happy contributing! 🚀*
