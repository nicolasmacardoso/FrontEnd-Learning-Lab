# 1 - O que é autenticação?

Processo usado para descobrir quem é o usuário.

Na prática, ela responde esta pergunta?

> Quem está usando o sistema

Exemplo comum:

1. O usuário informa e-mail e senha.
2. O front-end envia esses dados para a API.
3. A API valida se os dados estão corretos.
4. Caso estejam corretos, a API devolve uma forma de identificar esse usuário nas próximas requisições.

Autenticação não é a mesma coisa que autorização.

Autenticação:

> <span style="color: white;">Quem é você?</span>

Autorização:

> <span style="color: white;">Você tem permissão para isso?</span>

Exemplo:

Um usuário pode estar autenticado, mas não ter permissão para acessar uma área administrativa. Nesse caso, ele está logado, mas não autorizado.

---

# 2 - Por que autenticação existe?

Sem ela, qualquer pessoa poderia acessar qualquer dado da aplicação.

Por exemplo, uma tela de perfil:

```http
GET /profile
```

Se essa rota não tiver autenticação, qualquer usuário poderia tentar acessar dados privados.

Com autenticação, a API exige alguma prova de identidade, normalmente um token:

```http
GET /profile
Authorization: Bearer token_aqui
```

Assim, o servidor consegue identificar quem está fazendo a requisição.

---

# 3 - O que é JWT?

Significa JSON Web Token.

É um tipo de token muito usado para autenticação em APIs.

De forma simples, um JWT é uma string gerada pelo servidor depois que o usuário faz login. Essa string carrega informações que ajudam o servidor a reconhecer o usuário nas próximas requisições.

Exemplo visual de um JWT:

```txt
xxxxx.yyyyy.zzzzz
```

Ele costuma ter três partes:

- header: informações sobre o tipo de token e algoritmo usado;
- payload: dados sobre o usuário ou sessão;
- signature: assinatura usada para verificar se o token foi alterado.

O ponto importante aqui é:

> <span style="color: white;">O front-end não cria o JWT. Quem cria e valida o JWT é o back-end.</span>

---

# 4 - Fluxo básico com JWT

Fluxo comum:

1 - Usuário faz login.
2 - Front-end envia e-mail e senha para a API.
3 - API valida os dados.
4 - API gera um JWT.
5 - Front-end guarda o token.
6 - Front-end envia o token nas próximas requisições.
7 - API valida o token antes de devolver dados protegidos.

Exemplo de login:

```http
POST /auth/login
Content-Type: application/json

{
    "email": "usuario@email.com",
    "password": "123456"
}
```

Resposta:

```json
{
    "token": "token_jwt_aqui"
}
```

Depois disso, o token deve ser enviado no header ``Authorization``.

---

# 5 - Enviando JWT nas requisições

Depois do login, o front-end usa o token para acessar rotas protegidas.

```js
async function buscarPerfil() {
    const response = await fetch("/api/profile", {
        method: "GET",
        headers: {
            Accept: "application/json",
            Authorization: `Bearer ${token}`
        }
    });

    if (!response.ok) {
        throw new Error("Erro ao buscar perfil");
    }

    return response.json();
}
```

O header:
```http
Authorization: Bearer token_jwt_aqui
``` 

Segue o padrão Bearer Token.

`Bearer` indica que quem possui aquele token pode usá-lo para acessar o recurso, desde que ele seja válido.

---

# 6 - O que acontece no back-end?

Quando a API recebe uma requisição protegida, ela normalmente faz estes passos:

1 - Lê o header `Authorization`.
2 - Verifica se existe um token.
3 - Valida a assinatura do JWT.
4 - Verifica se o token expirou.
5 - Extrai as informações do usuário.
6 - Decide se o usuário pode acessar o recurso.

Exemplo conceitual:

```txt
Request -> Middleware de autenticação -> Controller -> Service -> Response
```

Em APIs reais, essa validação geralmente fica em middlewares, filters ou mecanismos próprios do framework.

---

# 7 - Payload do JWT

O payload pode carregar informações como:

```json
{
    "sub": "123",
    "name": "Nícolas",
    "role": "admin",
    "exp": 1712345678
}
```

Campos comuns: 

- `sub`: identificador do usuário.
- `name`: nome do usuário.
- `role`: perfil ou permissão do usuário.
- `exp`: data em timestamp de expiração do token.

Cuidado importante:
> <span style="color: white;">O payload do JWT não deve conter dados sensíveis.</span>

Mesmo que o token pareça embaralhado, o payload pode ser decodificado. Portanto, não coloque senha, CPF, dados bancários ou informações sigilosas dentro dele.

---

# 8 - Token assinado não é token criptografado

Um erro comum é achar que JWT é sempre criptografado.
Na maioria dos casos, é apenas ASSINADO.

Significa:

> <span style="color: white;">O servidor consegue verificar se o token foi alterado.</span>

Criptografado significa:

