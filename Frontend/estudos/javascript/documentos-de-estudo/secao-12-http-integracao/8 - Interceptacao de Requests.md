# Interceptação de Requests

# 1 - O que é interceptação

Interceptar uma requisição significa executar uma lógica comum antes de enviá-la ou depois de receber sua resposta.

Isso é útil para:

- adicionar a URL base;
- incluir autenticação;
- padronizar headers;
- converter JSON;
- tratar erros recorrentes;
- registrar informações de diagnóstico.

A Fetch API não possui interceptors nativos. Em JavaScript puro, criamos uma função que envolve o `fetch`.

---

# 2 - Por que centralizar

Sem centralização, cada função repete a mesma configuração:

```js
const response = await fetch("https://api.exemplo.com/produtos", {
    headers: {
        Accept: "application/json",
        Authorization: `Bearer ${token}`
    }
});
```

Repetição aumenta o risco de uma tela esquecer autenticação, interpretar erros de forma diferente ou usar outra URL.

---

# 3 - Cliente HTTP básico

```js
const API_URL = "https://api.exemplo.com";

async function apiFetch(caminho, options = {}) {
    const token = sessionStorage.getItem("accessToken");
    const headers = new Headers(options.headers);

    headers.set("Accept", "application/json");

    if (token) {
        headers.set("Authorization", `Bearer ${token}`);
    }

    const response = await fetch(`${API_URL}${caminho}`, {
        ...options,
        headers
    });

    if (response.status === 401) {
        sessionStorage.removeItem("accessToken");
        throw new Error("Sua sessão expirou.");
    }

    if (!response.ok) {
        throw new Error(`Erro HTTP: ${response.status}`);
    }

    return response;
}
```

Uso:

```js
async function buscarProdutos() {
    const response = await apiFetch("/produtos");
    return response.json();
}
```

`new Headers(options.headers)` preserva os headers específicos da chamada e permite acrescentar os padrões.

---

# 4 - Enviando JSON

Uma função auxiliar pode cuidar do body:

```js
async function apiJson(caminho, options = {}) {
    const headers = new Headers(options.headers);
    headers.set("Content-Type", "application/json");

    const response = await apiFetch(caminho, {
        ...options,
        headers,
        body: options.body ? JSON.stringify(options.body) : undefined
    });

    if (response.status === 204) {
        return null;
    }

    return response.json();
}
```

```js
const produto = await apiJson("/produtos", {
    method: "POST",
    body: {
        nome: "Teclado",
        preco: 150
    }
});
```

Não use esse helper para `FormData`, pois upload não deve receber `Content-Type: application/json`.

---

# 5 - Tratamento de erros

Em projetos maiores, crie um erro com dados da resposta:

```js
class ApiError extends Error {
    constructor(message, status, data) {
        super(message);
        this.name = "ApiError";
        this.status = status;
        this.data = data;
    }
}
```

A camada central pode tentar ler o body de erro e lançar `ApiError`. A tela ainda decide como apresentar a falha: mensagem no formulário, aviso global ou redirecionamento.

Centralizar não significa esconder todas as decisões da interface.

---

# 6 - Refresh token e concorrência

Quando várias requisições recebem `401` ao mesmo tempo, iniciar um refresh para cada uma pode gerar erros e invalidações indevidas. Uma implementação real deve manter uma única renovação em andamento e fazer as demais aguardarem.

Esse fluxo exige cuidado para:

- não criar loop de refresh;
- não tentar renovar o próprio endpoint de refresh;
- repetir uma requisição no máximo uma vez;
- encerrar a sessão se a renovação falhar.

Só implemente refresh automático quando esse for o contrato real da API.

---

# 7 - Interceptors em bibliotecas e frameworks

Axios possui interceptors próprios. Angular usa `HttpInterceptor`. Em React, normalmente se usa um cliente HTTP compartilhado ou uma biblioteca de cache de servidor.

O conceito é o mesmo, mas a implementação deve seguir a ferramenta já adotada pelo projeto.

---

# 8 - Erros comuns

- chamar o `fetch` original e o wrapper sem necessidade;
- sobrescrever headers específicos da chamada;
- aplicar JSON a `FormData`;
- redirecionar em qualquer erro, incluindo `403` e `500`;
- criar repetição infinita após `401`;
- misturar regra visual dentro do cliente HTTP;
- registrar tokens ou senhas no console.

---

# 9 - Boas práticas

- mantenha o cliente HTTP pequeno;
- preserve opções e headers da chamada;
- diferencie `401`, `403` e erros de servidor;
- não acople o cliente a uma única tela;
- evite abstração antes de existir repetição real;
- teste fluxos de sucesso, erro, expiração e cancelamento.

---

# 10 - Conclusão

Interceptação concentra comportamentos técnicos repetidos sem retirar da tela as decisões de interface. Com Fetch, um wrapper simples costuma ser suficiente. A abstração deve facilitar leitura e manutenção, não esconder todo o funcionamento HTTP.
