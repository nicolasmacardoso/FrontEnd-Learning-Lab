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

# 9 - Objetos

Um objeto também pode ter sua estrutura tipada:

```ts
const usuario: {
    nome: string;
    idade: number;
    ativo: boolean;
} = {
    nome: "Nícolas",
    idade: 20,
    ativo: true
};
```

O TypeScript exige que o objeto respeite essa estrutura.

Entretanto, repetir a estrutura diretamente pode deixar o código difícil de manter.
Podemos criar tipos reutilizáveis:

```ts
type Usuario = {
    nome: string;
    idade: number;
    ativo: boolean;
};

const usuario: Usuario = {
    nome: "Nícolas",
    idade: 20,
    ativo: true
};
```

O `type Usuario` funciona como um modelo para a estrutura do objeto.

Também podemos utilizá-lo em funções:

```ts
function apresentarUsuario(usuario: Usuario): string {
    return `${usuario.nome} tem ${usuario.idade} anos.`;
}
```

# 10 - Propriedades opcionais

Nem todas as propriedades precisam ser obrigatórias.

```ts
type Usuario = {
    nome: string;
    idade: number;
    telefone?: string;
};
```

O `?` informa que `telefone` é opcional.

Os dois objetos abaixo são válidos:

```
const primeiroUsuario: Usuario = {
    nome: "Nícolas",
    idade: 20
};

const segundoUsuario: Usuario = {
    nome: "Maria",
    idade: 25,
    telefone: "11999999999"
};
```

Ao acessar ma propriedade opcional, precisamos considerar que ela pode ser `undefined`:

```ts
if (segundoUsuario.telefone) {
    console.log(segundoUsuario.telefone);
}
```

# 11 - Union types

Um valor pode aceitar mais de um tipo:

```ts
let identificador: string | number;

identificador = 10;
identificador = "usuario-10";
```

O símbolo `|` significa "ou".

Em uma função:

```ts
function exibirId(id: string | number): void {
    console.log(`Identificador: ${id}`);
}
```

Antes de realizar operações específicas, podemos verificar o tipo:

```ts
function formatarId(id: string | number): string {
    if (typeof id === "string") {
        return id.toUpperCase();
    }
    return id.toString();
}
```

Essa verificação é chamada de estreitamento de tipo, ou `type narrowing`. Depois do `typeof`, o TypeScript consegue entender qual tipo está sendo tratado em cada parte.

# 12 - Evite usar `any` sem necessidade

O tipo `any` é o tipo mais genérico do TypeScript. Ele permite que uma variável aceite qualquer tipo de valor.

```ts
let valor: any = 10;

valor = "texto";
valor = true;
valor.metodoInexistente();
```

Isso aproxima o comportamento do JavaScript sem verificação de tipos.

Se você não sabe ainda qual é o tipo de um valor, `unknown` é mais seguro:

```ts
function exibirValor(valor: unknown): void {
    if (typeof valor === "string") {
        console.log(valor.toUpperCase());
    }
}
```

Com `unknown`, precisamos verificar o tipo antes de utilizar opreações específicas.

> Não use `any` apenas para fazer um erro desaparecer. Primeiro entenda por que o tipo não está correto.

# 13 - TypeScript não valida dados externos sozinho 

Considere uma resposta de API:

´´´ts
type Usuario = {
    nome: string;
    idade: number;
}

const resposta = await fetch("/usuarios/1");
const usuario: Usuario = await resposta.json();
´´´

A anotação não garante que a API realmente devolveu um objeto válido. Se a resposta tiver campos ausentes ou tipos incorretos, siso ainda poderá causar problemas durante a execução.

TypeScript verifica seu código. Dados externos continuam precisando de validação quando forem importantes.

# 14 - Exemplo prático completo

```ts
type Produto = {
    nome: string;
    preco: number;
    quantidade: number;
    desconto?: number;
};

function calcularTotal(produto: Produto): number {
    const valorBruto = produto.preco * produto.quantidade;
    const desconto = produto.desconto ?? 0;

    return valorBruto - desconto;
}

const produto: Produto = {
    nome: "Teclado",
    preco: 150,
    quantidade: 2,
    desconto: 20
};

const total = calcularTotal(produto);

console.log(`${produto.nome}: R$ ${total}`);
```

O comportamento é este:
1 - `Produto` define a estrutura esperada.
2 - `desconto?` permite que o desconto não seja informado.
3 - A função aceita somente objetos compatíveis com `Produto`.
4 - `?? 0` utiliza zero quando `desconto` é `null` ou `undefined`.
5 - A função garante um retorno do tipo `number`.
6 - O total calculado é `280`.

Esse exemplo mostra o principal benefício do TypeScript: os dados e as funções passam a ter contratos claros.

