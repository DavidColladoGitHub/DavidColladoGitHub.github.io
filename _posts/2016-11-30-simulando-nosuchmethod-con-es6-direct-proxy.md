---
title: Simulando __noSuchMethod__ con el Direct Proxy de ES6
---

A muchos os sonará el __noSuchMethod__ y su utilidad antes de pasar a estar obsoleto y desaparecer. Para los que no, en esencia es un método que se invoca cuando llamamos a un método que no existe en un objeto.

No voy a entrar en la polémica de si es correcto usarlo o no, o los motivos de que lo hicieran obsoleto. Lo cierto es que tenía su utilidad, y con los proxies de ES6 podemos simularlo e incluso mejorarlo.

## ¿Qué es el proxy de ES6?
Antes de nada y para evitar confusiones, no tiene ninguna relación con los servidores proxy. Haciendo un resumen, es un envoltorio que nos permite interceptar las llamadas a métodos y modificaciones o consultas de propiedades de un objeto. Basicamente podemos "controlar" cualquier operación que se intente hacer con ese objeto.

No voy a entrar en detalle en su descripción, ya hay mucho escrito sobre este tema y el objetivo del post actual es simular el __noSuchMethod__ más que explicar el Proxy en profundidad.

## ¿Cómo se usa?

Una vez más no voy a entrar en profundidad. Quizá deba escribir un post sobre el Proxy...
