---
layout: post
title: 'Map | Filter | Reduce - Javascript'
excerpt: "No processo de aprendizagem de uma linguagem de programação, vamos, certamente, nos deparar com o tratamento de Arrays. Em Javascript e em outras linguagens de programação, temos três métodos que são muito importantes para trabalharmos com eles: **map**, **reduce**, **filter**. Vamos conversar um pouco sobre estes métodos e sua importância para a escrita de um código melhor"
tags: [Javascript, NodeJS, C#, Development]
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
Note a quantidade de código necessária para fazermos uma tarefa relativamente simples. A função map irá nos ajudar a deixar o código muito mais conciso:

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

Claro, este resultado não é o que queremos para uma função como esta. Precisamos de uma função que retorne somente os resultados que satisfaçam determinada condição que buscamos. Para tal, devemos utilizar a função *filter*:

```javascript
let numbers = [1, 2, 3];

let oddNumbers = numbers.filter(el => el % 2 === 1);

console.log(oddNumbers); //[1, 3]
```

Uma das grandes vantagens dos métodos *map* e *filter* é que eles retornam instâncias de Arrays, o que significa que podemos fazer utilizações como:

```javascript
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];

let oddDoubledNumbers = numbers.filter(x => x % 2 === 1).map(x => x * 2);

console.log(oddDoubledNumbers); //[2, 6, 10, 14, 18, 22, 26, 30] -> Números ímpares dobrados.
```

Além de modificar todos os itens de um array ou filtrar itens por determinadas condições, por vezes queremos efetuar operações que agreguem os valores, tais como a somatória de todos os itens ou a média dos valores do array:

```javascript
let numbers = [1, 3, 5];
let average = numbers.reduce((total, currentElement) => total + currentElement, 0) / numbers.length;

console.log(average); //3
```

O *reduce* tem uma assinatura um pouco mais complexa que os outros métodos:

```javascript
Array.prototype.reduce(function(previousValue, currentValue, index, array)[, initialValue])
```
* **callback**: função que é executada em cada valor no array
   * **previousValue**: É o valor retornado na última invocação do callback, ou o argumento initialValue, se fornecido;
   * **currentValue**: O elemento atual que está sendo processado no array;
   * **index**: O índice do elemento atual que está sendo processado no array;
   * **array**: O array ao qual a função reduce foi executado;
* **initialValue**: Objeto que será utilizado como valor do argumento *previousValue* no momento da primeira chamada da função de callback.

## Na vida real
Em um software que irá para produção, provavelmente utilizaremos estas funções para trabalhar com arrays que contém objetos complexos e iremos fazer operações que façam mais sentido no mundo real que somente procurar números ímpares dentro de um array. Vamos simular a seguinte situação:

**Preciso de uma listagem dos alunos de determinada turma que foram aprovados (regra para aprovação: média maior ou igual 7). Comparativamente, quão melhor os alunos aprovados foram que os alunos reprovados (quantos % a média dos alunos aprovados foi superior aos alunos reprovados)?**

Vamos ao código:

```javascript
let Aluno = function(nome) {
	notas: [],
  this.nome = nome,
  this.definirNotas = function(...n) {
  	this.notas = n;
  },
  this.calcularMedia = function() {
  	return this.notas.reduce((p, c) => p + c) / this.notas.length;	
  }
}

let joao = new Aluno('joao');
let pedro = new Aluno('pedro');
let bruna = new Aluno('bruna');

joao.definirNotas(1, 4, 7);
pedro.definirNotas(5, 8, 9);
bruna.definirNotas(10, 6, 10);

let alunos = [joao, pedro, bruna];

let alunosAprovados = alunos.filter(el => el.calcularMedia() >= 7);
let alunosReprovados = alunos.filter(el => el.calcularMedia() < 7);

let mediaDosAlunosAprovados = alunosAprovados.map(el => el.notas.reduce((p, e) => p + e)).reduce((p, e) => p + e) / alunosAprovados.map(el => el.notas.length).reduce((p, e) => p + e);

let mediaDosAlunosReprovados =
alunosReprovados.map(el => el.notas.reduce((p, e) => p + e)).reduce((p, e) => p + e) / alunosReprovados.map(el => el.notas.length).reduce((p, e) => p + e);


console.log('Os alunos aprovados foram: ' + alunosAprovados.map(x => x.nome));//[Pedro, Bruna]
console.log('A média dos alunos aprovados foi: ' + mediaDosAlunosAprovados);//8
console.log('A média dos alunos reprovados foi: ' + mediaDosAlunosReprovados);//4
console.log('Os alunos aprovados tiveram uma média de notas ' + ((mediaDosAlunosReprovados /mediaDosAlunosAprovados) * 100) + '%' + ' superior aos alunos reprovados');//50%
```

## Performance
Se quiser ver a comparação entre esta versão do código fonte, outra versão utilizando *for* ao invés dos métodos e uma terceira versão que retira a duplicação de código para o cálculo da média dos alunos, veja o comparativo feito utilizando a ferramenta [jsperf.com](https://jsperf.com/jsmapreducefilter)

## Outras linguagens
Muitas outras linguagens utilizam este mesmo conceito para trabalhar com arrays e coleções de objetos. No C#, desde a implementação do [Linq](https://msdn.microsoft.com/en-us/library/bb308959.aspx), métodos como Select/SelectMany, Where e Sum tem finalidades parecidas.

## Escrevendo códigos melhores
Devemos sempre procurar utilizar os recursos da linguagem para escrever códigos que sejam mais legíveis, antes mesmo de tentar escrever códigos que tenham a melhor performance possível. Os recursos apresentados trazem o melhor dos dois mundos: Tornam o código mais legível **e**, muitas vezes, melhoram a performance do código Javascript. Para saber o custo benefício de utilizar uma abordagem ou funcionalidade da linguagem para melhorar a performance do algoritmo, em detrimento da legibilidade do código, não há outra maneira do que testar estas diferentes abordagens.

Nos próximos posts iremos utilizar este mesmo exemplo de código para criar um projeto nodejs e vamos estudar como separar e organizar este código na estrutura da solução.
