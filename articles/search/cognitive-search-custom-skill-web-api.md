---
title: Vlastní dovednosti webového rozhraní API v dovednosti
titleSuffix: Azure Cognitive Search
description: Rozšíření schopností Azure Kognitivní hledání dovednosti voláním na webová rozhraní API. Použijte vlastní dovednosti webového rozhraní API a integrujte svůj vlastní kód.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011927"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Vlastní dovednosti webového rozhraní API v kanálu pro rozšíření Azure Kognitivní hledání

**Vlastní dovednosti webového rozhraní API** umožňuje rozšířit rozšíření AI voláním koncového bodu webového rozhraní API, který poskytuje vlastní operace. Podobně jako u integrovaných dovedností má **vlastní dovednost webového rozhraní API** vstupy a výstupy. V závislosti na vstupech vaše webové rozhraní API obdrží datovou část JSON, když indexer běží, a vypíše datovou část JSON jako odpověď spolu s kódem stavu úspěch. Očekává se, že odpověď bude mít výstupy určené vaší vlastní dovedností. Jakákoli jiná odpověď se považuje za chybu a neprovádí se žádné obohacení.

Struktura datových částí JSON je podrobněji popsána v tomto dokumentu.

> [!NOTE]
> Indexer se dvakrát opakuje u určitých standardních stavových kódů HTTP vrácených z webového rozhraní API. Tyto stavové kódy HTTP: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. Custom. WebApiSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| `uri` | Identifikátor URI webového rozhraní API, na které se pošle datová část _JSON_ Je povolené jenom schéma identifikátoru URI **protokolu HTTPS** . |
| `httpMethod` | Metoda, která se má použít při odesílání datové části Povolené metody jsou `PUT` nebo `POST` |
| `httpHeaders` | Kolekce párů klíč-hodnota, kde klíče reprezentují názvy a hodnoty hlaviček, představuje hodnoty hlaviček, které budou odeslány do webového rozhraní API spolu s datovou částí. Následující záhlaví jsou v této kolekci zakázaná:  `Accept` , `Accept-Charset` , `Accept-Encoding` , `Content-Length` , `Content-Type` , `Cookie` , `Host` , `TE` , `Upgrade` , `Via` |
| `timeout` | Volitelné Když se tato parametr zadá, označuje časový limit pro klienta http, který provádí volání rozhraní API. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Například `PT60S` po dobu 60 sekund. Pokud není nastavená, vybere se výchozí hodnota 30 sekund. Časový limit lze nastavit na maximálně 230 sekund a minimálně 1 sekundu. |
| `batchSize` | Volitelné Určuje, kolik záznamů dat (viz struktura datové části _JSON_ níže) se pošle na volání rozhraní API. Pokud není nastavená, vybere se výchozí hodnota 1000. Doporučujeme použít tento parametr k dosažení vhodného kompromisu mezi propustností indexování a zatížením v rozhraní API. |
| `degreeOfParallelism` | Volitelné Když se tato hodnota zadá, označuje počet volání, která indexer provede paralelně se zadaným koncovým bodem. Tuto hodnotu můžete snížit, pokud se Váš koncový bod nedaří s příliš vysokým zatížením žádosti nebo ho zvýšit, pokud je váš koncový bod schopný přijmout více požadavků a vy chcete zvýšit výkon indexeru.  Pokud není nastavená, použije se výchozí hodnota 5. `degreeOfParallelism`Hodnota může být nastavená na maximálně 10 a minimálně 1. |

## <a name="skill-inputs"></a>Vstupy dovedností

Pro tuto dovednost neexistují žádné předdefinované vstupy. Můžete vybrat jedno nebo více polí, která budou již k dispozici v době provádění této dovednosti, jako vstupy a datová část _JSON_ odesílaná do webového rozhraní API budou mít různá pole.

## <a name="skill-outputs"></a>Výstupy dovedností

Pro tuto dovednost nejsou k dispozici žádné předdefinované výstupy. V závislosti na reakci, kterou webové rozhraní API vrátí, přidejte výstupní pole tak, aby bylo možné z odpovědi _JSON_ vybrat.


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
## <a name="sample-input-json-structure"></a>Ukázka vstupní struktury JSON

Tato struktura _JSON_ představuje datovou část, která se pošle do webového rozhraní API.
Vždy se bude řídit těmito omezeními:

* Je volána entita nejvyšší úrovně `values` a bude polem objektů. Počet takových objektů bude maximálně `batchSize`
* Každý objekt v `values` poli bude mít
    * `recordId`Vlastnost, která je **jedinečný** řetězec, který slouží k identifikaci daného záznamu.
    * `data`Vlastnost, která je objektem _JSON_ . Pole `data` vlastnosti budou odpovídat "názvům" uvedeným v `inputs` části definice dovednosti. Hodnota těchto polí bude z `source` těchto polí (což může být z pole v dokumentu nebo potenciálně z jiné dovednosti).

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

## <a name="sample-output-json-structure"></a>Ukázka výstupní struktury JSON

"Výstup" odpovídá odpovědi vrácené z vašeho webového rozhraní API. Webové rozhraní API by mělo vracet jenom datovou část _JSON_ (ověřená tak, že si prohlíží `Content-Type` hlavičku odpovědi) a má splnit tato omezení:

* Měla by být volána entita nejvyšší úrovně `values` , která by měla být pole objektů.
* Počet objektů v poli musí být stejný jako počet objektů odeslaných do webového rozhraní API.
* Každý objekt by měl mít:
   * `recordId`Vlastnost
   * `data`Vlastnost, která je objektem, kde jsou obohacena pole s "názvy" v oblasti `output` a jejíž hodnota je považována za obohacení.
   * `errors`Vlastnost, která obsahuje seznam všech zjištěných chyb, které budou přidány do historie spouštění indexeru. Tato vlastnost je povinná, ale může mít `null` hodnotu.
   * `warnings`Vlastnost, která obsahuje seznam všech zjištěných upozornění, která budou přidána do historie spouštění indexeru. Tato vlastnost je povinná, ale může mít `null` hodnotu.
* Objekty v `values` poli nemusí být ve stejném pořadí jako objekty v `values` poli odeslané jako požadavek do webového rozhraní API. Nicméně se `recordId` používá pro korelaci, takže jakýkoliv záznam v odpovědi obsahující a, `recordId` který nebyl součástí původní žádosti webového rozhraní API, se zahodí.

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
Kromě nedostupného webového rozhraní API nebo odeslání stavových kódů, které nejsou úspěšné, se považují za chybné případy následující:

* Pokud webové rozhraní API vrátí stavový kód úspěchu, ale odezva indikuje, že není, `application/json` odpověď se považuje za neplatnou a neprovede se žádná rozšíření.
* Pokud jsou záznamy v poli Response **neplatné** ( `recordId` nejsou v původním požadavku nebo mají duplicitní hodnoty) `values` , pro **tyto** záznamy se neprovede žádné obohacení.

V případech, kdy webové rozhraní API není k dispozici nebo vrátí chybu protokolu HTTP, se do historie spouštění indexeru přidá popisné chyba s podrobnostmi o chybě HTTP.

## <a name="see-also"></a>Viz také

+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Přidání vlastní dovednosti do kanálu pro obohacení AI](cognitive-search-custom-skill-interface.md)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md)
