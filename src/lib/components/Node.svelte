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
    isStart ? 'bg-green-500' :
    isEnd ? 'bg-red-500' :
    isWall ? 'bg-slate-800 scale-110 transition-transform' :
    isPath ? 'bg-yellow-400' :
    isVisited ? 'bg-blue-300' :
    'bg-white'
  );
</script>

<div 
  id={`node-${row}-${col}`}
  class={`w-6 h-6 border border-blue-100 cursor-pointer ${bgClass}`}
  onmousedown={() => onMouseDown(row, col)}
  onmouseenter={() => onMouseEnter(row, col)}
  onmouseup={onMouseUp}
  draggable="false"
></div>