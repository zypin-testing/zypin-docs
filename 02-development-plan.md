# Zypin Testing Framework - Development Plan

## 🚀 Phase 1: Core Foundation (Week 1-2) ✅ COMPLETED

### Deliverables:
```
@zypin/core/
├── package.json                    # Core framework setup
├── cli.js                         # CLI with plugin architecture
├── core/
│   ├── plugin-loader.js           # Discover @zypin/* packages
│   ├── template-scanner.js        # Scan plugins for templates
│   ├── package-installer.js       # Installation instructions
│   ├── process-manager.js         # Process spawn/kill/health
│   ├── template-manager.js        # Copy templates to projects
│   ├── server.js                  # Built-in HTTP server
│   └── config.js                  # Load global config
└── bin/zypin                      # Executable symlink
```

### Features:
- ✅ **CLI foundation** with auto-help behavior (`zypin` = `zypin --help`)
- ✅ **Plugin loader** for discovering @zypin/* packages
- ✅ **Template scanner** for namespaced templates
- ✅ **Package installer** for missing package instructions
- ✅ **Process manager** for basic start/health of processes
- ✅ **Built-in server** for remote operations
- ✅ **Template manager** for project creation

### Goals:
- ✅ Users can install @zypin/core globally
- ✅ Basic commands work with help text
- ✅ Foundation for plugin system established
- ✅ Plugin discovery and loading working

### Tasks:
1. ✅ Setup Node.js project with package.json
2. ✅ Create basic CLI using Commander.js
3. ✅ Implement auto-help behavior (command without args = help)
4. ✅ Build plugin loader for @zypin/* package discovery
5. ✅ Create template scanner for plugin templates
6. ✅ Build package installer for missing package instructions
7. ✅ Setup executable bin/zypin symlink
8. ✅ Basic error handling and logging

---

## 🔧 Phase 2: Selenium Plugin (Week 3-4) ✅ COMPLETED

### Deliverables:
```
@zypin/selenium/
├── package.json                   # Selenium plugin dependencies
├── index.js                       # Plugin interface exports
├── start.js                       # java -jar lib/selenium-server.jar
├── run.js                         # Plugin run logic with templates
├── health.js                      # Check selenium grid
├── lib/
│   ├── webdriver.js               # WebDriver utilities
│   └── cucumber-bdd/              # Cucumber BDD support files
└── config/defaults.json           # Default selenium config
```

### Features:
- ✅ **`zypin start --packages selenium`** - Starts bundled Selenium Grid
- ✅ **`zypin health --server <url>`** - Shows selenium grid status
- ✅ **Process management** for selenium server
- ✅ **Plugin interface** with standard exports
- ✅ **Template support** for basic-webdriver and cucumber-bdd

### Goals:
- ✅ Users can start selenium grid easily
- ✅ Foundation for plugin interface established
- ✅ Real testing server running
- ✅ Plugin discovery and loading working

### Tasks:
1. ✅ Create selenium plugin structure with standard interface
2. ✅ Build start.js to spawn java process using bundled jar
3. ✅ Create health.js to check grid status (HTTP ping)
4. ✅ Integrate with core process manager
5. ✅ Add selenium to plugin discovery system
6. ✅ Test start/health cycle
7. ✅ Implement plugin interface (index.js)

---

## 📁 Phase 3: Template System (Week 5-6) ✅ COMPLETED

### Deliverables:
```
@zypin/selenium/templates/
├── basic-webdriver/
│   ├── runner.js                  # Plugin execution logic
│   ├── test.js                   # Simple WebDriver test
│   ├── package.json              # Template dependencies
│   └── README.md                 # Instructions
└── cucumber-bdd/
    ├── runner.js                  # Cucumber execution logic
    ├── features/
    │   └── demo.feature          # Sample BDD scenario
    ├── step-definitions/
    │   └── index.js              # Step implementations
    ├── support/
    │   └── index.js              # Cucumber world setup
    ├── package.json              # Cucumber dependencies
    └── README.md                 # Instructions

@zypin/core/core/template-manager.js  # Copy templates to user projects
```

### Features:
- ✅ **`zypin create-project my-tests --template selenium/basic-webdriver`**
- ✅ **Template detection** from package.json zypin section
- ✅ **`zypin run --input test.js`** - Execute basic WebDriver tests
- ✅ **3-tier config merging** system (defaults → package.json → CLI parameters)
- ✅ **CLI parameter overrides** - `zypin run --input test.js --browser firefox --headless`
- ✅ **Namespaced templates** - selenium/basic-webdriver, selenium/cucumber-bdd

### Goals:
- ✅ Users can create and run basic test projects
- ✅ Template system working end-to-end
- ✅ First complete user workflow functioning
- ✅ Multiple template types supported

### Tasks:
1. ✅ Build template-manager.js for copying templates
2. ✅ Create basic-webdriver template with simple test
3. ✅ Implement template runner.js execution logic
4. ✅ Add create-project CLI command
5. ✅ Build package.json template detection
6. ✅ Implement 3-tier config merging system (defaults → package.json → CLI params)
7. ✅ Add run command with --input parameter and config CLI parameters
8. ✅ Implement CLI parameter parsing for common config options:
   - `--browser chrome|firefox|safari|edge`
   - `--headless` (boolean flag)
   - `--timeout <milliseconds>`
   - `--parallel <number>`
   - `--retries <number>`
   - `--window-size <width>x<height>`
9. ✅ Add config validation and normalization functions
10. ✅ Test complete workflow: create → install → run → run with overrides

---

## 🥒 Phase 4: Advanced Templates (Week 7-8) ✅ COMPLETED

### Deliverables:
```
@zypin/selenium/templates/
├── basic-webdriver/              # ✅ Already done
└── cucumber-bdd/
    ├── runner.js                # ✅ Cucumber execution
    ├── features/
    │   └── demo.feature         # ✅ Sample feature
    ├── step-definitions/
    │   └── index.js             # ✅ Step implementations
    ├── support/
    │   └── index.js             # ✅ Cucumber world
    ├── package.json             # ✅ Cucumber dependencies
    └── README.md                # ✅ Instructions
```

### Features:
- ✅ **Cucumber BDD template** with full Gherkin support
- ✅ **Multiple input types** - `zypin run --input features/` or `--input tests/`
- ✅ **Template-specific runners** for different test types
- ✅ **CLI parameter support** across all templates with consistent behavior
- ✅ **Plugin architecture** with namespaced templates

### Goals:
- ✅ Support multiple testing patterns
- ✅ Real-world test project templates
- ✅ Comprehensive selenium plugin

### Tasks:
1. ✅ Create cucumber-bdd template with sample features
2. ✅ Build cucumber runner.js with proper cucumber-js execution
3. ✅ Enhance input handling for directories vs files
4. ✅ Ensure CLI parameter support works consistently across all templates
5. ✅ Test different input patterns and template combinations
6. ✅ Add template validation and error handling

---

## 🎭 Phase 5: Playwright Plugin (Week 9-10) 🔄 NEXT

### Deliverables:
```
@zypin/playwright/
├── package.json                   # Playwright plugin dependencies
├── index.js                       # Plugin interface exports
├── start.js                       # npx playwright serve  
├── run.js                         # Playwright execution
├── health.js                      # Playwright health check
├── config/defaults.json           # Default playwright config
└── templates/
    ├── basic-playwright/
    │   ├── runner.js              # Playwright execution logic
    │   ├── tests/
    │   │   └── example.spec.js    # Basic Playwright test
    │   ├── playwright.config.js   # Playwright configuration
    │   ├── package.json           # Project dependencies
    │   └── README.md              # Instructions
    └── visual-testing/
        ├── runner.js              # Visual testing execution logic
        ├── tests/
        │   └── visual.spec.js     # Screenshot comparison tests
        ├── screenshots/           # Expected screenshots
        ├── package.json           # Project dependencies
        └── README.md              # Instructions
```

### Features:
- 🔄 **Playwright server management**
- 🔄 **Playwright test templates**
- 🔄 **Visual testing support**
- 🔄 **Multi-plugin support** - `zypin start --packages selenium,playwright`

### Goals:
- Prove plugin-based design works
- Multiple testing tools supported
- Plugin system scalable

### Tasks:
1. Create @zypin/playwright plugin structure
2. Implement plugin interface (index.js)
3. Implement playwright server start logic
4. Build playwright health check
5. Create basic-playwright template
6. Create visual-testing template with screenshot comparison
7. Test multi-plugin startup (selenium + playwright)
8. Validate plugin interface consistency

---

## 🔧 Phase 6: Dependencies & Polish (Week 11-12) 🔄 IN PROGRESS

### Decision Point: Choose Dependency Strategy
Based on current implementation:
- **Current**: Plugin-based approach with separate @zypin/* packages
- **Dependency Strategy**: Each plugin manages its own dependencies
- **Installation**: Users install plugins separately from core framework

### Features:
- ✅ **Plugin-based dependency management** - each plugin manages its own deps
- 🔄 **Error handling** and user-friendly messages (basic implementation)
- ✅ **Logging system** for debugging (winston)
- 🔄 **Configuration validation** (basic implementation)
- 🔄 **Template validation** and error recovery (basic implementation)

### Goals:
- Production-ready stability
- Great developer experience
- Comprehensive error handling

### Tasks:
1. ✅ **DECISION**: Plugin-based dependency strategy implemented
2. 🔄 Add comprehensive error handling throughout
3. ✅ Implement logging system with debug levels
4. 🔄 Add configuration validation and helpful error messages
5. 🔄 Build template validation and auto-repair
6. 🔄 Add process cleanup on unexpected exit
7. 🔄 Performance optimization and testing
8. 🔄 Security review and input sanitization

---

## 📚 Phase 7: Documentation & Examples (Week 13-14) 🔄 IN PROGRESS

### Deliverables:
```
@zypin/core/
├── README.md                    # Main framework README
├── docs/
│   ├── getting-started.md      # Quick start guide
│   ├── cli-reference.md        # Complete CLI documentation
│   ├── creating-plugins.md     # How to create new @zypin/* plugins
│   ├── template-guide.md       # How to create templates
│   └── troubleshooting.md      # Common issues and solutions
├── examples/
│   ├── selenium-cucumber-example/
│   ├── selenium-basic-example/
│   └── playwright-visual-example/
└── templates/                  # Community template contributions
```

### Features:
- 🔄 **Complete documentation** with examples (architecture docs updated)
- 🔄 **Tutorial series** for different use cases
- 🔄 **Example projects** users can clone
- 🔄 **Contributing guide** for new plugins/templates
- 🔄 **Plugin publishing** to npm

### Goals:
- Ready for public release
- Clear onboarding for new users
- Community contribution ready

### Tasks:
1. 🔄 Write comprehensive README with quick start
2. 🔄 Create detailed CLI reference documentation
3. 🔄 Write plugin creation guide with examples
4. 🔄 Create template development guide
5. 🔄 Build example projects for each major use case
6. 🔄 Write troubleshooting guide with common issues
7. 🔄 Prepare for npm publication
8. 🔄 Create contributing guidelines
9. 🔄 Setup GitHub repository with templates and automation

---

## 🎯 Success Metrics

### Phase 1-3: Foundation Working ✅ ACHIEVED
- ✅ CLI commands execute without errors
- ✅ Users can start selenium and create basic projects
- ✅ Basic test execution working
- ✅ Auto-help behavior functional
- ✅ Process management stable
- ✅ Plugin architecture working

### Phase 4: Advanced Templates ✅ ACHIEVED
- ✅ Multiple templates and plugins working
- ✅ Complex test scenarios supported (BDD, WebDriver)
- ✅ Plugin-based architecture functional
- ✅ Input handling robust for files and directories
- ✅ Namespaced templates working

### Phase 5-7: Production Ready 🔄 IN PROGRESS
- 🔄 Error-free experience for common workflows
- 🔄 Comprehensive documentation with examples
- 🔄 Ready for community adoption
- ✅ Plugin-based dependency strategy implemented
- 🔄 Performance and security validated

---

## 🤔 Key Decisions Made

### ✅ Week 5: Dependency Strategy - DECIDED
**Chosen**: Plugin-based approach with separate @zypin/* packages
- Each plugin manages its own dependencies
- Users install plugins separately from core framework
- Provides flexibility and scalability

### ✅ Week 6: Configuration Format - DECIDED
**Chosen**: JSON configuration with 3-tier system
- Plugin defaults → package.json config → CLI parameters
- Simple, predictable, and widely supported

### ✅ Week 11: Package Distribution - DECIDED
**Chosen**: Separate npm packages per tool (@zypin/selenium, @zypin/playwright)
- Plugin architecture allows independent development
- Better scalability and maintenance
- Each plugin can have its own release cycle

---

## 🛠️ Technical Stack

### Core Dependencies:
- **CLI Framework**: Commander.js ✅
- **Process Management**: Node.js child_process ✅
- **File Operations**: fs-extra ✅
- **HTTP Requests**: Built-in fetch ✅
- **Template Engine**: Basic file copying + string replacement ✅
- **Logging**: Winston ✅
- **Server**: Express ✅

### Testing Tools Integration:
- **Selenium**: selenium-webdriver, selenium-server-standalone ✅
- **Cucumber**: @cucumber/cucumber ✅
- **Playwright**: @playwright/test 🔄 (Phase 5)
- **Test Frameworks**: Jest, Mocha (template dependent) ✅

### Development Tools:
- **Testing**: Jest for framework tests 🔄
- **Linting**: ESLint with standard config 🔄
- **Documentation**: Markdown with GitHub Pages 🔄
- **CI/CD**: GitHub Actions for testing and publishing 🔄

---

## 🚦 Risk Mitigation

### High Risk:
- **Java dependency for Selenium** - Provide clear Java installation guide
- **Process management complexity** - Implement robust cleanup and monitoring
- **Template compatibility** - Version testing for major dependency updates

### Medium Risk:
- **Cross-platform compatibility** - Test on Windows, macOS, Linux
- **Network security** - Validate all URLs and downloads
- **Performance with multiple packages** - Monitor resource usage

### Low Risk:
- **CLI usability** - User testing and feedback iteration
- **Documentation completeness** - Community feedback and iteration

---

This development plan provides a structured approach to building zypin with clear milestones, deliverables, and decision points. Each phase builds upon the previous one, ensuring a solid foundation while progressively adding complexity and features.
