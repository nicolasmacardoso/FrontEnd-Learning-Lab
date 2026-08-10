# 1 - O que é o TypeScript

TypeScript é uma linguagem baseada em JavaScript que adiciona principalmente:

- verificação estática de tipos;
- melhor documentação do código;
- sugestões mais precisas no editor;
- detecção antecipada de erros;
- recursos para organizar aplicações maiores.

Todo código JavaScript válido pode, em geral, ser usado dentro de um arquivo TypeScript.
A principal diferença é que o TypeScript permite declarar quais tipos de valores o código espera receber.

```ts
const nome: string = "Nícolas";
const idade: number = 20;
const estudando: boolean = true;
```

Se tentarmos guardar um valor incompatível, o TypeScript aponta o erro:

```ts
let idade: number = 20;

idade = "vinte";
```

A atribuição é inválida porque `"vinte"` é uma `string`, mas a variável aceita apenas `number`.

# 2 - Por que TypeScript existe

JavaScript possui tipagem dinâmica. Isso significa que uma variável pode receber valores de tipos diferentes durante a execução:

```js
let quantidade = 10;
quantidade = "dez";
```

Dependendo da aplicação, isso pode causar erros difíceis de perceber.

Observe essa função:

```js
function somar(numeroA, numeroB) {
    return numeroA + numeroB;
}

somar(10,"5");
```

O editor aponta que `"5"` não pode ser enviado para um parâmero do tipo `number`.

O TypeScript existe para encontrar problemas como esse durante o desenvolvimento, antes que o usuário execute a aplicação.

# 3 - TypeScript não substitui o JavaScript

Navegadores executam JavaScript, não TypeScript diretamente.

Por isso, o código TypeScript precisa ser transformado em JavaScript.

```text
Código TypeScript
        ↓
Verificação dos tipos
        ↓
    Compilação
        ↓
Código JavaScript
        ↓
Execução no navegador ou Node.js
```

O código

```ts
const mensagem: string = "Olá, TypeScript!";

console.log(mensagem);
```

Depois da compilação, o JavaScript gerado poderá ser semelhante a:

```js
const mensagem = "Olá, TypeScript!";

console.log(mensagem);
```

A anotação `: string` desaparece. Ela serve para o desenvolvimento e para a verificação do código, não para a execução.

Esse é um ponto essencial:

- Os tipos do TypeScript normalmente não existem durante a execução do programa.

Por isso, TypeScript não substitui validações de dados recebidos de usuários, APIs ou arquivos.

# 4 - Tipagem estática

Quando dizemos que o TypeScript utiliza tipagem estática, queremos dizer que os tipos são analisados antes da execução.

```ts
let preco: number = 50;

preco = "R$ 50";
```

O TypeScript consegue detectar a incompatibilidade sem precisar executar o programa.

Isso ajuda principalmente em:

- projetos com muitos arquivos;
- funções utilizadas em vários lugares;
- trabalho em equipe;
- alterações e refatorações;
- objetos vindos de APIs;
- aplicações React, Angular e Node.js