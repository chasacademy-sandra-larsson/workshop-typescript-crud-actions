# 🖼️ API-anrop - Gallery UI

En övning på API-anrop med ett tillhörande användargränssnitt i form av ett konstgalleri.

## 🛀🏼 1. Projektstruktur "Simple SPA"

Ni ska utgå från en enklare Singe Page Application (SPA) skriven i Typescript, Vite och utan ramverk. 
Utgå från [detta repo](https://github.com/chasacademy-sandra-larsson/simple-spa-ts) och utforska tillsammans i teamet hur projektstrukturen är uppbyggt.

Skapa en ny route ```/paintings``` och visa konstverken som laddats upp där.

## 🫙 2. Installera och använda JSON Server som Mock API

1. Om du inte installerat JSON Server gör du det med `npm install -g json-server`
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
   http://lo
   ```
4. Öppna db.json för att se sparade konstverk

## 3. Lägg till en komponent ```ìnput.ts``` 

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
```





