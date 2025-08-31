---
title: ESLint Format on Save
---

This guide will help you migrate from automatic code formatting with Prettier (defined in `.prettierrc` or `prettier.config.js`) to automatic formatting with ESLint.

- **Part 1** covers migrating your Prettier settings into ESLint.
- **Part 2** shows how to configure VS Code to format on save using ESLint.

## Part 1: Migrating Prettier Rules to ESLint

### Install Required Packages

Before configuring ESLint for formatting, ensure you have the necessary packages installed. Run these commands in your project's root directory:

```bash
npm install eslint prettier eslint-plugin-prettier eslint-config-prettier --save-dev
```

**Package breakdown:**
- [ESLint](https://www.npmjs.com/package/eslint)
- [Prettier](https://www.npmjs.com/package/prettier)
- [eslint-plugin-prettier](https://www.npmjs.com/package/eslint-plugin-prettier) Integrates Prettier with ESLint 
- [eslint-config-prettier](https://www.npmjs.com/package/eslint-config-prettier) Disables ESLint rules that conflict with Prettier

*NOTE: We need both eslint-plugin-prettier and eslint-config-prettier. The difference between them is that the eslint-config-prettier turns off all rules that are 
unnecessary or might conflict with Prettier and the eslint-plugin-prettier will allow us to add prettier configuration to eslint rules in `eslintrc.json`.*

### Create or Update .eslintrc.json

To migrate your rules from `.prettierrc`, add them to your ESLint configuration:

```json
{
  "plugins": ["prettier"],
  "extends": ["prettier"],
  "rules": {
    "prettier/prettier": [
      "error",
      {
        "singleQuote": true,
        "endOfLine": "auto",
        "tabWidth": 2,
        "quoteProps": "consistent"
      }
    ]
  }
}
```
[Here's a list of all available prettier options](https://prettier.io/docs/en/options)

**Available Prettier options:** [Complete list of Prettier configuration options](https://prettier.io/docs/en/options)

**Alternative: Use Prettier's recommended settings:**

```json
{
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "extends": ["plugin:prettier/recommended"]
}
```

**Note:** The `eslintrc.json` file allows you to omit the "eslint-config-" and "eslint-plugin-" prefixes from package names, which is why "prettier" appears in both the `plugins` and `extends` properties.

### Remove Prettier Configuration

Once you've migrated your Prettier rules to ESLint, you can remove your separate `.prettierrc` or `prettier.config.js` file, as Prettier rules will now be handled by ESLint.

## Part 2: Configure VSCode for Format on Save

### Install Required Extensions

Ensure you have the [ESLint extension for VSCode](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) installed.

### Configure VSCode Settings

To enable ESLint formatting on save, add these settings to your VSCode `settings.json`:

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "dbaeumer.vscode-eslint"
}
```

**Settings explanation:**
- **`"editor.codeActionsOnSave"`**: Enables ESLint to automatically fix issues on save
- **`"editor.formatOnSave"`**: Enables automatic formatting when saving files
- **`"editor.defaultFormatter"`**: Sets ESLint as the default formatter

**Important:** If you have a `.vscode` folder with project-specific settings, those will override your global VSCode settings.

### Apply Changes

After making these changes, reload or restart Visual Studio Code for the settings to take effect.

## Result 🏁

Now, when you save a JavaScript or TypeScript file in your project, ESLint will enforce Prettier formatting rules as defined in your ESLint configuration, along with all other ESLint rules you have configured.

## Troubleshooting

If formatting isn't working as expected:

1. **Verify ESLint extension is active** in VSCode
2. **Check your ESLint configuration** for syntax errors
3. **Ensure all required packages** are installed
4. **Restart VSCode** after configuration changes
5. **Check the VSCode output panel** for ESLint errors

## Summary

This setup provides a unified approach to code formatting and linting, eliminating the need for separate Prettier configuration while maintaining all formatting capabilities through ESLint.

If you're interested I have a post about my own personal [[My EsLint Config]]