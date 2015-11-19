## **Resumo [Aula 04  - Parte 2](https://www.youtube.com/watch?time_continue=61&v=ozbmQb6SVQk)** ##

----------
## **options** ##
O objeto de options servira para configurar alguns valores diferentes do update
```
{
	upsert: boolean,
	multi: boolean, 
	writeConcern: document,
}
```

### **upsert** ###
Caso o documento que você esteja buscando na sua query  não seja encontrado em sua coleção ele sera inserido.

**Default: false**

Como exemplo vamos adicionar o campo active:true em nosso Testmon
```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {name: 'Testmon'}

mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$set: {active:true}}

mjunior(mongod-3.0.7) be-mean-instagram> var options = {upsert:true}

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod,options)

Updated 1 existing record(s) in 5ms
WriteResult({
  "nMatched": 1, // 1 encontrato
  "nUpserted": 0, // 0 criados
  "nModified": 1 // 1 modificado
})
mjunior(mongod-3.0.7) be-mean-instagram> 

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("564d3ea1b5605ebec8fc3150"),
  "name": "Testmon",
  "description": "Essa é a minha nova descrição alterada com sucesso",
  "attack": 8001,
  "height": 2.1,
  "defensee": 8000,
  "type": "TesteType",
  "active": true
}

```
Como nosso pokemon Testmon já existia foi apenas adicionado o campo **active:true** no documento.

Como exemplo vamos adicionar o campo active:true em nosso pokemon TestmonInexistente e ver o que acontece.

```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {name: 'TestmonInexistente'}

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod,options)

Updated 1 new record(s) in 2ms
WriteResult({
  "nMatched": 0, // Nenhum encontrado
  "nUpserted": 1, // 1 criado
  "nModified": 0, // Nenhum modificado
  "_id": ObjectId("564d4f9d676c466685bfe5da")
})
```
Quando é **criado um objeto nos recebemos o _ID** dele como retorno, assim fica fácil realizarmos uma nova busca e descobrir os dados inseridos

```
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find({_id:ObjectId("564d4f9d676c466685bfe5da")})
{
  "_id": ObjectId("564d4f9d676c466685bfe5da"),
  "name": "TestmonInexistente",
  "active": true
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 
```

Como percebemos foi inserido apenas o campo active:true no documento, pois foi o unico informado na hora da modificação.

### **$setOnInsert** ###
Ele ira inserir um documento adicionar a nossa modificação caso o aconteça um $upsert.

Como exemplo vamos buscar um pokemon que não existe, então nos vamos inserir eles com os valores padrão em nossa collection.

```
// Nossa query de modificação com setOnInsert
var mod = {
	  "$set": {
	    "active": true
	  },
	  "$setOnInsert": {
	    "name": "NaoExisteMon",
	    "attack": null,
	    "defense": null,
	    "height": null,
	    "description": "Sem informações"
	  }
	}

```

```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {name: 'NaoExisteMon'}

mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update(query,mod,options)
Updated 1 new record(s) in 2ms
WriteResult({
  "nMatched": 0, // 0 Encontrados
  "nUpserted": 1, // 1 Criado
  "nModified": 0, // 0 Modificado
  "_id": ObjectId("564d51f1676c466685bfe5db")
})

```
Como podemos ver nosso pokemon foi criado com a **_ID: ObjectId("564d51f1676c466685bfe5db")** ao fazermos a busca por sua ID vemos as informaçoes abaixo.

```
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find({_id:ObjectId("564d51f1676c466685bfe5db")})
{
  "_id": ObjectId("564d51f1676c466685bfe5db"),
  "name": "NaoExisteMon",
  "active": true,
  "attack": null,
  "defense": null,
  "height": null,
  "description": "Sem informações"
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 

```


### **multi** ###

No MongoDB você **só consegue alterar um documento por vez**. Isso evita um **UPDATE SEM WHERE **  **:P**, mas caso você tenha certeza que precisa alterar mais de um documento você deve passar em options {**multi:true**}

### **writeConcern** ###
É um documento que descreve qualquer garantia que o mongodb vai fornecer ao relatar sucesso de sua escrita. Se você quer que ele seja rápido ele terá uma **PREOCUPAÇÃO FRACA**, com uma **PREOCUPAÇÃO FORTE** o banco sera mais lento, mas você tem uma garantia melhor da escrita.

As operações emitidas com **writeConcern fracos** em caso de erro podem não persistir. Com o **writeConcern fortes** os clientes aguardam o mongodb confirmar a operação de escrita

