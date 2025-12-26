# Internationalization (i18n) Guide

**Version:** 1.0  
**Last Updated:** December 26, 2025

## 1. Introduction & Philosophy

The Subscription Management platform is designed for a global audience. This document outlines the technical strategy and procedures for internationalization (i18n), ensuring that the application can be easily and accurately translated into multiple languages. Our philosophy is to use a robust, industry-standard framework and leverage AI to streamline the translation process, making it efficient and scalable.

As an AI agent, you must follow these guidelines for all frontend development to ensure the application is fully translatable.

## 2. Technology Stack

*   **i18n Framework:** **i18next**
*   **React Integration:** **react-i18next**
*   **Language Detection:** `i18next-browser-languagedetector` will be used to automatically detect the user's language from the browser settings.

This stack is chosen for its maturity, extensive features, and strong community support.

## 3. Implementation Strategy

### 3.1. Directory Structure

All translation files will be stored in the `public/locales/{lng}` directory of the frontend application. This structure allows for easy loading of language files.

```
frontend/
└── public/
    └── locales/
        ├── en/
        │   └── translation.json
        └── es/
            └── translation.json
```

### 3.2. Translation File Format

All translations will be stored in JSON format. The structure will be a nested object, allowing for logical grouping of translation keys.

**Example: `en/translation.json`**

```json
{
  "dashboard": {
    "title": "Dashboard",
    "welcome_message": "Welcome back, {{name}}!"
  },
  "subscriptions": {
    "add_new": "Add New Subscription",
    "status": {
      "active": "Active",
      "trial": "Trial"
    }
  },
  "common": {
    "save": "Save",
    "cancel": "Cancel"
  }
}
```

**Example: `es/translation.json`**

```json
{
  "dashboard": {
    "title": "Panel de Control",
    "welcome_message": "¡Bienvenido de nuevo, {{name}}!"
  },
  "subscriptions": {
    "add_new": "Añadir Nueva Suscripción",
    "status": {
      "active": "Activa",
      "trial": "De Prueba"
    }
  },
  "common": {
    "save": "Guardar",
    "cancel": "Cancelar"
  }
}
```

### 3.3. Initial Configuration

You must configure `i18next` in the main `App.tsx` or a dedicated `i18n.ts` file.

**Example: `i18n.ts`**

```typescript
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import HttpApi from 'i18next-http-backend';
import LanguageDetector from 'i18next-browser-languagedetector';

i18n
  .use(HttpApi) // Load translations from /public/locales
  .use(LanguageDetector) // Detect user language
  .use(initReactI18next) // Pass i18n down to react-i18next
  .init({
    fallbackLng: 'en',
    supportedLngs: ['en', 'es'],
    debug: process.env.NODE_ENV === 'development',
    interpolation: {
      escapeValue: false, // React already safes from xss
    },
    backend: {
      loadPath: '/locales/{{lng}}/translation.json',
    },
  });

export default i18n;
```

## 4. Usage in React Components

All user-facing strings **must** be implemented using the `useTranslation` hook from `react-i18next`.

**NEVER hardcode user-facing text in components.**

**Example Component:**

```typescript
import React from 'react';
import { useTranslation } from 'react-i18next';

export function DashboardHeader() {
  const { t } = useTranslation();

  return (
    <div>
      <h1>{t('dashboard.title')}</h1>
      <button>{t('subscriptions.add_new')}</button>
    </div>
  );
}
```

### 4.1. Interpolation

For dynamic values, use interpolation.

```typescript
const { t } = useTranslation();
const userName = 'Pete';

// ...

<p>{t('dashboard.welcome_message', { name: userName })}</p>
```

### 4.2. Plurals

For strings that change based on a count, use the pluralization features of `i18next`.

**JSON:**
```json
{
  "subscriptions_count": "You have {{count}} subscription.",
  "subscriptions_count_plural": "You have {{count}} subscriptions."
}
```

**Component:**
```typescript
const { t } = useTranslation();
const count = 5;

// ...

<p>{t('subscriptions_count', { count })}</p>
```

## 5. Translation Workflow (AI-Assisted)

Adding a new language or updating existing ones will follow an AI-assisted workflow.

### 5.1. Adding a New Language (e.g., French `fr`)

1.  **AI Agent Task:** Create a new directory `public/locales/fr`.
2.  **AI Agent Task:** Copy the `en/translation.json` file to `fr/translation.json`.
3.  **AI Agent Task:** For each key-value pair in `fr/translation.json`, send the English value to a translation LLM with the prompt:
    > "Translate the following English UI text to professional, natural-sounding French. Maintain the interpolation placeholders like `{{name}}`. Text: `{{english_text}}`"
4.  **AI Agent Task:** Replace the English value with the translated French value.
5.  **AI Agent Task:** Add `'fr'` to the `supportedLngs` array in the `i18n.ts` configuration file.
6.  **Human Review:** A human fluent in French should review the AI-generated translations for accuracy, tone, and context.

### 5.2. Updating Translations

When a new key is added to `en/translation.json`:

1.  **CI/CD Pipeline:** A script in the CI/CD pipeline will automatically detect the missing key in other language files (e.g., `es/translation.json`).
2.  **AI Agent Task:** The AI agent will be triggered to perform the translation workflow described above for the missing key across all other languages.
3.  **Human Review:** The new translations will be flagged for human review.

## 6. Best Practices

*   **Keep Keys Consistent:** Use a consistent, nested structure for keys.
*   **Avoid Concatenating Strings:** Do not build sentences by combining translated strings. Use full sentences with interpolation instead.
    *   **Bad:** `<span>{t('common.hello')} {userName}</span>`
    *   **Good:** `<span>{t('common.greeting', { name: userName })}</span>`
*   **Context is Key:** When requesting AI translations, provide context where possible (e.g., "This text is for a button," "This is a title on the dashboard").
*   **Dates, Numbers, and Currencies:** Use a library like `Intl.DateTimeFormat`, `Intl.NumberFormat`, and `Intl.NumberFormat` with the `currency` option to format these values correctly for the user's locale. Do not handle this manually.

    manually.

```typescript
const { i18n } = useTranslation();
const price = 15.99;

const formattedPrice = new Intl.NumberFormat(i18n.language, {
  style: 'currency',
  currency: 'USD' // This should come from the subscription data
}).format(price);

// Renders as $15.99 in 'en', 15,99 $ in 'es'
```
