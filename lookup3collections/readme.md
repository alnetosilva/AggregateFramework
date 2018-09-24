# MongoDB Agreggate, relacionamento entre 3 coleções

Nesse exemplo, vou mostrar como relacionar várias coleções

## Inserindo a massa de dados:
Inicialmente vou inserir uma massa de dados, temos as seguintes coleções:

- Users é a coleção de usuarios no sistema

```
db.users.find({}).pretty()
{
	"_id" : ObjectId("5b2060bd17cf702a714b64ed"),
	"email" : "kinho@hotmail.com",
	"username" : "kinho",
	"password" : "$2a$10$YcyHHldJ9dY449wRLVflb.7DW7bX3wb4XvLY0UCPaEeScnvz0pxby",
	"__v" : 0
}
{
	"_id" : ObjectId("5ba2ecc220b88b5fb52d20c9"),
	"condominio" : [
		ObjectId("5ba2ec4420b88b5fb52d20c8")
	],
	"role" : "condomino",
	"email" : "ju@hotmail.com",
	"username" : "juli",
	"password" : "$2a$10$Mc63VfxvZChK824uDhg3uuDRIlecJDIuy5UnwQimnFr35jOeFUDKi",
	"__v" : 0
}
{
	"_id" : ObjectId("5ba3e6cf3eeafb43597bdd34"),
	"condominio" : [
		ObjectId("5ba3e6af3eeafb43597bdd33")
	],
	"role" : "condomino",
	"email" : "marcos@marcos.com",
	"username" : "marcos",
	"password" : "$2a$10$cmsrx0Fvruc6zAF4eAkDgeq5hddTS9gSsKTs14DKuZdlsTQVGqq3C",
	"__v" : 0
}
```

- Também temos a coleção dos tickets

```
db.tickets.find({}).pretty()
{
	"_id" : ObjectId("5ba3e6433eeafb43597bdd31"),
	"createdAt" : ISODate("2018-09-20T18:13:16.980Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "Iniciando novo ticket",
	"body" : "continua os testes!",
	"createdBy" : "juli",
	"condominio" : ObjectId("5ba2ec4420b88b5fb52d20c8"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba3e9a14664864683d29470"),
	"createdAt" : ISODate("2018-09-20T18:39:53.367Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "vamos ver",
	"body" : "o que dá",
	"createdBy" : "marcos",
	"condominio" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba3ea663f1c1446d6cc7899"),
	"createdAt" : ISODate("2018-09-20T18:42:11.404Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "teste novo form",
	"body" : "novo form",
	"createdBy" : "marcos",
	"condominio" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba4053e32526b522c45b4bd"),
	"createdAt" : ISODate("2018-09-20T20:37:58.663Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "aaaaaaaaa",
	"body" : "aaaaaaaaaaaaaaa",
	"createdBy" : "marcos",
	"condominio" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba5a4dc1eb22e43c54cd6ed"),
	"createdAt" : ISODate("2018-09-21T17:52:29.925Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "my new ticket",
	"body" : "vamos ver como vai ficar",
	"createdBy" : "marcos",
	"condominio" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba5a5531eb22e43c54cd6ee"),
	"createdAt" : ISODate("2018-09-21T17:52:29.925Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "my new ticket",
	"body" : "vamos ver como vai ficar",
	"createdBy" : "marcos",
	"condominio" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"__v" : 0
}
{
	"_id" : ObjectId("5ba5bb151705577cbb36fd96"),
	"createdAt" : ISODate("2018-09-22T03:43:24.873Z"),
	"likes" : 0,
	"likedBy" : [ ],
	"dislikes" : 0,
	"dislikedBy" : [ ],
	"title" : "lá vamos nós",
	"body" : "testando novamente",
	"createdBy" : "juli",
	"condominio" : ObjectId("5ba2ec4420b88b5fb52d20c8"),
	"__v" : 0
}
```

- A coleção de condominios

```
db.condominios.find({}).pretty()
{
	"_id" : ObjectId("5ba2ec4420b88b5fb52d20c8"),
	"createdAt" : ISODate("2018-09-19T23:50:21.647Z"),
	"title" : "Moradas Club Azaleias",
	"createdBy" : "kinho",
	"__v" : 0
}
{
	"_id" : ObjectId("5ba3e6af3eeafb43597bdd33"),
	"createdAt" : ISODate("2018-09-20T18:13:16.956Z"),
	"title" : "Condominio Terra Nova I",
	"createdBy" : "juli",
	"__v" : 0
}
```
## Relacionamento simples com uma coleção

- Podemos fazer um lookup com $let e $pipeline

```
db.users.aggregate([
  { $unwind: "$condominio" },
  { $lookup: {
    from: "condominios",
    let: { c: "$condominio" },
    pipeline: [
      {$match: {
        $expr:{
          $eq: ["$_id", "$$c"]
        }
      }},
    ],
    as: "cond"
  }}
]).pretty()
```
- Ou com o _id

```
db.users.aggregate([
  { $unwind: "$condominio" },
  { $lookup: {
    from: "condominios",
    localField: "condominio",
    foreignField: "_id",
    as: "cond"
  }}
]).pretty()
```
## Relacionamento multiplo com outras coleções

- Podemos também, relacionar com outras coleções usando $lookup com localField e foreignField.

Quando temos um relacionamento multiplo, podemos fazer um $unwind para tirar os ids dos condominios do array de condominios. Depois disso, podemos usar o retorno de uma coleção como parametro de entrada do próximo $lookup

```
db.users.aggregate([
  { $unwind: "$condominio" },
  { $lookup: {
    from: "condominios",
    localField: "condominio",
    foreignField: "_id",
    as: "condominios"
  }},
  { $lookup: {
    from: "tickets",
    localField: "condominios._id",
    foreignField: "condominio",
    as: "tickets"
  }}
]).pretty()
```

- Podemos também, relacionar com outras coleções com let e pipeline

O let nós usamos para criar variáveis locais que serão usadas dentro do $lookup em um novo pipeline (um sub-aggregate)

O pipeline é como se fosse um novo aggregate para que possamos usar quais-quer operadores do pipeline

```
db.users.aggregate([
  { $unwind: "$condominio" },
  { $lookup: {
    from: "condominios",
    let: { c: "$condominio" },
    pipeline: [
      {$match: {
        $expr:{
          $eq: ["$_id", "$$c"]
        }
      }},
      { $lookup: {
        from: "tickets",
        localField: "_id",
        foreignField: "condominio",
        as: "tickets"
      }}
    ],
    as: "cond"
  }}
]).pretty()
```
- Podemos escolher também, quais fields vamos retornar

```
db.users.aggregate([
  { $unwind: "$condominio" },
  { $lookup: {
    from: "condominios",
    let: { c: "$condominio" },
    pipeline: [
      { $match: {
        $expr:{
          $eq: ["$_id", "$$c"]
        }
      }},
      /* 
			 * Lembre-se que estamos dentro de outro estágio de pipeline.
       * Imagine que estamos percorrendo o retorno match anterior
       */
      { $lookup: {
        from: "tickets",
        localField: "_id",
        foreignField: "condominio",
        as: "tickets"
      }},
      // Por exemplo: vamos remover o __v do mongoose
      { $project: {
        "tickets.__v": false
      }}
    ],
    as: "cond"
  }}
]).pretty()
```