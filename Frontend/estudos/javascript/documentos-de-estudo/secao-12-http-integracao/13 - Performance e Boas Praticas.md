# Performance e Boas Práticas em Integrações HTTP

# 1 - O que performance significa

Performance não é apenas reduzir milissegundos. Para o usuário, uma integração rápida e confiável:

- evita requisições desnecessárias;
- transfere somente os dados úteis;
- informa carregamento;
- permite cancelar ações;
- reaproveita resultados quando seguro;
- trata falhas de forma previsível.

Meça antes de otimizar. Use a aba **Network** para observar quantidade, duração, tamanho, cache e sequência das requisições.

---

# 2 - Evitar requisições desnecessárias

Antes de chamar a API, verifique se:

- os dados já estão disponíveis;
- uma chamada idêntica está em andamento;
- o filtro realmente mudou;
- a busca pode esperar um debounce;
- a tela ainda precisa do resultado.

Evite buscar novamente em todo evento de renderização de um framework. A chamada deve estar ligada a uma mudança real de dados ou navegação.

---

# 3 - Paralelo ou sequencial

Requisições independentes podem executar em paralelo:

```js
const [usuarioResponse, notificacoesResponse] = await Promise.all([
    fetch("/api/usuario"),
    fetch("/api/notificacoes")
]);
```

Se a segunda depende do resultado da primeira, mantenha a sequência:

```js
const usuarioResponse = await fetch("/api/usuario");
const usuario = await usuarioResponse.json();

const pedidosResponse = await fetch(`/api/usuarios/${usuario.id}/pedidos`);
```

Paralelizar dependências produz código incorreto; sequenciar chamadas independentes aumenta o tempo total.

---

# 4 - Evitar o problema N+1

```js
const produtos = await buscarProdutos();

const produtosComDetalhes = await Promise.all(
    produtos.map(async (produto) => {
        const response = await fetch(`/api/produtos/${produto.id}/detalhes`);
        return response.json();
    })
);
```

Uma lista de 100 produtos gera 101 requisições. Quando a tela sempre precisa dos detalhes, o contrato da API pode oferecer expansão, endpoint em lote ou uma resposta adequada à tela.

Não esconda um problema de contrato apenas adicionando mais paralelismo.

---

# 5 - Paginação e payload

Paginação limita o volume inicial. O back-end também pode permitir seleção ou expansão controlada de campos.

Evite:

- baixar todos os registros para filtrar localmente;
- devolver imagens grandes dentro de JSON em Base64;
- pedir detalhes que a tela não exibe;
- duplicar os mesmos dados em vários endpoints sem necessidade.

Compressão HTTP e formatos de resposta são configurados principalmente no servidor, mas o front-end deve observar o tamanho transferido.

---

# 6 - Cache HTTP

O servidor pode orientar o cache:

```http
Cache-Control: public, max-age=300
ETag: "produto-42-v3"
```

Em uma revalidação:

```http
If-None-Match: "produto-42-v3"
```

Se nada mudou, a API pode responder `304 Not Modified`.

Não desative cache globalmente para “garantir atualização”. Dados públicos e estáveis podem aproveitar cache; dados privados e mutáveis exigem política adequada.

---

# 7 - Cache da aplicação

Uma aplicação pode guardar resultados em memória para evitar chamadas repetidas. Porém, cache cria perguntas:

- quando expira?
- quem invalida após uma alteração?
- dados podem ficar desatualizados?
- o cache é compartilhado entre usuários?

Bibliotecas de server state ajudam com deduplicação, revalidação e invalidação. Use-as quando o projeto possui essa complexidade, não apenas para substituir uma única chamada Fetch.

---

# 8 - Estados de carregamento

Uma boa interface diferencia:

- primeira carga;
- atualização em segundo plano;
- resultado vazio;
- erro recuperável;
- cancelamento;
- envio de formulário.

Evite apagar conteúdo útil durante uma atualização curta. Em formulários, bloqueie submissões duplicadas e dê feedback claro.

---

# 9 - Segurança e privacidade

Performance nunca justifica:

- armazenar credenciais inseguramente;
- expor token em URL;
- registrar body sensível;
- remover validação;
- aceitar resposta sem verificar status e formato;
- liberar CORS para qualquer origem sem necessidade.

Logs devem ajudar no diagnóstico sem conter senhas, tokens ou dados pessoais.

---

# 10 - Organização do código

Uma estrutura simples pode separar:

```txt
api/
  client.js
  produtos.js
  usuarios.js
```

`client.js` concentra detalhes comuns. Cada módulo representa operações de um recurso. A interface chama funções com intenção clara:

```js
const produtos = await produtosApi.listar(filtros);
```

Evite criar uma camada genérica tão abstrata que ninguém consiga descobrir qual URL, método ou body é usado.

---

# 11 - Checklist prático

Antes de considerar uma integração pronta:

- o status HTTP é verificado?
- respostas vazias, JSON e erros são tratados corretamente?
- a chamada pode ser cancelada quando perde utilidade?
- buscas digitadas usam debounce?
- listas grandes usam paginação?
- requisições independentes podem rodar em paralelo?
- existe N+1?
- cache e invalidação estão coerentes?
- carregamento, vazio e erro aparecem na interface?
- autenticação, CORS e credenciais seguem o contrato?
- logs evitam dados sensíveis?

---

# 12 - Erros comuns

- otimizar sem medir;
- adicionar cache sem estratégia de invalidação;
- fazer chamadas em loops;
- repetir requisições idênticas;
- baixar dados demais;
- esconder toda chamada atrás de abstrações genéricas;
- confundir resposta rápida com experiência completa.

---

# 13 - Conclusão

Boas integrações HTTP transferem o necessário, evitam trabalho repetido e deixam o comportamento visível para o usuário e para quem mantém o sistema. O ganho mais importante costuma vir de um contrato adequado, paginação, cancelamento e redução de chamadas — não de truques complexos no front-end.
