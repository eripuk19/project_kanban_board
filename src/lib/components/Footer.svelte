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
  <p class="text-lg font-semibold">
    🌍 Your country: {country}
  </p>
</footer>
