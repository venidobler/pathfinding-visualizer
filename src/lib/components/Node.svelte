<script lang="ts">
  let {
    row,
    col,
    isStart = false,
    isEnd = false,
    isWall = false,
    isVisited = false,
    isPath = false,
    // SVELTE 5: Recebemos as funções de evento como propriedades
    onMouseDown,
    onMouseEnter,
    onMouseUp
  } = $props<{
    row: number;
    col: number;
    isStart?: boolean;
    isEnd?: boolean;
    isWall?: boolean;
    isVisited?: boolean;
    isPath?: boolean;
    onMouseDown: (row: number, col: number) => void;
    onMouseEnter: (row: number, col: number) => void;
    onMouseUp: () => void;
  }>();

  let bgClass = $derived(
    isStart ? 'bg-green-500 border-green-600' :
    isEnd ? 'bg-red-500 border-red-600' :
    isWall ? 'bg-slate-800 dark:bg-slate-300 scale-110 transition-transform' : // Parede clara no escuro!
    isPath ? 'bg-yellow-400 border-yellow-500' :
    isVisited ? 'bg-blue-300 border-blue-400' :
    'bg-white dark:bg-slate-800' // Fundo vazio escuro
  );
</script>

<div 
  id={`node-${row}-${col}`}
  class={`w-6 h-6 border border-blue-100 dark:border-slate-700 cursor-pointer ${bgClass}`}
  onmousedown={() => onMouseDown(row, col)}
  onmouseenter={() => onMouseEnter(row, col)}
  onmouseup={onMouseUp}
  draggable="false"
></div>