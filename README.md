

# Dictionary Lookup Web App

A single-page **Dictionary Lookup Web App** built with **HTML, CSS, and JavaScript**. This app allows users to search for words, view their definitions, hear pronunciations, copy definitions, and even look up any word in the results by double-clicking it.

It uses the free and public **[DictionaryAPI.dev](https://dictionaryapi.dev/)**.

---

## Live Demo

**[Click to VIew Live Page](https://fitandfine.github.io/dictionary/)**

---

## Features

* **Search words** via input or pressing Enter.
* **View definitions and examples** in a clean, modern card layout.
* **Pronunciation support**:

  * Audio from the API (speaker icon).
  * Text-to-speech fallback for all words.
* **Copy definitions** to clipboard.
* **Double-click any word** in the results to look it up instantly.
* **Clear results** with a single button.
* **About tab** with instructions for users.
* Fully **responsive and mobile-friendly** design.

---

## How to Use

1. Enter a word in the input field.
2. Click **Search** or press **Enter**.
3. Click the **speaker icon** to hear pronunciation.
4. Click **Copy** to copy a definition.
5. Double-click any word in the results to search it.
6. Click **Clear** to reset the search.
7. Switch to the **About** tab for instructions.

---

## Project Structure

This project consists of a single `index.html` file containing:

* **HTML**: Page structure, search input, results container, about section.
* **CSS**: Modern layout, card styles, buttons, responsiveness.
* **JavaScript**: API calls, dynamic rendering, audio features, double-click word lookup.

---

## Detailed Code Explanation

Below is a detailed explanation of how the code works.

---

### 1. HTML Structure

```html
<div class="container">
  <h1>Dictionary Lookup</h1>
  <div class="tab-buttons">
    <button id="searchTab" class="active" onclick="showTab('search')">Search</button>
    <button id="aboutTab" onclick="showTab('about')">About</button>
  </div>
  ...
</div>
```

* `<div class="container">`: The main wrapper for the page content, styled with CSS to have rounded corners, padding, and shadow.
* `<h1>`: Page title.
* `.tab-buttons`: Two buttons to switch between **Search** and **About** tabs.
* `onclick="showTab('search')"`: Calls the **JavaScript function** `showTab` to toggle visibility.

---

### 2. About Section

```html
<div id="about" class="about">
  <p><strong>How to Use:</strong></p>
  <ul>
    <li>Type a word in the input box and click "Search" or press Enter.</li>
    <li>Click the speaker icon to hear pronunciation.</li>
    <li>Click "Copy" to copy a definition to clipboard.</li>
    <li>Double-click any word in the results to look it up immediately.</li>
    <li>Click "Clear" to reset the search.</li>
  </ul>
</div>
```

* Hidden by default (`display: none`).
* Visible when the **About tab** is selected.
* Provides instructions for users.

---

### 3. Search Section

```html
<div id="searchSection">
  <div class="search-box">
    <input type="text" id="wordInput" placeholder="Enter a word..."/>
    <button onclick="lookupWord()">Search</button>
  </div>
  <div class="clear-btn-container">
    <button class="clear-btn" onclick="clearResults()">Clear</button>
  </div>
  <div id="results"></div>
</div>
```

* `#wordInput`: Text input for the word to search.
* **Search button**: Triggers `lookupWord()` function.
* **Clear button**: Calls `clearResults()` to reset input and results.
* `#results`: Container where API results (definitions, examples, speaker icons) are dynamically rendered.

---

### 4. JavaScript Functions

#### a) `showTab(tab)`

```javascript
function showTab(tab) {
  if(tab==='search'){
    searchSection.style.display='block';
    aboutSection.classList.remove('visible');
  } else {
    searchSection.style.display='none';
    aboutSection.classList.add('visible');
  }
}
```

* **Purpose**: Switches between Search and About tabs.
* `style.display` toggles visibility.
* `classList.add/remove('visible')` handles About section display.

**Alternate use cases**: Can be used for **multi-tab interfaces**, **single-page apps**, or **conditional displays**.

---

#### b) `lookupWord(wordParam)`

```javascript
async function lookupWord(wordParam){
  const inputWord = wordParam || document.getElementById("wordInput").value.trim();
  const resultsDiv = document.getElementById("results");
  resultsDiv.innerHTML="";
  if(!inputWord){ resultsDiv.innerHTML="<p class='error'>Please enter a word.</p>"; return; }
  try {
    const res = await fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${inputWord}`);
    if(!res.ok) throw new Error("Word not found");
    const data = await res.json();
    const entry = data[0];
    ...
  } catch(err){
    resultsDiv.innerHTML=`<p class="error">${err.message}</p>`;
  }
}
```

* **`async/await`**: Handles asynchronous API call cleanly.
* `fetch(url)`: Makes a GET request to DictionaryAPI.
* `res.ok`: Checks if the HTTP response was successful.
* `res.json()`: Parses JSON response.
* **Error handling**: Catches network or API errors and displays user-friendly messages.

**Alternate uses**: Any external API fetch, like weather, news, or stock prices.

---

#### c) Rendering definitions and examples

```javascript
entry.meanings.forEach(meaning=>{
  meaning.definitions.forEach(def=>{
    const defText = def.definition;
    const exampleText = def.example || "";
    const defHtml = defText.split(" ").map(word=>`<span class="word-span" ondblclick="lookupWord('${word.replace(/'/g,"\\'")}')">${word}</span>`).join(" ");
    const exampleHtml = exampleText.split(" ").map(word=>`<span class="word-span" ondblclick="lookupWord('${word.replace(/'/g,"\\'")}')">${word}</span>`).join(" ");
    html += `<div class="definition">
      <button class="copy-btn" onclick="copyText(\`${defText.replace(/`/g,"\\`")}\`)">Copy</button>
      <h3>${meaning.partOfSpeech}</h3>
      <p><strong>Definition:</strong> ${defHtml}</p>
      ${exampleText?`<p><strong>Example:</strong> ${exampleHtml}</p>`:""}
    </div>`;
  });
});
```

* **`forEach`**: Iterates over meanings and definitions.
* Splits each word in definitions and examples into `<span>` tags with **double-click lookup**.
* `replace(/'/g,"\\'")`: Escapes single quotes to avoid breaking JS strings.
* Dynamically builds HTML string and inserts it into `#results`.

**Alternate uses**: Dynamic rendering of **search results**, **chat messages**, or **interactive lists**.

---

#### d) `copyText(text)`

```javascript
function copyText(text){
  navigator.clipboard.writeText(text).then(()=>{ alert("Copied to clipboard!"); });
}
```

* Uses **Clipboard API** to copy text programmatically.
* `then()`: Executes callback when copy succeeds.

**Alternate uses**: Copy **URLs, code snippets, or credentials** to clipboard.

---

#### e) `playAudio(url)` and `speakText(text)`

```javascript
function playAudio(url){ const audio = new Audio(url); audio.play(); }

function speakText(text){
  if('speechSynthesis' in window){
    const utter = new SpeechSynthesisUtterance(text);
    window.speechSynthesis.speak(utter);
  } else alert("Text-to-Speech not supported.");
}
```

* `Audio(url)`: Plays external audio (from API).
* `SpeechSynthesisUtterance(text)`: Browser-native text-to-speech.
* Fallback ensures pronunciation always works.

**Alternate uses**: Reading out **notifications, articles, or messages**.

---

#### f) `clearResults()`

```javascript
function clearResults(){
  document.getElementById("wordInput").value="";
  document.getElementById("results").innerHTML="";
}
```

* Resets input and results.
* Simple **DOM manipulation** using `getElementById` and `.innerHTML`.

---

#### g) Event Listener for Enter Key

```javascript
document.getElementById("wordInput").addEventListener("keypress", function(e){
  if(e.key==="Enter") lookupWord();
});
```

* Listens for **Enter key press** in the input.
* Calls `lookupWord()` automatically for convenience.

**Alternate uses**: Any input form submission or real-time search functionality.

---

## JavaScript Features Used

| Feature                       | Explanation                      | Alternate Use Cases                   |
| ----------------------------- | -------------------------------- | ------------------------------------- |
| `fetch()`                     | Makes API calls                  | Weather apps, news apps, stock APIs   |
| `async/await`                 | Handles asynchronous operations  | Form submissions, DB queries          |
| `forEach`                     | Iterates over arrays             | Rendering lists, dynamic content      |
| `map`                         | Creates transformed array        | Word wrapping, string manipulation    |
| `classList`                   | Adds/removes classes dynamically | Toggling UI themes, tabs              |
| `SpeechSynthesisUtterance`    | Text-to-speech                   | Read articles, accessibility features |
| `navigator.clipboard`         | Copy text to clipboard           | Sharing content, copying codes        |
| Template literals `` `...` `` | Embeds variables in strings      | Dynamic HTML rendering                |
| Event listeners               | Respond to user actions          | Click, hover, keyboard events         |


---

## Credits

* Dictionary API: [https://dictionaryapi.dev/](https://dictionaryapi.dev/)
* Developer: Anup Chapain

---

## License

This project is **free to use** for learning and demonstration purposes.