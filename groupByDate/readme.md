# MongoDB Agreggate, agrupamento por timestamp

Nesse exemplo, vou mostrar várias formas de aggrupamento de elementos baseado em um field do tipo date.

## Inserindo a massa de dados:
Inicialmente vou inserir uma massa de dados onde "start" é o momento de inicio de um determinado compromisso e "end" é o momento de fim desse compromisso. Em resumo uma agenda:.
```
db.teste.insertMany([
  { "_id" : ObjectId("5b1c9df9d96bba61b9ce56ff"), "start" : { "dateTime" : ISODate("2018-06-07T03:39:57.512Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-07T04:09:57.512Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b1cdb2bbc9cd40c444e4503"), "start" : { "dateTime" : ISODate("2018-06-10T08:02:30.100Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-10T08:02:30.100Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b1cdc13bc9cd40c444e4506"), "start" : { "dateTime" : ISODate("2018-06-10T08:06:24.953Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-10T08:06:24.953Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b1d4d4d1ae501111ed0c116"), "start" : { "dateTime" : ISODate("2018-06-10T16:09:18.628Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-10T16:09:18.628Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b22729af6ef0e0b4f022ee6"), "start" : { "dateTime" : ISODate("2018-06-14T13:48:35.497Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-14T13:48:35.497Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b292d33b60c692739966e1e"), "start" : { "dateTime" : ISODate("2018-06-19T16:19:17.383Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-19T16:19:17.383Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b2d4d1a66199c081136674c"), "start" : { "dateTime" : ISODate("2018-06-22T19:19:48.209Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-06-22T19:19:48.209Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b537188b054953322a50b0f"), "start" : { "dateTime" : ISODate("2018-07-21T17:46:30.734Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-21T17:46:30.734Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5a2a5eeb45e3102ecb5d32"), "start" : { "dateTime" : ISODate("2018-07-26T20:08:34.895Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-26T20:18:34.895Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5a8f6c1440bc2ef05e9641"), "start" : { "dateTime" : ISODate("2018-07-27T03:18:25.418Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-27T03:18:25.418Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5b737e32c8ca6ac309f5d3"), "start" : { "dateTime" : ISODate("2018-07-27T19:31:56.568Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-27T20:21:56.568Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5b754832c8ca6ac309f5d8"), "start" : { "dateTime" : ISODate("2018-07-27T19:39:58.239Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-27T19:59:58.239Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5b841b3682fd0835e1d3ec"), "start" : { "dateTime" : ISODate("2018-07-27T20:34:30.306Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-27T20:34:30.306Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5b878c3682fd0835e1d3ef"), "start" : { "dateTime" : ISODate("2018-08-01T17:00:59.172Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-08-01T17:10:59.172Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b5b87943682fd0835e1d3f0"), "start" : { "dateTime" : ISODate("2018-08-01T16:00:24.072Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-08-01T16:10:24.072Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b60832c8ec77421f67991f2"), "start" : { "dateTime" : ISODate("2018-07-31T15:41:17.668Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T15:41:17.668Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b60838d8ec77421f67991f3"), "start" : { "dateTime" : ISODate("2018-07-31T15:42:56.552Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T16:12:56.552Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b608dc18ec77421f67991f5"), "start" : { "dateTime" : ISODate("2018-07-31T16:25:15.801Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T16:55:15.801Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b608ee88ec77421f67991f6"), "start" : { "dateTime" : ISODate("2018-07-31T16:31:05.523Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T17:21:05.523Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b608fd18ec77421f67991f7"), "start" : { "dateTime" : ISODate("2018-07-31T16:32:25.268Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T17:32:25.268Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b60ae93caa4452f7bde0e21"), "start" : { "dateTime" : ISODate("2018-07-31T09:00:00Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-07-31T12:00:00Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b7343d059ce8b13d54ca632"), "start" : { "dateTime" : ISODate("2018-08-14T21:03:22.188Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-08-14T22:03:22.188Z"), "timeZone" : "America/Sao_Paulo" } },
  { "_id" : ObjectId("5b87eb40418775070529e6ab"), "start" : { "dateTime" : ISODate("2018-08-28T21:30:00Z") }, "end" : { "dateTime" : ISODate("2018-08-28T22:00:00Z") } },
  { "_id" : ObjectId("5b9027fc29cea5634c201813"), "start" : { "dateTime" : ISODate("2018-09-05T17:30:00Z"), "timeZone" : "America/Sao_Paulo" }, "end" : { "dateTime" : ISODate("2018-09-05T18:00:00Z"), "timeZone" : "America/Sao_Paulo" } }
])
```

## Manipulando a massa:
Observe que agora já temos uma coleção com os nossos compromissos agendados e podemos começar a definir os indexes da nossa pesquisa. Imaginando que nossa empresa tem filiais em paises com fusos distintos. Precisamos levar em consideração o timeZone do agendamento (start.timeZone) para não recuperarmos falso positivo de eventos que já estão em andamento ou acabaram.

