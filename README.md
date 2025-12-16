# 🖼️ API-anrop - Gallery UI

En övning på API-anrop med ett tillhörande användargränssnitt i form av ett konstgalleri.

## 🛀🏼 1. Projektstruktur "Simple SPA"

Ni ska utgå från en enklare Singe Page Application (SPA) skriven i Typescript, Vite och utan ramverk. 
Utgå från [detta repo](https://github.com/chasacademy-sandra-larsson/simple-spa-ts) och utforska tillsammans i teamet hur projektstrukturen är uppbyggd.

Målet är att ni ska ha en ny route ```/paintings``` och där ska de konstverk som laddats upp visas. I formuläret laddar man upp en bild-url-adress från exempelvis [Unsplash](https://unsplash.com/) 

## 🫙 2. Installera och använda JSON Server som Mock API

1. Installera JSON Server med `npm install -g json-server`
2. Kör sedan `json-server --watch db.json` för att starta en lokal server med filen `db.json` som mock-databas
3. Responsen från terminalen borde se ungefär såhär ut:

   ```js
   JSON Server started on PORT :3000
   Press CTRL-C to stop
   Watching db.json...

   (˶ᵔ ᵕ ᵔ˶)

   Index:
   http://localhost:3000/

   Static files:
   Serving ./public directory if it exists

   Endpoints:
   http://localhost:3000
   ```
4. Sparade konstverk kommer att sparas i filen ```db.json```

## 3. Lägg till en komponent ```input.ts``` 

I projektstrukturen under ```/components```

```ts
export default function input({
  type,
  name,
  label,
  classes = "",
}: {
  type: string;
  name: string;
  label: string;
  classes?: string;
}) {
  const inputContainer = document.createElement("fieldset");
  inputContainer.className = classes;
  inputContainer.innerHTML = `
    <legend>${label}</legend>
    <input type="${type}" name="${name}" id="${name}">
  `;
  return inputContainer;
}
````

## 4. Lägg till ett formulär i vyn 

```ts
import input from "../components/input";
import { type Painting } from "../libs/api";
import { setPainting } from "../libs/store";

export default function form() {
  const form = document.createElement("form");
  form.appendChild(
    input({ type: "text", name: "name", label: "Painting name" })
  );
  form.appendChild(
    input({ type: "text", name: "painterName", label: "Painter name" })
  );
  form.appendChild(
    input({
      type: "textarea",
      name: "description",
      label: "Painting description",
    })
  );
  form.appendChild(
    input({ type: "text", name: "imageUrl", label: "Image URL" })
  );
  
  const button = document.createElement("button");
  button.type = "submit";
  button.textContent = "Add painting";
  form.appendChild(button);
  
  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const formData = new FormData(form);
    const { name, description, imageUrl, painterName } =
      Object.fromEntries(formData);
    setPainting({
      name,
      description,
      imageUrl,
      painter: { name: painterName },
    } as Painting);
  });
  
  return form;
}
````

## 5. Hämta och skapa konstverk

Lägg följande kod i ```/lib/api.ts``` och **skriv färdigt funktionerna för GET OCH POST och ```interface Painting```**


```ts
const BASE_URL = "http://localhost:3000";

export interface Painting {}

const get = async <T>(url: string) => {};

const post = async <T>(url: string, data: T) => {};

export const getPaintings = async () =>
  get<Painting[]>(`${BASE_URL}/paintings`);

export const addPainting = async (data: Painting) =>
  post<Painting>(`${BASE_URL}/paintings`, data);
```

## 6. Uppdate Store-klassen

Ta bort befintligt kod i ```store.ts``` så att globalt state nu istället hanterar att lägga till och ta bort konstmverk.

```ts
import {
  addPainting,
  getPaintings as getPaintingsRequest,
  type Painting,
} from "./api";

class Store {
  renderCallback: () => void;

  constructor() {
    this.renderCallback = () => {};
  }

  async getPaintings() {
    try {
      const paintings = await getPaintingsRequest();
      return paintings;
    } catch (error) {
      return [];
    }
  }

  async setPainting(painting: Painting) {
    try {
      await addPainting(painting);
      this.triggerRender();
    } catch (error) {
      console.error("Failed to add painting:", error);
      throw error;
    }
  }

  setRenderCallback(renderApp: () => void) {
    this.renderCallback = renderApp;
  }

  triggerRender() {
    if (this.renderCallback) {
      this.renderCallback();
    }
  }
}
const store = new Store();

export const getPaintings = store.getPaintings.bind(store);
export const setPainting = store.setPainting.bind(store);
export const setRenderCallback = store.setRenderCallback.bind(store);
```
