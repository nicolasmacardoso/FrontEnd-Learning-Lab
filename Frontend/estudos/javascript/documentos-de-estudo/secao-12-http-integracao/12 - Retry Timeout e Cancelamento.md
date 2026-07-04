# Retry, Timeout e Cancelamento

# 1 - Diferenças

- **retry**: tenta novamente após uma falha transitória;
- **timeout**: define quanto tempo a aplicação aceita esperar;
- **cancelamento**: encerra uma operação que perdeu a utilidade.

São mecanismos diferentes, embora todos usem tratamento assíncrono.

---

# 2 - Cancelamento com AbortController

```js
const controller = new AbortController();

const promise = fetch("/api/produtos", {
    signal: controller.signal
});

controller.abort();
```

Quando a requisição é abortada, a Promise rejeita. É importante diferenciar cancelamento de erro:

```js
try {
    const response = await promise;
    const produtos = await response.json();
} catch (error) {
    if (error.name === "AbortError") {
        console.log("Requisição cancelada.");
    } else {
        throw error;
    }
}
```

---

# 3 - Cancelar uma busca anterior

```js
let controllerAtual;

async function buscarProdutos(termo) {
    controllerAtual?.abort();
    controllerAtual = new AbortController();

    try {
        const params = new URLSearchParams({ busca: termo });
        const response = await fetch(`/api/produtos?${params}`, {
            signal: controllerAtual.signal
        });

        if (!response.ok) {
            throw new Error(`Erro HTTP: ${response.status}`);
        }

        return response.json();
    } catch (error) {
        if (error.name === "AbortError") {
            return null;
        }

        throw error;
    }
}
```

Isso evita que resultados antigos substituam uma busca mais recente.

---

# 4 - Timeout

Ambientes modernos permitem criar um signal de timeout:

```js
const response = await fetch("/api/relatorio", {
    signal: AbortSignal.timeout(5000)
});
```

Quando compatibilidade ou composição exigirem controle manual:

```js
async function fetchComTimeout(url, options = {}, timeout = 5000) {
    const controller = new AbortController();
    const timerId = setTimeout(() => controller.abort(), timeout);

    try {
        return await fetch(url, {
            ...options,
            signal: controller.signal
        });
    } finally {
        clearTimeout(timerId);
    }
}
```

Um timeout do front-end não significa que o servidor interrompeu todo o processamento. Ele apenas encerra a espera do cliente.

---

# 5 - Quando usar retry

Retry pode ajudar em falhas temporárias:

- erro de rede;
- `408 Request Timeout`;
- `429 Too Many Requests`;
- alguns erros `5xx`, como `502`, `503` e `504`.

Normalmente não deve repetir:

- `400` por dados inválidos;
- `401` sem renovar a sessão;
- `403`;
- `404`;
- falhas permanentes de validação.

---

# 6 - Cuidado com métodos que alteram dados

Repetir `GET` costuma ser seguro. Repetir `POST` pode criar dois pedidos ou duas cobranças se a primeira resposta falhar depois de o servidor concluir a operação.

Para operações críticas, o contrato pode oferecer uma chave de idempotência:

```http
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
```

Não implemente retry automático de mutações sem garantia do back-end.

---

# 7 - Exponential backoff

Várias tentativas imediatas aumentam a sobrecarga. O backoff aumenta a espera:

```js
function esperar(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
}

async function fetchComRetry(url, options = {}, tentativas = 3) {
    let ultimoErro;

    for (let tentativa = 0; tentativa < tentativas; tentativa += 1) {
        try {
            const response = await fetch(url, options);

            if (response.ok || response.status < 500) {
                return response;
            }

            ultimoErro = new Error(`Erro HTTP: ${response.status}`);
        } catch (error) {
            ultimoErro = error;
        }

        if (tentativa < tentativas - 1) {
            const atraso = 500 * 2 ** tentativa;
            await esperar(atraso);
        }
    }

    throw ultimoErro;
}
```

Uma implementação de produção também deve respeitar `Retry-After`, aceitar cancelamento e adicionar pequena variação aleatória para evitar que muitos clientes repitam juntos.

---

# 8 - Combinando signals

Uma operação pode ser cancelada pelo usuário e também ter timeout. Em ambientes com suporte:

```js
const controller = new AbortController();
const signal = AbortSignal.any([
    controller.signal,
    AbortSignal.timeout(5000)
]);

await fetch("/api/produtos", { signal });
```

Confirme o suporte exigido pelo projeto antes de usar APIs recentes.

---

# 9 - Erros comuns

- repetir qualquer status;
- aplicar retry infinito;
- repetir `POST` sem idempotência;
- mostrar cancelamento esperado como erro;
- esquecer de limpar o timer;
- achar que `Promise.race` cancela o `fetch`;
- usar timeout curto sem considerar a operação real.

---

# 10 - Boas práticas

- cancele operações substituídas ou telas desmontadas;
- defina timeout conforme a experiência esperada;
- use poucas tentativas e backoff;
- respeite `Retry-After`;
- limite retry a falhas transitórias e operações seguras;
- permita que o signal atravesse as camadas da aplicação;
- registre a falha final sem expor dados sensíveis.

---

# 11 - Conclusão

Cancelamento remove trabalho que perdeu a utilidade, timeout limita a espera e retry tenta recuperar falhas temporárias. Usados sem critério, esses mecanismos duplicam operações e sobrecarregam a API; usados com contrato claro, tornam a interface mais previsível e resiliente.
