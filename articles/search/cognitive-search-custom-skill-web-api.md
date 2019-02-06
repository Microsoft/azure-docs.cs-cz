---
title: Hledání vlastního kognitivních dovedností – Azure Search
description: Rozšiřte funkce kognitivního vyhledávání dovednosti volání k webovým rozhraním API
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: 1fcb12fc2cfae98376210e1924a670cce444f4f2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757638"
---
# <a name="custom-web-api-skill"></a>Vlastních dovedností webového rozhraní API

**Vlastního webového rozhraní API** dovednosti umožňuje rozšířit kognitivního vyhledávání ve volání do koncového bodu webového rozhraní API poskytuje vlastní operace. Předdefinované dovednosti, podobně jako **vlastního webového rozhraní API** dovedností se vstupy a výstupy. V závislosti na vstupy, webového rozhraní API přijímá datovou část JSON při spuštění indexeru a vrací datovou část JSON jako odpověď, spolu s stavový kód úspěchu. Odpověď má mít výstupy určené vlastních dovedností. Druhá odpověď se považuje za chybu a jsou prováděny žádné obohacení.

Struktura datové části JSON jsou popsány dále dolů v tomto dokumentu.

> [!NOTE]
> Indexer bude opakovat dvakrát pro určité standardní vrácené stavové kódy HTTP z webového rozhraní API. Tyto stavové kódy HTTP jsou: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| identifikátor uri | Identifikátor URI webové rozhraní api, ke kterému _JSON_ odešle datovou část. Pouze **https** schéma identifikátoru URI je povolen. |
| httpMethod | Metody pro použití při odesílání datové části. Povolené metody jsou `PUT` nebo `POST` |
| httpHeaders | Kolekce párů klíč hodnota, kde klíče představují hlavičky názvy a hodnoty představují hodnoty hlavičky, které se odešlou do webového rozhraní API spolu s datovou část. Nebudou v této kolekci jsou zakázány následující hlavičky: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (Volitelné) -Li zadána, určuje časový limit pro volání rozhraní API klienta http. Musí být naformátovaná jako hodnotu "dayTimeDuration" XSD (omezená podmnožina [ISO 8601 trvání](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnota). Například `PT60S` po dobu 60 sekund. Pokud není sada, je vybrán výchozí hodnota je 30 sekund. Časový limit lze nastavit na maximálně 90 sekund a minimálně 1 sekunda. |
| batchSize | (Volitelné) Označuje, kolik "záznamů dat" (naleznete v tématu _JSON_ datovou část s následující strukturou) se odešlou na volání rozhraní API. Pokud není sada, je vybrán výchozí hodnota je 1000. Doporučujeme, abyste vytvořili pomocí tohoto parametru dosáhnout vhodný kompromis mezi zatížení vašeho rozhraní API a propustnost indexování |

## <a name="skill-inputs"></a>Vstupy dovedností

Neexistují žádné vstupy "předdefinovaný" pro tento dovedností. Můžete použít jeden nebo více polí, které by se už k dispozici v době spuštění této dovedností jako vstupy a _JSON_ datová část odeslaná do webového rozhraní API se mají různá pole.

## <a name="skill-outputs"></a>Výstupy dovedností

Neexistují žádné "předdefinovaný" výstupy pro tento dovedností. V závislosti na odpovědi na vaše webové rozhraní API vrátí, přidejte výstupních polí tak, aby se mohou být zachyceny z _JSON_ odpovědi.


## <a name="sample-definition"></a>Ukázková definice

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
## <a name="sample-input-json-structure"></a>Ukázkové vstupní struktuře JSON

To _JSON_ struktura představuje datovou část, která se pošle na vaše webové rozhraní API.
Bude vždy následovat tato omezení:

* Je volána vysoké úrovně entity `values` a pole objektů. Počet těchto objektů, které budou nejvíce na `batchSize`
* Každý objekt v `values` bude mít pole
    * A `recordId` vlastnost, která je **jedinečný** řetězec používaný k identifikaci záznamu.
    * A `data` vlastnost, která je _JSON_ objektu. Pole `data` vlastnost bude odpovídat "názvy" podle `inputs` část definice dovedností. Hodnota pole bude od `source` těchto polí (které by mohly být z polí v dokumentu nebo potenciálně z jiné dovednosti)

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

## <a name="sample-output-json-structure"></a>Ukázkový výstup JSON struktura

"output" odpovídá odpověď vrácenou z vašeho webového rozhraní api. Webové rozhraní api mělo vracet pouze _JSON_ datovou část (ověřit pohledem `Content-Type` hlavička odpovědi) a musí splňovat následující omezení:

* Měla by existovat nejvyšší úrovně entitu s názvem `values` který by měl být pole objektů.
* Počet objektů v poli, které by měl být stejný jako počet objektů, které jsou odeslány do webového rozhraní api.
* Každý objekt by měl mít:
   * A `recordId` vlastnost
   * A `data` vlastnost, která je objekt, kde jsou tato pole obohacení odpovídající "názvy" v `output` a jehož hodnota je považován za obohacení.
   * `errors` Vlastnosti, pole výpis všech zjištěných chyb, které se přidají do historie spuštění indexeru. Tato vlastnost je vyžadována, ale může mít `null` hodnotu.
   * A `warnings` vlastnosti, pole výpis upozornění došlo k, které budou přidány do historie spuštění indexeru. Tato vlastnost je vyžadována, ale může mít `null` hodnotu.
* Objekty v `values` pole nemusí být ve stejném pořadí jako objekt ve `values` pole odeslána jako požadavek do webového rozhraní API. Ale `recordId` se používá pro korelaci, takže libovolný záznamu v odpovědi obsahující `recordId` nebyla součástí původní požadavek do webového rozhraní API, která se zahodí.

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

## <a name="error-cases"></a>Případy chyb
Kromě webového rozhraní API za následek nedostupné nebo odesílání navýšení kapacity – úspěšné stavové kódy následující jsou považovány za chybné případy:

* Pokud webové rozhraní API vrátí stavový kód úspěchu, ale z odpovědi vyplývá, že není `application/json` odpovědi se považuje za neplatný a bude provedena žádná obohacení.
* Pokud existují **neplatný** (s `recordId` není v původní požadavek, nebo s duplicitními hodnotami) záznamy v odpovědi `values` pole, žádná rozšíření se provede pro **těchto** záznamy.

Pro případy, když webového rozhraní API není k dispozici nebo vrátí chybu HTTP popisná chybová se všechny dostupné podrobnosti o chybě HTTP se přidají do historie spuštění indexeru.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Přidání vlastních dovedností kognitivního vyhledávání](cognitive-search-custom-skill-interface.md)
+ [Vytvoření vlastních dovedností pomocí rozhraní API převede Text](cognitive-search-create-custom-skill-example.md)