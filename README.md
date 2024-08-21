# Preact Rosetta

Preact Rosetta is a lightweight and powerful internationalization (i18n) library for Preact applications. It provides an intuitive API for managing translations, language switching, and advanced internationalization features, inspired by react-i18next but optimized for Preact's ecosystem.

## Table of Contents

1. [Overview](#overview)
2. [Installation](#installation)
3. [Basic Usage](#basic-usage)
4. [Advanced Features](#advanced-features)
5. [Configuration](#configuration)
6. [API Reference](#api-reference)
7. [Examples](#examples)
8. [Contributing](#contributing)
9. [Changelog](#changelog)
10. [Troubleshooting and FAQ](#troubleshooting-and-faq)
11. [License](#license)

## Overview

Preact Rosetta offers:

- Seamless integration with Preact applications
- Efficient pluralization system with support for complex language rules
- Date and number formatting according to locales
- Performance optimizations including lazy loading and caching
- TypeScript support
- Custom HTML elements for easy inline translations
- Hooks for functional components and HOCs for class components

## Installation

Install Preact Rosetta using npm:

```bash
npm install preact-rosetta
```

Or using yarn:

```bash
yarn add preact-rosetta
```

## Basic Usage

Here's a quick example to get you started:

```jsx
import { h } from 'preact';
import { RosettaProvider, useTranslation, RosettaInstance } from 'preact-rosetta';

const rosetta = new RosettaInstance({
  lng: 'en',
  fallbackLng: 'en',
  resources: {
    en: {
      translation: {
        hello: 'Hello, {{name}}!',
        items: ['{{count}} item', '{{count}} items'],
      },
    },
  },
});

function App() {
  return (
    <RosettaProvider rosetta={rosetta}>
      <MyComponent />
    </RosettaProvider>
  );
}

function MyComponent() {
  const { t } = useTranslation();
  return (
    <div>
      <h1>{t('hello', { name: 'World' })}</h1>
      <p>{t('items', { count: 1 })}</p>
      <p>{t('items', { count: 5 })}</p>
    </div>
  );
}
```

## Advanced Features

### Custom Pluralization Rules

Preact Rosetta now supports complex pluralization rules for various languages. You can define custom rules:

```javascript
import { PluralRuleRegistry } from 'preact-rosetta';

const russianPluralRule = {
  language: 'ru',
  pluralForms: ['one', 'few', 'many'],
  selectPlural: (count) => {
    if (count % 10 === 1 && count % 100 !== 11) return 0;
    if ([2, 3, 4].includes(count % 10) && ![12, 13, 14].includes(count % 100)) return 1;
    return 2;
  },
};

rosetta.pluralRuleRegistry.addRule(russianPluralRule);
```

### Date and Number Formatting

Use the new formatting functions for locale-aware date and number formatting:

```javascript
import { useFormatting } from 'preact-rosetta';

function FormattedDate() {
  const { formatDate, formatNumber } = useFormatting();
  return (
    <div>
      <p>{formatDate(new Date(), { dateStyle: 'full' })}</p>
      <p>{formatNumber(1234.56, { style: 'currency', currency: 'EUR' })}</p>
    </div>
  );
}
```

### Performance Optimizations

Preact Rosetta now includes lazy loading of translations and an improved caching mechanism:

```javascript
import { loadNamespace } from 'preact-rosetta';

// Lazy load a namespace
await loadNamespace('en', 'common');

// Translations will now be cached for faster access
```

## Configuration

Configure Preact Rosetta with various options:

```javascript
const rosetta = new RosettaInstance({
  lng: 'en',
  fallbackLng: 'en',
  resources: {
    en: { /* translations */ },
    fr: { /* translations */ },
  },
  interpolation: {
    escapeValue: true,
    prefix: '{{',
    suffix: '}}',
  },
  pluralSeparator: '_',
});
```

## API Reference

### RosettaInstance

The main class for initializing Preact Rosetta.

```javascript
new RosettaInstance(options: RosettaOptions)
```

### useTranslation

A hook for accessing translation functions in functional components.

```javascript
const { t, rosetta } = useTranslation(namespace?: string)
```

### Trans

A component for handling complex translations with interpolation and nested components.

```jsx
<Trans i18nKey="keyWithTags" values={{ name: 'John' }} components={[<strong />, <em />]} />
```

### useLanguageSwitcher

A hook for changing the current language.

```javascript
const { language, changeLanguage } = useLanguageSwitcher()
```

For a full API reference, please see our [API documentation](link-to-api-docs).

## Examples

### Language Switching

```jsx
function LanguageSwitcher() {
  const { language, changeLanguage } = useLanguageSwitcher();
  return (
    <select value={language} onChange={(e) => changeLanguage(e.target.value)}>
      <option value="en">English</option>
      <option value="fr">Français</option>
    </select>
  );
}
```

### Using Namespaces

```jsx
function Header() {
  const { t } = useTranslation('common');
  return <header>{t('title')}</header>;
}
```

For more examples, check our [examples directory](link-to-examples).

## Contributing

We welcome contributions to Preact Rosetta! Please read our [Contributing Guide](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## Changelog

For a detailed changelog, please see the [CHANGELOG.md](CHANGELOG.md) file.

## Troubleshooting and FAQ

**Q: Can I use Preact Rosetta with React?**
A: Preact Rosetta is specifically designed for Preact. For React applications, we recommend using react-i18next.

**Q: How do I handle RTL languages?**
A: Preact Rosetta now includes built-in support for RTL languages. Use the `dir` attribute in your HTML and CSS to handle text direction.

For more FAQs and troubleshooting tips, see our [FAQ page](FAQ).

## License

Preact Rosetta is [MIT licensed](LICENSE).
