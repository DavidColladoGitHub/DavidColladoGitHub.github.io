---
layout: post
title: El debugger que mentía
subtitle: O sobre consecuencias de la optimización del V8
---

Es innegable que el motor V8 de Google ha dado muchas posibilidades nuevas al Javascript de manera tanto indirecta (node.js) como directa (rendimiento de Chrome). Sin embargo todos cometemos errores, y las optimizaciones llevadas a cabo han tenido un efecto curioso en la depuración.

## El problema

No sería raro que, depurando, en un momento dado queramos ver el valor de una variable global. Quizá esa variable no se esté usando en realidad dentro del contexto actual, pero queremos saber su valor. El estado de la situación es la siguiente:

{% highlight javascript linenos %}
var x = "a";

document.getElementById("botonCualquiera").addEventListener("click", function(){
  debugger;
});
{% endhighlight %}

Entonces observamos algo interesante. Al intentar ver el valor de X en la consola, obtenemos un aviso explicando que dicha variable no está definida. Esto es raro. Estamos seguro de que la hemos definido, de modo que cambiamos el código y ponemos una alerta.

{% highlight javascript linenos %}
document.getElementById("botonCualquiera").addEventListener("click", function(){
  alert(x);
});
{% endhighlight %}

Mágicamente aparece una alerta... y el valor mostrado es el correcto.

Si en este punto aún tienes ganas, puedes probar el Internet Explorer y comprobar con estupefacción que ahí todo funciona como es de esperar (quién lo iba a decir...).

## La explicación

En resumidas cuentas, si no hacemos ninguna referencia a una variable, el contexto de depuración no contiene dicha variable. Dicho de otro modo, si no está en la función, no puedes consultarla en consola.

Es una consecuencia de la optimización del motor V8, y está por ver si lo solucionarán. El bug está [avisado](https://bugs.chromium.org/p/v8/issues/detail?id=3491).

## La solución

En realidad no es necesario buscar una solución. Al menos si no eres Google. En caso de querer depurar tendrás que hacer alguna referencia a la variable, o usar algo como:

{% highlight javascript linenos %}
eval("debugger");
{% endhighlight %}

Aunque no es lo que yo haría...

Por lo demás no hay que preocuparse. Esto solo aplica a la depuración. Si finalmente decides usar la variable en tu código, tendrá valor como siempre.


## Referencias
[Bug reportado](https://bugs.chromium.org/p/v8/issues/detail?id=3491)
