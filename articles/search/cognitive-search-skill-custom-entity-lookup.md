---
title: Dovednost kognitivního vyhledávání vlastního vyhledávání entit
titleSuffix: Azure Cognitive Search
description: Extrahujte různé vlastní entity z textu v kanálu kognitivního vyhledávání Azure Cognitive Search. Tato dovednost je v současné době ve verzi Public Preview.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369773"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Kognitivní dovednosti vyhledávání vlastních entit (náhled)

> [!IMPORTANT] 
> Tato dovednost je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.

Dovednost **Vlastní vyhledávání entit** vyhledá text z vlastního, uživatelem definovaného seznamu slov a frází. Pomocí tohoto seznamu označí všechny dokumenty odpovídajícími entitami. Dovednost také podporuje stupeň fuzzy odpovídající, které lze použít k nalezení shody, které jsou podobné, ale ne zcela přesné.  

Tato dovednost není vázána na rozhraní API služeb Cognitive Services a lze ji během období náhledu používat zdarma. Stále byste měli [připojit prostředek služeb Cognitive Services](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services), abyste však přepsali denní limit obohacení. Denní limit platí pro bezplatný přístup ke službám Cognitive Services při přístupu prostřednictvím Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Omezení dat
+ Maximální podporovaná velikost vstupního záznamu je 256 MB. Pokud potřebujete rozdělit data před odesláním do vlastní entity vyhledávání dovednost, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).
+ Maximální podporovaná tabulka definic entit je 10 MB, pokud je k dispozici pomocí parametru *entitiesDefitionUri.* 
+ Pokud entity jsou definovány v řádku, pomocí *inlineEntitiesDefinition* parametr, maximální podporovaná velikost je 10 KB.

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| entityDefiniceUri    | Cesta k souboru JSON nebo CSV obsahující veškerý cílový text, proti který se má shodovat. Tato definice entity se čte na začátku spuštění indexeru; všechny aktualizace tohoto souboru v polovině spuštění nebudou realizovány až do následných spuštění. Tato konfigurace musí být přístupná přes protokol HTTPS. Viz [Vlastní formát definice entity"](#custom-entity-definition-format) níže pro očekávané schéma CSV nebo JSON.|
|inlineEntitiesDefinice | Vřadit definice entit JSON. Tento parametr nahrazuje entitiesDefinitionUri parametr, pokud je k dispozici. Vestavěná konfigurace nesmí být k dispozici více než 10 KB. Viz [Vlastní definice entity](#custom-entity-definition-format) níže očekávané schéma JSON. |
|defaultLanguageCode |    (Nepovinné) Kód jazyka vstupního textu použitého k tokenizaci a vymezení vstupního textu. Podporovány jsou následující `da, de, en, es, fi, fr, it, ko, pt`jazyky: . Výchozí nastavení je`en`angličtina ( ). Pokud předáte formát languagecode-countrycode, použije se pouze část formátu languagecode.  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název      | Popis                   |
|---------------|-------------------------------|
| text          | Text k analýze.          |
| languageCode    | Nepovinný parametr. Výchozí je `"en"`.  |


## <a name="skill-outputs"></a>Výstupy dovedností


| Název výstupu      | Popis                   |
|---------------|-------------------------------|
| Entity | Pole objektů, které obsahují informace o shody, které byly nalezeny a související metadata. Každý z identifikovaných subjektů může obsahovat následující pole:  <ul> <li> *název*: Identifikovaný subjekt nejvyšší úrovně. Entita představuje formulář "normalizované". </li> <li> *id*: Jedinečný identifikátor entity, jak je definován uživatelem v "Vlastní formát definice entity".</li> <li> *popis*: Popis entity definovaný uživatelem v "Vlastní formát definice entity". </li> <li> *typ:* Typ entity, jak je definován uživatelem v "Vlastní formát definice entity".</li> <li> *podtyp:* Podtyp entity definovaný uživatelem v "Vlastní formát definice entity".</li>  <li> *Match*: Kolekce, která popisuje každou shodu pro tuto entitu ve zdrojovém textu. Každý zápas bude mít následující členy: </li> <ul> <li> *text*: Nezpracovaný text se shoduje ze zdrojového dokumentu. </li> <li> *Posun*: Umístění, kde byla nalezena shoda v textu. </li> <li> *délka*: Délka odpovídajícího textu. </li> <li> *matchDistance*: Počet znaků odlišných od této shody byl od původního názvu entity nebo aliasu.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formát definice vlastní entity

Existují 3 různé způsoby, jak poskytnout seznam vlastních entit do dovednosti vlastní vyhledávání entit. Seznam můžete zadat v . CSV soubor, a . JSON nebo jako vrozenou definici jako součást definice dovednosti.  

Pokud je definiční soubor . CSV nebo . JSON, cesta souboru musí být poskytnuta jako součást *entitiesDefitionUri* parametr. V tomto případě je soubor stažen jednou na začátku každého spuštění indexeru. Soubor musí být přístupné tak dlouho, dokud indexer je určen ke spuštění. Soubor musí být také zakódován UTF-8.

Pokud je definice uvedena vřádku, měla by být poskytnuta jako vřadná jako obsah parametru dovednosti *inlineEntitiesDefinition.* 

### <a name="csv-format"></a>Formát CSV

Můžete zadat definici vlastníentity hledat v souboru hodnota oddělená čárkami (CSV) poskytnutím cesty k souboru a nastavením v *entitiesDefitionUri* skill parametr. Cesta by měla být v umístění https. Definiční soubor může mít velikost až 10 MB.

Formát CSV je jednoduchý. Každý řádek představuje jedinečnou entitu, jak je znázorněno níže:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

V tomto případě existují tři entity, které mohou být vráceny jako entity nalezeny (Bill Gates, Satya Nadella, Microsoft), ale budou identifikovány, pokud některý z podmínek na řádku (aliasy) jsou spárovány na text. Například pokud je v dokumentu nalezen řetězec "William H. Gates", bude vrácena shoda entity "Bill Gates".

### <a name="json-format"></a>Formát JSON

Můžete také zadat definici vlastních entit, které chcete vyhledat v souboru JSON. Formát JSON poskytuje trochu větší flexibilitu, protože umožňuje definovat odpovídající pravidla pro období. Můžete například určit vzdálenost přibližné shody (vzdálenost Damerau-Levenshtein) pro každý termín nebo zda má být shoda rozlišována malá a velká písmena nebo ne. 

 Stejně jako u souborů CSV, musíte poskytnout cestu k souboru JSON a nastavit jej v *entitiesDefitionUri* skill parametr. Cesta by měla být v umístění https. Definiční soubor může mít velikost až 10 MB.

Nejzákladnější definice seznamu vlastních entit JSON může být seznam entit, které se mají shodovat:

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

Složitější příklad definice JSON může volitelně poskytnout id, popis, typ a podtyp každé entity - stejně jako další *aliasy*. Pokud je spárován termín aliasu, bude vrácena také entita:

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

Níže uvedené tabulky podrobněji popisují různé konfigurační parametry, které můžete nastavit při definování entit tak, aby odpovídaly:

|  Název pole  |        Popis  |
|--------------|----------------------|
| jméno | Popisovač entity nejvyšší úrovně. Shody ve výstupu dovednosti budou seskupeny podle tohoto názvu a měly by představovat "normalizovanou" formu nalezeného textu.  |
| description  | (Nepovinné) Toto pole lze použít jako průchod pro vlastní metadata o odpovídající text (y). Hodnota tohoto pole se zobrazí s každou shodou jeho entity ve výstupu dovednosti. |
| type | (Nepovinné) Toto pole lze použít jako průchod pro vlastní metadata o odpovídající text (y). Hodnota tohoto pole se zobrazí s každou shodou jeho entity ve výstupu dovednosti. |
| Podtypu | (Nepovinné) Toto pole lze použít jako průchod pro vlastní metadata o odpovídající text (y). Hodnota tohoto pole se zobrazí s každou shodou jeho entity ve výstupu dovednosti. |
| id | (Nepovinné) Toto pole lze použít jako průchod pro vlastní metadata o odpovídající text (y). Hodnota tohoto pole se zobrazí s každou shodou jeho entity ve výstupu dovednosti. |
| malá a velká písmena | (Nepovinné) Výchozí hodnota je nepravdivá. Logická hodnota označující, zda by porovnání s názvem entity mělo být citlivé na pouzdře znaků. Ukázka shody bez rozlišování malých a velkých písmen "Microsoft" může být: Microsoft, microSoft, MICROSOFT |
| fuzzyEditovatVzdálenost | (Nepovinné) Výchozí hodnota je 0. Maximální hodnota 5. Označuje přijatelný počet rozdílných znaků, které by stále představovaly shodu s názvem entity. Nejmenší možná fuzziness pro daný zápas je vrácena.  Například, pokud je vzdálenost úprav nastavena na 3, "Windows 10" by stále odpovídalo "Windows", "Windows10" a "Windows 7". <br/> Pokud je citlivost malých a velkých písmen nastavena na hodnotu false, rozdíly mezi jednotlivými písmeny se nezapočítávají do tolerance rozmazání, ale jinak ano. |
| výchozí proztuže malých a malých a | (Nepovinné) Změní výchozí hodnotu rozlišování malých a velkých písmen pro tuto entitu. Slouží ke změně výchozí hodnoty všech aliasů caseSensitive hodnoty. |
| výchozí FuzzyEditDistance | (Nepovinné) Změní výchozí hodnotu vzdálenosti fuzzy úprav pro tuto entitu. Lze změnit výchozí hodnotu všech aliasů fuzzyEditDistance hodnoty. |
| Aliasy | (Nepovinné) Pole složitých objektů, které lze použít k určení alternativního pravopisu nebo synonym pro název kořenové entity. |

| Vlastnosti aliasu | Popis |
|------------------|-------------|
| text  | Alternativní pravopis nebo reprezentace názvu některé cílové entity.  |
| malá a velká písmena | (Nepovinné) Chová stejně jako kořenová entita "caseSensitive" parametr výše, ale platí pouze pro tento jeden alias. |
| fuzzyEditovatVzdálenost | (Nepovinné) Chová stejně jako kořenová entita "fuzzyEditDistance" parametr výše, ale platí pouze pro tento jeden alias. |


### <a name="inline-format"></a>Vsazená formát

V některých případech může být vhodnější poskytnout seznam vlastních entit tak, aby odpovídaly vsazení přímo do definice dovednosti. V takovém případě můžete použít podobný formát JSON, který je popsán výše, ale je vložen do definice dovednosti.
Pouze konfigurace, které jsou menší než 10 KB ve velikosti (serializované velikosti) lze definovat vložená. 

##    <a name="sample-definition"></a>Definice vzorku

Ukázková definice dovedností pomocí vsazený formát je uveden níže:

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
        "type": "Harware",
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
Případně pokud se rozhodnete poskytnout ukazatel na soubor definice entit, ukázková definice dovedností pomocí entityDefinitionUri formát je uveden níže:

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

##    <a name="sample-input"></a>Vstup vzorku

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
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

## <a name="errors-and-warnings"></a>Chyby a varování

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Upozornění: Dosáhl maximální kapacity pro zápasy, přeskočení všech dalších duplicitních shod.

Toto upozornění bude vyzařováno, pokud je počet zjištěných shod větší než maximální povolená hodnota. V takovém případě přestaneme zařazovat duplicitní shody. Pokud je to pro vás nepřijatelné, podejte prosím [lístek podpory,](https://ms.portal.azure.com/#create/Microsoft.Support) abychom vám mohli pomoci s vaším individuálním případem použití.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Dovednost rozpoznávání entit (hledání známých entit)](cognitive-search-skill-entity-recognition.md)
