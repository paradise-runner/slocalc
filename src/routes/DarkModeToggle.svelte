<script>
    import { onMount } from 'svelte';
  
    let darkMode = false;
  
    onMount(() => {
      const isDarkMode = localStorage.getItem('darkMode') === 'true';
      darkMode = isDarkMode;
      updateTheme(isDarkMode);
    });
  
    function toggleDarkMode() {
      darkMode = !darkMode;
      updateTheme(darkMode);
      localStorage.setItem('darkMode', darkMode);
    }
  
    function updateTheme(isDark) {
      if (isDark) {
        document.documentElement.classList.add('dark');
      } else {
        document.documentElement.classList.remove('dark');
      }
    }
  </script>
  
  <button
    on:click={toggleDarkMode}
    class="p-2 rounded-full bg-gray-200 dark:bg-gray-700 text-gray-800 dark:text-gray-200"
  >
    {#if darkMode}
      🌙
    {:else}
      ☀️
    {/if}
  </button>