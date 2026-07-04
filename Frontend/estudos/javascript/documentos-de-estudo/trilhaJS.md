# trilhaJS.md

Onde encontrar cada conteúdo desse diretório:

---

# conteúdo da secao 1 do JavaScript  
(secao-1-fundamentos)

## !. 1 - Arrays.md
- Estrutura de listas
- Métodos principais
- map / filter / reduce
- find / some / every
- sort / slice / splice
- arrays de objetos
- destructuring
- spread
- imutabilidade
- performance
- uso real com APIs

## !. 2 - Objetos.md
- Criação de objetos
- Propriedades e métodos
- this
- objetos aninhados
- object.keys / values / entries
- freeze
- cópia rasa vs profunda
- propriedades dinâmicas
- wrappers

## !. 3 - Funções.md
- function declaration
- function expression
- arrow functions
- callbacks
- closures
- iife
- factory functions
- funções construtoras
- recursão
- generators

## !. 4 - Expressões.md
- expressão vs statement
- atribuição
- ternário
- function expression
- arrow function expression
- chamadas de função
- testes práticos

## !. 5 - Avaliação de curto-circuito.md
- &&
- ||
- ??
- truthy
- falsy
- atribuições inteligentes

---

# conteúdo da secao 2 do JavaScript  
(secao-2-assincronismo)

## !. 1 - Promises e async.md
- programação assíncrona
- síncrono vs assíncrono
- callbacks
- callback hell
- promises
- then / catch / finally
- chaining
- async / await
- try / catch
- Promise.resolve
- Promise.reject
- Promise.all
- Promise.allSettled
- Promise.race
- Promise.any
- uso no AngularJS
- erros comuns

## !. 2 - Memória.md
- stack
- heap
- call stack
- passagem por valor
- passagem por referência
- mutabilidade
- garbage collector
- memory leaks
- closures e memória
- shallow copy
- deep copy
- structuredClone
- event loop relacionado à memória

---

# conteúdo da secao 3 do JavaScript  
(secao-3-orientacao-a-objetos)

## !. 1 - Prototype.md
- prototype
- __proto__
- prototype chain
- herança prototipal
- Object.create
- shadowing
- operador new
- resolução de propriedades

## !. 2 - Classes.md
- sintaxe class
- constructor
- métodos
- this em classes
- static
- getters e setters
- extends
- super
- class fields
- métodos privados #
- encapsulamento
- class como açúcar sintático

## !. 3 - this, bind, call e apply.md
- contexto do this
- bind()
- call()
- apply()
- diferenças práticas
- casos reais
- uso em UI
- herança com call

## !. 4 - Objeto Map.md
- diferenças Map vs Object
- chaves de qualquer tipo
- ordem de inserção
- set / get / has / delete
- clear / size
- keys / values / entries
- iteração
- cache
- contagem
- conversão Map <-> Object

---

# conteúdo da secao 4 do JavaScript  
(secao-4-modularizacao)

## !. 1 - ES6 Modules.md
- o que são módulos
- export default
- named export
- import default
- named import
- import *
- alias com as
- múltiplos exports
- um export default por arquivo
- organização em arquivos
- módulos no browser
- módulos no node.js
- type="module"
- escopo isolado
- vantagens da modularização

## !. 2 - Modularização moderna.md
- arquitetura por módulos
- separação por responsabilidade
- services
- utils
- constants
- components
- barrels (index.js)
- clean code com módulos
- escalabilidade
- padrão profissional de projetos

---

# conteúdo da secao 5 do JavaScript  
(secao-5-engine-browser)

## !. 1 - Motor JavaScript.md
- parsing
- compilação JIT
- execução
- inline cache
- hidden classes
- de-optimization
- interpretação vs compilação
- performance real

## !. 2 - DOM.md
- (a fazer)

## !. 3 - Eventos.md
- (a fazer)

## !. 4 - Web storage API.md
- (a fazer)

---

# Conteúdo da seção 12 do JavaScript
(secao-12-http-integracao)

## !. 1 - Fetch API.md
- O que é Fetch API
- Sintaxe básica
- Exemplo com async await
- Entendendo a resposta (Response)
- Converter resposta
- GET
- POST
- PUT
- DELETE
- Tratando erros
- Headers
- Query Params
- Extra profissional: AbortController
- Extra profissional: Credenciais
- Extra profissional: Estrutura ideal
- Resumo final

## !. 2 - REST API.md
- O que é API
- O que é REST
- Recursos
- Métodos HTTP
- URLs em REST
- Parâmetros de rota
- Query parameters
- Body da requisição
- JSON
- Exemplos de resposta
- Stateless
- Autenticação e autorização
- CORS
- Paginação, filtros e ordenação
- Boas práticas
- Exemplo de CRUD
- Contrato entre front-end e back-end
- Versionamento de API

## !. 3 - Status HTTP.md
- O que é Status HTTP
- Por que Status HTTP existe
- Famílias de status HTTP
- Status 2xx
- Status 3xx
- Status 4xx
- Status 5xx
- Status HTTP com Fetch API
- Tratamento por status
- Boas práticas

## !. 4 - Headers HTTP.md
- O que são headers
- Headers de requisição e resposta
- Content-Type, Accept e Authorization
- Leitura de headers com Fetch
- Erros comuns e boas práticas

## !. 5 - Autenticação e JWT.md
- Autenticação e autorização
- Estrutura e fluxo do JWT
- Bearer token
- Expiração, refresh token e logout
- Armazenamento e segurança

## !. 6 - CORS.md
- Origem e política do navegador
- Requisições simples e preflight
- Headers Access-Control-Allow
- CORS com credenciais
- Diagnóstico e erros comuns

## !. 7 - Upload de Arquivos.md
- File e input de arquivo
- FormData e multipart/form-data
- Validação, segurança e cancelamento
- Erros comuns

## !. 8 - Interceptação de Requests.md
- Cliente HTTP compartilhado
- Headers e autenticação
- JSON e tratamento de erros
- Refresh token e concorrência
- Interceptors em frameworks

## !. 9 - Cookies, Sessão e Credentials.md
- Cookies e sessão
- HttpOnly, Secure e SameSite
- Credentials na Fetch API
- CORS com cookies
- XSS, CSRF e logout

## !. 10 - Query Params e URLSearchParams.md
- Parâmetros de rota e query params
- URL e URLSearchParams
- Parâmetros repetidos e opcionais
- Leitura e sincronização com a URL

## !. 11 - Paginação, Filtros e Ordenação.md
- Paginação por página, offset e cursor
- Filtros e ordenação
- Debounce e cancelamento
- Estado da interface

## !. 12 - Retry, Timeout e Cancelamento.md
- AbortController
- Timeout
- Retry e falhas transitórias
- Idempotência e exponential backoff
- Composição de signals

## !. 13 - Performance e Boas Práticas.md
- Requisições paralelas e sequenciais
- Problema N+1
- Paginação, payload e cache
- Estados da interface
- Organização e checklist

# ordem recomendada de estudo

1. Arrays  
2. Objetos  
3. Funções  
4. Expressões  
5. Curto-circuito  
6. Promises e async  
7. Memória  
8. this / bind / call / apply  
9. Prototype  
10. Classes  
11. Map  
12. ES6 Modules  
13. Modularização moderna  
14. Motor JavaScript  
15. DOM
16. Eventos
17. Web storage API

---

# foco para AngularJS legado

Prioridade máxima:

- Arrays
- Objetos
- Funções
- Promises
- this
- DOM
- Eventos
- Web Storage
