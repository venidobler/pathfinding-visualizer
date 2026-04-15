<script lang="ts">
  import Node from './Node.svelte';
  // Importamos as funções matemáticas que acabamos de criar!
  import { executarBFS, pegarCaminhoMaisCurto } from '../algorithms/bfs';
  import { executarAStar } from '../algorithms/astar';
  import { executarDFS } from '../algorithms/dfs';

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
  let mouseIsPressed = $state(false);
  
  // Nova variável para bloquear o usuário de desenhar paredes ou clicar várias vezes enquanto a animação roda
  let isAnimating = $state(false);

  let hasResults = $state(false);

  let algoritmoSelecionado = $state('BFS');

  // --- LÓGICA DO MOUSE (Pincel) ---
  function toggleWall(row: number, col: number) {
    if (isAnimating) return; // Não deixa desenhar durante a animação
    const node = grid[row][col];
    if (node.isStart || node.isEnd) return;
    grid[row][col].isWall = !grid[row][col].isWall;
  }

  function handleMouseDown(row: number, col: number) {
    if (isAnimating) return;

    if (hasResults) {
      limparCaminho();
    }

    mouseIsPressed = true;
    toggleWall(row, col);
  }

  function handleMouseEnter(row: number, col: number) {
    if (!mouseIsPressed || isAnimating) return;
    toggleWall(row, col);
  }

  function handleMouseUp() {
    mouseIsPressed = false;
  }

  // --- LÓGICA DA ANIMAÇÃO (A Mágica!) ---
  function iniciarAlgoritmo() {
    if (isAnimating) return;
    
    // Se o usuário clicar de novo sem limpar o caminho, limpamos automaticamente
    if (hasResults) limparCaminho();
    
    isAnimating = true;
    const startNode = grid[START_NODE_ROW][START_NODE_COL];
    const endNode = grid[END_NODE_ROW][END_NODE_COL];

    let resultado; // Vai guardar a resposta da matemática
    
    // NOVO: Lógica de decisão
    if (algoritmoSelecionado === 'BFS') {
      resultado = executarBFS(grid, startNode, endNode);
    } else if (algoritmoSelecionado === 'ASTAR') {
      resultado = executarAStar(grid, startNode, endNode);
    } else if (algoritmoSelecionado === 'DFS') {
      resultado = executarDFS(grid, startNode, endNode);
    }

    const { nosVisitadosEmOrdem, nosAnteriores } = resultado;
    const caminhoMaisCurto = pegarCaminhoMaisCurto(endNode, nosAnteriores);

    animarAlgoritmo(nosVisitadosEmOrdem, caminhoMaisCurto);
  }

  function animarAlgoritmo(visitados: NodeType[], caminho: NodeType[]) {
    for (let i = 0; i <= visitados.length; i++) {
      // Quando terminamos de pintar todos os visitados, começamos a pintar o caminho final!
      if (i === visitados.length) {
        setTimeout(() => {
          animarCaminho(caminho);
        }, 10 * i); // Executa logo após a última célula visitada
        return;
      }

      // O 'setTimeout' faz com que cada célula mude de cor 10 milissegundos DEPOIS da anterior
      setTimeout(() => {
        const no = visitados[i];
        if (!no.isStart && !no.isEnd) {
          grid[no.row][no.col].isVisited = true;
        }
      }, 10 * i);
    }
  }

  function animarCaminho(caminho: NodeType[]) {
    for (let i = 0; i < caminho.length; i++) {
      setTimeout(() => {
        const no = caminho[i];
        if (!no.isStart && !no.isEnd) {
          grid[no.row][no.col].isPath = true; // Pinta de amarelo
        }
        
        // Se for a última célula do caminho, libera a tela para uso novamente
        if (i === caminho.length - 1) {
          isAnimating = false;
          hasResults = true;
        }
      }, 30 * i); // O caminho desenha um pouquinho mais devagar (30ms) para dar impacto visual
    }
  }

  function limparTabuleiro() {
    if (isAnimating) return;
    grid = createGrid(); // Reseta tudo criando uma matriz novinha em folha
    hasResults = false;
  }

  function limparCaminho() {
    if (isAnimating) return; // Não deixa limpar enquanto está animando
    
    // Percorremos toda a matriz
    for (let row = 0; row < NUM_ROWS; row++) {
      for (let col = 0; col < NUM_COLS; col++) {
        // Apagamos apenas o histórico de visita e o caminho final
        grid[row][col].isVisited = false;
        grid[row][col].isPath = false;
      }
    }
    hasResults = false;
  }

</script>

<div class="flex flex-col items-center justify-center p-4">
  
  <div class="flex gap-4 mb-6 items-center">
    <select 
      class="px-4 py-2 border border-slate-300 rounded shadow-sm bg-white text-slate-700 font-medium focus:outline-none focus:border-blue-500 cursor-pointer disabled:opacity-50"
      bind:value={algoritmoSelecionado}
      disabled={isAnimating}
    >
      <option value="BFS">Busca em Largura (BFS)</option>
      <option value="ASTAR">Algoritmo A* (A-Star)</option>
      <option value="DFS">Busca em Profundidade (DFS)</option>
    </select>

    <button 
      class="px-6 py-2 bg-blue-600 text-white font-semibold rounded shadow-md hover:bg-blue-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
      onclick={iniciarAlgoritmo}
      disabled={isAnimating}
    >
      Iniciar Busca
    </button>
    
    <button 
      class="px-6 py-2 bg-yellow-500 text-white font-semibold rounded shadow-md hover:bg-yellow-600 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
      onclick={limparCaminho}
      disabled={isAnimating}
    >
      Limpar Caminho
    </button>
    
    <button 
      class="px-6 py-2 bg-slate-200 text-slate-800 font-semibold rounded shadow-md hover:bg-slate-300 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
      onclick={limparTabuleiro}
      disabled={isAnimating}
    >
      Limpar Tabuleiro Completo
    </button>
  </div>

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

  <div class="mt-8 flex flex-wrap justify-center gap-6 p-4 bg-white rounded-lg shadow-sm border border-slate-200">
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-green-500 border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Início</span>
  </div>
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-red-500 border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Objetivo</span>
  </div>
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-slate-800 border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Parede</span>
  </div>
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-blue-300 border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Visitado</span>
  </div>
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-yellow-400 border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Caminho Final</span>
  </div>
  <div class="flex items-center gap-2">
    <div class="w-5 h-5 bg-white border border-slate-200"></div>
    <span class="text-sm font-medium text-slate-600">Não Visitado</span>
  </div>
</div>
</div>