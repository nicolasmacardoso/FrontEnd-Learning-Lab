# CORS

# 1 - O que é CORS

CORS significa **Cross-Origin Resource Sharing**. É o mecanismo usado pelo navegador para decidir se uma página pode acessar uma resposta fornecida por outra origem.

Uma origem é formada por:

```txt
protocolo + domínio + porta
```

Estas URLs possuem origens diferentes:

```txt
http://localhost:3000
http://localhost:8080
https://api.exemplo.com
```

Mesmo que pertençam ao mesmo projeto, mudar protocolo, domínio ou porta muda a origem.

---

# 2 - Por que CORS existe

Sem essa proteção, um site malicioso aberto no navegador poderia tentar ler dados de outro sistema no qual o usuário está autenticado.

CORS não é uma permissão configurada pelo front-end. O servidor informa quais origens, métodos e headers aceita, e o navegador aplica a regra.

Por isso uma chamada pode funcionar no Postman ou Insomnia e falhar no navegador: clientes HTTP fora do navegador não aplicam a política CORS.

---

# 3 - Requisição de origem diferente

```js
const response = await fetch("https://api.exemplo.com/produtos");

if (!response.ok) {
    throw new Error(`Erro HTTP: ${response.status}`);
}

const produtos = await response.json();
```

Para o JavaScript poder ler a resposta, a API pode devolver:

```http
Access-Control-Allow-Origin: https://app.exemplo.com
```

O header libera uma origem específica. Usar `*` libera qualquer origem, mas não é compatível com requisições que incluem credenciais.

---

# 4 - Requisições simples e preflight

Algumas requisições podem ser enviadas diretamente. Outras exigem uma verificação anterior chamada **preflight**.

Antes da requisição real, o navegador envia `OPTIONS`:

```http
OPTIONS /usuarios
Origin: https://app.exemplo.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type, Authorization
```

O servidor responde com as permissões:

```http
Access-Control-Allow-Origin: https://app.exemplo.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

Se a resposta não autorizar a operação, o navegador bloqueia a requisição real ou impede o front-end de acessar a resposta.

---

# 5 - CORS com cookies

Para enviar cookies em outra origem:

```js
const response = await fetch("https://api.exemplo.com/profile", {
    credentials: "include"
});
```

A API precisa responder com uma origem explícita e:

```http
Access-Control-Allow-Credentials: true
```

Além disso, o cookie deve estar configurado de forma compatível, normalmente com `SameSite` e `Secure` adequados. Esses pontos são aprofundados em [9 - Cookies Sessao e Credentials.md](./9%20-%20Cookies%20Sessao%20e%20Credentials.md).

---

# 6 - Erros comuns

## Tentar corrigir CORS apenas no front-end

Adicionar headers inventados no `fetch` não autoriza a origem. A correção normalmente está na configuração da API ou do proxy usado no desenvolvimento.

## Usar `mode: "no-cors"`

Isso não libera CORS. A resposta se torna opaca: o front-end não consegue ler status, headers ou body.

## Liberar qualquer origem sem necessidade

Em produção, prefira uma lista explícita de origens confiáveis, especialmente quando houver autenticação.

## Confundir CORS com autenticação

CORS controla o acesso do navegador à resposta. Autenticação identifica o usuário. Uma API ainda precisa validar token, sessão e permissões.

---

# 7 - Como investigar

1. Abra a aba **Network** do navegador.
2. Procure a requisição `OPTIONS`, quando existir.
3. Confira `Origin` na requisição.
4. Confira os headers `Access-Control-Allow-*` na resposta.
5. Verifique se método, headers e credenciais foram autorizados.
6. Corrija a configuração do servidor ou do proxy.

---

# 8 - Boas práticas

- configure CORS no back-end;
- libere apenas origens, métodos e headers necessários;
- não use `no-cors` como correção;
- trate desenvolvimento e produção com listas de origens adequadas;
- não use CORS como substituto de autenticação e autorização;
- configure credenciais dos dois lados quando usar cookies entre origens.

---

# 9 - Conclusão

CORS é uma política de segurança aplicada pelo navegador. O front-end inicia a requisição, mas quem declara a permissão é a API. Entender origem, preflight e headers `Access-Control-Allow-*` permite diagnosticar o problema sem recorrer a soluções que apenas escondem o erro.
