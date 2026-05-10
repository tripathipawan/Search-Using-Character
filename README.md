# Search Using Character

A real-time, character-by-character product search UI built with HTML, CSS, and JavaScript. As the user types into the search bar, matching products appear and non-matching ones disappear — instantly, on every keystroke — with no button click, no API call, and no page reload. The entire search logic runs against 21 hard-coded product cards already rendered in the DOM.

---

## What This Project Does

The page renders 21 product cards in a scrollable dark-themed card panel. Each card shows a 70×70px product image and a name. Typing any character into the search input immediately calls `search()` via `onkeyup`, which compares the current input against every product's `<h2>` name (case-insensitively) and shows or hides each card by toggling its `display` style. Clearing the input shows all cards again.

---

## The 21 Products (Hard-coded in HTML)

All products are `div.product` elements inside `.product-list`, each containing an image and a `.p-details` div with an `<h2>` name and `<h3>` price. All prices are `$25`.

| # | Product Name | Category |
|---|---|---|
| 1 | Tshirt For Everyone | T-shirt |
| 2 | Fashion Bag | Bag |
| 3 | Tranya Smart Watch | Watch |
| 4 | New Sneaker | Sneaker |
| 5 | Poco World Phone | Phone |
| 6 | Styles Belt | Belt |
| 7 | Office Bag | Bag |
| 8 | Grand Court Sneaker | Sneaker |
| 9 | Tranya Smart Watch | Watch |
| 10 | Tranya Tshirt | T-shirt |
| 11 | Samsung Phone | Phone |
| 12 | Asian Sneaker | Sneaker |
| 13 | Ghost Hood | Hoodie |
| 14 | Apple Phone | Phone |
| 15 | Tiger Belt | Belt |
| 16 | Apple Smart Watch | Watch |
| 17 | Gym Bag | Bag |
| 18 | Gildan Men's Crew Tshirt | T-shirt |
| 19 | Fashionable hoodies | Hoodie |
| 20 | Printing Hood | Hoodie |
| 21 | Office Belt | Belt |

---

## How the JavaScript Works — `Script.js`

The entire search logic is a single function called `search()`, triggered by `onkeyup="search()"` on the input field — meaning it fires on every key release, including backspace, arrow keys, and delete:

```js
let search = () => {
  let searchBox = document.getElementById("search-item").value.toUpperCase();
  let storeItems = document.getElementById("product-list");
  let product = document.querySelectorAll(".product");
  let pname = document.getElementsByTagName("h2");

  for (var i = 0; i < pname.length; i++) {
    let match = product[i].getElementsByTagName("h2")[0];

    if (match) {
      let textValue = match.textContent || match.innerHTML;

      if (textValue.toUpperCase().indexOf(searchBox) > -1) {
        product[i].style.display = "";
      } else {
        product[i].style.display = "none";
      }
    }
  }
}
```

**Step-by-step breakdown:**

**1. Read and uppercase the input:**
```js
let searchBox = document.getElementById("search-item").value.toUpperCase();
```
The input value is converted to uppercase immediately. All comparisons happen in uppercase on both sides — making the search fully case-insensitive.

**2. Select all product cards and all `<h2>` elements:**
```js
let product = document.querySelectorAll(".product");   // NodeList of 21 .product divs
let pname = document.getElementsByTagName("h2");       // HTMLCollection of all h2s on the page
```
`pname.length` is used as the loop bound — since there are exactly 21 `<h2>` elements (one per product), the loop runs 21 times.

**3. For each index, get the `<h2>` inside that specific product card:**
```js
let match = product[i].getElementsByTagName("h2")[0];
```
Instead of using `pname[i]` directly, the code calls `getElementsByTagName("h2")[0]` scoped to `product[i]` — this ensures it always reads the `<h2>` that belongs to that specific card, not any `<h2>` from elsewhere.

**4. Extract the text content:**
```js
let textValue = match.textContent || match.innerHTML;
```
`textContent` is tried first (returns plain text, stripping HTML tags). `match.innerHTML` is the fallback if `textContent` is unavailable.

**5. Match check using `indexOf`:**
```js
if (textValue.toUpperCase().indexOf(searchBox) > -1) {
  product[i].style.display = "";       // show — reset to default display
} else {
  product[i].style.display = "none";   // hide
}
```
`indexOf(searchBox) > -1` returns `true` if `searchBox` appears anywhere in the product name — at any position, not just the start. This means typing `"bag"` matches "Fashion **Bag**", "Office **Bag**", and "Gym **Bag**". Typing `"watch"` matches "Tranya Smart **Watch**" and "Apple Smart **Watch**". Setting `display = ""` (empty string) removes any inline display style and lets the CSS default take over — restoring the card to its normal visible state.

