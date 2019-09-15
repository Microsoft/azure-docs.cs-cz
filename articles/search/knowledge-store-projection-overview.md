---
title: Práce s projekcemi ve znalostní bázi Knowledge Store (Preview) – Azure Search
description: Uložení obohacených dat z kanálu indexování AI pro použití ve scénářích jiných než hledání
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.subservice: cognitive-search
ms.openlocfilehash: 85376bddbfbf8249438c9027eaf4dc63b83fe2fe
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004007"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Práce s projekcemi v úložišti znalostí v Azure Search

> [!Note]
> Znalostní databáze je ve verzi Preview a není určená pro produkční použití. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Azure Search umožňuje rozšiřování obsahu prostřednictvím dovedností rozpoznávání AI a vlastní dovednosti jako součást indexování. Obohacení do dokumentů přidávají strukturu a vyhledává se efektivněji. V mnoha instancích jsou obohacené dokumenty užitečné pro jiné scénáře, než je hledání, například pro dolování znalostí.

Projekce, součást služby [Knowledge Store](knowledge-store-concept-intro.md), představují zobrazení obohacených dokumentů, které je možné uložit do fyzického úložiště pro účely dolování v rámci vědomostí. Projekce umožňuje "projekt" vašich dat do tvaru, který se podle vašich potřeb sjednotí, zachovávání vztahů, aby nástroje, jako Power BI, mohly data číst bez dalšího úsilí. 

Projekce můžou být tabulkové, s daty uloženými v řádcích a sloupcích ve službě Azure Table Storage nebo objekty JSON uložené ve službě Azure Blob Storage. Můžete definovat více výčnělků vašich dat při jejich obohacení. To je užitečné, pokud chcete, aby stejný tvar dat byl pro jednotlivé případy použití odlišný. 

Znalostní databáze podporuje dva typy projekce:

+ **Tabulky**: Pro data, která jsou nejlépe reprezentovaná jako řádky a sloupce, vám umožňují definovat schematized tvar nebo projekci v úložišti tabulek. 

+ **Objekty**: Když budete potřebovat reprezentaci dat a rozšíření JSON, jsou objekty projekce uloženy jako objekty blob.

Pokud chcete zobrazit projekce definované v kontextu, Projděte si téma [jak začít s úložištěm Knowledge Store](knowledge-store-howto.md).

## <a name="projection-groups"></a>Skupiny projekce

V některých případech budete muset promítnout obohacená data v různých tvarech, aby splňovala různé cíle. Znalostní báze umožňuje definovat více skupin projekce. Skupiny projekce mají následující klíčové charakteristiky vzájemného výhradního práva a příbuznosti.

### <a name="mutually-exclusivity"></a>Vzájemná výlučná práva

Veškerý obsah, který je promítnut do jedné skupiny, je nezávislý na datech, které jsou promítnuty do jiných skupin projekce. To znamená, že můžete mít stejný stejný tvar dat, který se v každé skupině projekce opakuje jinak. 

Jedno omezení, které je vynutilo ve skupinách projekce, je vzájemné vlastnictví typů projekce se skupinou projekce. V rámci jedné skupiny můžete definovat pouze buď projekce tabulek, nebo projekce objektů. Pokud chcete, aby byly tabulky a objekty, definovat jednu skupinu projekce pro tabulky a druhou skupinu projekce pro objekty.

### <a name="relatedness"></a>Příbuznost

Veškerý obsah, který je promítnut v rámci jedné skupiny projekce, zachovává vztahy v rámci těchto dat. Relace jsou založené na vygenerovaném klíči a každý podřízený uzel zachovává odkaz na nadřazený uzel. Relace nezahrnují skupiny projekce a tabulky nebo objekty vytvořené v jedné skupině projekce nemají žádný vztah k datům vygenerovaným v jiných skupinách projekce.

## <a name="input-shaping"></a>Vstupní tvarování
Načtení dat do pravého tvaru nebo struktury je klíčem k efektivnímu použití, jedná se o tabulky nebo objekty. Schopnost tvarovat nebo strukturovat data na základě toho, jak plánujete přístup a používat, je klíčovou funkcí, která je vystavena jako **Shaper** dovednost v rámci dovednosti.  

Pokud máte objekt ve stromu obohacení, který odpovídá schématu projekce, je snazší definovat projekce. Aktualizovaná [Shaper dovednost](cognitive-search-skill-shaper.md) umožňuje vytvořit objekt z různých uzlů stromu obohacení a jejich nadřazených objektů v rámci nového uzlu. **Shaper** dovednost umožňuje definovat komplexní typy s vnořenými objekty.

Když máte definovaný nový tvar, který obsahuje všechny prvky, které potřebujete k vyzkoušení projektu, můžete teď tento tvar použít jako zdroj pro vaše projekce nebo jako vstup pro jinou dovednost.

## <a name="table-projections"></a>Projekce tabulek