## **FIND** ##
 Mais sobre find na **[aula 03](https://github.com/mjsjunior/be-mean-modulo-mongodb/tree/master/classes/Class-03)**
 
----------
Vamos atualizar todos nossos pokemons com o attack investida. Como vamos atualizar mais de 1 documento precisamos passar em options nosso { multi:true }
 
```
mjunior(mongod-3.0.7) be-mean-instagram> var mod = {$set:{moves:['investida']}}
mjunior(mongod-3.0.7) be-mean-instagram> var options = {multi:true}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update({},mod,options)
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.update({},mod,options)
Updated 9 existing record(s) in 3ms
WriteResult({
  "nMatched": 9,
  "nUpserted": 0,
  "nModified": 9
})
mjunior(mongod-3.0.7) be-mean-instagram> 

```

## **$in** ##
"OR no array"

O operador $in retorna os documentos que possui algum valor passado no array de valores

```
{
	campo: {$in: [Array_De_Valores] }
}
```
Como exemplo vamos pesquisar por todos os pokemons que possuem o attack (move) Choque do trovão ou Lança-chamas

> **/valor/i** expressão regular no javascript /i ignora diferença letra maiuscula e minuscula

```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {moves:{$in: [/choque do trovão/i,/lança-chamas/i]}}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "investida",
    "Choque do trovão"
  ]
}
{
  "_id": ObjectId("56426f6ba4e4c4604c3cd105"),
  "name": "Charmander",
  "description": "Esse é o cão chupando manga de fofinho",
  "type": "fogo",
  "attack": 52,
  "height": 0.6,
  "moves": [
    "investida",
    "lança-chamas"
  ]
}
Fetched 2 record(s) in 3ms
mjunior(mongod-3.0.7) be-mean-instagram> 

```

## **$nin** ##
"NOR no array"

Faz exatamente o contrario do $in. Retorna qualquer documento que não contenha os valores passado no array
```
{
	campo: {$in: [Array_De_Valores] }
}
```

## **$all** ##
Retorna apenas documentos que contenha todos os atributos do array.
Vamos pesquisar todos os pokemons que possuem os movimentos 'Choque do trovão' e 'Investida'.

```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {moves:{$all: [/choque do trovão/i,/investida/i]}}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "investida",
    "Choque do trovão"
  ]
}
Fetched 1 record(s) in 1ms
mjunior(mongod-3.0.7) be-mean-instagram> 

```

## **Operadores de negação** ##

###**$ne**###
not equal, diferente
```
{
	campo: {$ne:'valor'}
}
```
Vamos pesquisar todos os pokemons que não são do tipo grama
```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {type: {$ne:'grama'}}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.find(query)
{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4,
  "moves": [
    "investida",
    "Choque do trovão"
  ]
}
{
  "_id": ObjectId("56426f6ba4e4c4604c3cd105"),
  "name": "Charmander",
  "description": "Esse é o cão chupando manga de fofinho",
  "type": "fogo",
  "attack": 52,
  "height": 0.6,
  "moves": [
    "investida",
    "lança-chamas"
  ]
}
{
  "_id": ObjectId("56427048a4e4c4604c3cd107"),
  "name": "Caterpie",
  "description": "Larva lutadora",
  "type": "inseto",
  "attack": 30,
  "height": 0.3,
  "defense": 35,
  "moves": [
    "investida"
  ]
}

```

## **$not** ##
```
{
	name:{$not : valor}
}
```

## **remove()** ##
Para apargar um registro utiliza-se o **remove()** para apagar uma coleção inteira usamos o **drop()**

Vamos apagar nosso pokemon de teste, Testmon

```
mjunior(mongod-3.0.7) be-mean-instagram> var query = {name: 'Testmon'}
mjunior(mongod-3.0.7) be-mean-instagram> db.pokemons.remove(query)
Removed 1 record(s) in 27ms
WriteResult({
  "nRemoved": 1
})
mjunior(mongod-3.0.7) be-mean-instagram> 

```

 **Caso remove() seja feito sem query todos os documentos serão apagados, o atributo MULTI é valido apenas para o UPDATE**

##**Exercício** 

 1. Adicionar 2 ataques ao mesmo tempo para: Pikachu,Squirtle,Bulbassauro e Charmander
 2. Adicionar 1 movimento em todos os pokemons: desvio
 3. Adicionar o pokemon 'AindaNaoExisteMon' caso ele nao exista com todos os dados null e a desriçao 'Sem maiores informações'
 4. Pesquisar todos os pokemons com ataque investida e mais um que voce adicionou, escolha seu pokemon favorito
 5. Pesquisar todos os pokemons que possuam os ataques que voce adicionou, escolha seu pokemon favorito
 6. Pesquisar todos que não sao to tipo eletrico
 7. Remova todos os pokemos do tipo agua com attack menor que 50

