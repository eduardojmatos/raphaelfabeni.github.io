---
layout: post
title:  "Dicas de bolso de JavaScript - parte 1"
description: "Dicas rápidas de JavaScript, uma linguagem beeem legal mas cheia de pegadinhas do malandro."
type: Post
date: 2015-04-14
image: 'js-tips.jpg'
alt: 'Cadeira de madeira vermelha em um quintal coberto de neve'
category: "JavaScript"
---

## Guarde o tamanho do array

Com nossos brothers loops `for`, conseguimos iterar em arrays ou também em objetos semelhantes a arrays. Semelhantes? Como assim!? Por exemplo os objetos `arguments` e `HTMLCollection`. Provavelmente você já deve ter se deparado com um loop `for` (já deve ter escrito alguns):

{% highlight js %}
for(var i = 0; i < arrayFabeni.length; i++) {
    // magic
}
{% endhighlight %}

De bate pronto, conseguimos perceber algo não tão bacana no código acima. O comprimento (`length`) do array é acessado em toda iteração do loop. Isso não fica tão legal quando por exemplo, o objeto é um `HTMLCollection`. Lembra o que são esses caras? Eles que são retornados quando a gente chama:

* `getElementsByName()`
* `getElementsByClassName()`
* `getElementsByTagName()`

Tá! Legal! Mas eaí né?! A zica mesmo é que toda vez que a gente itera sobre esses caras significa que estamos consultando o nosso *DOM* ao vivo e a cores, e a *toda hora*, o que não é nada bacana.

Com base nisso, uma solução que podemos chegar seria *guardarmos* o comprimento do array; algo parecido com isso:

{% highlight js %}
for(var i = 0, max = arrayFabeni.length;  i < max; i++) {
    // magic
}
{% endhighlight %}

O que fizemos acima foi armazenar o valor da propriedade `length`, evitando assim ter que calculá-la a cada iteração do loop.

## Verifique se a propriedade pertence àquele objeto

Além do nosso amigo do exemplo anterior temos o loop `for-in` que usamos pra iterar em objetos. Uma coisa bacana de se fazer e que pode evitar que algo que você não queira aconteça, é usar o método `hasOwnProperty()`. Esse método simplesmente vai filtrar apenas as propriedades do objeto em si, excluindo as propriedades herdadas pelo `prototype`.

Um exemplo rápido:

{% highlight js %}
var burger = {
    queijo: 'cheddar',
    pao: 'integral',
    hamburguer: 'picanha',
    molho: 'barbecue'
};
{% endhighlight %}

Aí em uma parte obscura, aparece algo que adiciona uma propriedade a todos os objetos.

{% highlight js %}
if(!Object.prototype.feijao) {
  Object.prototype.feijao = 'preto';
}
{% endhighlight %}

O que aconteceu acima foi que verificamos se existe a propriedade `feijao` em `Object` e, caso ela não exista definimos ela com o valor `preto`. Aí que está o negócio da coisa, nosso objeto `burger`, já herda a propriedade `feijao` via `prototype`.

Com isso, para evitarmos que `feijao` apareça quando listarmos as propriedades de `burger` (até porque feijão, na minha opinião, não combina muito com hamburguer), fazemos o seguinte:

{% highlight js %}
for(var i in burger) {
  if(burger.hasOwnProperty(i)) {
     console.log(i + ' => ' + burger[i]);
  }
}

// Resultará no seguinte:
// queijo => cheddar
// pao => integral
// hamburguer => picanha
// molho => barbecue
{% endhighlight %}

Do contrário, caso não fizéssemos essa verificação, teríamos algo assim:

{% highlight js %}
for(var i in burger) {
    console.log(i + ' => ' + burger[i]);
}

// Resultará no seguinte:
// queijo => cheddar
// pao => integral
// hamburguer => picanha
// molho => barbecue
// feijao => preto
{% endhighlight %}

Gostou? Escrevi alguma groselha? Quer melhorar? Abra uma [issue](https://github.com/raphaelfabeni/raphaelfabeni.github.io/issues) com a hashtag *1postperweek* e vamos conversar.

Obs. Queria deixar aqui meu *muito obrigado* ao [Ademílson Tonato](https://github.com/ftonato) pela ajuda na correção do post.
