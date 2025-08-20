# 🤝 Contributing to Preglish

We love your input! We want to make contributing to Preglish as easy and transparent as possible, whether it's:

- Reporting a bug
- Discussing the current state of the code
- Submitting a fix
- Proposing new features
- Becoming a maintainer

## 🚀 Development Process

We use GitHub to host code, to track issues and feature requests, as well as accept pull requests.

### 1. Fork the Repository

```bash
# Fork the repo on GitHub, then clone your fork
git clone https://github.com/yourusername/preglish.git
cd preglish

# Add the original repo as upstream
git remote add upstream https://github.com/originalowner/preglish.git
```

### 2. Set Up Development Environment

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm run test

# Run linting
npm run lint
```

### 3. Create a Branch

```bash
# Create a new branch for your feature
git checkout -b feature/amazing-feature

# Or for a bug fix
git checkout -b fix/bug-description
```

## 📝 Pull Request Process

1. **Update your fork** before starting work:
   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   ```

2. **Make your changes** following our coding standards

3. **Test your changes**:
   ```bash
   npm run test
   npm run lint
   npm run type-check
   ```

4. **Commit your changes** using conventional commits:
   ```bash
   git commit -m "feat: add amazing feature"
   git commit -m "fix: resolve audio playback issue"
   git commit -m "docs: update contributing guidelines"
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/amazing-feature
   ```

6. **Create a Pull Request** with:
   - Clear description of changes
   - Screenshots/GIFs for UI changes
   - Link to any related issues

## 🎯 Coding Standards

### TypeScript Guidelines

- Use strict TypeScript configuration
- Define proper types for all props and state
- Avoid `any` type usage
- Use meaningful variable and function names

```typescript
// ✅ Good
interface GameState {
  currentStage: number;
  questionsCompleted: number;
  totalScore: number;
}

// ❌ Bad
interface State {
  stage: any;
  questions: any;
  score: any;
}
```

### React Guidelines

- Use functional components with hooks
- Implement proper error boundaries
- Follow React best practices for performance
- Use proper dependency arrays in useEffect

```typescript
// ✅ Good
const GameComponent: React.FC<GameProps> = ({ stage, onComplete }) => {
  const [isPlaying, setIsPlaying] = useState(false);
  
  useEffect(() => {
    if (stage.autoPlay) {
      setIsPlaying(true);
    }
  }, [stage.autoPlay]);

  return <div>...</div>;
};

// ❌ Bad
function GameComponent(props: any) {
  // Component logic without proper typing
}
```

### CSS/Styling Guidelines

- Use Tailwind CSS utility classes
- Create custom animations in the config file
- Follow mobile-first responsive design
- Maintain consistent spacing and colors

```tsx
// ✅ Good
<button className="px-4 py-2 bg-blue-500 hover:bg-blue-600 rounded-lg transition-colors">
  Click me
</button>

// ❌ Bad
<button style={{padding: '8px 16px', backgroundColor: '#3b82f6'}}>
  Click me
</button>
```

## 🧪 Testing Guidelines

### Unit Tests

Write tests for:
- Utility functions
- Custom hooks
- Component logic

```typescript
// Example test
import { scoreCalculator } from '@/utils/scoreCalculator';

describe('scoreCalculator', () => {
  it('should calculate correct score with bonus', () => {
    const result = scoreCalculator({
      isCorrect: true,
      responseTime: 2,
      difficulty: 2,
      hasRelistened: false
    });
    
    expect(result).toBeGreaterThan(100);
  });
});
```

### Integration Tests

Test user workflows:
- Complete game flow
- Audio playback functionality
- Score calculation accuracy

## 🐛 Bug Reports

Create detailed bug reports with:

**Bug Description**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected Behavior**
What you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Environment:**
- OS: [e.g. iOS]
- Browser: [e.g. chrome, safari]
- Version: [e.g. 22]
- Device: [e.g. iPhone X, Desktop]

## 💡 Feature Requests

We track feature requests as GitHub issues. Provide:

- **Clear title** summarizing the feature
- **Detailed description** of the proposed functionality
- **User story** explaining who would use this and why
- **Mockups/wireframes** if applicable
- **Implementation ideas** if you have technical suggestions

## 🏷️ Issue Labels

- `bug` - Something isn't working
- `enhancement` - New feature or request
- `documentation` - Improvements or additions to documentation
- `good first issue` - Good for newcomers
- `help wanted` - Extra attention is needed
- `question` - Further information is requested
- `priority-high` - Critical issues
- `priority-medium` - Important features
- `priority-low` - Nice to have

## 📋 Commit Convention

We use [Conventional Commits](https://conventionalcommits.org/):

- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `style:` - Code style changes
- `refactor:` - Code refactoring
- `test:` - Adding or updating tests
- `chore:` - Maintenance tasks

## 🎨 Design Guidelines

### Visual Design
- Follow modern, minimalist design principles
- Use consistent spacing (4px, 8px, 16px, 24px, 32px)
- Maintain high contrast for accessibility
- Use the defined color palette

### Animation Guidelines
- Keep animations under 300ms for micro-interactions
- Use easing functions: `ease-out` for entrances, `ease-in` for exits
- Provide reduced motion alternatives
- Test on lower-end devices

### Accessibility
- Maintain WCAG 2.1 AA compliance
- Use semantic HTML elements
- Provide keyboard navigation
- Test with screen readers
- Ensure color contrast ratios

## 🌍 Internationalization

When adding new text:
- Use translation keys instead of hardcoded strings
- Keep text concise and clear
- Consider cultural differences
- Test with longer translations (German, Finnish)

## 📦 Release Process

1. **Version Bumping**: Use semantic versioning (MAJOR.MINOR.PATCH)
2. **Changelog**: Update CHANGELOG.md with new features and fixes
3. **Testing**: Ensure all tests pass and manual testing is complete
4. **Documentation**: Update README.md if needed
5. **Release Notes**: Create detailed release notes

## 🏆 Recognition

Contributors will be recognized in:
- README.md contributors section
- Release notes
- Project documentation

## 📞 Getting Help

- 💬 **Discussions**: Use GitHub Discussions for questions
- 🐛 **Issues**: Report bugs via GitHub Issues
- 📧 **Email**: Contact maintainers directly for sensitive issues
- 💭 **Discord**: Join our community server (link in README)

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to Preglish! 🎉⚽🎧
