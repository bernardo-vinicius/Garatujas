/* Código TypeScript */

// Caminho do arquivo JSON onde os dados serão armazenados
// __dirname -> pasta atual do arquivo
const jsonFilePath = __dirname + '/data.temp.json';

// Carrega os dados do arquivo assim que o sistema inicia
// A lista será um array de strings
const list: string[] = await loadFromFile();

// Função responsável por carregar os dados do arquivo JSON
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

// Função responsável por salvar os dados no arquivo JSON
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

// Adiciona um novo item na lista
async function addItem(item: string) {

  // Adiciona o item no array
  list.push(item);

  // Salva a lista atualizada no arquivo
  await saveToFile();
}

// Retorna todos os itens da lista
async function getItems() {

  // Retorna o array completo
  return list;
}
