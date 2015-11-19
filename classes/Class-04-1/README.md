## **Resumo [aula 04 - Parte 01](https://www.youtube.com/watch?time_continue=67&v=ONzJsNbv15U)** ##
----------
Um documento pode ser alterado de duas formas no mongodb. A primeira é utilizando o **save** e a segunda o **update.** 

Com o save é preciso buscar o documento antes de realizar a alteração, já com o update isso não é necessário, então vamos aprender a usar o **UPDATE**

### **Sintaxe** ###
```
	db.colecao.update(query,modificacao,options);

	//options é opcional
```

###**$SET**###
O operador $set **modifica** um valor ou cria o **campo caso ele não exista**

    {$set: {campo:'novo valor'}}

### **Exemplos** ###

Para exemplo vamos adicionar um novo pokemon de teste (Testmon) que sera editado.
```
mjunior(mongod-3.0.7) be-mean-instagram> var poke = {name:'Testmon',description:'Pokemon de teste',attack:8000,height:2.1,defensee:8000}

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.save(poke)

```
Vamos alterar a description deste pokemon e adicionar o campo TYPE

```
//Realize uma busca anterior pelo nome do Testmon e peguei o _ID para a query.

mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$set: {description:'Essa é a minha nova descrição alterada com sucesso',type:'TesteType'}}
mjunior(mongod-3.0.7) be-mean-instagram> var query = {_id:ObjectId("564d3ea1b5605ebec8fc3150")}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod)
Updated 0 record(s) in 2ms
WriteResult({
  "nMatched": 1,
  "nUpserted": 0,
  "nModified": 1
})

// Resultado após o update

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("564d3ea1b5605ebec8fc3150"),
  "name": "Testmon",
  "description": "Essa é a minha nova descrição alterada com sucesso",
  "attack": 8000,
  "height": 2.1,
  "defensee": 8000,
  "type": "TesteType"
}
Fetched 1 record(s) in 1ms

```


###  **$unset** ###


Este operador deleta os campos do documento JSON. Para isso você deve passar como **true** o campo que deseja deletar

```
{ 
	$unset: {campo : 1} 
}


```

### **$inc** ###
$inc **incremento** um valor no campo desejado. Caso o campo não exista ele **cria o campo** com o valor informado. 

Para **decrementar** basta informar um valor negativo

Como exemplo vamos aumentar em 1 o attack de nosso pokemon de teste (Testmon)
```
mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$inc: {attack:1}}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod)
Updated 1 existing record(s) in 2ms

//Fazemos a busca para ver o update
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("564d3ea1b5605ebec8fc3150"),
  "name": "Testmon",
  "description": "Essa é a minha nova descrição alterada com sucesso",
  "attack": 8001,
  "height": 2.1,
  "defensee": 8000,
  "type": "TesteType"
}
Fetched 1 record(s) in 1ms

```

## **Operadores de Array** ##

### **$push** ###
Ele adiciona um valor ao campo. Caso o campo não exista ele ira criar um novo campo com o valor passado no $push

Caso o campo não seja um array ira retornar um erro!

```
{$push: {campo: valor}}

```
Como exemplo vamos adicionar o move Choque do trovão no pokemon 'Pikahcu'
```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {name: 'Pikachu'}
mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$push: {moves: 'Choque do trovão'}}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod)
Updated 1 existing record(s) in 2ms

//Como o array moves não existia ele foi criado com o valor que enviamos no $push

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "Choque do trovão"
  ]
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 
```
 
### **$pushAll** ###
Tem a mesma funcionalidade do $push porem você pode passar um array de valores que todos serão adicionados no campo desejado

```
{
	$pushAll: {campo: arrayValores } 
}

```

Como exemplo vamos adicionar mais 3(três) moves ao 'Pikachu'
```
mjunior(mongod-3.0.7) be-mean-instagram> var newMoves = ['Investida','Ataque Rápido','Bola Elétrica']

mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$pushAll: {moves: newMoves} }

mjunior(mongod-3.0.7) be-mean-instagram> 
db.pokemons.update(query,mod)
Updated 1 existing record(s) in 3ms

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "Choque do trovão",
    "Investida",
    "Ataque Rápido",
    "Bola Elétrica"
  ]
}
Fetched 1 record(s) in 3ms
mjunior(mongod-3.0.7) be-mean-instagram> 
``` 


### **$pull** ###
Retira o valor do campo array

```
{$pull: {
		campo: valor;
	}
}
```

Como exemplo vamos retirar o move 'Bola Elétrica' do 'Pikachu'

```
mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$pull: {moves: 'Bola Elétrica'} }

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod)
Updated 1 existing record(s) in 2ms

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "Choque do trovão",
    "Investida",
    "Ataque Rápido"
  ]
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 

```

### **$pullAll** ###
Retira todos os valores informados no array do campo array desejado
```
{$pullAll: {campo:[valor,valor2,valor3]}
}
```

Como exemplo vamos retirar os moves 'Investida' e 'Choque do trovão' do 'Pikachu'

```
mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$pullAll: {moves: ['Investida','Choque do trovão']} }

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod)
Updated 1 existing record(s) in 2ms

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "Ataque Rápido"
  ]
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 

```

