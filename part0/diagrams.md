# Part 0 diagrams

## 0.4: New note diagram

Creating a new note on the traditional (non-SPA) page at
`https://studies.cs.helsinki.fi/exampleapp/notes`

```mermaid
sequenceDiagram
    participant browser
    participant server

    Note right of browser: User writes a note into the text field and clicks Save

    browser->>server: POST https://studies.cs.helsinki.fi/exampleapp/new_note
    activate server
    Note right of server: Server adds the new note to the notes array
    server-->>browser: 302 Found, redirect to /exampleapp/notes
    deactivate server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/notes
    activate server
    server-->>browser: HTML document
    deactivate server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/main.css
    activate server
    server-->>browser: the css file
    deactivate server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/main.js
    activate server
    server-->>browser: the JavaScript file
    deactivate server

    Note right of browser: The browser starts executing the JavaScript code that fetches the JSON from the server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/data.json
    activate server
    server-->>browser: [{ "content": "HTML is easy", "date": "2023-1-1" }, ..., { "content": "the new note", "date": "2023-1-1" }]
    deactivate server

    Note right of browser: The browser executes the callback function that renders the notes, now including the new one
```

Note: clicking Save submits a plain HTML form, so the browser does a full page reload. The whole GET-notes/GET-css/GET-js/GET-data.json cycle from the first diagram happens again after the redirect.

## 0.5: Single page app diagram

Loading the SPA version at `https://studies.cs.helsinki.fi/exampleapp/spa`

```mermaid
sequenceDiagram
    participant browser
    participant server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/spa
    activate server
    server-->>browser: HTML document
    deactivate server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/main.css
    activate server
    server-->>browser: the css file
    deactivate server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/spa.js
    activate server
    server-->>browser: the JavaScript file
    deactivate server

    Note right of browser: The browser starts executing the JavaScript code that requests the JSON data from the server

    browser->>server: GET https://studies.cs.helsinki.fi/exampleapp/data.json
    activate server
    server-->>browser: [{ "content": "HTML is easy", "date": "2023-1-1" }, ... ]
    deactivate server

    Note right of browser: The browser executes the callback function that renders the notes to the page
```

Note: unlike the multi-page version, the HTML returned here is (almost) empty — it just references spa.js. All rendering happens in the browser via JavaScript, with no further full-page reloads.

## 0.6: New note in Single page app diagram

Creating a new note in the SPA version

```mermaid
sequenceDiagram
    participant browser
    participant server

    Note right of browser: User writes a note into the text field and clicks Save

    Note right of browser: The button's event handler prevents the default form submission,<br/>adds the new note to the notes list, and rerenders the page.<br/>The note appears on the page immediately, before the server has responded.

    browser->>server: POST https://studies.cs.helsinki.fi/exampleapp/new_note_spa
    activate server
    Note left of browser: Payload: { content: "the new note", date: "2023-1-1" } as JSON
    Note right of server: Server adds the new note to the notes array
    server-->>browser: 201 Created
    deactivate server
```

Note: the request is sent asynchronously (via fetch/XHR) with a JSON body instead of a form submission, and the server's response is just a status code — no HTML and no redirect, since the browser already updated the page itself.
