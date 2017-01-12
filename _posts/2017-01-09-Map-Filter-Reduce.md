---
layout: post
title: 'Map | Filter | Reduce - Javascript'
excerpt: "Trabalhando com Arrays em Javascript"
tags: [Javascript, C#, Development]
comments: true
---

No processo de aprendizagem de uma linguagem de programação, vamos, certamente, nos deparar com o tratamento de Arrays. Em Javascript e em outras linguagens de programação, temos três métodos que são muito importantes para trabalharmos com eles: **map**, **reduce**, **filter**. Vamos conversar um pouco sobre estes métodos e sua importância para a escrita de um código melhor.

# Array.prototype.map
Pense no método **map** como um loop for-each que irá *transformar* todos os elementos de seu array. Quando usamos o método map, todos os elementos do array serão transformados e retornados como um novo array de dados.

pense no seguinte código

```javascript
let numbers = [1,2,3];
let doubledNumbers = [];

for (let i = 0; i < numbers.length; i++) {
    doubledNumbers[i] = numbers[i] * 2;
}

console.log('números duplicados: ' + doubledNumbers); //[2, 4, 6];

```
Note a quantidade de código necessária para fazermos uma tarefa relativamente simples. A função map irá nos ajudar a deixar o código muito mais consiso:

```javascript
let numbers = [1,2,3];

let doubledNumbers = numbers.map((el) => el * 2);

console.log('numeros duplicados: ' + doubledNumbers) //[2, 4, 6]
```
Note que o método map retorna uma instancia de um array com o resultado retornado na função passada como argumento. Note também que utilizei aqui uma novidade (não mais tão nova assim) do ES6, as [Arrow Functions](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow_functions).

O fato da função map retornar um Array, também faz com que eu possa fazer códigos como:

```javascript
let numbers = [1,2,3];

let doubledNumbers = numbers.map(el => el * 2).map(el => el + 1);

console.log('numeros duplicados e incrementado: ' + doubledNumbers) //[3, 5, 7]
```

Como disse anteriormente, o número de elementos de entrada, será o mesmo número dos elementos de saída. Portanto, imagine o seguinte código:

```javascript
let numbers = [1, 2, 3];

let oddNumbers = numbers.map(el => {
    if (el % 2 === 1){
      return el;
    }
})

console.log(oddNumbers); //[1, undefined, 3];
```

Claro, este resultado não é o que queremos para uma função como esta. 
