---
title: Vytvoření dovednosti v kanálu vyhledávání vnímání – Azure Search
description: Definování extrakce dat, zpracování přirozeného jazyka nebo kroků analýzy obrázků za účelem obohacení a extrakce strukturovaných informací z vašich dat pro použití v Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 457157b93e6fb6be8ed734ae6f58c3b8717fc83d
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183474"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Postup vytvoření dovednosti v kanálu pro rozšíření

Rozpoznávání rozpoznávání rozbalí data a vylepšuje je, aby je bylo možné prohledávat Azure Search. Vyvoláme kroky pro extrakcia obohacení, které jsou zkombinovány do *dovednosti* , na který se odkazuje při indexování. Dovednosti může používat [integrované dovednosti](cognitive-search-predefined-skills.md) nebo vlastní dovednosti (viz [příklad: Vytvoření vlastní dovednosti pro hledání](cognitive-search-create-custom-skill-example.md) vnímání, kde najdete další informace.

V tomto článku se dozvíte, jak vytvořit kanál pro rozšíření pro dovednosti, které chcete použít. Dovednosti je připojen k indexeru Azure Search [](search-indexer-overview.md). Jedna součást návrhu kanálu, která je popsaná v tomto článku, sestavuje dovednosti sebe sama. 

> [!NOTE]
> Další součástí návrhu kanálu je zadání indexeru, který je popsaný v [dalším kroku](#next-step). Definice indexeru zahrnuje odkaz na dovednosti a mapování polí, která se používají pro připojení vstupů k výstupům v cílovém indexu.

Klíčové body, které si zapamatujete:

+ Můžete mít jenom jeden dovednosti na indexer.
+ Dovednosti musí mít alespoň jednu dovednost.
+ Můžete vytvořit více dovedností stejného typu (například varianty obrazu analýzy obrázků).

## <a name="begin-with-the-end-in-mind"></a>Zahajte na mysli

Doporučený počáteční krok je rozhodování o tom, která data se mají extrahovat z nezpracovaných dat a jak chcete tato data používat v řešení hledání. Vytvořením ilustrace celého kanálu pro rozšíření vám může pomáhat identifikovat nezbytné kroky.

Předpokládejme, že máte zájem o zpracování sady svých komentářů finančního analytika. Pro každý soubor budete chtít extrahovat názvy společností a obecné míněníy komentářů. Můžete také vytvořit vlastní nástroj pro obohacení, který používá službu Vyhledávání entit Bingu k vyhledání dalších informací o společnosti, například o tom, jaký druh firmy je ve společnosti zapojen. V podstatě budete chtít extrahovat informace, jako jsou následující, indexované pro každý dokument:

| record-text | Firma | mínění | popisy společnosti |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99. | ["Microsoft Corporation je americká Vícevrstvá technologie...", "LinkedIn je síť, která je zaměřená na obchodní a výdělečnou práci..."]

Následující diagram znázorňuje hypotetický kanál pro obohacení:

![Hypotetický kanál pro obohacení](media/cognitive-search-defining-skillset/sample-skillset.png "Hypotetický kanál pro obohacení")


Jakmile budete mít k dispozici lepší představu o tom, co chcete v kanálu, můžete vyjádřit dovednosti, které poskytuje tyto kroky. Funkce dovednosti se vyjadřuje při nahrávání definice indexeru do Azure Search. Další informace o tom, jak nahrát indexer, najdete v dokumentaci k [indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


V diagramu se krok vytrhlinování *dokumentu* provede automaticky. V podstatě Azure Search ví, jak otevřít dobře známé soubory a vytvoří pole *obsahu* obsahující text extrahovaný z každého dokumentu. Prázdné čtverečky jsou integrované nástroje pro obohacení a pole s tečkami Vyhledávání entit Bingu představuje vlastní obohacení, které vytváříte. Jak je znázorněno, dovednosti obsahuje tři dovednosti.

## <a name="skillset-definition-in-rest"></a>Definice dovednosti v REST

Dovednosti je definován jako pole dovedností. Každá dovednost definuje zdroj svých vstupů a název vytvořených výstupů. Pomocí [REST API Create dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset)můžete definovat dovednosti, který odpovídá předchozímu diagramu: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

Při vytváření dovednosti můžete zadat popis, který provede samoobslužný dokument dovednosti. Popis je nepovinný, ale užitečný pro sledování toho, co dovednosti. Vzhledem k tomu, že dovednosti je dokument JSON, který nepovoluje komentáře, musíte pro `description` tento prvek použít element.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Další část dovednosti je pole dovedností. Každou dovednost si můžete představit jako primitivu obohacení. Každá dovednost provádí v tomto kanálu rozšíření malý úkol. Každé z nich přebírá vstup (nebo sadu vstupů) a vrací některé výstupy. V následujících částech se zaměříte na to, jak určit předdefinované a vlastní dovednosti, zřetězení dovedností společně prostřednictvím vstupních a výstupních odkazů. Vstupy můžou pocházet ze zdrojových dat nebo z jiné dovednosti. Výstupy lze namapovat na pole v indexu vyhledávání nebo použít jako vstup pro dovednost s podřízenou možností.

## <a name="add-built-in-skills"></a>Přidání integrovaných dovedností

Pojďme se podívat na první dovednost, což je integrovaná [dovednost pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Každá integrovaná dovednost má `odata.type`, `input`a `output` vlastnosti. Vlastnosti specifické pro dovednost poskytují další informace, které se vztahují na tuto dovednost. Pro rozpoznávání `categories` entit je jedna entita mezi pevnou sadou typů entit, kterou může předvlakový model rozpoznat.

* Každá dovednost by měla mít ```"context"```. Kontext představuje úroveň, na které operace probíhají. V dovednostech výše je kontext celý dokument, což znamená, že dovednost rozpoznávání entit se volá jednou pro každý dokument. Výstupy jsou také vytvářeny na této úrovni. Přesněji řečeno, ```"organizations"``` jsou generovány jako ```"/document"```člen. V případě dovedností pro příjem dat můžete na tyto nově vytvořené informace odkazovat ```"/document/organizations"```jako na.  ```"context"``` Pokud pole není explicitně nastaveno, je výchozím kontextem dokument.

* Dovednost má jeden vstup s názvem "text", ve kterém je zdrojová vstupní sada ```"/document/content"```nastavená na. Dovednost (rozpoznávání entit) pracuje na poli *obsah* každého dokumentu, což je standardní pole vytvořené indexerem objektů BLOB v Azure. 

* Dovednost má jeden výstup s názvem ```"organizations"```. Výstupy existují pouze během zpracování. Chcete-li tento výstup zřetězit na vstup pro příjem dat, odkazujte na ```"/document/organizations"```výstup.

* V případě konkrétního dokumentu ```"/document/organizations"``` je hodnota pole organizací extrahovaných z textu. Příklad:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Některé situace volají pro odkazování na každý prvek pole samostatně. Předpokládejme například, že chcete každý prvek ```"/document/organizations"``` předat samostatně do jiné dovednosti (například vlastní rozšíření pro vyhledávání entit Bingu). Na každý prvek pole můžete odkazovat přidáním hvězdičky do cesty:```"/document/organizations/*"``` 

Druhá dovednost pro extrakci mínění se řídí stejným vzorem jako první obohacení. Jako vstup ```"/document/content"``` bere a vrátí mínění skóre pro každou instanci obsahu. Vzhledem k tomu, že jste ```"context"``` pole nezadali explicitně, výstup (mySentiment) je teď ```"/document"```podřízeným objektem.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Přidat vlastní dovednost

Odvolání struktury vlastního rozšíření pro vyhledávání entit Bingu:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Tato definice je [vlastní dovednost](cognitive-search-custom-skill-web-api.md) , která jako součást procesu rozšíření volá webové rozhraní API. Pro každou organizaci identifikovanou nástrojem pro rozpoznávání entit volá tato dovednost webové rozhraní API, kde najdete popis této organizace. Orchestrace, kdy volat webové rozhraní API a postup toku přijatých informací, je zpracována interně modulem pro obohacení. Nicméně inicializace nutná pro volání tohoto vlastního rozhraní API musí být uvedena ve formátu JSON (například identifikátor URI, httpHeaders a očekávané vstupy). Pokyny k vytvoření vlastního webového rozhraní API pro kanál pro rozšíření najdete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md).

Všimněte si, že pole "Context" je nastaveno ```"/document/organizations/*"``` na hvězdičku, což znamená, že je krok rozšíření volán *pro každou* organizaci ```"/document/organizations"```v rámci. 

Výstup, v tomto případě se pro každou identifikovanou organizaci vygeneruje popis společnosti. Při odkazování na popis v rámci navazujícího kroku (například při extrakci klíčových frází) byste použili cestu ```"/document/organizations/*/description"``` k tomu. 

## <a name="add-structure"></a>Přidat strukturu

Dovednosti generuje strukturované informace mimo nestrukturovaná data. Vezměte v úvahu v následujícím příkladu:

*"Ve čtvrtém čtvrtletí se společnost Microsoft zaznamenala $1 100 000 000 ve tržbách z LinkedInu, což je společnost sociální sítě, kterou koupili minulý rok. Akvizice umožňuje Microsoftu kombinovat možnosti LinkedInu s funkcemi CRM a Office. V tomto případě se drží v tomto stavu. "*

Pravděpodobným výsledkem by byla vygenerovaná struktura podobná následujícímu obrázku:

![Ukázka výstupní struktury](media/cognitive-search-defining-skillset/enriched-doc.png "Ukázka výstupní struktury")

Až do této struktury byla tato struktura jenom interní, jenom paměť a používá se jenom v Azure Search indexech. Přidání znalostní báze vám dává možnost ukládat obohacení na tvar pro použití mimo hledání.

## <a name="add-a-knowledge-store"></a>Přidat znalostní bázi Knowledge Store

[Znalostní báze](knowledge-store-concept-intro.md) je funkce ve verzi preview v Azure Search pro uložení obohaceného dokumentu. Znalostní báze, kterou vytvoříte, je zajištěné účtem služby Azure Storage, kde je úložiště, ve kterém jsou rozšířená data uložena. 

Do dovednosti se přidá definice znalostní databáze. Návod k celému procesu najdete v tématu [jak začít používat znalostní bázi Knowledge Store](knowledge-store-howto.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Můžete zvolit ukládání obohacených dokumentů jako tabulek s hierarchickými vztahy, které jsou zachovány nebo jako dokumenty JSON ve službě BLOB Storage. Výstup ze všech dovedností v dovednosti se dá nacházet jako vstup pro projekci. Pokud hledáte data do konkrétního tvaru, aktualizovaná [Shaper dovednost](cognitive-search-skill-shaper.md) teď může modelovat komplexní typy, které můžete použít. 

<a name="next-step"></a>

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni s kanálem pro obohacení a dovednosti, pokračujte s odkazem na [poznámky v dovednosti](cognitive-search-concept-annotations-syntax.md) nebo [jak mapovat výstupy na pole v indexu](cognitive-search-output-field-mapping.md). 
