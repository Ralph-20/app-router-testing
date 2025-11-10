# Code Captain for Claude.dev

> **VS Code extension with direct Claude API integration and custom agent configurations**

Claude.dev provides direct Claude API integration with flexible agent configurations, making it ideal for custom Code Captain workflows and advanced AI coordination.

## 🚀 Installation

### Automatic Installation (Recommended)

```bash
npx @devobsessed/code-captain
```

The installer will detect Claude.dev and install to:
- `.claude/` - Agent configurations and command documentation
- `.code-captain/` - Complete workflow system

### Manual Installation

```bash
# Clone or download the claude-code/ directory contents to .claude/
cp -r claude-code/ .claude/
cp -r .code-captain/ .
```

## 🎯 Agent Integration

Code Captain in Claude.dev uses custom agent configurations that provide structured workflows:

### Available Agents
Located in `.claude/agents/`:

- **`code-captain.md`** - Main Code Captain agent with complete workflow system
- **`spec-generator.md`** - Specialized agent for feature specification creation
- **`story-creator.md`** - Agent focused on user story generation
- **`tech-spec.md`** - Technical specification and architecture documentation

### Command Documentation
Located in `.claude/commands/`:

- **`cc-initialize.md`** - Project initialization and analysis workflow

## 🛠️ Available Workflows

### 📋 Project Setup & Analysis
- **Project Initialization** - Comprehensive codebase analysis and documentation setup
- **Technology Assessment** - Evaluate existing tech stack and patterns
- **Architecture Documentation** - Generate system architecture documentation
- **Code Pattern Analysis** - Identify and document code patterns and conventions

### 📝 Requirements & Planning
- **Feature Specifications** - Detailed feature specs with technical implementation plans
- **User Story Generation** - INVEST-principle user stories with acceptance criteria
- **Technical Specifications** - Low-level technical documentation and API specs
- **Architecture Decision Records** - Systematic ADR creation with research integration

### ⚙️ Implementation Support
- **Code Analysis** - Deep code understanding with context-aware explanations
- **Implementation Guidance** - Step-by-step implementation recommendations
- **Testing Strategy** - Comprehensive testing approach and test generation
- **Code Quality Improvement** - Systematic code cleanup and optimization

## 🔄 Workflow Examples

### Using the Main Code Captain Agent

1. **Open Claude.dev** in VS Code
2. **Load the agent**: Reference `.claude/agents/code-captain.md`
3. **Start with initialization**: "Please help me initialize this project using the cc-initialize workflow"
4. **Follow systematic workflows** for each development phase

### Specialized Agent Usage

**For Feature Specifications:**
1. **Load spec-generator agent**: `.claude/agents/spec-generator.md`
2. **Request specification**: "Please create a comprehensive specification for user authentication"
3. **Review generated outputs** in `.code-captain/specs/`

**For User Stories:**
1. **Load story-creator agent**: `.claude/agents/story-creator.md`
2. **Generate stories**: "Please create user stories for the payment processing feature"
3. **Validate story quality** against INVEST principles

**For Technical Documentation:**
1. **Load tech-spec agent**: `.claude/agents/tech-spec.md`
2. **Request documentation**: "Please document the API architecture for the user service"
3. **Review technical specifications** in appropriate folders

### Complete Development Cycle

```
1. Project Analysis
   → Load: code-captain.md
   → Execute: cc-initialize workflow
   → Output: .code-captain/docs/

2. Feature Planning
   → Load: spec-generator.md
   → Create: Feature specification
   → Output: .code-captain/specs/

3. User Story Creation
   → Load: story-creator.md
   → Generate: User stories with acceptance criteria
   → Output: .code-captain/specs/user-stories/

4. Technical Documentation
   → Load: tech-spec.md
   → Document: API and architecture details
   → Output: .code-captain/docs/

5. Implementation Guidance
   → Load: code-captain.md
   → Execute: Development workflows
   → Output: Code + documentation
```

## 📁 File Organization

Claude.dev integration creates this structure:

