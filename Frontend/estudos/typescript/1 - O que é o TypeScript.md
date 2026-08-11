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

O resultado será a string `"105"`, porque o operador `+` também realiza concatenação quando um dos valores é uma string.

Com TypeScript, podemos declarar o que a função espera:

```ts
function somar(numeroA: number, numeroB: number): number {
    return numeroA + numeroB;
}

somar(10, "5");
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

# 5 - Anotação e inferência de tipos

O TypeScript pode conhecer um tipo de duas formas principais.

### Anotação explícita

Você informa o tipo:

```ts
const curso: string = "TypeScript";
```

O trecho `: string` é uma anotação de tipo.

### Inferência 

O TypeScript identifica o tipo pelo valor:

```ts
const curso = "TypeScript";
```

Mesmo sem escrever `: string`, o TypeScript entende que `curso` contém uma string.

Portanto, a forma mais simples e prática de informar os tipos seria:

```ts
const idade = 20;
const ativo = true;
const nome = "Nícolas";
```

Use anotações quando elas tomam o contrato do código mais claro, principalmente em parâmetros, retornos importantes e estruturas que não podem ser inferidas facilmente.

# 6 - Tipos primitivos

Os três tipos primitivos mais usados no início são:

### `string`

Representa textos:

```ts
let tecnologia: string = "TypeScript";
```
### `number`

Representa números inteiros e decimais:

```ts
let quantidade: number = 10;
let preco: number = 29.90;
```

### `boolean`

Representa `true` ou `false`:
```ts

let cursoFinalizado: boolean = true;
```

Os nomes dos tipos são escritos com letras minúsculas: `string`, `number`, `boolean`

# 7 - Arrays

Podemos declarar o tipo dos elementos de um array:

```ts
const tecnologias: string[] = [
    "JavaScript",
    "TypeScript",
    "React"
];
```

Esse array aceita somente strings:

```ts
tecnologias.push("Angular");
tecnologias.push(10);
```

A segunda operação gera erro.

Outra sintaxe possível é:

```ts
const tecnologias: Array<string> = [
    "JavaScript", 
    "TypeScript", 
    "React"
];
```

# 8 - Funções tipadas

Nas funções, podemos tipar:

- os parâmetros;
- o valor retornado.

```ts
function calcularTotal(
    preco: number,
    quantidade: number
): number {
    return preco * quantidade;
}

const total = calcularTotal(25, 3);
```

Aqui: 
- `preco`, `quantidade` e retorno da função precisam ser números;
- `total` será inferido como `number`.

uma função que não retorna um resultado pode usar `void`:

```ts
function exibirMensagem(mensagem: string): void {
    console.log(mensagem);
}
```

`void` indica que o objetivo da função não é entregar um valor para quem a chamou.
