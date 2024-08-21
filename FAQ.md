# Preact Rosetta FAQ

## Table of Contents

1. [General Questions](#general-questions)
2. [Installation and Setup](#installation-and-setup)
3. [Usage and Features](#usage-and-features)
4. [Performance and Optimization](#performance-and-optimization)
5. [Troubleshooting](#troubleshooting)
6. [Comparison with Other Libraries](#comparison-with-other-libraries)
7. [Contributing and Community](#contributing-and-community)

## General Questions

### Q: What is Preact Rosetta?
A: Preact Rosetta is a lightweight internationalization (i18n) library specifically designed for Preact applications. It provides tools for managing translations, language switching, pluralization, and other i18n features.

### Q: Why should I use Preact Rosetta instead of other i18n libraries?
A: Preact Rosetta is optimized for Preact's ecosystem, offering a smaller bundle size and better performance compared to React-based alternatives. It provides a familiar API for those coming from react-i18next while leveraging Preact's strengths.

### Q: Is Preact Rosetta suitable for large-scale applications?
A: Yes, Preact Rosetta is designed to scale with your application. It includes features like lazy loading of translations and efficient caching mechanisms to handle large translation sets and complex applications.

## Installation and Setup

### Q: How do I install Preact Rosetta?
A: You can install Preact Rosetta using npm or yarn:
```bash
npm install preact-rosetta
# or
yarn add preact-rosetta
```

### Q: What are the peer dependencies for Preact Rosetta?
A: Preact Rosetta requires Preact as a peer dependency. It's compatible with Preact 10.x and above.

### Q: How do I set up Preact Rosetta in my application?
A: To set up Preact Rosetta, you need to create a RosettaInstance and wrap your app with the RosettaProvider:

```jsx
import { RosettaProvider, RosettaInstance } from 'preact-rosetta';

const rosetta = new RosettaInstance({
  lng: 'en',
  fallbackLng: 'en',
  resources: {
    en: { translation: { /* your translations */ } },
  },
});

function App() {
  return (
    <RosettaProvider rosetta={rosetta}>
      {/* Your app components */}
    </RosettaProvider>
  );
}
```

## Usage and Features

### Q: How do I translate text in my components?
A: Use the `useTranslation` hook in functional components:

```jsx
import { useTranslation } from 'preact-rosetta';

function MyComponent() {
  const { t } = useTranslation();
  return <h1>{t('hello')}</h1>;
}
```

### Q: How does pluralization work in Preact Rosetta?
A: Preact Rosetta supports pluralization through plural forms in your translations and plural rules:

```jsx
// In your translations
{
  "items": ["{{count}} item", "{{count}} items"]
}

// In your component
const { t } = useTranslation();
t('items', { count: 1 }); // "1 item"
t('items', { count: 5 }); // "5 items"
```

### Q: Can I use Preact Rosetta with class components?
A: Yes, you can use the `withTranslation` HOC for class components:

```jsx
import { withTranslation } from 'preact-rosetta';

class MyComponent extends Component {
  render({ t }) {
    return <h1>{t('hello')}</h1>;
  }
}

export default withTranslation()(MyComponent);
```

### Q: How do I switch languages at runtime?
A: Use the `useLanguageSwitcher` hook:

```jsx
import { useLanguageSwitcher } from 'preact-rosetta';

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

## Performance and Optimization

### Q: How does Preact Rosetta handle large translation sets?
A: Preact Rosetta supports lazy loading of translations. You can load translation namespaces on demand to reduce initial bundle size.

### Q: Is there a way to optimize performance for frequently accessed translations?
A: Yes, Preact Rosetta implements an efficient caching mechanism. Frequently accessed translations are stored in memory for faster retrieval.

### Q: How can I reduce the bundle size when using Preact Rosetta?
A: You can use tree-shaking and only import the features you need. Also, consider lazy loading translations and using code splitting in your application.

## Troubleshooting

### Q: My translations aren't showing up. What should I check?
A: Ensure that:
1. Your translations are correctly formatted in the resources object.
2. You're using the correct keys in your `t()` function calls.
3. The current language is set correctly.
4. The RosettaProvider is wrapping your application correctly.

### Q: I'm getting a "useTranslation must be used within a RosettaProvider" error. How do I fix this?
A: This error occurs when you try to use `useTranslation` in a component that isn't wrapped by the RosettaProvider. Make sure your App component is wrapped with RosettaProvider.

### Q: How can I debug translation issues?
A: You can enable debug mode in Preact Rosetta to get more detailed logging:

```javascript
const rosetta = new RosettaInstance({
  // other options...
  debug: true,
});
```

## Comparison with Other Libraries

### Q: How does Preact Rosetta compare to react-i18next?
A: Preact Rosetta offers similar functionality to react-i18next but is specifically optimized for Preact. It has a smaller bundle size and better performance in Preact applications.

### Q: Can I use Preact Rosetta with React?
A: Preact Rosetta is designed specifically for Preact and is not intended for use with React. For React applications, we recommend using react-i18next or other React-specific i18n libraries.

## Contributing and Community

### Q: How can I contribute to Preact Rosetta?
A: We welcome contributions! Please check our [Contributing Guide](CONTRIBUTING.md) for details on how to submit issues, feature requests, and pull requests.

### Q: Is there a community forum or chat for Preact Rosetta users?
A: Yes, you can join our [GitHub Discussions](https://github.com/your-repo/discussions) to ask questions, share ideas, and connect with other users and contributors.

### Q: How do I report a bug or request a feature?
A: Please use our [GitHub Issues](https://github.com/your-repo/issues) page to report bugs or suggest new features. Be sure to check if a similar issue already exists before creating a new one.
