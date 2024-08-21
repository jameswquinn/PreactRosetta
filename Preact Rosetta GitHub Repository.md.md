# Preact Rosetta GitHub Repository

Here's the complete directory structure and file contents for the Preact Rosetta project:

```
preact-rosetta/
├── .github/
│   └── workflows/
│       └── ci.yml
├── src/
│   ├── components/
│   │   └── Trans.tsx
│   ├── hooks/
│   │   ├── useTranslation.ts
│   │   └── useLanguageSwitcher.ts
│   ├── pluralization/
│   │   ├── index.ts
│   │   ├── types.ts
│   │   ├── PluralRuleRegistry.ts
│   │   └── rules/
│   │       ├── english.ts
│   │       └── french.ts
│   ├── RosettaInstance.ts
│   └── index.ts
├── tests/
│   ├── pluralization.test.ts
│   └── translation.test.ts
├── examples/
│   └── basic-usage.tsx
├── .gitignore
├── .npmignore
├── .eslintrc.js
├── .prettierrc
├── tsconfig.json
├── jest.config.js
├── rollup.config.js
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE
├── package.json
└── README.md
```

Now, let's go through the content of each file:

1. `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm ci
    - run: npm run build
    - run: npm test
```

2. `src/components/Trans.tsx`:

```typescript
import { h, Fragment, VNode } from 'preact';
import { useTranslation } from '../hooks/useTranslation';

interface TransProps {
  i18nKey: string;
  values?: Record<string, any>;
  components?: VNode[];
}

export function Trans({ i18nKey, values = {}, components = [] }: TransProps) {
  const { t } = useTranslation();
  const translated = t(i18nKey, { ...values, returnObjects: true });

  if (typeof translated === 'string') {
    return <Fragment>{translated}</Fragment>;
  }

  return (
    <Fragment>
      {translated.map((item, index) => {
        if (typeof item === 'string') {
          return item;
        }
        const Component = components[index] || Fragment;
        return <Component key={index}>{item.children}</Component>;
      })}
    </Fragment>
  );
}
```

3. `src/hooks/useTranslation.ts`:

```typescript
import { useContext } from 'preact/hooks';
import { RosettaContext } from '../RosettaInstance';

export function useTranslation(namespace?: string) {
  const rosetta = useContext(RosettaContext);
  if (!rosetta) throw new Error('useTranslation must be used within a RosettaProvider');

  const t = (key: string, options?: object) => {
    const fullKey = namespace ? `${namespace}:${key}` : key;
    return rosetta.t(fullKey, options);
  };

  return { t, rosetta };
}
```

4. `src/hooks/useLanguageSwitcher.ts`:

```typescript
import { useTranslation } from './useTranslation';
import { effect } from "@preact/signals";

export function useLanguageSwitcher() {
  const { rosetta } = useTranslation();
  const language = rosetta.currentLang;

  effect(() => {
    document.documentElement.lang = language.value;
  });

  return { language: language.value, changeLanguage: rosetta.changeLanguage.bind(rosetta) };
}
```

5. `src/pluralization/index.ts`:

```typescript
export * from './types';
export * from './PluralRuleRegistry';
export * from './rules/english';
export * from './rules/french';
```

6. `src/pluralization/types.ts`:

```typescript
export interface PluralRule {
  language: string;
  pluralForms: string[];
  selectPlural: (count: number) => number;
}
```

7. `src/pluralization/PluralRuleRegistry.ts`:

```typescript
import { PluralRule } from './types';

export class PluralRuleRegistry {
  private rules: Map<string, PluralRule> = new Map();

  addRule(rule: PluralRule): void {
    this.rules.set(rule.language, rule);
  }

  getRule(language: string): PluralRule | undefined {
    return this.rules.get(language);
  }
}
```

8. `src/pluralization/rules/english.ts`:

```typescript
import { PluralRule } from '../types';

export const englishPluralRule: PluralRule = {
  language: 'en',
  pluralForms: ['one', 'other'],
  selectPlural: (count: number) => (count === 1 ? 0 : 1),
};
```

9. `src/pluralization/rules/french.ts`:

```typescript
import { PluralRule } from '../types';

export const frenchPluralRule: PluralRule = {
  language: 'fr',
  pluralForms: ['one', 'other'],
  selectPlural: (count: number) => (count === 0 || count === 1 ? 0 : 1),
};
```

10. `src/RosettaInstance.ts`:

```typescript
import { createContext } from 'preact';
import { signal } from '@preact/signals';
import { PluralRuleRegistry, PluralRule } from './pluralization';

export interface RosettaOptions {
  lng: string;
  fallbackLng: string;
  resources: Record<string, Record<string, Record<string, string>>>;
}

export class RosettaInstance {
  currentLang = signal<string>('');
  private fallbackLang: string;
  private resources: Record<string, Record<string, Record<string, string>>>;
  private pluralRuleRegistry: PluralRuleRegistry;

  constructor(options: RosettaOptions) {
    this.currentLang.value = options.lng;
    this.fallbackLang = options.fallbackLng;
    this.resources = options.resources;
    this.pluralRuleRegistry = new PluralRuleRegistry();
  }

  t(key: string, options?: { count?: number; [key: string]: any }): string {
    // Implementation as before
  }

  changeLanguage(lang: string): void {
    this.currentLang.value = lang;
  }

  // Other methods...
}

export const RosettaContext = createContext<RosettaInstance | null>(null);
```

