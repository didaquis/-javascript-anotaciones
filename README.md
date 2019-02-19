# 📚 JavaScript: breves anotaciones sobre el lenguaje  

Listado personal de anotaciones, trucos, recordatorios, utilidades o ejemplos interesantes para JavaScript.  

## Tabla de Contenido
- [Declaración de variables](#declaracion-de-variables)
- [Operadores](#operadores)
- [Condicionales](#condicionales)
- [Funciones y argumentos](#funciones-y-argumentos)
- [Clases en ES5](#clases-en-es5)
- [Clases en ES6](#clases-en-es6)
- [Singleton](#singleton)
- [Tratamiento de arrays](#tratamiento-de-arrays)
- [Tratamiento de strings](#tratamiento-de-strings)
- [Objetos](#objetos)
- [Set](#set)
- [Map](#map)
- [Debugging y console](#debugging-y-console)
- [De callback hell a promesas](#de-callback-hell-a-promesas)
- [Promesas](#promesas)
- [Async-Await](#async-await)
- [setTimeout](#setTimeout)


----------------------------------------------------------
## Declaración de variables:  

* Declarar múltiples variables de manera más compacta:
```javascript
var i, j, k;
var l = m = n = 0;
var a = 1, b = 2;
```

----------------------------------------------------------
## Operadores:  

* Operador lógico _OR_ (evalua de izquierda a derecha las expresiones y retorna la primera de ellas evaluable como "truthy". Si todas se evaluan como "falsy", retorna _false_. Todos los valores son "truthy", excepto: _false_, _0_, _""_, _null_, _undefined_, y _NaN_)
```javascript
var foo = false || ':-)'; // ':-)'

var bar = 3 || false; // 3

var foobar = null || 0 || undefined || "NyanCat"; // "NyanCat"
```

* Operador ternario: _?_:
```javascript
var now = new Date();  
var greeting = "Good" + ((now.getHours() > 17) ? " evening." : " day.");
console.log(greeting);
```

* Operador _spread_:
```javascript
let foo = ['foo 1', ' foo 2'];
let bar = ['bar 1', 'bar 2', ...foo, 'bar 3'];
console.log(bar); // ["bar 1", "bar 2", "foo 1", " foo 2", "bar 3"]
```

* Doble uso del operador _!_ (not):
```javascript
// La doble negación retorna un booleano dependiendo de la "truthiness" de la expresión.
// Retornará false cuando el valor sea: false, 0, "", null, undefined, y NaN.
// Si no es ninguno de estos casos, entonces devolverá true.
// Tiene sentido usarlo para evaluar expresiones que no son booleanas.
let foo;
if (!!foo) { // 'Result is false' Dado que el valor de foo es null.
	console.log('Result is true');
} else {
	console.log('Result is false');
}

// Observa los siguientes ejemplos:
!!0 // false
!!1 // true
!!"" // false (string vacío es falsy)
!!window.biz // false (si la variable biz no está declarada, se evalua como falsy)
!!undefined // false (undefined es falsy)
!!null // false (null is falsy)
!!{} // true (an empty object is truthy)
!![] // true (an empty array is truthy. PHP programmers beware!)
!!NaN // false
```

----------------------------------------------------------
## Condicionales:  

* Condicional _if_ comparando múltiples criterios:
```javascript
const fruit = 'strawberry';

// Creamos un array con aquellos criterios a los que comparar
const criteria = ['apple', 'strawberry', 'cranberries'];

if (criteria.includes(fruit)) {
	// true
}
```

----------------------------------------------------------
## Funciones y argumentos:  

* _arguments_ (array de argumentos que se le pasan a una función):
```javascript
function printArguments(){
	for(var j = 0; j < arguments.length; j++){
		console.log(arguments[j]);
	}
}
printArguments(3712,7123);


// ES6 Style
function printArgumentsES6(...arg){
	arg.forEach((a) => console.log(a));
}
printArgumentsES6(3712,7123);

function printArgumentsES6ForOf(){
	for(let argument of arguments){
		console.log(argument);
	}
}
printArgumentsES6ForOf(3712,7123);
```

* Establecer un valor por defecto a los argumentos de una función (ES5). Si no pasamos un parámetro, JavaScript lo interpreta como "undefined":
```javascript
function valorPorDefectoEnUnArgumento(txt){
	// siempre que "txt" sea "undefined", "txt" será igual a "valor por defecto"
	txt = txt || "valor por defecto";
	
	console.log(txt);
}

valorPorDefectoEnUnArgumento("foo"); // "foo"
valorPorDefectoEnUnArgumento(); // "valor por defecto"
```

* Uso de destructuring para mejorar la legibilidad del siginificado de los parámetros que mandamos a una función:
```javascript
function createMenu({ title, body, buttonText, cancellable=false }) {
	// ...
	// Fíjate que establecemos un valor por defecto al argumento "cancellable"
}

createMenu({
	title: 'my title',
	body: 'my body',
	buttonText: 'send form',
	cancellable: true
}); // observa que así es totalmente comprensible el significado de los parámetros. A esto lo llamamos "named parameters".
// Sin embargo, no hubieramos entendido nada así: createMenu('my title', 'my body', 'send form', true);
// ¿Sabríamos entender que significa el true en la segunda llamada? Probablemente no!

// Podemos hacer lo siguiente si todos los parámetros son opcionales:
function findUser({ id, username } = {}) {
	// ...
}
```

* Self Invoking Functions, también llamadas IIFE (Immediately Invoked Function Expression):
```javascript
var hello;
(function (){

	hello = function(){
		return "Hi boy!";
	};

})();

console.log( hello() ); // "Hi boy!"


var result = (function () {
	var name = "Barry";
	return name;
})();

console.log(result); // El contenido de la variable result es el resultado de la ejecución de la función.
// En este caso: "Barry"
```

* Factory Functions (una "Factory Function" es una función que crea objetos y los retorna):
```javascript
// Ejemplo de Factory Function
const cat = () => {

	const sound = 'miau';
	let color = 'white';

	// definimos getters and setters para acceder a las propiedades
	const setColor = (newColor) => {
		color = newColor;
	}
	const getColor = (newColor) => {
		return color;
	}

	return {
		talk: () => sound,
		setColor,
		getColor
	}
}

const smurfy = cat(); // No debes usar "new"!
console.log( smurfy.talk() ); // miau
// console.log( smurfy instanceof cat ); // TypeError
console.log( smurfy.getColor() ); // 'white'
smurfy.setColor('black');
console.log( smurfy.getColor() ); // 'black'


// Otro ejemplo potenciando el uso de "clousures"
const makeCounter = function() {
	// propiedades privadas
	let count = 0;

	// métodos privados
	const changeBy = (val) => {
		count += val;
	}

	// definimos los métodos publicos
	return {
		increment: function() {
			changeBy(1);
		},
		decrement: function() {
			changeBy(-1);
		},
		getValue: function() {
			return count;
		}
	}
};

const Counter1 = makeCounter();
const Counter2 = makeCounter();

console.log(Counter1.getValue()); // 0

Counter1.increment();
Counter1.increment();
console.log(Counter1.getValue()); // 2

Counter1.decrement();
console.log(Counter1.getValue()); // 1

console.log(Counter2.getValue()); // 0
```
Otro ejemplo de _Factory Functions_ más avanzado:
```javascript
// Ejemplo avanzado de Factory Function:
const dataConnection = (data = {}) => {
	const settings = data; // Guardo los parámetros que me han pasado al llamar a la factory function.
	// De esta manera puedo definir propiedades del nuevo objeto.
	
	return Object.freeze({
		// Object.freeze evita la eliminación y adición de propiedades, modificación del prototype, etc.
		getSettings: () => settings,

		modifySettings: (addData = {}) => {
			return Object.assign(settings, addData); 
		// Añadimos al objeto 'settings' nuevas propiedades
		// o bien modificamos el valor de las existentes.
		}
	});
}

const connection = dataConnection( { ip: '127.0.0.1', port: '8080' } );
// Le paso como parámetro un objeto

console.log( connection.getSettings() ); // {ip: "127.0.0.1", port: "8080"}
console.log( connection.getSettings().ip ); // 127.0.0.1

connection.method = 'http'; // Esto NO funcionará!
// Gracias a 'Object.freeze' no puedo añadir propiedades al objeto.
// Sólo veré un error si uso: 'use strict';

//connection.settings.method = 'http'; // Esto NO funcionará! TypeError

connection.modifySettings( { method: 'http' } );
// Usamos un método definido en el objeto para modificar los valores que contiene el objeto.

console.log( connection.getSettings() ); // {ip: "127.0.0.1", port: "8080", method: "http"}

connection.modifySettings( { ip: '192.168.2.11' } );
// Podemos sobreescribir los valores del objeto 'settings'.

console.log( connection.getSettings() ); // {ip: "192.168.2.11", port: "8080", method: "http"}
```


----------------------------------------------------------
## Clases en ES5

* Ejemplo de clase en ES5:
```javascript
function Person(name) {
	this.name = name;

	this.salute = function() {
		console.log("Hello, my name is " + this.name);
	}
}

var john_doe = new Person("John Doe"); // Observa que estoy usando "new" !!
john_doe.salute(); // Hello, my name is John Doe

console.log(john_doe instanceof Person); // true
console.log(john_doe instanceof Object); // true
```

----------------------------------------------------------
## Clases en ES6

* Ejemplo de clase en ES6:
```javascript
class Person {
	constructor(name) {
		// constructor es un método opcional. Si la clase no tiene propiedades puedes omitirlo
		this.name = name;
	}

	salute() {
		console.log(`Hello, my name is ${this.name}`);
	}
}

let john_doe = new Person("John Doe");

john_doe.salute(); // Hello, my name is John Doe

console.log(john_doe instanceof Person); // true
console.log(john_doe instanceof Object); // true

console.log( typeof john_doe); // object

console.log( typeof Person); // function !!
// (Cuidado, podríamos pensar que no retornará "class" pero nos retorna "function")

console.log( typeof Person.prototype.salute); // function !!
// (Cuidado, podríamos pensar que no retornará "method" pero nos retorna "function")
```

* Ejemplo de Chaining Methods (métodos encadenados):
```javascript
class Car {
	constructor(brand, model, color) {
		this.brand = brand;
		this.model = model;
		this.color = color;
	}

	setBrand(brand) {
		this.brand = brand;
		return this; // Returning this for chaining
	}
	setModel(model) {
		this.model = model;
		return this; // Returning this for chaining
	}
	setColor(color) {
		this.color = color;
		return this; // Returning this for chaining
	}

	getCarInfo() {
		return this; // Returning this for chaining
	}
}

const car = new Car('Ford','F-150','red').setColor('pink').setModel('Fiesta'); // Chaining methods!

console.log( car.getCarInfo() ); // {brand: "Ford", model: "Fiesta", color: "pink"}
```


* Métodos estáticos:
```javascript
class Utilities {
	static generateRandomInteger() {
		// Esto es un método estático. 
		// Permite ser llamado sin necesidad de instanciar la clase.
		return Math.floor(Math.random() * 11); // retorna numero entero aleatorio entre 0 y 10
	}

	static coinToss() {
		return Math.random() > .5; // retorna "true" o "false" de manera aleatoria
	}

	static capitalize(str) {
		return str.charAt(0).toUpperCase() + str.slice(1); // retorna un string poniendo en mayúscula la primera letra
	}

	static delay(miliseconds, callback) {
		setTimeout(callback, miliseconds);
	}

	static generateToken() {
		const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789_-$';
		let rand, i;
		let bits = 64;
		let result = '';
		while (bits > 0) {
			rand = Math.floor(Math.random() * 0x100000000); // 32-bit integer
			// base 64 means 6 bits per character, so we use the top 30 bits from rand to give 30/6=5 characters.
			for (i = 26; i > 0 && bits > 0; i -= 6, bits -= 6) result += chars[0x3F & rand >>> i];
		}
		return result;
	}
}

let num = Utilities.generateRandomInteger(); // Llamamos directamente al método estático
console.log(num); // 2

console.log(Utilities.coinToss()); // true | false

console.log(Utilities.capitalize('batman')); // 'Batman'

Utilities.delay(3000, function () {
	return console.log('delayed output');
}); // 'delayed output', tres segundos después

Utilities.generateToken(); // 'DyvlJ_mhQ8X'
```

* Herencia:
```javascript
class Rectangle {
	constructor(len, wid) {
		this.len = len;
		this.wid = wid;
	}

	getArea() {
		return this.len * this.wid;
	}

	getInfo () {
		return 'I am a naughty rectangle!';
	}
}

class Square extends Rectangle { // extendemos la clase
	constructor(len) {
		super(len, len); // Invocamos al constructor de la clase padre
	}

	// Podría hacer referencia al método del padre, pero no es necesario!
	// Puedo omitirlo, ya que el método es heredado automáticamente.
	// getArea() {
	// 	return super.getArea(); // Fíjate que así invoco a un determinado método del padre.
	// }

	// Sobreescribir un método
	getInfo () {
		return 'I am a cheeky square!';
	}
}

let square = new Square(4);
console.log(square.getArea()); // 16. Observa que este método ha sido hererado de la clase padre

console.log(square instanceof Square); // true
console.log(square instanceof Rectangle); // true. Recuerda que Square extiende la clase Rectangle

console.log(square.getInfo()); // I am a cheeky square!
```


----------------------------------------------------------
## Singleton:

* Ejemplo de singleton pattern usando clases de ES6:
```javascript
class Singleton {
	constructor(){
		if (!Singleton.instance) {
			this._data = [];
			Singleton.instance = this;
		}
		return Object.freeze(Singleton.instance);
	}

	// Methods
	add(item){
		this._data.push(item);
	}

	get(id){
		return this._data.find(d => d.id === id);
	}
}

// Puedes exportar esta clase como un módulo:
// module.exports = Singleton;

// Impórtalo así:
// const Singleton = require('./Singleton');
// const foo = new Singleton();

let first = new Singleton();
let second = new Singleton();

first.add( { id: 1, name: 'john' } );
first.add( { id: 2, name: 'louis' } );

console.log(second); // [ {id: 1, name: "john"}, {id: 2, name: "louis"} ]

console.log(second.get(2)); // { id: 2, name: 'louis' }

console.log(first===second); // true (son la misma instancia)
```

----------------------------------------------------------
## Tratamiento de arrays:

* Crear un array con _n_ número de posiciones con un valor por defecto
```javascript
new Array(3).fill(0); // [0,0,0]
```

* Clonar un array
```javascript
/**
 * recursiveArrayClone() Crea un clon exacto de un array. Soporta matrices multidimensionales.
 *
 * @code
 * 	console.log( recursiveArrayClone( ['a', {}, [4, 0]] ) ); // ['a', {}, [4, 0]]
 * @endcode
 *
 * @param {array} arrayToClone. El array que se quiere clonar.
 * @return {array} clonedArray. Retorna un nuevo array.
 */
function recursiveArrayClone(arrayToClone){
	var clonedArray = [];
	for (var i = 0; i < arrayToClone.length; i++) {
		if( Array.isArray(arrayToClone[i]) ){
			clonedArray.push( recursiveArrayClone(arrayToClone[i]) );
		}else{
			clonedArray.push(arrayToClone[i]);
		}
	}
	return clonedArray;
}
```

* Vaciar un array
```javascript
var myArray = ['a', 'b', 'c'];
myArray.length = 0;
console.log(myArray); // []
```

* Quitar elementos _falsy_ de un array
```javascript
const myArray = [0, "0", "example", null, undefined, NaN, false];

let arrayFiltered = myArray.filter(Boolean);
console.log(arrayFiltered); // ["0", "example"]
```

* Uso de: _array.join_ (retorna un string)
```javascript
var a = ['Wind', 'Rain', 'Fire'];
a.join();      // 'Wind,Rain,Fire'
a.join('');    // 'WindRainFire'


var text = "Add the potato please, I always prefer more potato";
text = text.split("potato").join("cheese");
// Sustituye "potato" por "cheese" en todas las apariciones que haya en "text"
```

* Uso de: _array.concat_ (crea un nuevo array con la concatenación de 2 o más arrays)
```javascript
var arr1 = ['a', 'b', 'c'];
var arr2 = ['d', 'e', 'f'];
var arr3 = arr1.concat(arr2); // arr3 is a new array [ "a", "b", "c", "d", "e", "f" ]
```

* Uso de: _array.slice_ (crea una copia del array pero quitando, o no, algunos elementos del original)
```javascript
var fruits = ['Banana', 'Orange', 'Lemon', 'Apple', 'Mango'];
var citrus = fruits.slice(1, 3); // citrus == ['Orange','Lemon']


var reduced_fruits = fruits.slice(1); // reduced_fruits == ['Orange', 'Lemon', 'Apple', 'Mango']
```

* Uso de: _array.splice_ (permite añadir o eliminar elementos a un array. También es útil para partir un array en dos)
```javascript
var instruments = ['guitar', 'bass', 'saxophone'];
instruments.splice(2, 0, 'drums'); // instruments == ['guitar', 'bass', 'drums','saxophone']


var planets = ['Mercury','Venus','Earth','Mars','Pluto','Jupiter','Saturn','Uranus','Neptune'];
planets.splice(4,1);
// (sorry, Pluto) planets == ['Mercury','Venus','Earth','Mars','Jupiter','Saturn','Uranus','Neptune']


array_example.splice(0, 0, "foo"); // Añade "foo" a la primera posición de un array


var myFish = ['angel', 'clown', 'mandarin', 'sturgeon', 'cherubfish'];
var removedFish = myFish.splice(2);
console.log(myFish); // ["angel", "clown"]
console.log(removedFish); // ["mandarin", "sturgeon", "cherubfish"]
```

* Uso de: _array.indexOf_ (busca un determinado elemento en un array)
```javascript
var a = ["foo", "bar", "biz"]; 
a.indexOf("bar"); // 1 
a.indexOf("NyanCat"); // -1

if (a.indexOf("NyanCat") === -1) {
  // element doesn't exist in array
}
```

* Uso de: _array.filter_ (crea un nuevo array a partir de otro con aquellos elementos que pasan el filtro especificado)
```javascript
function isBigEnough(value) {
  return value >= 10;
}

var numbers = [12, 5, 8, 130, 44];

var filteredNumbers = numbers.filter(isBigEnough); // filteredNumbers is [12, 130, 44]
```

* Uso de: _array.map_ (crea un nuevo array a partir de realizar una función en cada uno de los elementos del array original)
```javascript
var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt); // roots is now [1, 2, 3]


var nums = [1,2,3];
var doubleNums = nums.map(
	function(number){
		return number * 2;
	}
);
// doubleNums is now [2, 4, 6]
```

* Uso de: _array.forEach_ (recorre el array aplicando una función a cada elemento)
```javascript
var a = ['a', 'b', 'c'];
a.forEach(function(element) {
	console.log(element);
});

// Otra manera de implementarlo:
function fooFunction(currentValue,index,array) {
	// ...
}
a.forEach(fooFunction, this); // Podemos nombrar la función en vez de declararla dentro del forEach.
// Adicionalmente, podemos pasarle el objeto 'this' a la función.
```

* Uso de: _array.reduce_ (aplica una función a un acumulador y a cada valor de un array (de izquierda a derecha) para reducirlo a un único valor.)
```javascript
cart = [ {ref: "yuh", price: 23}, {ref: "jum", price: 44} ];
var result = cart.reduce(function(accum, v){
	return accum + v.price;
},0); // en este caso, el 0 es el acumulador.
console.log(result); // 67


var arrayOfBooleans = [true, false, true, true];
var totalOfTrues = arrayOfBooleans.reduce( function(accumulator, arrayElement){
			return arrayElement ? ++accumulator : accumulator;
		},0
	);
console.log(totalOfTrues); // 3
```

----------------------------------------------------------
## Tratamiento de strings:  

* Uso de: _string.match_ (crea un array a partir de comparar un string con una regular expression)
```javascript
var str = "The rain in SPAIN stays mainly in the plain"; 
var res = str.match(/ain/gi); // ["ain","AIN","ain","ain"]


var text = "The phone number is 93 555 22 33";
var result = text.match(/\d+/); // ["93"]
var allResults = text.match(/\d+/g); // allResults is ["93", "555", "22", "33"]
var numberOfResults = text.match(/\d+/g).length; // 4


var text = "<p>Hello {{name}}, you are {{age}} years old.</p>";
var extract = text.match(/\{\{(.+?)\}\}/g);
console.log(extract); // ["{{name}}", "{{age}}"]
```

* Uso de: _string.search_ (retorna un boolean si encuentra una coincidencia en el string con el patrón indicado)
```javascript
var str = "The best things in life are free";
var pattern = new RegExp(/life/g);
var res = pattern.test(str); // true
```

* Diferencias entre _string.substr_ y _string.substring_ (ambos métodos retornan un nuevo string a partir del original, pero observa la diferencia de resultados):
```javascript
var text = "Example";
console.log(text.substr(1,5)); // "xampl" 
// (a partir del índice dado, retorna tantos caracteres como indiques en el segundo parámetro)


console.log(text.substring(1,5)); // "xamp" 
// (a partir del índice dado, retorna caracteres hasta el otro índice dado)
```

* Uso de: _string.split_ (genera un nuevo array a partir de trocear un string por el parámetro indicado)
```javascript
var array_of_words = "A new car".split(" ");
console.log(array_of_words); // ["A", "new", "car"]


console.log( "hello".split("") ); // ["h", "e", "l", "l", "o"]


var text = "Add the potato please, I always prefer more potato";
text = text.split("potato").join("cheese");
// Sustituye "potato" por "cheese" en todas las apariciones que haya en "text"
```

----------------------------------------------------------
## Objetos:

* Contexto de _this_:
```javascript
var biz = {
	nombre: "Dídac",
	"nombre-apellido": "Dídac García", // entrecomillado por contener un guión!

	// error, "this" apunta al objecto global:
	saludar: 'Hola, soy ' + this.nombre,

	// correcto, "this" apunta a "biz":
	presentarse: function(){
		return 'Mi nombre es ' + this.nombre;
	},

	get nombreCompleto() { return this["nombre-apellido"]}
}

console.log(biz.saludar); // 'Hola, soy undefined'
console.log(biz.presentarse()); // 'Mi nombre es Pedro'

console.log(biz.nombreCompleto); // "Dídac García"

console.log(biz.nombre-apellido); // Error!
console.log(biz["nombre-apellido"]); // "Dídac García"

delete biz["nombre-apellido"]; // Siempre retorna true! Incluso cuando no existe la propiedad a borrar
```

* Obtener un array a partir de las propiedades de un objeto:
```javascript
var foo = {
	nombre: 'Juan',
	apellido: 'Ruiz'
}

var arrayOfProperties = Object.keys(foo);
console.log(arrayOfProperties); // ['nombre', 'apellido']
```

* Crear un array a partir de un objeto:
```javascript
var biz = {
	length: 2,
	'0': 'biz',
	'1': 'foo'
}

var arrayFromObject = Array.prototype.slice.call(biz);
console.log(arrayFromObject); // ['biz', 'foo']
console.log(arrayFromObject.length); // 2
console.log(arrayFromObject[1]); // 'foo'
```

* Clonar un objeto:
```javascript
function deepClone(originalObject) {
	const clonedObject = {};
	for (let key in originalObject) {
		if (typeof (originalObject[key]) != 'object') {
			clonedObject[key] = originalObject[key];
		} else {
			clonedObject[key] = deepClone(originalObject[key]);
		}
	}
	return clonedObject;
}

const newObject = deepClone(originalObject);
```

----------------------------------------------------------
## Set: 

* Ejemplo básico de _set_:
```javascript
const set = new Set([1, 2, 3, 4, 5]);
// Almacena valores únicos de cualquier tipo, incluso valores primitivos u objetos de referencia.
// Se forma a partir de un objeto iterable. Se puede crear vacío pasándole null.

set.has(4); // true
set.has('4'); // false. No se ha almacenado un string, si no un entero.

set.delete(5); // true

set.add('foo'); // {1, 2, 3, 4, "foo"}

const s = new Set([{a:1},{b:2},{a:1}]);
console.log(s); // {{a:1},{b:2},{a:1}}
// Observa que en este caso parece existir un objeto duplicado, en realidad son objetos distintos!

console.log(s.size); // 3

s.clear(); // Vacía de elementos el set
```

* Descomponer strings usando _set_ (recuerda que no permite valores duplicados):
```javascript
let mySet = new Set('foooooooooo');
// Esto sucede porque le estamos pasando un valor iterable (en este caso, un string)
console.log(mySet); // {"f", "o"}

for (let i of mySet) {
	console.log(i);
} // Una manera de iterar un set

mySet.forEach(i => console.log(i)); // Otra manera de iterar un set.
// Ojo, no funcionan: filter, map y reduce.
// Aunque puedes transformarlo en un array para usar estos métodos así: [...mySet]
```

* Eliminar elementos duplicados en un array mediante _set_ y _spread_ operator:
```javascript
let nums = [1, 2, 2, 2, 2, 3, 4];
function deleteDuplicated(items) {
	return [ ... new Set(items) ]; // retorna un array
}
console.log( deleteDuplicated(nums) ); // [1, 2, 3, 4]
```

----------------------------------------------------------
## Map: 

* Primeros pasos con _map_:
```javascript
const biz = new Map();
biz.set('name', 'John');
biz.set('age', 53);

console.log(biz.size); // 2

for (let [key, value] of biz) {
  console.log(`${key} => ${value}`);
  // name => John
  // age => 53
}

console.log(biz.get('age')); // 53

biz.delete('age'); // true
console.log(biz); // {"name" => "John"}

biz.clear(); // {}
```

* Más opciones interesantes con _map_:
```javascript
const biz = new Map( [ ['name','John'], ['Surname', 'Doe'], [undefined, null] ] );

console.log(biz.has('name')); // true
console.log(biz.has('Give me a cookie')); // false

console.log(biz); // {"name" => "John", "Surname" => "Doe", undefined => null}

const arrayOfKeys = Array.from(biz.keys()); // ["name", "Surname", undefined]

biz.forEach( (value, key, originalMap) => {
	if(value === null) {
		originalMap.delete(key);
	}
});
console.log(biz); // {"name" => "John", "Surname" => "Doe"}

biz.set('name', 'Maria'); // {"name" => "Maria", "Surname" => "Doe"}
```

----------------------------------------------------------
## Debugging y console:

* Uso del comando _debugger_ (establece un "breakpoint" en el código):
```javascript
// some code...
debugger;
// some code...
```

* La propiedad _table_  del objeto _console_ (retorna una tabla a partir de un objeto o un array)
```javascript
var person = { firstName:"John", lastName:"Doe", age:50 };
console.table(person);
```

* Destructuring en el _console.log_
```javascript
let person = { firstName:"John", lastName:"Doe", age:34 };
console.log( { person } );
```

* Uso de _console.group()_ / _console.groupEnd()_ en bucles:
```javascript
for(var i = 0; i < 2; i++){
	console.group("Primer nivel: ", i);

	for(var k = 0; k < 2; k++){
		console.group("Segundo nivel: ", k);
		
		// Your code...
		console.log("Value of i: " + i);
		console.log("Value of k: " + k);

		console.groupEnd();
	}

	console.groupEnd();
}
```

* Uso de _console.log_ con estilos CSS:
```javascript
var foo = "AWESOME";
console.log('%c' + foo, 'color: yellow; background-color: black;');
```

* Uso de _console.error_:
```javascript
console.error("Algo salió mal");
```

* Un pequeño truco para saber con que tipo de objeto trabajamos:
```javascript
function whatTypeIs (element){
	return Object.prototype.toString.call(element).slice(8,-1);
}

console.log( whatTypeIs( ["A", "B"] ) ); // "Array"
console.log( whatTypeIs( "foo" ) ); // "String"
console.log( whatTypeIs( 4 ) ); // "Number"
console.log( whatTypeIs( /[a]/g ) ); // "RegExp"
console.log( whatTypeIs( new Set() ) ); // "Set"
console.log( whatTypeIs( new Map() ) ); // "Map"
console.log( whatTypeIs( () => {} ) ); // "Function"
console.log( whatTypeIs( null ) ); // "Null"
console.log( whatTypeIs( undefined ) ); // "Undefined"
console.log( whatTypeIs( NaN ) ); // "Number"
console.log( whatTypeIs( new Date ) ); // "Date"
```

* JSON.stringify fáciles de leer. Puedes usar el tercer parámetro para espicificar el tipo de indentación:
```javascript
const person = { firstName:"John", lastName:"Doe", age:34 };

JSON.stringify(person, null, 2); // Indentación: dos espacios
/*
{
  "firstName": "John",
  "lastName": "Doe",
  "age": 34
}
*/


JSON.stringify(person, null, '\t'); // Indentación: tabulador
/*
{
	"firstName": "John",
	"lastName": "Doe",
	"age": 34
}
*/
```


----------------------------------------------------------
## De callback hell a promesas:

```javascript
function justAnAsyncFunction(str, cb) {
	// Esto sólo simula ser algo asíncrono
	setTimeout(function(){
		if(!str) {
			cb({ok: false});
		} else {
			cb ({
				ok: true, 
				users: ['john', 'jack']
			});
		}
	}, 2000);
}


function retrieveUsers(cb) {
	// tipico ejemplo de anidación de funciones que contienen callback (callback hell)
	justAnAsyncFunction('SELECT * FROM users', function(results) {
		if (results.ok) {
			cb(results.users);
		} else {
			cb('Error');
		}
	});
}

retrieveUsers(console.log); // retorna ["john", "jack"] al cabo de dos segundos.


// Ahora fíjate como reescribiendo la función "retrieveUsers" se convierte en una promesa
function retrieveUsers_promisify() {
	return new Promise( (resolve, reject) => {
		justAnAsyncFunction('SELECT * FROM users', function(results) {
			if (results.ok) {
				resolve( results.users );
			} else {
				reject( new Error('Error') );
			}
		});
	});
}

// Esto nos permite llamarla e interactuar con ella como promesa!
function getUserUsingPromises(cb) {
	return retrieveUsers_promisify()
	.then((results) => {
		cb(results);
	})
	.catch((error) => {
		console.error(error.message);
	});
}

getUserUsingPromises(console.log); // esto es una promesa que tardará dos segundos en resolverse y dará este resultado: ["john", "jack"]

console.log(await retrieveUsers_promisify()); // También puedo hacer esto! Ojo, aquí estoy deteniendo la ejecución hasta que el código asíncrono acabe de ejecutarse
```

----------------------------------------------------------
## Promesas:

* Ejemplo de una cadena de promesas en la que la primera provee los datos para que la segunda puedan ejecutarse:
```javascript
const runAsyncFunctions = async () => {
  const users = await getUsers(); // Esto retornaría un array

  Promise.all(
	users.map(async user => {
	  const userId = await getIdFromUser(user);

	  const capitalizedId = await capitalizeIds(userId);
	})
  );
}

runAsyncFunctions(); // esto es una promesa
```

----------------------------------------------------------
## Async-Await:

Recuerda que _async_ crea una promesa.  

* Uso de _async-await_:
```javascript
// Ejemplo 1:
async function cooking() {
	const a = await obtainIngredientA();
	const b = await obtainIngredientB();
	const recipe = await newRecipe(a,b);
	return recipe;
} // async convierte esta función en una promesa

cooking().then((result) => {
	console.log(result);
}).catch((e) => {
	// if 'cooking' function or 'then' method throw an error...
	console.error(e.message);
});


// Ejemplo 2:
const getIngredients = async () => {
	try {
		const butter = await getButter();
		const flour = await getFlour();
		const sugar = await getSugar();
		const eggs = await getEggs();
		return [
			butter,
			flour,
			sugar,
			eggs,
		];
	} catch(e) { 
		return e; 
	}
}

getIngredients().then((result) => {
	if (result instanceof Error) {
		// if 'getIngredients' function throw an error...
		console.error(result.message);
	}
	// ...
}).catch((e) => {
	// if 'then' method throw an error...
	console.error(e.message);
});


// Ejemplo 3:
const to = (promise) => {
	return promise.then((data) => [null, data]).catch((err) => [err]);
};

async function getDataFromAsyncCode() {
	const [err, response] = await to(justAnAsyncFunction());
	if (err) throw new Error(`Error: ${err}`);
	return response;
}

getDataFromAsyncCode(); // Esto es una promesa. Debes tratar los resultados con 'then()' y 'catch()';

// También puedes hacer esto:
console.log(await getDataFromAsyncCode()); // Ojo, estas deteniendo la ejecución del código


// Ejemplo 4:
// Traditional promise chaining
getIssue()
	.then(issue => getOwner(issue.ownerId))
	.then(owner => sendEmail(owner.email, 'Some text'));

// Using async functions! (Considera agruparlo bajo una función async si no quieres detener la ejecución del script)
const issue = await getIssue();
const owner = await getOwner(issue.ownerId);
await sendEmail(owner.email, 'Some text');


// Ejemplo 5:
async function foo(things) {
	const results = [];
	for (let thing of things) { // use "for ... of" in arrays or sets; use "for ... in" in objects
		// good: all asynchronous operations are inmediately started
		results.push( asyncFooFunction(thing) );
	}

	// now that all the asynchronous operations are running, here we wait until they all complete
	return await Promise.all(results);
}
```

----------------------------------------------------------

## setTimeout:

* Truco para ejecutar un código al momento y repetir su ejecución de manera periódica:
```javascript
(function loop() {
	// ... Your code!

	setTimeout(loop, 2000);
})();

// También puedes hacer esto:
(async function loop() {
	// ... Your code!
	await foo();

	setTimeout(loop, 2000);
})();
```

----------------------------------------------------------
