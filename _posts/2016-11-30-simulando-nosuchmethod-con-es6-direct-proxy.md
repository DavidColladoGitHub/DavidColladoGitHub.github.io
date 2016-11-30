---
title: Simulando __noSuchMethod__ con el Direct Proxy de ES6
---

A muchos os sonará el __noSuchMethod__ y su utilidad antes de pasar a estar obsoleto y desaparecer. Para los que no, en esencia es un método que se invoca cuando llamamos a otro método que no existe en un objeto.

No voy a entrar en la polémica de si es correcto usarlo o no, o los motivos de que lo hicieran obsoleto. Lo cierto es que tenía su utilidad, y con los proxies de ES6 podemos simularlo e incluso mejorarlo.

## ¿Qué es el proxy de ES6?
Antes de nada y para evitar confusiones, no tiene ninguna relación con los servidores proxy. Haciendo un resumen, es un envoltorio que nos permite interceptar las llamadas a métodos y modificaciones o consultas de propiedades de un objeto. Basicamente podemos "controlar" cualquier operación que se intente hacer con ese objeto.

No voy a explicarlo en detalle, ya hay mucho escrito sobre este tema y el objetivo del post actual es simular el __noSuchMethod__ más que explicar el Proxy en profundidad. Sin embargo, vamos a repasar brevemente como usarlo.

## ¿Cómo se usa?

Para crear el proxy usaremos su constructor, que espera dos parámetros:

```javascript
var p = new Proxy(target, handler);
```

El objeto p resultante será un envoltorio sobre el objeto que pasemos como *target*. Podemos modificar su comportamiento a través de "traps" que son métodos que pasaremos en un objeto en el segundo parámetro (en el ejemplo llamado *handler*).

Estos "traps" están definidos y hay una lista que podemos usar. Son entre otros el *get*, el *set*, el *apply*, el *has*... En el handler pasaremos uno o más con el código que nosotros queramos dentro. Los "traps" que hayamos definido dentro interceptarán las llamadas a propiedades y métodos del objeto y nos permitirán definir qué hacer. Un ejemplo de handler podría ser el siguiente:

```javascript
var handler = {
    get: function(target, name){
        alert(target[name]())
    }
};
```

En este ejemplo cuando llamemos a un método, nuestro "interceptador" lo ejecutará y nos hará un alert con el valor que devuelva.

El código completo sería el siguiente:

{% highlight javascript linenos %}
/* Creamos una "clase" */
var Obj = function () {}

Obj.prototype.hi = function(){
	return "Hola";
};

/* Este será el objeto que usemos como target en el proxy */
var miObj = new Obj();

/* Nuestro handler de antes */
var handler = {
    get: function(target, name){
    debugger;
        alert(target[name]())
    }
};

/* Creamos el proxy */
var p = new Proxy(miObj, handler);

/* Llamamos al método del target que será interceptado por el handler */
p.hi()
{% endhighlight %}

## Pasando a la acción
Nos hemos desviado un poco con toda la explicación del proxy, pero ya estamos de vuelta. En realidad lo que vamos a hacer no es muy diferente del ejemplo anterior.

Primero creamos una función que tome como parámetro un objeto y lo devuelva "envuelto" en un objeto Proxy:

{% highlight javascript linenos %}
function addNoSuchMethod(obj) {
  let handler = {
    get(target, name, receiver) {
      if (name in target) {
        return target[name];
      } else {
        return function (...args) {
          let result = target["__noSuchMethod__"].call(this, name, args);
          return result;
        };
      }
    }
  };
  
  return new Proxy(obj, handler);
}
{% endhighlight %}

Ahora creamos una clase para probarlo. Con el método "clásico":

{% highlight javascript linenos %}
function Animal(name) {
	this.name = name;
};

Animal.prototype.sayHi = function () {
  alert("Hi! i'm a " + this.name);
};

Animal.prototype.__noSuchMethod__ = function (name, args) {
  alert("Method " + name + " doesn't exist");
};
 
 var koala = new Animal("Koala")
 var koalaProxy = addNoSuchMethod(koala);
 koalaProxy.sayHi();
 koalaProxy.sayGoodbye();
{% endhighlight %}

Usando clases de ES6:

{% highlight javascript linenos %}
 class Animal {
   constructor(name) {
     this.name = name;
   }
   sayHi () {
     alert("Hi! i'm a " + this.name);
   }
   __noSuchMethod__ (name, args) {
   	alert("Method " + name + " doesn't exist");
   }
 }
 
 var koala = new Animal("Koala")
 var proxyKoala = addNoSuchMethod(koala);
 proxyKoala.sayHi();
 proxyKoala.sayGoodbye();
{% endhighlight %}

En caso de que quisiéramos que todas las instancias de una clase tuvieran el proxy (cosa más que probable) podemos devolver el proxy desde el constructor de la clase directamente. Esto nos evita tener que hacerlo individualmente para cada instancia.

{% highlight javascript linenos %}
class Animal {
   constructor(name) {
     this.name = name;
     return addNoSuchMethod(this);
   }
   sayHi () {
     alert("Hi! i'm a " + this.name);
   }
   __noSuchMethod__ (name, args) {
   	alert("Method " + name + " doesn't exist");
   }
 }
 
 var koala = new Animal("Koala")
 koala.sayHi();
 koala.sayGoodbye();
{% endhighlight %}

Con esto ya tenemos un __noSuchMethod__ propio. Hay casi ilimitadas formas diferentes de hacer esto mismo, pero la base siempre es la misma. Usar un Proxy, interceptar una llamada, y comprobar que existe el método.

## Referencias
* [Proxy - Javascript en MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
* [Especificación ES6](http://www.ecma-international.org/ecma-262/6.0/#sec-proxy-objects)
