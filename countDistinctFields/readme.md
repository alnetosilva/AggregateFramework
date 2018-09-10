
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

    //First we'll extract only what we need find, on this case distinct users
    db.teste.aggregate([
      {$group:{
        _id: "$user"
      }}
    ])

    //Aqui vamos pegar os indices da busca reinserir no nosso filtro
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

    //Vamos contrar o total de elementos reinseridos
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
            total:{$sum:1}
          }}
        ],
        as: "res"
      }}
    ])

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