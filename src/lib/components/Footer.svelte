<script>
  import { onMount } from 'svelte';

  let country = 'Loading...';

  async function fetchCountry() {
    try {
      const res = await fetch('https://ipapi.co/json/');
      const data = await res.json();
      country = data.country_name || 'Unknown';
    } catch (err) {
      console.error('Error fetching country:', err);
      country = 'Unknown';
    }
  }

  onMount(() => {
    fetchCountry();
  });
</script>

<footer class="w-full bg-pink-600 text-white text-center py-4 mt-4 rounded-t-lg shadow-lg">
  <p class="text-<script>
  import { onMount } from 'svelte';

  let country = 'Loading...';

  async function fetchCountry() {
    try {
      const res = await fetch('https://ipapi.co/json/');
      const data = await res.json();
      country = data.country_name || 'Unknown';
    } catch (err) {
      console.error('Error fetching country:', err);
      country = 'Unknown';
    }
  }

  onMount(() => {
    fetchCountry();
  });
</script>

<footer
  class="w-full bg-gradient-to-r from-pink-600 via-pink-500 to-pink-600 
             text-white text-center py-5 mt-6 rounded-t-2xl shadow-lg 
             transition-transform duration-300 hover:-translate-y-0.5 hover:shadow-xl"
>
  <p class="text-lg font-semibold tracking-wide flex items-center justify-center space-x-2">
    <span>🌍</span>
    <span>Your country:</span>
    <span class="font-bold text-white/90">{country}</span>
  </p>

  <p class="text-sm text-white/70 mt-1">
    © {new Date().getFullYear()} Kanban Board 
  </p>
</footer>
lg font-semibold">
    🌍 Your country: {country}
  </p>
</footer>
