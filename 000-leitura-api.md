# Código TypeScript

## Caminho do arquivo JSON

```ts
// Caminho do arquivo JSON onde os dados serão armazenados
// __dirname -> pasta atual do arquivo
const jsonFilePath = __dirname + '/data.temp.json';
```

---

## Carregamento inicial dos dados

```ts
// Carrega os dados do arquivo assim que o sistema inicia
// A lista será um array de strings
const list: string[] = await loadFromFile();
```

---

## Função `loadFromFile`

```ts
// Função assíncrona responsável por carregar os dados do arquivo JSON
async function loadFromFile() {
  try {
    
    // Abre o arquivo usando a API do Bun
    const file = Bun.file(jsonFilePath);

    // Lê o conteúdo do arquivo como texto
    const content = await file.text();

    // Converte o texto JSON em um array de strings
    return JSON.parse(content) as string[];

  // Bloco que capta possíveis erros na função
  } catch (error: any) {

    // Se o arquivo ainda não existir: retorna um array vazio para evitar erros
    if (error.code === 'ENOENT')
      return [];

    // Caso seja outro erro, ele será lançado novamente
    throw error;
  }
}
```

---

## Função `saveToFile`

```ts
// Função assíncrona responsável por salvar os dados no arquivo JSON
async function saveToFile() {
  try {

    // Converte o array "list" em JSON e salva no arquivo
    await Bun.write(jsonFilePath, JSON.stringify(list));

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

  // Adiciona o item no array
  list.push(item);

  // Salva a lista atualizada no arquivo
  await saveToFile();
}
```

---

## Função `getItems`

```ts
// Função assíncrona que retorna todos os itens da lista
async function getItems() {

  // Retorna o array completo
  return list;
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

  // Substitui o item antigo pelo novo
  list[index] = newItem;

  // Salva a lista atualizada
  await saveToFile();
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

  // Remove 1 item na posição informada
  list.splice(index, 1);

  // Salva a lista após a remoção
  await saveToFile();
}
```

---

## Exportação das funções

```ts
// Exporta as funções para serem utilizadas em outros arquivos
export default {
  addItem,
  getItems,
  updateItem,
  removeItem
};
```

---

# Servidor HTTP com Bun

## Importação do módulo principal

```ts
// Importa o módulo "todo" contendo as funções CRUD
import todo from "./core.ts";
```

---

## Criação do servidor

```ts
// Cria um servidor HTTP usando Bun
const server = Bun.serve({

  // Porta onde o servidor irá rodar
  port: 3000,

  // Rotas da aplicação
  routes: {

    // Rota principal "/"
    // Retorna o arquivo HTML da interface
    "/": new Response(Bun.file("./public/index.html")),

    /* ROTAS DO TODO */
    
    "/api/todo": {

      // Método GET
      // Retorna todos os itens da lista
      GET: async () => {

        // Busca os itens
        const items = await todo.getItems();

        // Retorna os dados em formato JSON
        return Response.json(items);
      },

      // Método POST
      // Adiciona um novo item
      POST: async (req) => {

        // Converte o corpo da requisição em JSON
        const data = await req.json() as any;

        // Pega o campo "item"
        const item = data.item || null;

        // Verifica se o item foi enviado
        if (!item)
          return Response.json(
            'Por favor, forneça um item para adicionar',
            { status: 400 }
          );

        // Adiciona o item na lista
        await todo.addItem(item);

        // Retorna os dados enviados
        return Response.json(data);
      },
    },

    // Rota com parâmetro dinâmico ":index"
    "/api/todo/:index": {

      // Método PUT
      // Atualiza um item específico
      PUT: async (req) => {

        // Converte o índice da URL para número
        const index = parseInt(req.params.index);

        // Verifica se o índice é válido
        if (isNaN(index))
          return Response.json(
            'Índice inválido um número inteiro é esperado',
            { status: 400 }
          );

        // Obtém os dados enviados
        const data = await req.json() as any;

        // Novo valor do item
        const newItem = data.newItem || null;

        // Verifica se o novo item foi enviado
        if (!newItem)
          return Response.json(
            'Por favor, forneça um novo item para atualizar',
            { status: 400 }
          );

        try {

          // Atualiza o item
          await todo.updateItem(index, newItem);

          // Retorna mensagem de sucesso
          return Response.json(
            `Item no índice ${index} atualizado para "${newItem}"`
          );

        } catch (error: any) {

          // Retorna erro caso aconteça algum problema
          return Response.json(error.message, { status: 400 });
        }
      },

      // Método DELETE
      // Remove um item pelo índice
      DELETE: async (req) => {

        // Converte o índice para número
        const index = parseInt(req.params.index);

        // Verifica se é válido
        if (isNaN(index))
          return Response.json('Índice inválido', { status: 400 });

        try {

          // Remove o item
          await todo.removeItem(index);

          // Retorna mensagem de sucesso
          return Response.json(
            `Item no índice ${index} removido com sucesso`
          );

        } catch (error: any) {

          // Retorna erro caso aconteça
          return Response.json(error.message, { status: 400 });
        }
      },
    },

    /* EXEMPLOS DE ROTAS */

    "/api/exemplo": {

      // GET simples
      GET: () => {

        // Retorna um texto com timestamp atual
        return new Response(`Esse é o exemplo: ${Date.now()}`);
      },

      // POST simples
      POST: async (req) => {

        // Recebe os dados enviados
        const data = await req.json() as any;

        // Adiciona a data atual
        data.recebidoEm = new Date().toLocaleDateString("pt-BR");

        // Retorna os dados atualizados
        return Response.json(data);
      },
    },

    // Rota dinâmica com ":id"
    "/api/exemplo/:id": {

      // Método PUT
      PUT: async (req, params) => {

        // Obtém o ID da URL
        const { id } = req.params;

        // Obtém os dados enviados
        const data = await req.json() as any;

        // Adiciona o ID nos dados
        data.id = id;

        // Adiciona data atual
        data.recebidoEm = new Date().toLocaleDateString("pt-BR");

        // Retorna JSON
        return Response.json(data);
      },

      // Método PATCH
      PATCH: async (req, params) => {

        // Obtém o ID da URL
        const { id } = req.params;

        // Dados enviados
        const data = await req.json() as any;

        // Mostra quais campos foram atualizados
        data.chavesAtualizadas = Object.keys(data);

        // Adiciona ID
        data.id = id;

        // Adiciona data da atualização
        data.atualizadoEm = new Date().toLocaleDateString("pt-BR");

        // Retorna os dados atualizados
        return Response.json(data);
      },

      // Método DELETE
      DELETE: (req, params) => {

        // Obtém o ID
        const { id } = req.params;

        // Retorna mensagem de exclusão
        return new Response(
          `Recurso com id ${id} deletado`,
          { status: 200 }
        );
      }
    }
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
// Mostra no terminal a URL do servidor
console.log(`Server running at http://localhost:${server.port}`);
```
