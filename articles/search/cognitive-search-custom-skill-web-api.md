---
title: Vlastní webové rozhraní API dovednosti v skillsets
titleSuffix: Azure Cognitive Search
description: Rozšiřte možnosti dovedností Azure Cognitive Search voláním na webová api. Pomocí vlastní webové rozhraní API dovednosti integrovat vlastní kód.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484121"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Dovednosti vlastního webového rozhraní API v kanálu obohacení Azure Cognitive Search

Vlastní **webové rozhraní API** dovednosti umožňuje rozšířit obohacení AI voláním koncového bodu webového rozhraní API poskytující vlastní operace. Podobně jako vestavěné dovednosti, **vlastní webové rozhraní API** dovednost má vstupy a výstupy. V závislosti na vstupech vaše webové rozhraní API obdrží datovou část JSON při spuštění indexeru a výstupy datové části JSON jako odpověď, spolu s kódem stavu úspěchu. Očekává se, že odpověď bude mít výstupy určené vlastní dovedností. Jakákoli jiná odpověď je považována za chybu a nejsou prováděny žádné obohacení.

Struktura datové části JSON jsou popsány dále v tomto dokumentu.

> [!NOTE]
> Indexer se bude opakovat dvakrát pro určité standardní kódy stavu HTTP vrácené z webového rozhraní API. Tyto stavové kódy HTTP jsou: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| Uri | Identifikátor URI webového rozhraní API, do kterého bude odeslána datová část _JSON._ Je povoleno pouze schéma **URI https.** |
| httpMetoda | Metoda, která má být používána při odesílání datové části. Povolené metody `PUT` jsou nebo`POST` |
| httpZáhlaví | Kolekce párů klíč hodnota, kde klíče představují názvy hlaviček a hodnoty představují hodnoty záhlaví, které budou odeslány do webového rozhraní API spolu s datovou částí. Následující záhlaví jsou zakázána v této `Accept`kolekci: `Content-Length` `Content-Type`, `Cookie` `Accept-Charset` `Accept-Encoding`, `Upgrade`, , , `Host`, `TE`, , , , , ,`Via` |
| timeout | (Nepovinné) Pokud je zadán, označuje časový čas pro klienta http volání rozhraní API. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [trvání ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Například `PT60S` po dobu 60 sekund. Pokud není nastavena, je vybrána výchozí hodnota 30 sekund. Časový limit lze nastavit na maximálně 230 sekund a minimálně 1 sekundu. |
| batchSize | (Nepovinné) Označuje, kolik "datových záznamů" (viz struktura datové části _JSON_ níže) bude odesláno na volání rozhraní API. Pokud není nastaveno, je vybráno výchozí 1000. Doporučujeme použít tento parametr k dosažení vhodného kompromisu mezi propustností indexování a zatížením rozhraní API. |
| degreeOfParallelism | (Nepovinné) Pokud je zadán, označuje počet volání indexeru bude provádět paralelně ke koncovému bodu, který jste zadali. Tuto hodnotu můžete snížit, pokud koncový bod selhává pod příliš vysokým zatížením požadavku, nebo jej zvýšit, pokud je koncový bod schopen přijmout další požadavky a chcete zvýšit výkon indexeru.  Pokud není nastavena, použije se výchozí hodnota 5. DegreeOfParallelism lze nastavit na maximálně 10 a minimálně 1. |

## <a name="skill-inputs"></a>Vstupy dovedností

Pro tuto dovednost neexistují žádné "předdefinované" vstupy. Můžete zvolit jedno nebo více polí, která by již byla k dispozici v době provádění této dovednosti jako vstupy a datová část _JSON_ odeslaná do webového rozhraní API bude mít různá pole.

## <a name="skill-outputs"></a>Výstupy dovedností

Pro tuto dovednost neexistují žádné "předdefinované" výstupy. V závislosti na odpovědi webového rozhraní API vrátí, přidejte výstupní pole tak, aby mohly být vyzvednuty z odpovědi _JSON._


## <a name="sample-definition"></a>Definice vzorku

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Vzorový vstup JSON struktura

Tato struktura _JSON_ představuje datovou část, která bude odeslána do webového rozhraní API.
Vždy bude dodržovat tato omezení:

* Entita nejvyšší úrovně `values` je volána a bude pole objektů. Počet těchto objektů bude maximálně`batchSize`
* Každý objekt `values` v poli bude mít
    * Vlastnost, `recordId` která je **jedinečný** řetězec, který slouží k identifikaci tohoto záznamu.
    * Vlastnost, `data` která je objektjkem _JSON._ Pole vlastnosti `data` budou odpovídat "názvům" `inputs` uvedeným v části definice dovednosti. Hodnota těchto polí bude podoby těchto `source` polí (která mohou popojít z pole v dokumentu nebo potenciálně z jiné dovednosti)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Vzorový výstup JSON struktura

"Výstup" odpovídá odpovědi vrácené z webového rozhraní API. Webové rozhraní API by mělo vrátit pouze datovou část `Content-Type` _JSON_ (ověřenou pohledem na hlavičku odpovědi) a mělo by splňovat následující omezení:

* Měla by existovat entita `values` nejvyšší úrovně, která by měla být pole objektů.
* Počet objektů v poli by měl být stejný jako počet objektů odeslaných do webového rozhraní API.
* Každý objekt by měl mít:
   * Vlastnost `recordId`
   * Vlastnost, `data` což je objekt, kde pole jsou obohacení odpovídající `output` "názvy" v a jehož hodnota je považována za obohacení.
   * Vlastnost, `errors` pole se seznamem všech chyb, které byly zjištěny, které budou přidány do historie provádění indexeru. Tato vlastnost je povinná, `null` ale může mít hodnotu.
   * Vlastnost, `warnings` pole se seznamem všech upozornění, která byla zjištěna a která budou přidána do historie provádění indexeru. Tato vlastnost je povinná, `null` ale může mít hodnotu.
* Objekty v `values` poli nemusí být ve stejném pořadí `values` jako objekty v poli odeslané jako požadavek webového rozhraní API. Však `recordId` se používá pro korelaci, takže všechny `recordId` záznamy v odpovědi obsahující a, která nebyla součástí původního požadavku na webové rozhraní API budou zahozeny.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Chybové případy
Kromě toho, že webové rozhraní API není k dispozici nebo odesíláte neúspěšné stavové kódy, jsou následující případy považovány za chybné případy:

* Pokud webové rozhraní API vrátí kód stavu úspěchu, `application/json` ale odpověď označuje, že není, je odpověď považována za neplatnou a nebude provedeno žádné obohacení.
* Pokud jsou v `recordId` poli odpovědí `values` **neplatné** (s neuvedeným v původním požadavku nebo s duplicitními hodnotami), nebude pro **tyto** záznamy provedeno žádné obohacení.

V případech, kdy webové rozhraní API není k dispozici nebo vrátí chybu HTTP, bude do historie spuštění indexeru přidána popisná chyba se všemi dostupnými podrobnostmi o chybě PROTOKOLU HTTP.

## <a name="see-also"></a>Viz také

+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Přidání vlastních dovedností do kanálu obohacení umělou ai.](cognitive-search-custom-skill-interface.md)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou aicí](cognitive-search-create-custom-skill-example.md)
