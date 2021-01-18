# Instructions for using bd-forms in a 3rd party website

## Introduction

This web component will provide the Automatic profit declaration plugin by the Belastingdienst. The plugin will calculate values from a provided RGS ledger, show forms to complete data, and generate a XML file to upload to the Belastingdienst. **This implementation is a work in progress and not final.**

## Setup the webcomponent

1. Add the reference to the CSS file in the head tag:

```html
<head>
    ...
    <link href=/css/app.css rel=stylesheet></head>
    ...
</head>
```

2. Add the HTML tags in the body:

```html
<body>
  ...
  <bd-forms></bd-forms>
  ...
</body>
```

3. Add the webcomponent settings ([settings](#Settings)):

```html
<body>
  ...
  <bd-forms></bd-forms>
  <script>
    // Get the webcomponent element
    const bdFormsElement = document.getElementsByTagName("bd-forms")[0];

    // Specific settings
    ...
  </script>
  ...
</body>
```

4. Underneath, add the script tag that refers to the JavaScript file:

```html
<body>
  ...
  <bd-forms></bd-forms>
  <script>
    ...
  </script>
  <script src="js/app.js"></script>
  ...
</body>
```

## Settings

### Requried

**Required settings are:**

- getRGSLedger (function)
- getECG
- saveCurrentState (function)
- getCurrentState (function)
- close (function)

### How do I deliver the RGS ledger XML content to the plugin

To start the plugin, the RGS ledger is required. You can set an async or sync function on the `getRGSLedger` property on the webcomponent. The return value need to be a string (or Promise\<string\>) with the XML content of the ledger. Implement it like:

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  // async
  bdFormEl.getRGSLedger = function () {
    return new Promise((resolve) => {
      // Take some async actions like a XHR Request.
      // Resolve the XML string
      resolve(`Ledger XML content string`);
    });
  };

  // sync
  bdFormEl.getRGSLedger = function () {
    return `Ledger XML content string`;
  };
</script>
```

**XHR request example with axios**

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  bdFormEl.getRGSLedger = function () {
    return new Promise((resolve) => {
      axios
        .get("/rgs-ledger.xml", {
          headers: {
            Authorization: "Bearer ${window.userToken}",
          },
        })
        .then((response) => resolve(response.data))
        .catch(console.error);
    });
  };
</script>
```

### How do I deliver the RGS ledger XML content to the plugin

To start the plugin, the ECG XML document is required. You can set an async or sync function on the `getECG` property on the webcomponent. The return value need to be a string (or Promise\<string\>) with the XML content of the ECG XML document. Implement it like:

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  // async
  bdFormEl.getECG = function () {
    return new Promise((resolve) => {
      // Take some async actions like a XHR Request.
      // Resolve the XML string
      resolve(`ECG XML content string`);
    });
  };

  // sync
  bdFormEl.getECG = function () {
    return `ECG XML document content string`;
  };
</script>
```

### How do I define a current state save function

The plugin will call a defined function to save the current state for the user. You have to define this function on the `saveCurrentState` property of the web component.

**This function will be called when:**

- The user clicks on the save button in the plugin.
- The user clicks on the `next` button within a form, when the form data is valid, it will save the values of the form and calls `saveCurrentState`.
- The user removes the filled data in the intro screen.
- The user clicks on the download screen to download the XML file.
- The user selects "yes" at the intro screen on question: `Have you sent the declaration in the tax authorities portal?`.

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  /**
   * Set the save function
   *
   * @param string currentState: JSON stringified representation of the current state.
   * @return void
   */
  bdFormsElement.saveCurrentState = function (currentState) {
    // Do a XHR request to your server with the currentState data

    console.log("currentState", currentState);
  };
</script>
```

### How do I define a current state save function

The plugin will call a defined function to save the current state for the user. You have to define this function on the `saveCurrentState` property of the web component.

**This function will be called when:**

- The user clicks on the save button in the plugin.
- The user clicks on the `next` button within a form, when the form data is valid, it will save the values of the form and calls `saveCurrentState`.
- The user removes the filled data in the intro screen.
- The user clicks on the download screen to download the XML file.
- The user selects "yes" at the intro screen on question: `Have you sent the declaration in the tax authorities portal?`.

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  /**
   * Set the save function
   *
   * @param string currentState: JSON stringified representation of the current state.
   * @return void
   */
  bdFormsElement.saveCurrentState = function (currentState) {
    // Do a XHR request to your server with the currentState data

    console.log("currentState", currentState);
  };
</script>
```

### How do I deliver the current state to the plugin

The plugin will call a defined function to get the current state for the user. You have to define this function on the `getCurrentState` property of the web component.
You can set an async or sync function. The return value need to be a string (or Promise\<string\>) with the JSON stringified representation of the current state. If there is no current state set, you can return a empty string. Implement it like:

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  // async
  bdFormEl.getCurrentState = function () {
    return new Promise((resolve) => {
      // Take some async actions like a XHR Request.
      // Resolve the currentState string.
      resolve(`JSON stringified currentState...`);
    });
  };

  // sync
  bdFormEl.getCurrentState = function () {
    return `JSON stringified currentState...`;
  };
</script>
```

### How do I define a close function

The plugin will call a defined function when the user wants to close the plugin. This function is required and can be set on the `close` property of the web component.

```html
<script>
  const bdFormEl = document.getElementsByTagName("bd-forms")[0];

  // Function with redirect example
  bdFormsElement.close = function () {
    window.location.href = "http://google.com";
    // You can also call a function to close a modal or hide a wrapper around the bd forms element.
  };
</script>
```
