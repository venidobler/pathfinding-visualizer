<script lang="ts">
  import Grid from '$lib/components/Grid.svelte';

  // Controla o estado do Dark Mode
  let isDark = $state(false);

  // Função que injeta a classe 'dark' direto no HTML do navegador
  function toggleDarkMode() {
    isDark = !isDark;
    if (isDark) {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
  }
</script>

<main class="min-h-screen bg-slate-100 dark:bg-slate-900 flex flex-col items-center py-10 font-sans transition-colors duration-300">
  
  <div class="w-full max-w-5xl flex justify-between items-center px-4 mb-6">
    <div>
      <h1 class="text-4xl font-bold text-slate-800 dark:text-slate-100 mb-2 transition-colors">
        Visualizador de Algoritmos
      </h1>
      <p class="text-slate-500 dark:text-slate-400 transition-colors">
        Construído com SvelteKit e TailwindCSS
      </p>
    </div>
    
    <button 
      onclick={toggleDarkMode} 
      class="px-4 py-2 rounded-full font-medium shadow-sm transition-colors
             bg-white text-slate-800 hover:bg-slate-200 
             dark:bg-slate-800 dark:text-yellow-400 dark:hover:bg-slate-700"
    >
      {isDark ? '☀️ Modo Claro' : '🌙 Modo Escuro'}
    </button>
  </div>

  <Grid />

</main>