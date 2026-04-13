<script lang="ts">
  import Node from './Node.svelte';

  const NUM_ROWS = 20;
  const NUM_COLS = 40;

  const START_NODE_ROW = 10;
  const START_NODE_COL = 5;
  const END_NODE_ROW = 10;
  const END_NODE_COL = 34;

  type NodeType = {
    row: number;
    col: number;
    isStart: boolean;
    isEnd: boolean;
    isWall: boolean;
    isVisited: boolean;
    isPath: boolean;
  };

  function createGrid(): NodeType[][] {
    const initialGrid: NodeType[][] = [];
    for (let row = 0; row < NUM_ROWS; row++) {
      const currentRow: NodeType[] = [];
      for (let col = 0; col < NUM_COLS; col++) {
        currentRow.push({
          row,
          col,
          isStart: row === START_NODE_ROW && col === START_NODE_COL,
          isEnd: row === END_NODE_ROW && col === END_NODE_COL,
          isWall: false,
          isVisited: false,
          isPath: false,
        });
      }
      initialGrid.push(currentRow);
    }
    return initialGrid;
  }

  let grid = $state(createGrid());
  
  // Estado para saber se o usuário está segurando o clique
  let mouseIsPressed = $state(false);

  // Função que realmente desenha/apaga a parede
  function toggleWall(row: number, col: number) {
    const node = grid[row][col];
    // Regra de ouro: Não podemos colocar parede em cima do Início ou Fim!
    if (node.isStart || node.isEnd) return;
    
    // Inverte o estado (se for parede vira vazio, se for vazio vira parede)
    grid[row][col].isWall = !grid[row][col].isWall;
  }

  // Quando o usuário CLICA na célula
  function handleMouseDown(row: number, col: number) {
    mouseIsPressed = true;
    toggleWall(row, col);
  }

  // Quando o usuário PASSA O MOUSE por cima da célula
  function handleMouseEnter(row: number, col: number) {
    if (!mouseIsPressed) return; // Só pinta se estiver segurando o clique
    toggleWall(row, col);
  }

  // Quando o usuário SOLTA o clique
  function handleMouseUp() {
    mouseIsPressed = false;
  }
</script>

<div class="flex flex-col items-center justify-center p-4">
  <div 
    class="grid gap-0 border border-slate-300 shadow-xl bg-slate-50"
    style="grid-template-columns: repeat({NUM_COLS}, minmax(0, 1fr));"
    onmouseleave={handleMouseUp}
  >
    {#each grid as row}
      {#each row as node}
        <Node 
          row={node.row}
          col={node.col}
          isStart={node.isStart}
          isEnd={node.isEnd}
          isWall={node.isWall}
          isVisited={node.isVisited}
          isPath={node.isPath}
          onMouseDown={handleMouseDown}
          onMouseEnter={handleMouseEnter}
          onMouseUp={handleMouseUp}
        />
      {/each}
    {/each}
  </div>
</div>