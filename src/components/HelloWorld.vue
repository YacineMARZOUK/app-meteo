<script setup>
import { ref } from 'vue'

defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>
  <div class="hello-container">
    <h1 class="title">{{ msg }}</h1>

    <div class="card">
      <button type="button" @click="count++" class="counter-button">
        <span class="counter-text">Count is {{ count }}</span>
      </button>
      <p class="hint">
        Edit
        <code class="code-highlight">components/HelloWorld.vue</code> to test HMR
      </p>
    </div>

    <div class="links-section">
      <p class="link-paragraph">
        Check out
        <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank" class="link-item">create-vue</a>, the official Vue + Vite starter
      </p>
      <p class="link-paragraph">
        Learn more about IDE Support for Vue in the
        <a href="https://vuejs.org/guide/scaling-up/tooling.html#ide-support" target="_blank" class="link-item">Vue Docs Scaling up Guide</a>.
      </p>
    </div>
    
    <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
  </div>
</template>

<style scoped>
:root {
  --primary-color: #2563eb;
  --primary-dark: #1e40af;
  --primary-light: #dbeafe;
  --secondary-color: #10b981;
  --accent-color: #f59e0b;
  --dark-text: #1e293b;
  --light-text: #f8fafc;
  --gray-text: #64748b;
}

.hello-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  background-color: #f8fafc;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(15, 23, 42, 0.1);
}

.title {
  font-size: 2.6rem;
  font-weight: 700;
  margin-bottom: 2rem;
  color: var(--primary-color);
  letter-spacing: -0.5px;
  position: relative;
  display: inline-block;
}

.title::after {
  content: "";
  position: absolute;
  bottom: -10px;
  left: 25%;
  width: 50%;
  height: 4px;
  background: linear-gradient(90deg, var(--primary-color), var(--accent-color));
  border-radius: 2px;
}

.card {
  padding: 2rem;
  margin: 2rem 0;
  background-color: white;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(15, 23, 42, 0.05);
  border: 1px solid #e2e8f0;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(15, 23, 42, 0.1);
}

.counter-button {
  padding: 0.8rem 2rem;
  font-size: 1.1rem;
  font-weight: 600;
  background: linear-gradient(135deg, var(--primary-color), var(--primary-dark));
  color: white;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 10px rgba(37, 99, 235, 0.3);
  margin-bottom: 1.5rem;
}

.counter-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(37, 99, 235, 0.4);
}

.counter-button:active {
  transform: translateY(0);
  box-shadow: 0 2px 5px rgba(37, 99, 235, 0.4);
}

.counter-text {
  position: relative;
}

.hint {
  color: var(--gray-text);
  font-size: 1rem;
  margin-top: 1rem;
}

.code-highlight {
  background-color: #f1f5f9;
  color: var(--primary-dark);
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-family: monospace;
  font-size: 0.9rem;
  border: 1px solid #e2e8f0;
}

.links-section {
  margin: 2rem 0;
  padding: 1.5rem;
  background-color: #f1f5f9;
  border-radius: 12px;
  border-left: 4px solid var(--accent-color);
}

.link-paragraph {
  margin: 1rem 0;
  color: var(--dark-text);
  font-size: 1rem;
  line-height: 1.6;
}

.link-item {
  color: var(--primary-color);
  text-decoration: none;
  font-weight: 500;
  position: relative;
  padding-bottom: 2px;
  transition: color 0.2s ease;
}

.link-item::after {
  content: '';
  position: absolute;
  width: 100%;
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: var(--primary-color);
  transform: scaleX(0);
  transform-origin: bottom right;
  transition: transform 0.3s ease;
}

.link-item:hover {
  color: var(--primary-dark);
}

.link-item:hover::after {
  transform: scaleX(1);
  transform-origin: bottom left;
}

.read-the-docs {
  color: var(--gray-text);
  font-size: 0.9rem;
  font-style: italic;
  margin-top: 2.5rem;
  background-color: #f8fafc;
  padding: 0.8rem;
  border-radius: 8px;
  display: inline-block;
  border: 1px dashed #cbd5e1;
}
</style>