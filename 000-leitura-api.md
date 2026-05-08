# Código TypeScript

## CORE.TS

## Caminho do arquivo JSON

```ts
const jsonFilePath = __dirname + '/data.temp.json'; // Caminho do arquivo JSON onde os dados serão armazenados
```

---

## Carregamento inicial dos dados

```ts
const list: string[] = await loadFromFile(); // Carrega os dados do arquivo assim que o sistema inicia
```

---

## Função `loadFromFile`

```ts
// Função assíncrona responsável por carregar os dados do arquivo JSON
async function loadFromFile() {
  try {
    const file = Bun.file(jsonFilePath); // Abre o arquivo usando a API do Bun

    const content = await file.text(); // Lê o conteúdo do arquivo como texto

    return JSON.parse(content) as string[]; // Converte o texto JSON em um array de strings

  // Bloco que capta possíveis erros na função
  } catch (error: any) {

    // Se o arquivo ainda não existir: retorna um array vazio para evitar erros
    if (error.code === 'ENOENT')
      return [];

    throw error; // Caso seja outro erro, ele será lançado novamente
  }
}
```

---

## Função `saveToFile`

```ts
// Função assíncrona responsável por salvar os dados no arquivo JSON
async function saveToFile() {
  try {
    await Bun.write(jsonFilePath, JSON.stringify(list)); // Converte o array "list" em JSON e salva no arquivo

  // Bloco que capta possíveis erros na função
  } catch (error: any) {

    // Caso aconteça algum erro ao salvar, lança um novo erro personalizado
    throw new Error("Erro ao salvar os dados no arquivo: " + error.message);
  }
}
```

---

## Função `addItem`

```ts
// Função assíncrona que adiciona 1 item na lista
async function addItem(item: string) {
  list.push(item); // Adiciona o item no array

  await saveToFile(); // Salva a lista atualizada no arquivo
}
```

---

## Função `getItems`

```ts
// Função assíncrona que retorna todos os itens da lista
async function getItems() {
  return list; // Retorna o array completo
}
```

---

## Função `updateItem`

```ts
// Função assíncrona que atualiza um item existente pelo índice
async function updateItem(index: number, newItem: string) {

  // Verifica se o índice é válido
  if (index < 0 || index >= list.length)
    throw new Error("Index fora dos limites");

  list[index] = newItem; // Substitui o item antigo pelo novo

  await saveToFile(); // Salva a lista atualizada
}
```

---

## Função `removeItem`

```ts
// Função assíncrona que remove um item da lista pelo índice
async function removeItem(index: number) {

  // Verifica se o índice existe
  if (index < 0 || index >= list.length)
    throw new Error("Index fora dos limites");

  list.splice(index, 1); // Remove 1 item na posição informada

  await saveToFile(); // Salva a lista após a remoção
}
```

---

## Exportação das funções

```ts
export default { addItem, getItems, updateItem, removeItem }; // Exporta as funções para serem utilizadas em outros arquivos
```

---

# Servidor HTTP com Bun (API.TS)

## Importação do módulo principal

```ts
import todo from "./core.ts"; // Importa o módulo "todo" contendo as funções CRUD
```

---

## Criação do servidor

```ts
// Cria um servidor HTTP usando Bun
const server = Bun.serve({

  port: 3000, // Porta onde o servidor irá rodar

  // Rotas da aplicação
  routes: {

    "/": new Response(Bun.file("./public/index.html")), // Rota principal "/" (Retorna o arquivo HTML da interface)

    /* ROTAS DO TODO */
    
    "/api/todo": {

      // Método GET (Retorna todos os itens da lista)
      GET: async () => {

        const items = await todo.getItems(); // Busca os itens

        return Response.json(items); // Retorna os dados em formato JSON
      },

      // Método POST (Adiciona um novo item)
      POST: async (req) => {

        const data = await req.json() as any; // Converte o corpo da requisição em JSON

        const item = data.item || null; // Pega o campo "item"

        // Verifica se o item foi enviado
        if (!item)
          return Response.json(
            'Por favor, forneça um item para adicionar',
            { status: 400 }
          );

        await todo.addItem(item); // Adiciona o item na lista

        return Response.json(data); // Retorna os dados enviados
      },
    },

    // Rota com parâmetro dinâmico ":index"
    "/api/todo/:index": {

      // Método PUT (Atualiza um item específico)
      PUT: async (req) => {

        const index = parseInt(req.params.index); // Converte o índice da URL para número

        // Verifica se o índice é válido
        if (isNaN(index))
          return Response.json(
            'Índice inválido um número inteiro é esperado',
            { status: 400 }
          );

        const data = await req.json() as any; // Obtém os dados enviados

        const newItem = data.newItem || null; // Novo valor do item

        // Verifica se o novo item foi enviado
        if (!newItem)
          return Response.json(
            'Por favor, forneça um novo item para atualizar',
            { status: 400 }
          );

        try {

          await todo.updateItem(index, newItem); // Atualiza o item

          return Response.json(
            `Item no índice ${index} atualizado para "${newItem}"`
          ); // Retorna mensagem de sucesso

        // Bloco que capta possíveis erros na função
        } catch (error: any) {

          return Response.json(error.message, { status: 400 }); // Retorna erro caso aconteça algum problema
        }
      },

      // Método DELETE (Remove um item pelo índice)
      DELETE: async (req) => {

        const index = parseInt(req.params.index); // Converte o índice para número

        // Verifica se é válido
        if (isNaN(index))
          return Response.json('Índice inválido', { status: 400 });

        try {

          await todo.removeItem(index); // Remove o item

          return Response.json(
            `Item no índice ${index} removido com sucesso`
          ); // Retorna mensagem de sucesso

        // Bloco que capta possíveis erros na função
        } catch (error: any) {

          return Response.json(error.message, { status: 400 }); // Retorna erro caso aconteça
        }
      },
    },
  },

  // Caso nenhuma rota seja encontrada, retorna o erro 404
  async fetch(req) {
    return new Response(`Not Found`, { status: 404 });
  },
});
```

---

## Inicialização do servidor

```ts
console.log(`Server running at http://localhost:${server.port}`); // Mostra no terminal a URL do servidor
```