Assim, para um agrupamento por ano, usaremos então o [$year](https://docs.mongodb.com/manual/reference/operator/aggregation/year/)

```
db.teste.aggregate([
  {$group:{
    _id: {
      ano:{ $year: {date:"$start.dateTime", timezone: "$start.timeZone"} }
    },
    total: {$sum:1}
  }}
]).pretty()
```

Observe que o retorno teve um ano null
```
{ "_id" : { "ano" : null }, "total" : 1 }
{ "_id" : { "ano" : 2018 }, "total" : 23 }
```
Para preservar a fonte de cada agrupamento, podemos manter os documentos ROOT.

```
db.teste.aggregate([
  {$group:{
    _id: {
      ano:{ $year: {date:"$start.dateTime", timezone: "$start.timeZone"} }
    },
    total: {$sum:1},
    fonte: {$push:"$$ROOT"}
  }}
]).pretty()
```

Assim, descobrimos que o documento que retornou _id null está sem timeZone na fonte. Para evitar esse tipo de ocorrencia, vamos declarar que todo documento sem timeZone vai respeitar o timezone do nosso local.

```
// No nodejs
let tz = new Date().toString().split(" ").slice(-2)[0].slice(3)

// No shell do mongo
let tz = "America/Sao_Paulo"

db.teste.aggregate([
  {$group:{
    _id: {
      ano:{ 
        $year: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      }
    },
    total: {$sum:1},
    fonte: {$push:"$$ROOT"}
  }}
]).pretty()
```

Agora podemos fazer conbinações com quais quer variaveis de Date.

Ex. uma busca com indice no ano, mes, dia, e a hora que queremos:

```
db.teste.aggregate([
  {$group:{
    _id: {
      ano:{ 
        $year: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      mes:{ 
        $month: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      dia:{ 
        $dayOfMonth: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      hora:{ 
        $hour: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      }
    },
    total: {$sum:1},
    fonte: {$push:"$$ROOT"}
  }}
]).pretty()
```

Retorno resumido em 2 docs:
```
{
	"_id" : {
		"ano" : 2018,
		"mes" : 9,
		"dia" : 5,
		"hora" : 14
	},
	"total" : 1,
	"fonte" : [
		{
			"_id" : ObjectId("5b9027fc29cea5634c201813"),
			"start" : {
				"dateTime" : ISODate("2018-09-05T17:30:00Z"),
				"timeZone" : "America/Sao_Paulo"
			},
			"end" : {
				"dateTime" : ISODate("2018-09-05T18:00:00Z"),
				"timeZone" : "America/Sao_Paulo"
			}
		}
	]
}
{
	"_id" : {
		"ano" : 2018,
		"mes" : 8,
		"dia" : 14,
		"hora" : 18
	},
	"total" : 1,
	"fonte" : [
		{
			"_id" : ObjectId("5b7343d059ce8b13d54ca632"),
			"start" : {
				"dateTime" : ISODate("2018-08-14T21:03:22.188Z"),
				"timeZone" : "America/Sao_Paulo"
			},
			"end" : {
				"dateTime" : ISODate("2018-08-14T22:03:22.188Z"),
				"timeZone" : "America/Sao_Paulo"
			}
		}
	]
}

```

Agora vamos detalhar em frações de hora usando $mod (sobra da divisão). Para minutos não precisamos de timezone.

Por ex. vamos pegar amostras de 30 em 30 min:

```
db.teste.aggregate([
  {$group:{
    _id: {
      ano:{ 
        $year: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      mes:{ 
        $month: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      dia:{ 
        $dayOfMonth: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      hora:{ 
        $hour: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      min:{
        $subtract: [
          { $minute: {
            date:"$start.dateTime", 
            timezone: {$ifNull: [ "$start.timeZone", tz ]}
          }},
          { $mod: [ { "$minute": "$start.dateTime" }, 30 ] }
        ]
      }
    },
    total: {$sum:1},
    fonte: {$push:"$$ROOT"}
  }}
]).pretty()
```

Por fim, vamos limitar nossa busca à um periodo de amostragem

```
ini = ISODate("2018-09-05T00:00:00.001Z")
end = ISODate("2018-09-05T23:59:59.999Z")

db.teste.aggregate([
  {$match:{
    "start.dateTime": {$gte: ini, $lte: end}
  }},
  {$group:{
    _id: {
      ano:{ 
        $year: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      mes:{ 
        $month: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      dia:{ 
        $dayOfMonth: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      hora:{ 
        $hour: {
          date:"$start.dateTime",
          timezone: {$ifNull: [ "$start.timeZone", tz ]}
        } 
      },
      min:{
        $subtract: [
          { $minute: {
            date:"$start.dateTime", 
            timezone: {$ifNull: [ "$start.timeZone", tz ]}
          }},
          { $mod: [ { "$minute": "$start.dateTime" }, 30 ] }
        ]
      }
    },
    total: {$sum:1},
    fonte: {$push:"$$ROOT"}
  }}
]).pretty()

```

## Fonte:
<https://docs.mongodb.com/manual/reference/operator/aggregation/#date-expression-operators>