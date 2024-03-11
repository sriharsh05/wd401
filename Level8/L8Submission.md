# Dynamic Content Translation

## Internationalization and Localization

Internationalization (i18n) and localization (l10n) are essential for creating a multilingual user experience. This involves adapting the application to different languages, regions, and cultures to ensure that users from diverse backgrounds can use the application effectively.

## Steps to Implement Dynamic Content Translation

1. **Create Language Files**: Develop language files or dictionaries that store translations for different languages. These files should contain key-value pairs for each translation, allowing the application to look up the appropriate translation based on the user's language preference.

#### Example Language File (Espaniol)

```json
{
    "translation":{
      "Live and completed matches": "Partidos en vivo y completados",
        "Sports Articles" : "Artículos deportivos",
        "Favourites" : "Favoritos",
        "Select sport" : "Seleccionar deporte",
        "Select Team" : "Seleccionar equipo"
    }
}
```

2. **Integrate Language Switching**: Implement a mechanism to dynamically switch between languages based on user preferences. This can involve providing a language selection menu.

#### Example Language Selection Menu

```tsx
const Home = () => {
  const storedLanguage = localStorage.getItem("selectedLanguage");

  const [selectedLanguage, setSelectedLanguage] = useState(initialLanguage);

  const languageOptions = [
    { value: "en", label: "English" },
    { value: "es", label: "Español" },
    { value: "fr", label: "Français" },
    { value: "de", label: "Deutsch" },
  ];

  return (
      <div className="flex items-center mb-4">
        <label className="mr-2">Select Language:</label>
        <select
          value={selectedLanguage}
          onChange={(e) => setSelectedLanguage(e.target.value)}
        >
          {languageOptions.map((option) => (
            <option key={option.value} value={option.value}>
              {option.label}
            </option>
          ))}
        </select>
      </div>

     // Rest of the code
  );
};

export default Home;

```

3. **Apply Translation to Components**: Apply translation to various components, text elements, and messages throughout the application.

#### Example Translation in React Component

```tsx

const { t, i18n: { changeLanguage } } = useTranslation();

  useEffect(() => {
    changeLanguage(selectedLanguage);
  }, [selectedLanguage]);

  return(
    <div className="mt-4 ml-2 p-2">
      <h2 className="text-xl font-bold font-custom">{t('Favourites')}</h2>
    </div> 
  )
```

# Date and Time Localization

## Localizing Date and Time Formats

Localizing date and time formats is crucial for accommodating different cultural preferences and regional standards. This involves adapting the presentation of dates and times to align with the conventions of the user's locale.

## Steps to Localize Date and Time Formats

1. **Identify User's Locale**: Determine the user's locale or language preference to understand the appropriate date and time formats for their region. This can be based on user input, system settings, or browser language settings.

2. **Utilize Intl.DateTimeFormat**: Use the `Intl.DateTimeFormat` object to format dates and times according to the user's locale. This built-in object provides a way to format dates and times based on the conventions of a specific locale.

#### Example Date and Time Formatting

```tsx
const dateFormatter = new Intl.DateTimeFormat(langString, {
    year: "numeric",
    month: "long",
    day: "numeric",
  });


const timeFormatter = new Intl.DateTimeFormat(langString, {
  hour: "numeric",
  minute: "numeric",
  second: "numeric",
});

const currencyFormatter = new Intl.NumberFormat(langString, {
  style: "currency",
  currency: "JPY", 
});
  
  const formattedDate = dateFormatter.format(date); 
  const formattedTime = timeFormatter.format(date); 
  const formattedCurrency = currencyFormatter.format(currencyValue);

  return(
    <div>
           <h3 className="text-l font-bold font-custom">
            Date : {formattedDate}
            Time : {formattedTime}
            Currency : {formattedCurrency} </h3>
    </div>
  )
  ```

# Locale Switching

## Enabling User Locale Selection

Allowing users to select their preferred language/locale is essential for creating a personalized and inclusive user experience. By enabling locale switching, users can interact with the application in their preferred language, enhancing accessibility and usability.

## Steps to Implement Locale Switching

1. **Create Language Selection UI**: Develop a user interface (UI) component or a settings panel that allows users to select their preferred language/locale. This can involve using dropdown menus, radio buttons, or other interactive elements for language selection.

#### Example Language Selection UI

```tsx
const Home = () => {
  const storedLanguage = localStorage.getItem("selectedLanguage");

  const [selectedLanguage, setSelectedLanguage] = useState(initialLanguage);

  const languageOptions = [
    { value: "en", label: "English" },
    { value: "es", label: "Español" },
    { value: "fr", label: "Français" },
    { value: "de", label: "Deutsch" },
  ];

  return (
      <div className="flex items-center mb-4">
        <label className="mr-2">Select Language:</label>
        <select
          value={selectedLanguage}
          onChange={(e) => setSelectedLanguage(e.target.value)}
        >
          {languageOptions.map((option) => (
            <option key={option.value} value={option.value}>
              {option.label}
            </option>
          ))}
        </select>
      </div>

     // Rest of the code
  );
};

export default Home;
```

2. **Dynamic Locale Switching**: Enable the application to respond dynamically to locale changes, updating content and formatting according to the selected locale. 

#### Example Dynamic Locale Switching

```tsx

const { t, i18n: { changeLanguage } } = useTranslation();

  useEffect(() => {
    changeLanguage(selectedLanguage);
  }, [selectedLanguage]);

  return(
    <div className="mt-4 ml-2 p-2">
      <h2 className="text-xl font-bold font-custom">{t('Favourites')}</h2>
    </div> 
  )
```

3. **Persistent Locale Switching**: Ensure that the locale switch is persistent across sessions, providing a smooth and personalized experience for users who want to interact with the application in their preferred language. This can involve storing the selected language in local storage or user preferences.

#### Example Persistent Locale Switching

```tsx
import React, { useState, useEffect } from "react";


const Home = () => {
  const storedLanguage = localStorage.getItem("selectedLanguage");
  const initialLanguage = storedLanguage || "en";

  const [selectedLanguage, setSelectedLanguage] = useState(initialLanguage);

  const languageOptions = [
    { value: "en", label: "English" },
    { value: "es", label: "Español" },
    { value: "fr", label: "Français" },
    { value: "de", label: "Deutsch" },
  ];

  useEffect(() => {
    localStorage.setItem("selectedLanguage", selectedLanguage);
  }, [selectedLanguage]);

  return (
    <main className="flex flex-col">
      <div className="flex items-center mb-4">
        <label className="mr-2">Select Language:</label>
        <select
          value={selectedLanguage}
          onChange={(e) => setSelectedLanguage(e.target.value)}
        >
          {languageOptions.map((option) => (
            <option key={option.value} value={option.value}>
              {option.label}
            </option>
          ))}
        </select>
        </div>
      <div>
    </main>
  );
};

export default Home;

```

## Video demonstration

https://drive.google.com/file/d/1WtApZ-v4QOxdUGous6OSwpWxLopvZvGc/view?usp=sharing
