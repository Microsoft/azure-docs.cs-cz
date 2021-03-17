---
title: Dovednost vyhledávání vlastního vyhledávání entit
titleSuffix: Azure Cognitive Search
description: Extrakce různých vlastních entit z textu v kanálu vyhledávání v Azure Kognitivní hledání. Tato dovednost je aktuálně ve verzi Public Preview.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844931"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Dovednosti při rozpoznávání vlastního vyhledávání entit (Preview)

> [!IMPORTANT] 
> Tato dovednost je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Dovednost **vyhledávání vlastních entit** hledá text z vlastního uživatelsky definovaného seznamu slov a frází. Pomocí tohoto seznamu jsou všechny dokumenty označeny všemi vyhovujícími entitami. Dovednost také podporuje stupeň přibližné shody, které lze použít pro hledání shod, které jsou podobné, ale nejsou zcela přesné.  

Tato dovednost není vázaná na rozhraní Cognitive Services API a je možné ji v období Preview použít zdarma. K přepsání denního limitu pro obohacení byste ale měli stále [připojit Cognitive Services prostředek](./cognitive-search-attach-cognitive-services.md). Denní limit se vztahuje na bezplatný přístup k Cognitive Services při přístupu prostřednictvím Azure Kognitivní hledání.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Omezení dat
+ Maximální podporovaná velikost vstupního záznamu je 256 MB. Pokud potřebujete data před odesláním do vlastní dovednosti pro vyhledávání vlastních entit rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).
+ Podporovaná tabulka definic entit je 10 MB, pokud je k dispozici pomocí parametru *entitiesDefinitionUri* . 
+ Pokud jsou entity definované jako vložené, používá parametr *inlineEntitiesDefinition* maximální podporovanou velikost 10 KB.

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Cesta k souboru JSON nebo CSV obsahujícímu veškerý cílový text, ke kterému se má shoda. Tato definice entity je čtena na začátku spuštění indexeru; žádné aktualizace tohoto souboru v polovině spuštění nebudou až do následného spuštění. Tato konfigurace musí být přístupná přes protokol HTTPS. Očekávané schéma CSV nebo JSON najdete níže v části formát [definice vlastní entity](#custom-entity-definition-format) .|
|`inlineEntitiesDefinition` | Vložené definice entit JSON Tento parametr nahrazuje parametr entitiesDefinitionUri, pokud je k dispozici. Vloženému programu nelze poskytnout více než 10 KB konfigurace. Očekávané schéma JSON najdete v části [definice vlastní entity](#custom-entity-definition-format) níže. |
|`defaultLanguageCode` |    Volitelné Kód jazyka vstupního textu, který slouží k tokenizovat a vymezení vstupního textu. Podporují se tyto jazyky: `da, de, en, es, fi, fr, it, ko, pt` . Výchozí hodnota je English ( `en` ). Pokud předáte formát LanguageCode-CountryCode, použije se pouze část formátu LanguageCode.  |
|`globalDefaultCaseSensitive` | Volitelné Výchozí hodnota pro velká a malá písmena pro dovednost Pokud není `defaultCaseSensitive` zadaná hodnota entity, stane se tato hodnota `defaultCaseSensitive` hodnotou pro tuto entitu. |
|`globalDefaultAccentSensitive` | Volitelné Výchozí hodnota pro rozlišení pro dovednost Pokud není `defaultAccentSensitive` zadaná hodnota entity, stane se tato hodnota `defaultAccentSensitive` hodnotou pro tuto entitu. |
|`globalDefaultFuzzyEditDistance` | Volitelné Výchozí přibližná hodnota vzdálenosti úprav pro dovednost Pokud není `defaultFuzzyEditDistance` zadaná hodnota entity, stane se tato hodnota `defaultFuzzyEditDistance` hodnotou pro tuto entitu. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                   |
|---------------|-------------------------------|
| `text`          | Text, který se má analyzovat          |
| `languageCode`    | Nepovinný parametr. Výchozí je `"en"`.  |


## <a name="skill-outputs"></a>Výstupy dovedností


| Název výstupu      | Popis                   |
|---------------|-------------------------------|
| `entities` | Pole objektů, které obsahují informace o nalezených shodách a související metadata. Každá z identifikovaných entit může obsahovat následující pole:  <ul> <li> *Name*: identifikovaná entita nejvyšší úrovně. Entita představuje "normalizovaný" tvar. </li> <li> *ID*: jedinečný identifikátor entity definovaný uživatelem ve formátu definice vlastní entity.</li> <li> *Popis*: Popis entity definovaný uživatelem ve formátu definice vlastní entity. </li> <li> *zadejte:* Typ entity definovaný uživatelem ve formátu definice vlastní entity.</li> <li> *podtyp:* Podtyp entity definovaný uživatelem ve formátu definice vlastní entity</li>  <li> *shody*: kolekce, která popisuje všechny shody pro danou entitu ve zdrojovém textu. Každá shoda bude mít následující členy: </li> <ul> <li> *text*: nezpracovaný text se shoduje se zdrojovým dokumentem. </li> <li> *offset*: umístění, kde se shoda našla v textu. </li> <li> *Length*: délka spárovaného textu. </li> <li> *matchDistance*: počet znaků, které jsou v této shodě odlišné, byly z názvu původní entity nebo aliasu.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Vlastní formát definice entity

Existují tři různé způsoby, jak poskytnout seznam vlastních entit pro vlastní schopnost vyhledávání vlastních entit. Seznam můžete zadat v. Soubor CSV, a. Soubor JSON nebo jako vložená definice v rámci definice dovednosti.  

Je-li soubor definice. Sdílený svazek clusteru nebo. Soubor JSON: cesta k souboru musí být zadaná jako součást parametru *entitiesDefinitionUri* . V tomto případě je soubor stažen jednou na začátku každého spuštění indexeru. Soubor musí být přístupný, dokud indexer má být spuštěn. Soubor musí mít také kódování UTF-8.

Pokud je definice poskytnuta jako vložená, měla by být uvedena jako vložená jako obsah parametru *inlineEntitiesDefinition* dovednosti. 

### <a name="csv-format"></a>Formát CSV

Můžete zadat definici vlastních entit pro hledání v souboru Comma-Separated hodnoty (CSV) zadáním cesty k souboru a jeho nastavením v parametru *entitiesDefinitionUri*  dovednosti. Cesta by měla být v umístění https. Definiční soubor může mít velikost až 10 MB.

Formát CSV je jednoduchý. Každý řádek představuje jedinečnou entitu, jak je znázorněno níže:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

V tomto případě jsou k dispozici tři entity, které lze vrátit jako nalezené entity (brány, Satya Nadella, Microsoft), ale budou identifikovány, pokud se na text shodují některé z podmínek na řádku (aliasy). Pokud je například v dokumentu nalezen řetězec "William H. branes", bude vrácena shoda pro entitu "vyúčtování".

### <a name="json-format"></a>Formát JSON

Můžete zadat i definici vlastních entit pro hledání v souboru JSON. Formát JSON poskytuje trochu větší flexibilitu, protože umožňuje definovat pravidla pro porovnání za podmínky. Například můžete zadat přibližnou vzdálenost pro každý termín (Damerau-Levenshtein distance), nebo zda má porovnávání rozlišovat velká a malá písmena. 

 Stejně jako u souborů CSV musíte zadat cestu k souboru JSON a nastavit ji v parametru *entitiesDefinitionUri* dovednosti. Cesta by měla být v umístění https. Definiční soubor může mít velikost až 10 MB.

Základní definice seznamu vlastních entit JSON může být seznam entit, které se mají porovnat:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Složitější příklad definice JSON může volitelně poskytnout ID, popis, typ a podtyp každé entity – a také další *aliasy*. V případě, že se podmínka aliasu shoduje, entita se vrátí také:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Následující tabulky popisují další podrobnosti o různých parametrech konfigurace, které můžete nastavit při definování entit tak, aby odpovídaly:

|  Název pole  |        Popis  |
|--------------|----------------------|
| `name` | Popisovač entity nejvyšší úrovně. Shody ve výstupu dovedností se budou seskupovat podle tohoto názvu a měly by představovat "normalizovaný" tvar textu, který se nachází.  |
| `description`  | Volitelné Toto pole se dá použít jako průchozí pro vlastní metadata o odpovídajících textech. Hodnota tohoto pole se zobrazí s každou shodou své entity ve výstupu dovedností. |
| `type` | Volitelné Toto pole se dá použít jako průchozí pro vlastní metadata o odpovídajících textech. Hodnota tohoto pole se zobrazí s každou shodou své entity ve výstupu dovedností. |
| `subtype` | Volitelné Toto pole se dá použít jako průchozí pro vlastní metadata o odpovídajících textech. Hodnota tohoto pole se zobrazí s každou shodou své entity ve výstupu dovedností. |
| `id` | Volitelné Toto pole se dá použít jako průchozí pro vlastní metadata o odpovídajících textech. Hodnota tohoto pole se zobrazí s každou shodou své entity ve výstupu dovedností. |
| `caseSensitive` | Volitelné Výchozí hodnota je false. Logická hodnota, která označuje, zda má být porovnání s názvem entity citlivé na znaky velikosti písmen. Nerozlišování velkých a malých písmen od společnosti Microsoft může být: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Volitelné Výchozí hodnota je false. Logická hodnota, která označuje, zda jsou písmena s diakritikou a nezvýrazněnými písmeny, například "é" a "e", shodná. |
| `fuzzyEditDistance` | Volitelné Výchozí hodnota je 0. Maximální hodnota je 5. Označuje přijatelný počet odlišných znaků, které by přesto představovaly shodu s názvem entity. Je vrácena nejmenší možná tomu pro danou shodu.  Pokud je například vzdálenost úprav nastavená na hodnotu 3, "Windows 10" by pořád odpovídala "Windows", "Windows10" a "Windows 7". <br/> Pokud je rozlišování velkých a malých písmen nastaveno na hodnotu false, rozdíly v různých velikostech se nepočítají směrem k tomu toleranci, ale jinak. |
| `defaultCaseSensitive` | Volitelné Změní výchozí hodnotu rozlišování velikosti písmen pro tuto entitu. Dá se použít ke změně výchozí hodnoty všechny aliasy hodnoty caseSensitive. |
| `defaultAccentSensitive` | Volitelné Změní výchozí hodnotu citlivosti zvýraznění pro tuto entitu. Dá se použít ke změně výchozí hodnoty všechny aliasy accentSensitive hodnoty.|
| `defaultFuzzyEditDistance` | Volitelné Změní výchozí hodnotu přibližné úpravy hodnoty vzdálenosti pro tuto entitu. Dá se použít ke změně výchozí hodnoty všechny aliasy fuzzyEditDistance hodnoty. |
| `aliases` | Volitelné Pole komplexních objektů, které lze použít k určení alternativních pravopisů nebo synonym pro název kořenové entity. |

| Vlastnosti aliasu | Popis |
|------------------|-------------|
| `text`  | Alternativní pravopis nebo reprezentace některých názvů cílových entit  |
| `caseSensitive` | Volitelné Funguje stejně jako parametr kořenové entity "caseSensitive" výše, ale vztahuje se pouze na tento alias. |
| `accentSensitive` | Volitelné Funguje stejně jako parametr accentSensitive pro kořenovou entitu, ale vztahuje se jenom na tento alias. |
| `fuzzyEditDistance` | Volitelné Funguje stejně jako parametr fuzzyEditDistance pro kořenovou entitu, ale vztahuje se jenom na tento alias. |


### <a name="inline-format"></a>Vložený formát

V některých případech může být pohodlnější poskytnout seznam vlastních entit, které odpovídají vloženému přímo do definice dovedností. V takovém případě můžete použít podobný formát JSON na výše popsaný, ale je v definici dovedností vložen.
Vloženy mohou být pouze konfigurace, které mají velikost menší než 10 KB (serializovaná velikost). 

##    <a name="sample-definition"></a>Definice vzorku

Ukázková definice dovedností pomocí vloženého formátu je uvedená níže:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Případně, pokud se rozhodnete poskytnout ukazatel na definiční soubor entit, je uvedena ukázková definice dovedností pomocí `entitiesDefinitionUri` formátu:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Ukázkový výstup

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Chyby a upozornění

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Upozornění: dosáhlo se maximální kapacity pro shody, přeskočily se všechny další duplicitní shody.

Toto upozornění bude vygenerováno, pokud je zjištěn počet shod větší než povolené maximum. V takovém případě budeme přerušit zahrnutí duplicitních shod. Pokud to nemůžete přijmout, napište [lístek podpory](https://ms.portal.azure.com/#create/Microsoft.Support) , abychom vám mohli pomoct s vaším individuálním případem použití.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Dovednosti v rozpoznávání entit (Pokud chcete vyhledat dobře známé entity)](cognitive-search-skill-entity-recognition.md)