11. `src/index.ts`:

```typescript
export * from './RosettaInstance';
export * from './components/Trans';
export * from './hooks/useTranslation';
export * from './hooks/useLanguageSwitcher';
export * from './pluralization';
```

12. `tests/pluralization.test.ts`:

```typescript
import { RosettaInstance } from '../src/RosettaInstance';
import { englishPluralRule, frenchPluralRule } from '../src/pluralization';

describe('Pluralization', () => {
  // Tests as implemented before
});
```

13. `tests/translation.test.ts`:

```typescript
import { RosettaInstance } from '../src/RosettaInstance';

describe('Translation', () => {
  let rosetta: RosettaInstance;

  beforeEach(() => {
    rosetta = new RosettaInstance({
      lng: 'en',
      fallbackLng: 'en',
      resources: {
        en: {
          translation: {
            hello: 'Hello, {{name}}!',
          },
        },
        fr: {
          translation: {
            hello: 'Bonjour, {{name}} !',
          },
        },
      },
    });
  });

  test('Basic translation', () => {
    expect(rosetta.t('hello', { name: 'World' })).toBe('Hello, World!');
  });

  test('Language switching', () => {
    rosetta.changeLanguage('fr');
    expect(rosetta.t('hello', { name: 'Monde' })).toBe('Bonjour, Monde !');
  });
});
```

14. `examples/basic-usage.tsx`:

```tsx
import { h } from 'preact';
import { RosettaProvider, useTranslation, useLanguageSwitcher, RosettaInstance } from 'preact-rosetta';

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
    fr: {
      translation: {
        hello: 'Bonjour, {{name}} !',
        items: ['{{count}} élément', '{{count}} éléments'],
      },
    },
  },
});

function App() {
  return (
    <RosettaProvider rosetta={rosetta}>
      <Header />
      <LanguageSwitcher />
      <Content />
    </RosettaProvider>
  );
}

function Header() {
  const { t } = useTranslation();
  return <h1>{t('hello', { name: 'World' })}</h1>;
}

function LanguageSwitcher() {
  const { language, changeLanguage } = useLanguageSwitcher();
  return (
    <select value={language} onChange={(e) => changeLanguage(e.target.value)}>
      <option value="en">English</option>
      <option value="fr">Français</option>
    </select>
  );
}

function Content() {
  const { t } = useTranslation();
  return (
    <div>
      <p>{t('items', { count: 1 })}</p>
      <p>{t('items', { count: 5 })}</p>
    </div>
  );
}

export default App;
```

15. `.gitignore`:

```
node_modules/
dist/
coverage/
.env
*.log
```

16. `.npmignore`:

```
src/
tests/
examples/
.github/
.gitignore
.eslintrc.js
.prettierrc
tsconfig.json
jest.config.js
rollup.config.js
```

17. `.eslintrc.js`:

```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  settings: {
    react: {
      pragma: 'h',
      version: 'detect',
    },
  },
  rules: {
    // Add custom rules here
  },
};
```

18. `.prettierrc`:

```json
{
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": true,
  "printWidth": 100
}
```

19. `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "esnext",
    "lib": ["dom", "esnext"],
    "importHelpers": true,
    "declaration": true,
    "sourceMap": true,
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "moduleResolution": "node",
    "baseUrl": "./",
    "paths": {
      "*": ["src/*", "node_modules/*"]
    },
    "jsx": "react",
    "jsxFactory": "h",
    "esModuleInterop": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

20. `jest.config.js`:

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  testPathIgnorePatterns: ['/node_modules/', '/dist/'],
  collectCoverageFrom: ['src/**/*.{ts,tsx}'],
  moduleNameMapper: {
    '^preact$': '<rootDir>/node_modules/preact/dist/preact.js',
  },
  globals: {
    'ts-jest': {
      tsconfig: 'tsconfig.json',
    },
  },
};
```

21. `rollup.config.js`:

```javascript
import typescript from 'rollup-plugin-typescript2';
import commonjs from '@rollup/plugin-commonjs';
import resolve from '@rollup/plugin-node-resolve';
import { terser } from 'rollup-plugin-terser';
import pkg from './package.json';

export default {
  input: 'src/index.ts',
  output: [
    {
      file: pkg.main,
      format: 'cjs',
      exports: 'named',
      sourcemap: true,
    },
    {
      file: pkg.module,
      format: 'es',
      exports: 'named',
      sourcemap: true,
    },
  ],
  plugins: [
    typescript({
      tsconfig: 'tsconfig.json',
      clean: true,
    }),
    resolve(),
    commonjs(),
    terser(),
  ],
  external: ['preact', '@preact/signals'],
};
```

22. `CHANGELOG.md`:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- Initial implementation of Preact Rosetta
- Basic pluralization support for English and French
- Core translation functionality
- Hooks for functional components
- Basic documentation and examples

## [0.1.0] - 2023-08-21
- Initial release
```

23. `CODE_OF_CONDUCT.md`:

```markdown
# Contributor Covenant Code of Conduct

## Our Pledge

In the interest of fostering an open and welcoming environment, we as contributors and maintainers pledge to making participation in our project and our community a harassment-free experience for everyone, regardless of age, body size, disability, ethnicity, sex characteristics, gender identity