> <span style="color: white;">O conteúdo fica escondido para quem não tem a chave</span>

Na prática, um JWT comum pode ser lido, mas não pode ser alterado sem invalidar a assinatura.

---

# 9 - Expiração do token

Tokens geralmente têm tempo de validade.

Isso evita que um token roubado funcione para sempre.

Exemplo:

```json
{
    "token": "token_jwt_aqui",
    "expiresIn": 3600
}
```

Nesse exemplo, o token expira em 3600 segundos (1 hora).

Quando o token expira, a API pode retornar:

```http
401 unauthorized
```

No front-end, uma reação comum é:
- remover o token salvo;
- redirecionar o usuário para a tela de login;
- exibir mensagem de sessão expirada.

---

# 10 - Onde guardar o token no front-end

Existem algumas opções, cada uma com prós e contras:

### Memória da aplicação:

O token fica em uma variável ou estado global.

Pró:
- não persiste depois de carregar a página.

Contra:
- O usuário perde a sessão ao atualizar a página.

### LocalStorage

O token fica salvo no navegador.

Prós:
- simples de usar;
- persiste depois de recarregar a página.

Contras:
- Pode ser acessado por JavaScript;
- Se houver XSS, o token pode ser roubado.

### Cookie HttpOnly

O token ou sessão fica em cookie configurado pelo servidor.

Prós:
- JavaScript não consegue ler cookie HttpOnly;
- Mais seguro contra XSS.
Contra:
- Exige cuidado com CORS, `credentials`, SameSite e CSRF.

Para estudo de front-end, é comum começar entendendo `Authorization: Bearer token`. Depois vale aprofundar cookies, sessão e credentials.

---

# 11 - Refresh token

Em sistemas mais completos, pode existir:

- access token;
- refresh token.

O `access token` é usado nas requisições normais e costuma durar pouco tempo. A duração é uma decisão de segurança do sistema, não um valor fixo do JWT.
O ``refresh token`` serve para pedir um novo access token sem obrigar o usuário a fazer login toda hora.

Fluxo comum:
1 - Usuário faz login.
2 - API retorna access token e refresh token.
3 - Front-end usa access token nas requisições.
4 - Access token expira.
5 - Front-end usa refresh token para pedir outro access token.
6 - Se o refresh token também estiver inválido, o usuário faz login novamente.

---

# 12 - Erros comuns

### Colocar dados sensíveis no JWT
JWT pode ser decodificado. Não coloque informações sigilosas no payload.

### Esquecer o Bearer
Errado:
```http
Authorization: token_jwt_aqui
```

Certo:

```http
Authorization: Bearer token_jwt_aqui
```

### Tratar todo erro como login inválido
Nem todo erro no login é senha errada.

Pode existir:
- 400: body inválido;
- 401: credenciais inválidas;
- 429: muitas tentativas;
- 500: erro interno.

### Achar que autenticação resolve autorização
Login identifica o usuário. Permissão define o que ele pode fazer.

---

# 13 - Logout

No logout, o front-end deve apagar os dados locais da sessão. Se o sistema usa refresh token ou sessão mantida pelo servidor, também deve chamar o endpoint de logout para invalidá-los.

Apagar apenas o access token da interface não invalida automaticamente um token que ainda seja aceito pelo back-end.

---

# 14 - Boas práticas
- use HTTPS em produção;
- não salve senha no front-end;
- não salve dados sensíveis no JWT;
- use expiração nos tokens;
- trate 401 e 403 separadamente;
- remova o token quando a sessão expirar;
- envie JWT pelo header ``Authorization``;
- valide permissões no back-end;
- não confie em validações apenas no front-end;
- use cookies ``HttpOnly`` quando o projeto exigir maior segurança;
- proteja rotas sensíveis contra excesso de tentativas.

---

# 15 - Relação com outros estudos
Antes deste conteúdo, vale revisar:
1 - [Fetch API.md](./1%20-%20Fetch%20API.md);
2 - [REST API.md](./2%20-%20REST%20API.md);
3 - [Status HTTP.md](./3%20-%20Status%20HTTP.md);
4 - [Headers HTTP.md](./4%20-%20Headers%20HTTP.md).

Este estudo depende principalmente de:
- fetch, para enviar requisições;
- headers, para usar Authorization;
- status HTTP, para entender 401 e 403;
- REST API, para entender rotas protegidas e comunicação stateless.

Depois deste conteúdo, faz sentido estudar:
- CORS;
- cookies;
- sessão;
- credentials;
- interceptação de requests.

---

# 16 - Conclusão

Autenticação comprova a identidade do usuário; autorização decide o que ele pode fazer. O JWT é uma forma de transportar informações assinadas entre cliente e servidor, mas não substitui HTTPS, validação no back-end ou uma estratégia segura de sessão.

No front-end, as responsabilidades principais são enviar as credenciais, armazenar a sessão conforme o contrato da aplicação, incluir o token quando necessário e tratar corretamente expiração, logout, `401` e `403`.
