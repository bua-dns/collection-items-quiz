# Czech Grammar Trainer

**A lightweight web-based app to practice Czech grammar**

Live demo:  
👉 [https://culturetogoberlin.github.io/cz-grammar-trainer/](https://culturetogoberlin.github.io/cz-grammar-trainer/)

---

## 📚 Features

Currently implemented:

- Interactive quiz for Czech noun declension
- Randomized questions with singular/plural and case variation
- Multiple attempts per question
- Progress tracking (correct, wrong, percentage)
- Skips previously asked combinations
- Option to reset quiz and score

---

## 🚀 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (version 18+ recommended)
- [pnpm](https://pnpm.io/) or npm

### Installation

```bash
pnpm install
```

### Run locally

```bash
pnpm run dev
```

### Build & Deploy to GitHub Pages

```bash
pnpm run deploy
```

> Requires `gh-pages` to be installed and configured. See `vite.config.js` for the correct `base` path.

---

## 📁 Project Structure

```text
src/
├─ assets/                  # JSON data with declension forms
├─ components/             # Reusable UI components (if any)
├─ views/HomeView.vue      # Main quiz view
├─ App.vue                 # Root component
├─ main.js                 # App bootstrap
```

---

## 📄 License

This project is licensed under the MIT License.

---

## 👤 Author

**Michael Müller**  
[Culture to go GbR](https://culture-to-go.de)
