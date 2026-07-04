# Upload de Arquivos

# 1 - Como um arquivo é enviado

Em aplicações web, o usuário escolhe um arquivo por meio de um `<input type="file">`. O navegador fornece objetos `File`, que contêm dados como nome, tipo, tamanho e conteúdo.

```html
<input id="arquivo" type="file" accept="image/png,image/jpeg">
```

```js
const input = document.querySelector("#arquivo");
const arquivo = input.files[0];

console.log(arquivo.name);
console.log(arquivo.type);
console.log(arquivo.size);
```

O atributo `accept` ajuda na seleção, mas não garante segurança. O back-end ainda deve validar o arquivo.

---

# 2 - FormData

Para enviar arquivos, o formato mais comum é `multipart/form-data`, construído no front-end com `FormData`.

```js
const formData = new FormData();

formData.append("arquivo", arquivo);
formData.append("descricao", "Foto do produto");

const response = await fetch("/api/uploads", {
    method: "POST",
    body: formData
});
```

Não defina manualmente o header `Content-Type`. O navegador adiciona o `boundary` necessário:

```http
Content-Type: multipart/form-data; boundary=...
```

Sem o boundary correto, o servidor pode não conseguir separar os campos.

---

# 3 - Exemplo completo

```js
const formulario = document.querySelector("#form-upload");
const mensagem = document.querySelector("#mensagem");

formulario.addEventListener("submit", async (event) => {
    event.preventDefault();

    const arquivo = formulario.elements.arquivo.files[0];

    if (!arquivo) {
        mensagem.textContent = "Selecione um arquivo.";
        return;
    }

    const limite = 5 * 1024 * 1024;

    if (arquivo.size > limite) {
        mensagem.textContent = "O arquivo deve ter no máximo 5 MB.";
        return;
    }

    const formData = new FormData();
    formData.append("arquivo", arquivo);

    try {
        mensagem.textContent = "Enviando...";

        const response = await fetch("/api/uploads", {
            method: "POST",
            body: formData
        });

        if (!response.ok) {
            throw new Error(`Falha no upload: ${response.status}`);
        }

        const resultado = await response.json();
        mensagem.textContent = `Arquivo enviado: ${resultado.url}`;
    } catch (error) {
        mensagem.textContent = error.message;
    }
});
```

---

# 4 - JSON e Base64

É possível transformar um arquivo em Base64 e enviá-lo dentro de JSON, mas isso aumenta o tamanho dos dados e o uso de memória.

Para uploads comuns, prefira `FormData`. Base64 faz sentido apenas quando o contrato da API exige esse formato ou para conteúdos pequenos e específicos.

---

# 5 - Progresso e cancelamento

A Fetch API não oferece progresso de upload de forma simples e amplamente suportada. Quando a interface precisa exibir porcentagem enviada, o projeto pode usar `XMLHttpRequest` ou uma biblioteca que exponha esse recurso.

O cancelamento pode ser feito com `AbortController`:

```js
const controller = new AbortController();

const upload = fetch("/api/uploads", {
    method: "POST",
    body: formData,
    signal: controller.signal
});

controller.abort();
```

O cancelamento é aprofundado em [12 - Retry Timeout e Cancelamento.md](./12%20-%20Retry%20Timeout%20e%20Cancelamento.md).

---

# 6 - Segurança

A validação do front-end melhora a experiência, mas pode ser burlada. O back-end deve validar:

- tamanho;
- tipo real do conteúdo;
- extensão permitida;
- nome seguro;
- permissões do usuário;
- local de armazenamento;
- presença de conteúdo malicioso.

Nunca confie somente em `file.type` ou na extensão enviada pelo navegador.

---

# 7 - Erros comuns

- definir `Content-Type: multipart/form-data` manualmente;
- enviar arquivo grande sem limite;
- confiar apenas no atributo `accept`;
- guardar uploads executáveis em uma pasta pública;
- não tratar `413 Payload Too Large`;
- manter o botão habilitado e gerar uploads duplicados.

---

# 8 - Boas práticas

- valide antes de enviar e novamente no servidor;
- informe formatos e tamanho máximo;
- mostre estado de envio e permita cancelamento quando necessário;
- desabilite novas submissões durante o upload;
- use nomes de campos iguais aos definidos no contrato da API;
- trate falhas sem apagar a seleção do usuário desnecessariamente.

---

# 9 - Conclusão

O fluxo padrão de upload no front-end é obter um `File`, montar um `FormData` e enviá-lo sem configurar manualmente o `Content-Type`. A experiência depende do front-end, mas a segurança e a validação definitiva pertencem ao back-end.
