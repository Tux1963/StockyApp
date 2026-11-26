<script setup>
const features = [
  {
    icon: '🏃',
    title: 'Daily activity',
    text: 'Log steps, workouts and active minutes to see how much you move each day.',
  },
  {
    icon: '😴',
    title: 'Sleep',
    text: 'Record when you go to bed and wake up, and spot patterns in how well you rest.',
  },
  {
    icon: '🥗',
    title: 'Meals',
    text: 'Keep a simple food diary so you know what you eat and when.',
  },
  {
    icon: '💧',
    title: 'Water intake',
    text: 'Track how much you drink and stay on top of your daily hydration goal.',
  },
  {
    icon: '⚖️',
    title: 'Weight',
    text: 'Note your weight over time and follow the trend, not just the daily number.',
  },
  {
    icon: '📈',
    title: 'Progress',
    text: 'See all your health data together and watch your progress week by week.',
  },
]
</script>

<template>
  <header class="hero">
    <h1>Stocky</h1>
    <p class="tagline">Your personal health tracker</p>
    <p class="intro">
      Stocky helps you build healthier habits by keeping track of your daily activity, sleep,
      meals, water intake and weight in one place, so you can see your progress over time.
    </p>
  </header>

  <main>
    <h2>What you can track</h2>
    <ul class="features">
      <li v-for="feature in features" :key="feature.title" class="feature">
        <span class="icon" aria-hidden="true">{{ feature.icon }}</span>
        <h3>{{ feature.title }}</h3>
        <p>{{ feature.text }}</p>
      </li>
    </ul>
  </main>
</template>

<style scoped>
.hero {
  text-align: center;
  padding: 3rem 0 2.5rem;
}

h1 {
  font-size: 3rem;
  font-weight: 600;
  color: var(--color-heading);
}

.tagline {
  font-size: 1.4rem;
  color: hsla(160, 100%, 37%, 1);
  margin-top: 0.25rem;
}

.intro {
  max-width: 40rem;
  margin: 1.25rem auto 0;
  font-size: 1.1rem;
}

h2 {
  font-size: 1.6rem;
  font-weight: 500;
  color: var(--color-heading);
  text-align: center;
  margin-bottom: 1.5rem;
}

.features {
  list-style: none;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 1.25rem;
}

.feature {
  padding: 1.5rem;
  border: 1px solid var(--color-border);
  border-radius: 12px;
  background: var(--color-background-soft);
  transition: border-color 0.3s;
}

.feature:hover {
  border-color: var(--color-border-hover);
}

.icon {
  font-size: 2rem;
}

h3 {
  font-size: 1.2rem;
  font-weight: 500;
  color: var(--color-heading);
  margin: 0.5rem 0 0.25rem;
}
</style>
