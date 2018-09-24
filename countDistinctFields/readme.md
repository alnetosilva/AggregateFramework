# MongoDB Agreggate, contando ocorrencias de um indice em multiplos fields

Nesse exemplo, vou mostrar como podemos buscar ocorrencias de um determinado indice em fields diversos.

## Inserindo a massa de dados:
Inicialmente vou inserir uma massa de dados:

```
db.teste.insertMany([
  {
    user: "USER1",
    last: "USER1"
  },
  {
    user: "USER1",
    last: "USER2"
  },
  {
    user: "USER2",
    last: "USER1"
  },
  {
    user: "USER2",
    last: "USER2"
  },
  {
    user: "USER1",
    last: "USER2"
  },
  {
    user: "USER1",
    last: null
  }
])
```

## Loading...

Primeiro vamos extrair nosso valor indice de pesquisa.

```
db.teste.aggregate([
  {$group:{
    _id: "$user"
  }}
])
```
Pronto, temos dois indices de busca para contarmos em nossos fields.

Retorno:
```
{ "_id" : "USER2" }
{ "_id" : "USER1" }

```

Agora que já temos a nossa base de procura, vamos reinserir nossos documentos na busca.
Vou usar o operador $lookup de uma forma mais dinâmica, com "let" e "pipeline"

```
db.teste.aggregate([
  {$group:{
    _id: "$user"
  }},
  {$lookup:{
    from: "teste",
    let: { indice: "$_id" },
    pipeline: [],
    as: "res"
  }}
])
```
Como o próprio nome já diz, o pipeline é um novo estagio de processamento. No entanto, para cada ocorrencia retornada no lookup do $lookup.
Assim, o "let" declara uma variavel local para ser usada nesse momento:

```
db.teste.aggregate([
  {$group:{
    _id: "$user"
  }},
  {$lookup:{
    from: "teste",
    let: { indice: "$_id" },
    pipeline: [
      {$group:{
        _id: "$$indice",
        total:{$sum:1}
      }}
    ],
    as: "res"
  }}
])
```
O resultado até o momento conta com o total de documentos como valor da soma.
Resultado:

```
{ "_id" : "USER2", "res" : [ { "_id" : "USER2", "total" : 6 } ] }
{ "_id" : "USER1", "res" : [ { "_id" : "USER1", "total" : 6 } ] }

```
Em uma colleção muito grande a quantidade de documentos processados pode facilmente ultrapassar o limite de 16MB do MongoDB. Por isso, vamos limitar nosso relacionamento:

```
db.teste.aggregate([
  {$group:{
    _id: "$user"
  }},
  {$lookup:{
    from: "teste",
    let: { indice: "$_id" },
    pipeline: [
      {$match:{
        $or:[
          {eq: ["$user", "$$indice"] },
          {eq: ["$last", "$$indice"] }
        ]
      }},
      {$group:{
        _id: "$$indice",
        total: { $sum: 1 }
      }}
    ],
    as: "res"
  }}
])
```

    //Agora vamos testar uma cond verdadeira
    db.teste.aggregate([
      {$group:{
        _id: "$user"
      }},
      {$lookup:{
        from: "teste",
        let: { indice: "$_id" },
        pipeline: [
          {$group:{
            _id: null,
            user:{$sum:{$cond:[
              {$eq:[true, true]}, 1, 0
            ]}}
          }}
        ],
        as: "res"
      }}
    ])

    //cond testada vamos extrair o que queremos
    db.teste.aggregate([
      {$group:{
        _id: "$user"
      }},
      {$lookup:{
        from: "teste",
        let: { indice: "$_id" },
        pipeline: [
          {$group:{
            _id: null,
            user:{$sum:{$cond:[
              {$eq:["$user", "$$indice"]}, 1, 0
            ]}},
            last:{$sum:{$cond:[
              {$eq:["$last", "$$indice"]}, 1, 0
            ]}}
          }}
        ],
        as: "res"
      }}
    ])

    //Vamos tirar o _id da sub-colection pois não precisamos dele
    db.teste.aggregate([
      {$group:{
        _id: "$user"
      }},
      {$lookup:{
        from: "teste",
        let: { indice: "$_id" },
        pipeline: [
          {$group:{
            _id: null,
            user:{$sum:{$cond:[
              {$eq:["$user", "$$indice"]}, 1, 0
            ]}},
            last:{$sum:{$cond:[
              {$eq:["$last", "$$indice"]}, 1, 0
            ]}}
          }},
          {$project:{
            _id: 0
          }}
        ],
        as: "res"
      }}
    ])