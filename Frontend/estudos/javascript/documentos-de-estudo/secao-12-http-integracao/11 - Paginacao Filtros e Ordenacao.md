# Paginação, Filtros e Ordenação

# 1 - Por que esses recursos existem

Uma API não deve devolver milhares de registros quando a tela precisa mostrar apenas uma pequena parte. Paginação limita o volume; filtros selecionam itens relevantes; ordenação define a sequência.

```txt
GET /produtos?pagina=2&limite=20&categoria=livros&ordenarPor=preco&ordem=asc
```

Os nomes e formatos não são universais. O front-end deve seguir o contrato da API.

---

# 2 - Paginação por página

O modelo mais simples usa número da página e quantidade de itens:

```txt
GET /produtos?page=2&pageSize=20
```

Uma resposta útil inclui dados e metadados:

```json
{
    "items": [
        {
            "id": 21,
            "nome": "Produto 21"
        }
    ],
    "page": 2,
    "pageSize": 20,
    "totalItems": 93,
    "totalPages": 5
}
```

Sem metadados, o front-end não sabe quantas páginas existem.

---

# 3 - Paginação por offset

Outro contrato comum usa:

```txt
GET /produtos?limit=20&offset=20
```

O `offset` indica quantos registros devem ser ignorados. Para a página 2 com 20 itens:

```js
const offset = (pagina - 1) * limite;
```

Esse modelo é simples, mas grandes offsets podem custar mais no banco e alterações durante a navegação podem deslocar resultados.

---

# 4 - Paginação por cursor

APIs com feeds ou muitos dados podem devolver um cursor:

```json
{
    "items": [],
    "nextCursor": "eyJpZCI6MTAwfQ==",
    "hasNextPage": true
}
```

A próxima requisição usa:

```txt
GET /produtos?limit=20&cursor=eyJpZCI6MTAwfQ%3D%3D
```

O front-end trata o cursor como valor opaco: não deve interpretá-lo ou inventá-lo.

---

# 5 - Filtros

```js
function criarParametros(consulta) {
    const params = new URLSearchParams();

    params.set("page", String(consulta.page));
    params.set("pageSize", String(consulta.pageSize));

    if (consulta.busca) {
        params.set("busca", consulta.busca);
    }

    if (consulta.categoria) {
        params.set("categoria", consulta.categoria);
    }

    if (consulta.precoMinimo !== undefined) {
        params.set("precoMinimo", String(consulta.precoMinimo));
    }

    return params;
}
```

Quando um filtro muda, normalmente a página volta para 1. Caso contrário, o usuário pode permanecer em uma página que não existe no novo resultado.

---

# 6 - Ordenação

Um contrato pode usar:

```txt
?sort=preco&order=asc
```

ou:

```txt
?sort=-preco
```

O back-end deve permitir apenas campos conhecidos. O front-end também deve oferecer opções válidas em vez de aceitar qualquer nome.

---

# 7 - Exemplo completo

```js
async function buscarProdutos(consulta, signal) {
    const params = criarParametros(consulta);

    params.set("sort", consulta.sort);
    params.set("order", consulta.order);

    const response = await fetch(`/api/produtos?${params}`, {
        signal
    });

    if (!response.ok) {
        throw new Error(`Erro ao buscar produtos: ${response.status}`);
    }

    return response.json();
}
```

Estado inicial:

```js
const consulta = {
    page: 1,
    pageSize: 20,
    busca: "",
    categoria: "",
    sort: "nome",
    order: "asc"
};
```

---

# 8 - Busca digitada e debounce

Não faça uma requisição para cada tecla sem controle. Um debounce aguarda uma pequena pausa:

```js
function debounce(funcao, atraso) {
    let timerId;

    return (...args) => {
        clearTimeout(timerId);
        timerId = setTimeout(() => funcao(...args), atraso);
    };
}

const buscarAoDigitar = debounce((busca) => {
    buscarProdutos({
        ...consulta,
        busca,
        page: 1
    });
}, 300);
```

Em uma interface real, cancele também a requisição anterior para impedir que uma resposta antiga sobrescreva a nova.

---

# 9 - Estado da interface

A tela deve prever:

- carregamento inicial;
- troca de página;
- nenhum resultado;
- erro;
- filtros ativos;
- botão anterior/próximo desabilitado;
- resposta antiga chegando depois da nova.

Paginação é mais que montar uma URL: é manter consulta, resposta e interface sincronizadas.

---

# 10 - Erros comuns

- filtrar apenas a página atual no front-end quando o conjunto completo está no servidor;
- não voltar à primeira página ao mudar filtros;
- confiar em nomes de ordenação não aceitos;
- fazer busca a cada tecla sem debounce;
- ignorar respostas fora de ordem;
- calcular total de páginas sem usar o contrato da API.

---

# 11 - Boas práticas

- mantenha a consulta em um único estado;
- use `URLSearchParams`;
- sincronize filtros compartilháveis com a URL;
- preserve metadados fornecidos pela API;
- cancele buscas substituídas;
- mantenha filtros no servidor quando os dados também estão paginados no servidor.

---

# 12 - Conclusão

Paginação, filtros e ordenação formam uma única consulta. O front-end descreve o que precisa; o back-end consulta os dados e devolve itens com metadados. A qualidade do fluxo depende de um contrato claro e de estados de interface bem tratados.
