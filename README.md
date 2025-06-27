# 🏛️ University Collections Quiz

An interactive online quiz built with Vue.js 3. Users explore and test their knowledge of objects from university collections through multiple-choice questions.

A demo version is accessible at [https://bua-dns.github.io/collection-items-quiz/](https://bua-dns.github.io/collection-items-quiz/).

## 🎯 Features

- Randomized question generation from a structured JSON dataset
- Intelligent shuffling of answer options
- Tracks attempts, correct answers, and performance statistics
- Simple and responsive layout ready for enhancement

## 🧰 Tech Stack

- [Vue.js 3](https://vuejs.org/) with `<script setup>` composition API
- Static JSON data source for questions
- No backend required — fully client-side

## 🚀 Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/university-collections-quiz.git
   cd university-collections-quiz
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the development server**
   ```bash
   npm run dev
   ```

4. **Build for production**
   ```bash
   npm run build
   ```

## 📁 Project Structure

```
src/
├── assets/data/questions.json  # Your quiz data source
├── views/HomeView.vue         # Main quiz component
├── App.vue                    # Root component
└── main.js                    # App bootstrap
```

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.
