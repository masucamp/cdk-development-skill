# Linting for CDK Projects

## Run ESLint

```bash
npx eslint --fix lib/ bin/
```

## eslint-plugin-awscdk Setup

```bash
npm install -D eslint-plugin-awscdk typescript-eslint
```

```javascript
// eslint.config.mjs
import eslint from "@eslint/js";
import { defineConfig } from "eslint/config";
import tseslint from "typescript-eslint";
import cdkPlugin from "eslint-plugin-awscdk";

export default defineConfig([
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  {
    files: ["lib/**/*.ts", "bin/*.ts"],
    extends: [cdkPlugin.configs.recommended],
  },
]);
```

## Key CDK Rules

| Rule | Purpose |
|------|---------|
| `no-mutable-property-of-props-interface` | Props must be `readonly` |
| `no-construct-stack-suffix` | No redundant suffixes |
| `pascal-case-construct-id` | Construct IDs in PascalCase |
| `no-variable-construct-id` | Static Construct IDs |
| `require-passing-this` | Pass `this` in constructs |
| `prevent-construct-id-collision` | No duplicate IDs in scope |
| `no-construct-in-interface` | No Construct types in interfaces |
