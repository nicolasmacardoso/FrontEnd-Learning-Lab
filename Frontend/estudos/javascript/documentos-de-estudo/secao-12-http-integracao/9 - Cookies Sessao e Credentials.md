# Cookies, Sessão e Credentials

# 1 - O que é um cookie

Cookie é um pequeno dado associado a um domínio e armazenado pelo navegador. Ele pode ser enviado automaticamente em requisições compatíveis.

Uma resposta pode criar um cookie:

```http
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Lax; Path=/
```

Nas próximas requisições, o navegador pode enviar:

```http
Cookie: sessionId=abc123
```

O JavaScript não deve montar manualmente o header `Cookie`; o navegador controla esse envio.

---

# 2 - O que é sessão

Sessão é o estado que representa a autenticação do usuário ao longo de várias requisições.

Em uma sessão tradicional:

1. o usuário envia login e senha;
2. o servidor valida os dados;
3. o servidor cria uma sessão e guarda seus dados;
4. o navegador recebe um cookie com o identificador;
5. nas próximas requisições, o servidor usa esse identificador para localizar a sessão.

```txt
Cookie com ID -> sessão armazenada no servidor -> dados do usuário
```

O cookie não precisa carregar todos os dados da sessão.

---

# 3 - Cookies e JWT

Cookie é um mecanismo de armazenamento e transporte do navegador. JWT é um formato de token. Eles não são concorrentes obrigatórios.

Um sistema pode usar:

- ID de sessão em cookie;
- JWT no header `Authorization`;
- JWT em cookie `HttpOnly`;
- access token em memória e refresh token em cookie.

A escolha depende do contrato e do modelo de segurança da aplicação.

---

# 4 - Atributos importantes

## HttpOnly

Impede que JavaScript leia o cookie. Ajuda a proteger seu conteúdo em caso de XSS, embora não impeça que requisições sejam feitas pelo navegador.

## Secure

Envia o cookie apenas por HTTPS, com exceções controladas para desenvolvimento local conforme o navegador.

## SameSite

Controla o envio do cookie em contextos entre sites:

- `Strict`: comportamento mais restritivo;
- `Lax`: permite alguns fluxos de navegação;
- `None`: permite contexto cross-site e exige `Secure`.

## Path e Domain

Limitam em quais caminhos e domínios o cookie pode ser enviado.

## Max-Age e Expires

Definem persistência. Sem eles, normalmente o cookie é de sessão do navegador.

---

# 5 - Credentials na Fetch API

A opção `credentials` controla o envio e o recebimento de credenciais:

```js
const response = await fetch("https://api.exemplo.com/profile", {
    credentials: "include"
});
```

Valores:

- `same-origin`: envia credenciais apenas para a mesma origem; é o padrão;
- `include`: inclui credenciais também em requisições entre origens;
- `omit`: não inclui credenciais.

Para cookies entre origens, a API também precisa configurar CORS corretamente:

```http
Access-Control-Allow-Origin: https://app.exemplo.com
Access-Control-Allow-Credentials: true
```

Não é permitido combinar credenciais com `Access-Control-Allow-Origin: *`.

---

# 6 - Exemplo de login com sessão

```js
async function login(email, password) {
    const response = await fetch("https://api.exemplo.com/auth/login", {
        method: "POST",
        credentials: "include",
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify({ email, password })
    });

    if (!response.ok) {
        throw new Error("Não foi possível entrar.");
    }

    return response.json();
}
```

Se a API responder com `Set-Cookie`, o navegador armazena o cookie conforme seus atributos. Um cookie `HttpOnly` não aparece em `document.cookie`, e isso é esperado.

---

# 7 - Logout

O front-end chama o endpoint; o servidor invalida a sessão e expira o cookie:

```js
await fetch("https://api.exemplo.com/auth/logout", {
    method: "POST",
    credentials: "include"
});
```

Apagar estado visual sem invalidar a sessão no servidor pode deixar a credencial utilizável.

---

# 8 - XSS e CSRF

**XSS** permite que código malicioso execute na página. Cookies `HttpOnly` reduzem o risco de leitura do token, mas a aplicação ainda precisa evitar injeção de scripts.

**CSRF** tenta fazer o navegador autenticado enviar uma ação indesejada. Proteções comuns incluem:

- `SameSite`;
- token CSRF;
- validação de `Origin`;
- confirmação para ações sensíveis.

O back-end deve definir a estratégia; o front-end precisa seguir o contrato.

---

# 9 - Erros comuns

- tentar ler cookie `HttpOnly`;
- esquecer `credentials: "include"` entre origens;
- usar `SameSite=None` sem `Secure`;
- liberar origem `*` com credenciais;
- guardar senha em cookie ou storage;
- acreditar que cookie elimina a necessidade de proteção contra XSS e CSRF.

---

# 10 - Boas práticas

- use HTTPS;
- prefira cookies `HttpOnly` para credenciais que não precisam ser lidas pelo JavaScript;
- defina `Secure`, `SameSite`, `Path` e expiração conscientemente;
- invalide a sessão no servidor durante logout;
- não escolha armazenamento apenas pela facilidade;
- alinhe front-end, CORS e back-end como um único contrato.

---

# 11 - Conclusão

Cookies transportam dados segundo regras do navegador; sessões mantêm a identidade entre requisições; `credentials` controla a participação dessas credenciais no Fetch. Entender a diferença evita misturar armazenamento, formato de token e estratégia de autenticação.
