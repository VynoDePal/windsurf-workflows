# Windsurf Workflows

A collection of automated workflows for web developers, designed to improve code quality, performance, security, and accessibility of modern web applications.

## About

Windsurf Workflows provides step-by-step guides to implement web development best practices in your project. Each workflow focuses on a specific aspect of development and contains detailed instructions, commands, and configurations.

## Available Workflows

### 🚀 Performance

- **Bundle Optimization** - Analyzes bundle size, identifies the heaviest modules, and recommends splitting strategies to improve loading performance.

- **Image Optimization** - Using next/image for native lazy loading, blur placeholders, and automatic conversion to modern formats (WebP/AVIF).

### 🔒 Security

- **Security Audit** - Dependency audit and code analysis to detect known vulnerabilities and XSS risks, suggesting security updates or patches.
  
- **Security and Validation** - Dependency audit (npm audit), API input validation (Zod), and secure HTTP headers configuration.

- **API Routes Validation** - Implementation of validation middlewares with Zod to secure API routes, including custom error messages and standardized response structure.

### ♿ Accessibility

- **Accessibility Audit** - Performs a WCAG audit: checks color contrast, ARIA attributes, semantic structure, and keyboard navigation, then proposes remedies for violations.

### 🔍 SEO

- **SEO Optimization** - Checks the site's meta tags (title, description), semantic HTML structure, and performance (loading time) to optimize search engine ranking.

### 🌐 Internationalization

- **i18n String Extraction** - Detection of untranslated texts and automatic extraction of translation keys via i18next-scanner to facilitate multilingual management.

### 🧩 Code Structure

- **Component and Testing Boilerplate** - Automates the generation of reusable React components, custom hooks, and unit tests to boost productivity.

- **Code Quality** - Applies linting (ESLint), formatting (Prettier), and strict typing (TypeScript) to respect code conventions and reduce errors.

## How to Use These Workflows

1. Navigate to the workflow you're interested in
2. Follow the documented steps
3. Run the commands in your project
4. Apply the provided recommendations

## Workflow Structure

Each workflow is structured with:
- A concise description
- Numbered and detailed steps
- Commands to execute
- Implementation notes
- References to best practices

## Supported Technologies

These workflows are designed to work with the following technologies:

### Frameworks and Libraries
- React and Next.js
- TypeScript
- Node.js and npm/yarn

### Code Quality Tools
- ESLint for code verification
- Prettier for formatting
- Jest and React Testing Library for tests

### Analysis and Performance
- Lighthouse and axe-core for auditing
- webpack and its plugins for bundle optimization
- source-map-explorer for size analysis

### Security and Validation
- npm audit for dependency verification
- Zod for schema validation
- Secure HTTP headers

## Contribution

To contribute to these workflows:
1. Create a branch from `main`
2. Add or modify a workflow
3. Submit a pull request with a detailed description of the changes
