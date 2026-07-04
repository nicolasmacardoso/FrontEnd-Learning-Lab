# Query Params e URLSearchParams

# 1 - O que são query parameters

Query parameters são pares `chave=valor` adicionados depois de `?` na URL:

```txt
/produtos?categoria=livros&pagina=2
```

O caractere `&` separa os parâmetros. Eles costumam representar busca, filtros, paginação, ordenação e opções de visualização.

Não use query params para dados sensíveis: URLs podem aparecer no histórico, em logs e em ferramentas de monitoramento.

---

# 2 - Parâmetro de rota ou query param

Parâmetro de rota identifica um recurso:

```txt
GET /produtos/42
```

Query param modifica a consulta de uma coleção:

```txt
GET /produtos?categoria=livros
```

Uma URL como `/produtos/42?incluirAvaliacoes=true` combina os dois papéis.

---

# 3 - Problema da concatenação manual

```js
const url = `/produtos?busca=${busca}&categoria=${categoria}`;
```

Espaços, acentos, `&`, `=` e outros caracteres precisam ser codificados. Também é fácil gerar valores como `undefined`.

`URLSearchParams` resolve a serialização:

```js
const params = new URLSearchParams({
    busca: "café especial",
    categoria: "alimentos"
});

console.log(params.toString());
```

Resultado:

```txt
busca=caf%C3%A9+especial&categoria=alimentos
```

---

# 4 - Construindo uma URL

```js
function criarUrlProdutos(filtros) {
    const url = new URL("/produtos", "https://api.exemplo.com");
    const params = new URLSearchParams();

    if (filtros.busca) {
        params.set("busca", filtros.busca);
    }

    if (filtros.categoria) {
        params.set("categoria", filtros.categoria);
    }

    if (filtros.pagina) {
        params.set("pagina", String(filtros.pagina));
    }

    url.search = params.toString();
    return url.toString();
}
```

```js
const url = criarUrlProdutos({
    busca: "teclado mecânico",
    categoria: "periféricos",
    pagina: 2
});

const response = await fetch(url);
```

As condições impedem o envio acidental de campos ausentes.

---

# 5 - Métodos principais

```js
const params = new URLSearchParams();

params.set("pagina", "1");
params.append("tag", "javascript");
params.append("tag", "frontend");

params.get("pagina");
params.getAll("tag");
params.has("pagina");
params.delete("pagina");
params.toString();
```

`set` substitui o valor existente. `append` permite repetir a chave, se a API aceitar esse formato:

```txt
tag=javascript&tag=frontend
```

O contrato também poderia exigir `tag=javascript,frontend`; confirme o padrão da API.

---

# 6 - Lendo parâmetros da página

```js
const params = new URLSearchParams(window.location.search);
const busca = params.get("busca") ?? "";
const pagina = Number(params.get("pagina") ?? "1");
```

Todos os valores chegam como string. Converta e valide antes de usar:

```js
const paginaValida = Number.isInteger(pagina) && pagina > 0 ? pagina : 1;
```

---

# 7 - Sincronização com a URL

Filtros na URL permitem compartilhar a busca, usar voltar/avançar e restaurar o estado ao recarregar.

```js
const url = new URL(window.location.href);
url.searchParams.set("busca", "monitor");
url.searchParams.set("pagina", "1");

window.history.pushState({}, "", url);
```

Em aplicações com framework, prefira o mecanismo de navegação do roteador.

---

# 8 - Valores opcionais

Evite enviar filtros vazios:

```txt
/produtos?categoria=&pagina=undefined
```

Monte apenas os parâmetros significativos. Também diferencie ausência de valores válidos como `0` ou `false`:

```js
if (filtros.ativo !== undefined) {
    params.set("ativo", String(filtros.ativo));
}
```

---

# 9 - Erros comuns

- concatenar texto sem codificação;
- enviar `undefined` ou `null`;
- assumir que números e booleanos já vêm convertidos;
- colocar senha ou token na URL;
- inventar formato de arrays diferente do contrato;
- não reiniciar a página ao mudar um filtro.

---

# 10 - Boas práticas

- use `URL` e `URLSearchParams`;
- mantenha nomes iguais aos definidos pela API;
- valide valores lidos da URL;
- omita parâmetros sem significado;
- use URL para estado que deve ser compartilhável;
- extraia a montagem para uma função quando houver muitos filtros.

---

# 11 - Conclusão

Query params descrevem variações de uma consulta sem mudar o recurso principal. `URLSearchParams` evita erros de codificação e deixa a montagem legível. O próximo passo é aplicá-los em paginação, filtros e ordenação.
