---
title: Vlastní dovednost pro hledání vnímání – Azure Search
description: Rozšíření schopností rozpoznávání vyhledávání dovednosti voláním na webová rozhraní API
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: bd38dacd9161812032512fcb8bfdcbe90d64731c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639872"
---
# <a name="custom-web-api-skill"></a>Vlastní dovednosti webového rozhraní API

**Vlastní dovednosti webového rozhraní API** umožňuje rozšiřování rozpoznávání vyhledávání voláním koncového bodu webového rozhraní API, který poskytuje vlastní operace. Podobně jako u integrovaných dovedností má **vlastní dovednost webového rozhraní API** vstupy a výstupy. V závislosti na vstupech vaše webové rozhraní API obdrží datovou část JSON, když indexer běží, a vypíše datovou část JSON jako odpověď spolu s kódem stavu úspěch. Očekává se, že odpověď bude mít výstupy určené vaší vlastní dovedností. Jakákoli jiná odpověď se považuje za chybu a neprovádí se žádné obohacení.

Struktura datových částí JSON je podrobněji popsána v tomto dokumentu.

> [!NOTE]
> Indexer se dvakrát opakuje u určitých standardních stavových kódů HTTP vrácených z webového rozhraní API. Tyto stavové kódy HTTP: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| uri | Identifikátor URI webového rozhraní API, na které se pošle datová část _JSON_ Je povolené jenom schéma identifikátoru URI **protokolu HTTPS** . |
| httpMethod | Metoda, která se má použít při odesílání datové části Povolené metody jsou `PUT` nebo`POST` |
| httpHeaders | Kolekce párů klíč-hodnota, kde klíče reprezentují názvy a hodnoty hlaviček, představuje hodnoty hlaviček, které budou odeslány do webového rozhraní API spolu s datovou částí. Následující `Accept`záhlaví jsou v této kolekci zakázaná:, `Cookie` `Accept-Encoding` `Accept-Charset`,, `Content-Length`, `Content-Type`,, `Host`, `TE`, `Upgrade`,`Via` |
| časový limit | Volitelné Když se tato parametr zadá, označuje časový limit pro klienta http, který provádí volání rozhraní API. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Například `PT60S` po dobu 60 sekund. Pokud není nastavená, vybere se výchozí hodnota 30 sekund. Časový limit lze nastavit na maximálně 230 sekund a minimálně 1 sekundu. |
| batchSize | Volitelné Určuje, kolik záznamů dat (viz struktura datové části _JSON_ níže) se pošle na volání rozhraní API. Pokud není nastavená, vybere se výchozí hodnota 1000. Doporučujeme použít tento parametr k dosažení vhodného kompromisu mezi propustností indexování a zatížením v rozhraní API. |

## <a name="skill-inputs"></a>Vstupy dovedností

Pro tuto dovednost neexistují žádné předdefinované vstupy. Můžete vybrat jedno nebo více polí, která budou již k dispozici v době provádění této dovednosti, jako vstupy a datová část _JSON_ odesílaná do webového rozhraní API budou mít různá pole.

## <a name="skill-outputs"></a>Výstupy dovedností

Pro tuto dovednost nejsou k dispozici žádné předdefinované výstupy. V závislosti na reakci, kterou webové rozhraní API vrátí, přidejte výstupní pole tak, aby bylo možné z odpovědi _JSON_ vybrat.


## <a name="sample-definition"></a>Definice vzorku

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
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
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Ukázka vstupní struktury JSON

Tato struktura _JSON_ představuje datovou část, která se pošle do webového rozhraní API.
Vždy se bude řídit těmito omezeními:

* Je volána `values` entita nejvyšší úrovně a bude polem objektů. Počet takových objektů bude maximálně`batchSize`
* Každý objekt v `values` poli bude mít
    * Vlastnost, která je jedinečný řetězec, který slouží k identifikaci daného záznamu. `recordId`
    * Vlastnost, která je objektem _JSON._ `data` Pole `data` vlastnosti budou odpovídat "názvům" uvedeným `inputs` v části definice dovednosti. Hodnota těchto polí bude z `source` těchto polí (což může být z pole v dokumentu nebo potenciálně z jiné dovednosti).

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Ukázka výstupní struktury JSON

"Výstup" odpovídá odpovědi vrácené z vašeho webového rozhraní API. Webové rozhraní API by mělo vracet jenom datovou část _JSON_ (ověřená tak `Content-Type` , že si prohlíží hlavičku odpovědi) a má splnit tato omezení:

* Měla by být volána `values` entita nejvyšší úrovně, která by měla být pole objektů.
* Počet objektů v poli musí být stejný jako počet objektů odeslaných do webového rozhraní API.
* Každý objekt by měl mít:
   * `recordId` Vlastnost
   * Vlastnost, která je objektem, kde jsou obohacena pole s "názvy" v oblasti `output` a jejíž hodnota je považována za obohacení. `data`
   * `errors` Vlastnost, která obsahuje seznam všech zjištěných chyb, které budou přidány do historie spouštění indexeru. Tato vlastnost je povinná, ale může `null` mít hodnotu.
   * `warnings` Vlastnost, která obsahuje seznam všech zjištěných upozornění, která budou přidána do historie spouštění indexeru. Tato vlastnost je povinná, ale může `null` mít hodnotu.
* Objekty v `values` poli nemusí být ve stejném pořadí jako objekty `values` v poli odeslané jako požadavek do webového rozhraní API. Nicméně se používá pro korelaci, takže jakýkoliv záznam v odpovědi obsahující a `recordId` , který nebyl součástí původní žádosti webového rozhraní API, se zahodí. `recordId`

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Chybové případy
Kromě nedostupného webového rozhraní API nebo odeslání stavových kódů, které nejsou úspěšné, se považují za chybné případy následující:

* Pokud webové rozhraní API vrátí stavový kód úspěchu, ale odezva indikuje, že `application/json` není, odpověď se považuje za neplatnou a neprovede se žádná rozšíření.
* Pokud jsou záznamy v poli Response `recordId` `values` neplatné (nejsou v původním požadavku nebo mají duplicitní hodnoty), pro **tyto** záznamy se neprovede žádné obohacení.

V případech, kdy webové rozhraní API není k dispozici nebo vrátí chybu protokolu HTTP, se do historie spouštění indexeru přidá popisné chyba s podrobnostmi o chybě HTTP.

## <a name="see-also"></a>Viz také:

+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Přidat vlastní dovednosti do vyhledávání rozpoznávání](cognitive-search-custom-skill-interface.md)
+ [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)