Vzhledem k tomu, že usnadňuje import, doporučujeme pro zkoumání dat pomocí Power BI použít projekce tabulek. Kromě toho mohou projekce tabulek umožňovat změnu mohutnosti mezi vztahem mezi tabulkami. 

Jeden dokument v indexu můžete promítnout do několika tabulek a zachovat vztahy. Když procházíte na více tabulek, celý tvar bude v každé tabulce projekt, pokud podřízený uzel není zdrojem jiné tabulky ve stejné skupině.

### <a name="defining-a-table-projection"></a>Definování projekce tabulky

Při definování projekce tabulky v rámci `knowledgeStore` prvku dovednosti začněte mapováním uzlu ve stromu rozšíření na zdroj tabulky. Obvykle je tento uzel výstupem **Shaper** dovednosti, kterou jste přidali do seznamu dovedností k vytvoření konkrétního tvaru, který potřebujete k Projectu v tabulkách. Uzel, který se rozhodnete pro projekt, lze rozdělit na více tabulek. Definice tabulek je seznam tabulek, které chcete projektovat. 

#### <a name="projection-slicing"></a>Průřez projekce
Při definování skupiny projekce tabulky lze jeden uzel ve stromu rozšíření rozřezat na více souvisejících tabulek. Přidání tabulky se zdrojovou cestou, která je podřízenou položkou existující projekce tabulky, způsobí, že bude podřízený uzel rozložen mimo nadřazený uzel a bude promítnut do nové tabulky, která je ještě v relaci. Díky tomu můžete v Shaper dovednosti definovat jeden uzel, který může být zdrojem pro všechny vaše projekce tabulek.

Každá tabulka vyžaduje tři vlastnosti:

+ Tabulky Název tabulky v Azure Storage.

+ generatedKeyName: Název sloupce pro klíč, který jednoznačně identifikuje tento řádek

+ Zdrojová Uzel ze stromu obohacení, ze kterého provádíte rozšíření, z. Většinou se jedná o výstup Shaper, ale může to být výstup kterékoli dovednosti.

Tady je příklad projekce tabulek.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
Jak je znázorněno v tomto příkladu, klíčové fráze a entity jsou modelovány do různých tabulek a budou obsahovat odkaz zpátky na nadřazenou (hlavní tabulku) pro každý řádek. 

Na následujícím obrázku je odkaz na Caselaw cvičení, jak začít [s úložištěm Knowledge Store](knowledge-store-howto.md). V případě, kdy má případ více stanovisek a každé stanovisko je obohaceno určením entit, které jsou v něm obsaženy, můžete modelovat projekce, jak je znázorněno zde.

![Entity a vztahy v tabulkách](media/knowledge-store-projection-overview/TableRelationships.png "Vztahy modelování v projekcich tabulek")

## <a name="object-projections"></a>Projekce objektů

Projekce objektů jsou reprezentace JSON stromu obohacení, kterou lze naformátovat z libovolného uzlu. V mnoha případech může být k vygenerování projekce objektu použita stejná **Shaper** dovednost, která vytvoří projekci tabulky. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Generování projekce objektu vyžaduje několik atributů specifických pro objekt:

+ storageContainer: Kontejner, do kterého budou objekty uloženy
+ Zdrojová Cesta k uzlu stromu obohacení, který je kořenem projekce
+ Zkrat Cesta, která představuje jedinečný klíč pro objekt, který má být uložen. Použije se k vytvoření názvu objektu BLOB v kontejneru.

## <a name="projection-lifecycle"></a>Životní cyklus projekce

Vaše projekce mají životní cyklus, který je svázán se zdrojovými daty ve zdroji dat. Po aktualizaci a opětovném indexování vašich dat se vaše projekce aktualizují o výsledky rozšíření, která zajišťují, že vaše projekce jsou nakonec konzistentní s daty ve zdroji dat. Výčnělky dědí zásadu odstranění, kterou jste nakonfigurovali pro váš index. 

## <a name="using-projections"></a>Použití projekce

Po spuštění indexeru můžete číst provedená data v kontejnerech nebo tabulkách, které jste určili pomocí projekce. 

V případě analýz je průzkum v Power BI jednoduchý jako nastavení úložiště tabulek Azure jako zdroje dat. Můžete velmi snadno vytvořit sadu vizualizací na vašich datech využívajících vztahy v rámci.

Případně, pokud potřebujete použít obohacená data v kanálu pro datové vědy, můžete [načíst data z objektů blob do PANDAS dataframe](../machine-learning/team-data-science-process/explore-data-blob.md).

Nakonec, pokud potřebujete exportovat data z znalostní báze Knowledge Store, Azure Data Factory obsahuje konektory pro export dat a pozemky v databázi podle vašeho výběru. 

## <a name="next-steps"></a>Další postup

Jako další krok si vytvořte svoje první úložiště znalostní báze s použitím ukázkových dat a pokynů.

> [!div class="nextstepaction"]
> [Postup vytvoření znalostní databáze](knowledge-store-howto.md).
