---
layout: post
title:  "Dicas de bolso de JavaScript - parte 2"
description: ""
type: Post
date: 2015-04-21
image: ''
alt: ''

## Criação de um array

Alguém usa o construtor `new Array()`? Eu particularmente não uso, mas quando vi isso, achei legal deixar registrado pois era algo que não sabia e vai que no dia a dia encontramos algum código com isso.

Se criamos um array através do `new Array()` passando argumentos entre os parênteses, temos um array com esses valores que foram passados. Algo assim:

{% highlight js %}
var megaSena = new Array(20, 29, 32, 45, 55, 58);
{% endhighlight %}

Seria o mesmo caso fizéssemos isso:

{% highlight js %}
var megaSena = [20, 29, 32, 45, 55, 58];
{% endhighlight %}

E se passássemos apenas um valor como argumento? Teríamos um array com apenas um item, certo? *Mahomenos...* Aí que vem a pegadinha do *JavaScript* malandro. Se criarmos usando a sintaxe literal, realmente temos um array com apenas um item:

{% highlight js %}
var megaSena = [20];

console.log(megaSena.length); // 1
console.log(megaSena[0]); // 20
{% endhighlight %}

Agora se criarmos usando `new Array()` o que acontece é que esse *único* valor passado como argumento vira o comprimento do array e não um item dele.

{% highlight js %}
var megaSena = new Array(20);

console.log(megaSena.length); // 20
console.log(megaSena[0]); // undefined
{% endhighlight %}

Gostou? Escrevi alguma groselha? Quer melhorar? Abra uma [issue](https://github.com/raphaelfabeni/raphaelfabeni.github.io/issues) com a hashtag *1postperweek* e vamos conversar.

## Funções imediatas e parâmetros

Já ouvimos bastante a respeito de funções imediatas, correto? Recapitulando rapidamente, ela permite que uma função seja executada assim que ela seja definida. Isso é bom principalmente pelo fato de fornecer um escopo temporário para a mágica que você vai fazer, sem a necessidade de poluir seu escopo global.

{% highlight js %}
(function() {
  // some magic
}());
{% endhighlight %}

Uma coisa bacana é que podemos passar argumentos para as funções imediatas. Podemos ter algo assim:

{% highlight js %}
(function(name, hobby) {
  console.log('Hi, my name is ' + name + ' and I like ' + hobby );
}('Fabeni', 'to travel'));

// "Hi, my name is Fabeni and I like to travel"
{% endhighlight %}

## `call` e `apply` sem medo

Esses dois caras são bem semelhantes. Ambos permitem invocar uma função em um outro contexto (que vai ser o *primeiro parâmetro* que você passar pra eles) e com os argumentos passarmos (que vai ser o *segundo parâmetro* que passarmos). Então, `call` e `apply` permitem que:

* 1º parâmetro => possamos dizer em qual *escopo* uma determinada função deve ser executada;
* 2º parâmetro => consigamos definir os argumentos que serão passados para a função.

O que muda entre `call` e `apply` é a forma de como passar o segundo parâmetro:

* `call` => uma lista de itens (a partir do 2º parâmetro);
* `apply` => um array de elementos.

*Para de falar Fabeni, mostra alguma coisa aí!*

{% highlight js %}
var mister = {
  name: 'Val Valentino',
  nickname: 'Mister M'
};

var hello = function(name, nickname) {
  return 'My name is ' + (name || this.name) + ' but you can also call me ' + (nickname || this.nickname);
};

hello.call(mister);
// "My name is Val Valentino but you can also call me Mister M"

hello.call(null, 'Raphael Fabeni', 'Fabeni');
// "My name is Raphael Fabeni but you can also call me Fabeni"

hello.apply(null, ['Raphael Fabeni', 'Fabeni']);
// "My name is Raphael Fabeni but you can also call me Fabeni"
{% endhighlight %}

O que temos acima é mais ou menos o seguinte:

* um objeto simples chamado `mister`;
* uma função `hello` que retorna uma `string` de acordo com os parâmetros passados;
* as chamadas das funções usando o formato padrão e usando `call` e `apply`.

Um outro exemplo que talvez possa ajudar, imagine que possamos ter uma função simples que vai iterar sobre os argumentos dessa função (o objeto `arguments`). Poderíamos pensar em algo assim:

{% highlight js %}
function something() {
  var likeArray = arguments;
  likeArray.forEach(function() {});

  return likeArray;
}

something('a', 'b');
{% endhighlight %}

Aí que mora o problema: vamos ter um erro se tentarmos algo assim.

* TypeError: likeArray.forEach is not a function
likeArray.forEach(function () {*

Isso acontece pois o nosso brother `arguments` é um objeto e não um *array*. Pra podermos conseguir usar o `forEach`, precisamos converter `arguments` em um *array* e conseguimos isso utilizando o método `slice`. No entanto, ele é um método que pertence ao `prototype` de `Array`. Daí que vem a pergunta: como fazemos então pra executar a função/método em um outro contexto (executar `slice` no contexto do objeto `arguments`)? `call` ou `apply`.

{% highlight js %}
function something() {
  var likeArray = Array.prototype.slice.call(arguments);
  likeArray.forEach(function() {});

  return likeArray;
}
{% endhighlight %}

No exemplo acima, alteramos a linha relacionada à variável `likeArray`, aplicando o método `slice` no contexto de `arguments` através do `call`.
