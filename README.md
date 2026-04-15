# Visualizador de Algoritmos de Pathfinding (Svelte Edition) 🧡

Este projeto é a contraparte do nosso **estudo comparativo de performance**, focado na eficiência da compilação e reatividade nativa.

## ⚡ O Experimento
Esta versão utiliza o **Svelte 5** com o novo sistema de **Runas** (`$state`, `$derived`). Diferente de frameworks baseados em Virtual DOM, o Svelte compila o código para manipular o DOM de forma cirúrgica.

### Observações Técnicas (Svelte):
- **Runas (Svelte 5):** O uso de `$state` permite que apenas o nó específico da grid seja atualizado quando o estado muda, sem a necessidade de comparar uma árvore inteira de componentes.
- **Performance:** Em testes de "stress" (pintura rápida de paredes), esta versão apresenta uma taxa de quadros (FPS) mais estável em dispositivos com hardware limitado.
- **Código Enxuto:** A lógica de estado e eventos é significativamente mais reduzida graças à sintaxe concisa do Svelte.

## 🛠️ Tecnologias
- **Framework:** Svelte 5 (SvelteKit/Vite)
- **Linguagem:** TypeScript
- **Estilização:** Tailwind CSS v4
- **Algoritmos:** BFS (Busca em Largura), A* (A-Star) e DFS (Busca em Profundidade).

## 🏁 Como rodar
1. Clone o repositório
2. Instale as dependências: `npm install`
3. Inicie o servidor: `npm run dev`

---
> **Nota de Estudo:** Confira a versão deste mesmo projeto feita em [React 19](https://github.com/venidobler/pathfinding-react) para entender as diferenças entre Reatividade Granular e Reconciliação de Virtual DOM.