```
.claude/
├── agents/
│   ├── code-captain.md      # Main workflow agent
│   ├── spec-generator.md    # Feature specification agent
│   ├── story-creator.md     # User story generation agent
│   └── tech-spec.md         # Technical documentation agent
└── commands/
    └── cc-initialize.md     # Initialization workflow documentation

.code-captain/
├── commands/                # Reference documentation
├── docs/                    # Generated documentation
├── research/                # Technical research reports
├── decision-records/        # Architecture Decision Records
├── specs/                   # Feature specifications
│   └── YYYY-MM-DD-feature/
│       ├── spec.md
│       ├── user-stories/
│       └── technical/
└── cc.md                   # Complete reference guide
```

## 🎯 Claude.dev-Specific Features

### Direct Claude API Integration
- **Full Claude 3.5 Sonnet capabilities** for complex reasoning
- **Large context windows** for comprehensive codebase analysis
- **Advanced code understanding** with sophisticated pattern recognition

### Custom Agent Configurations
- **Specialized agents** for different development phases
- **Context-aware responses** based on agent configuration
- **Flexible workflow execution** adapted to specific needs

### Advanced Reasoning Capabilities
- **Critical thinking integration** following best practices guidelines
- **Evidence-based recommendations** with detailed rationale
- **Systematic approach** to complex development challenges

## 🚀 Advanced Usage

### Custom Agent Creation

Create specialized agents in `.claude/agents/`:

```markdown
# Custom Domain Agent

## Role
You are an expert in [domain] with deep knowledge of [specific areas].

## Capabilities
- [List specific capabilities]
- [Include domain expertise]
- [Define output standards]

## Workflow Integration
- [Specify how agent integrates with Code Captain]
- [Define file organization patterns]
- [Include quality standards]

## Communication Style
- [Define interaction patterns]
- [Specify terminology and conventions]
- [Include critical thinking guidelines]
```

### Agent Chaining

Chain agents for complex workflows:

1. **code-captain.md** → Project analysis and setup
2. **spec-generator.md** → Feature specification creation
3. **story-creator.md** → User story generation
4. **tech-spec.md** → Technical documentation
5. **code-captain.md** → Implementation guidance

### Advanced Prompting

Leverage Claude's advanced capabilities:

```
System: Load the code-captain agent configuration
Human: I need to analyze this legacy codebase and create a modernization plan. Please:

1. Use the cc-initialize workflow to analyze current state
2. Identify technical debt and improvement opportunities
3. Create a systematic modernization specification
4. Generate user stories for incremental improvements
5. Document architectural decisions for the modernization approach

Please coordinate between agents as needed and maintain comprehensive documentation throughout.
```

## 🔧 Configuration

### Claude.dev Settings
Optimize Claude.dev for Code Captain:

```json
{
  "claude.maxTokens": 4096,
  "claude.temperature": 0.1,
  "claude.systemPrompt": "Load agent configurations from .claude/agents/ as needed"
}
```

### Agent Customization
Customize agents for your team and domain:

- **Domain-specific knowledge** integration
- **Team coding standards** and conventions
- **Project-specific patterns** and requirements

## 📊 Agent Reference

| Agent | Purpose | Specialization |
|-------|---------|----------------|
| `code-captain` | Complete workflows | Full development lifecycle |
| `spec-generator` | Feature specifications | Requirements and planning |
| `story-creator` | User stories | Agile story creation |
| `tech-spec` | Technical docs | Architecture and APIs |

## 🛠️ Troubleshooting

### Agent Not Loading Correctly
**Problem**: Claude doesn't follow agent configuration  
**Solution**: Explicitly reference the agent file path and reload the configuration

### Context Loss During Workflows
**Problem**: Agent loses context during complex workflows  
**Solution**: Break workflows into phases and explicitly reference previous outputs

### Output Quality Issues
**Problem**: Generated outputs don't match expected standards  
**Solution**: Ensure agent configuration includes quality guidelines and examples

## 🤝 Contributing

Claude.dev-specific contributions:

1. **Agent Development** - Create specialized agents for different domains
2. **Workflow Enhancement** - Improve agent coordination and workflow efficiency
3. **Documentation** - Add Claude.dev-specific examples and patterns
4. **Integration** - Enhance Claude API integration capabilities

---

**Ready to leverage Claude's power with Code Captain?**

1. **Install:** `npx @devobsessed/code-captain`
2. **Load Agent:** Reference `.claude/agents/code-captain.md`
3. **Begin:** "Please help me initialize this project using Code Captain workflows" 