**When input is empty:** `searchBox` is `""`. `"ANY STRING".indexOf("")` always returns `0` (which is `> -1`), so all 21 cards are shown when the input is cleared.

---

## Styling — `Style.css`

**Color palette:** Deep maroon body (`#410707`), darker maroon card (`#230006`), `darksalmon` accent for all text, borders, and icons.

**Body:** Full-viewport flex centering. `height: 100vh` with `display: flex; flex-direction: column; align-items: center; justify-content: center`.

**Scrollable container (`.container`):**
```css
width: 375px;
height: 540px;
background-color: #230006;
padding: 20px;
overflow-y: scroll;
border-radius: 15px;
box-shadow: 0px 0px 5px 3px white;
```
Fixed `375px × 540px` — a mobile-phone-width panel. `overflow-y: scroll` makes the product list scrollable inside the fixed height. `::-webkit-scrollbar { display: none }` hides the scrollbar visually while keeping scroll functionality.

**Search form:**
```css
width: 100%;
border: 2px solid darksalmon;
border-radius: 4px;
display: flex;
align-items: center;
background-color: currentColor;
```
The `<form>` holds a Font Awesome `fa-search` icon (`padding-left: 10px; color: darksalmon`) and the text input side by side in a flex row. The input has `background-color: #000`, `color: darksalmon`, `border: none; outline: none; box-shadow: none` — invisible as a field, blending into the dark background. Font size `16px`, font weight `600`.

**Product card (`.product`):**
```css
display: flex;
align-items: center;
cursor: pointer;
padding-bottom: 15px;
```
Each card is a horizontal flex row — image on the left, details on the right. `cursor: pointer` even though no click action is wired.

**Product image:**
```css
width: 70px;
height: 70px;
object-fit: cover;
border-radius: 5px;
```
Fixed square thumbnail. `object-fit: cover` ensures the image fills the square without distortion regardless of the original image's aspect ratio.

**Product name and price (`.p-details h2`, `.p-details h3`):**
```css
font-size: 20px; color: darksalmon;   /* h2 — name */
font-size: 18px; color: darksalmon;   /* h3 — price */
```
Both in `darksalmon`, with `padding-left: 15px` on `.p-details` to space text away from the image.

**Responsive breakpoint (`max-width: 375px`):**
```css
.container { width: 320px; height: 580px; border-radius: 0px; }
```
Container narrows to `320px`, height increases slightly to `580px`, and border-radius is removed — the card goes full-edge on very small screens.

---

## Tech Stack

| Technology | Role |
|---|---|
| HTML5 | 21 hard-coded `.product` divs — each with image, `<h2>` name, `<h3>` price |
| CSS3 | Dark maroon theme, fixed scrollable card panel, hidden scrollbar, 1 responsive breakpoint |
| JavaScript (Vanilla) | `search()` — `onkeyup` trigger, `toUpperCase()` case normalization, `indexOf()` substring match, `display` toggle |
| Font Awesome 6.7.2 (CDN) | `fa-search` icon inside the search form |

---

## Project Structure

```
Search-Using-Character/
├── Index.html      # Search form with onkeyup, 21 .product divs with h2 names and h3 prices
├── Style.css       # Dark maroon card, scrollable container, hidden scrollbar, product row layout
├── Script.js       # search() — uppercase normalization, indexOf match, display show/hide per card
└── Assets/
    ├── Bag 1.webp, Bag 2.webp, Bag 3.webp
    ├── Belt 1.webp, Belt 2.webp, Belt 3.webp
    ├── Hood 1.webp, Hood 2.webp, Hood 3.webp
    ├── Phone 1.webp, Phone 2.webp, Phone 3.webp
    ├── T shirt 1.webp, T shirt 2.webp, T shirt 3.webp
    ├── Watch 1.webp, Watch 2.webp, Watch 3.webp
    └── sneaker 1.webp, sneaker 2.webp, sneaker 3.webp
```

---

## How to Run

1. Clone the repository
   ```bash
   git clone https://github.com/tripathipawan/Search-Using-Character.git
   ```
2. Open `Index.html` directly in any modern browser — all assets are local. No internet connection, no server, no build step required.

---

## Repository

[https://github.com/tripathipawan/Search-Using-Character](https://github.com/tripathipawan/Search-Using-Character)
