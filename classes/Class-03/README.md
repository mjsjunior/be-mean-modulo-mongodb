
##**Resumo [aula 03](https://www.youtube.com/watch?time_continue=2523&v=cIHjA1hyPPY) - Curso mongo DB**##

**_ID**  é um **UUID**. Como o mongodb não trabalha com autoincrimento é adicionado a todo objeto inserido um ID Universal. Sua criação trabalha com 4 tipos de dados

 - 4 Bytes - Timestamp da hora que foi inserido
 - 3 Bytes - Identificador da maquina
 - 3 Bytes - ID do processo **mongod**
 - 3 Bytes - Contador hexadecimal inicializado aleatoriamente
 
----------

### **Realizando buscas** ###
## Sintaxe ##
    db.collection.find({clausula},{campos_p_retorno})

#### **Exemplos** ####
No exemplo abaixo vamos buscar todos os pokemons que tem nome Pikachu

    var query = {name:'Pikachu'}
    db.pokemons.find(query)
    
    {
      "_id": ObjectId("56426ebca4e4c4604c3cd103"),
      "name": "Pikachu",
      "description": "Rato elétrico bem fofinho",
      "type": "eletric",
      "attack": 55,
      "height": 0.4
    }

Comparando com um banco de dados relacional o campo *query* é o **WHERE** e os *campos* são o **SELECT** 

Na query devemos passar apenas um objeto para comparação.
Em **fields** passamos um objeto onde devemos informar o nome do campo com valor de **0** para **FALSE** e  **1** para **TRUE**

Portanto para realizar a pesquisa anterior retornando apenas o campo name e description devemos criar um objeto com **name : 1 e description:1**
```
    var fields = {name:1,description:1}
	db.pokemons.find(query,fields)
	{
	  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
	  "name": "Pikachu",
	  "description": "Rato elétrico bem fofinho"
	}
```
	
O campo **_id** vem por padrão em todas as consultas para desativar pasta setar **{ _ID:0 }** em fields

### **Operadores Aritméticos** ###

Buscamos apenas valores numericos

 - O sinal de **<** em mongodb **$lt** = **less than**
 - O sinal de **<=** em mongodb **$lte** = **less than or equal**
 - O sinal de **>** em mongodb **$gt** = **greater than**
 - O sinal de **>=** em mongodb **$gte** = **greater than or equal**

#### **Sintaxe** ####
```
var query = {campo : {
				operador : valor
				}
		}

```

#### **Exemplo** ####
No exemplo abaixo vamos buscar todos os pokemons que possuem altura **MENOR QUE 0.5.**

```
	var query = {height: { $lt:0.5 }}
	db.pokemons.find(query)

{
  "_id": ObjectId("56426ebca4e4c4604c3cd103"),
  "name": "Pikachu",
  "description": "Rato elétrico bem fofinho",
  "type": "eletric",
  "attack": 55,
  "height": 0.4
}
{
  "_id": ObjectId("56426f5aa4e4c4604c3cd104"),
  "name": "Bulbassauro",
  "description": "Chicote de trepadeira",
  "type": "grama",
  "attack": 49,
  "height": 0.4
}
{
  "_id": ObjectId("56427048a4e4c4604c3cd107"),
  "name": "Caterpie",
  "description": "Larva lutadora",
  "type": "inseto",
  "attack": 30,
  "height": 0.3,
  "defense": 35
}
```

### **Operadores Lógicos** ###

Para realizar um teste logico nos precisamos termar pelo menos duas condições, portando os operadores logicos recebem um array, onde cada posição sera um objeto json

**$OR**
```
	var query = {
				$or : [
					{name:'Pikachu'},
					{height:1.69}
				]}

```

No exemplo acima buscamos todos os pokemons que possuem name = Pikachu **OU** Altura igual 1.69

**$NOR**
```
	var query = {
				$nor : [
					{name:'Pikachu'},
					{height:1.69}
				]}

```
No exemplo acima fazemos o contrario do **$OR**, retornamos todos os pokemons que não possuem name == pikachu ou altura igual a 1.69

**$AND**
```
var query = {$and: 
				[{name: 'Pikachu'}, 
				{height:1.69}
			]}
```
No exemplo acima buscamos todos os pokemons que **possuem NAME = 'PIKACHU' e HEIGHT = 1.69**

**$exists**
Destaque para este operador. Como mongodb é schemaless nem todos nossos objetos irao possui o mesmo campo, assim utilizamos este operador para buscar todos os documentos que possuem o campo X

```
	var query = {campo: {$exists:true}}
```


### **Exercício da Aula** ###

 1. Liste todos os Pokemons com altura menor que 0.5;
 2. '' '' altura maior ou igual a 0.5
 3. '' '' menor ou igual a 0.5 E do tipo grama
 4. '' '' name = Pikachu ou Attack menor ou igual 0.5
 5. '' '' Liste todos pokemons com attack maior ou igual que 48 e com height menor ou igual que 0.5


