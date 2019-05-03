---
title: Práce s projekce v úložišti znalostní báze – Azure Search
description: Uložit a obrazce bohatších možností data z kanál indexování AI pro použití ve scénářích než vyhledávání
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: 3ab5ffafd1b20eb0e3e453d3e730840baf9233e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028362"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Práce s projekce ve znalostní bázi úložiště ve službě Azure Search

Služba Azure Search umožňuje rozšíření obsahu prostřednictvím kognitivních dovedností AI a indexování v rámci vlastních dovedností. Obohacení strukturovat vaše dokumenty a zefektivněte hledání. V mnoha případech jsou užitečné pro scénáře než vyhledávání, například dolování znalostní báze bohatších možností dokumenty.

Projekce, součást [znalostní báze store (preview)](knowledge-store-concept-intro.md), jsou zobrazení bohatších možností dokumenty, které lze uložit do fyzického úložiště pro účely dolování znalostní báze. Projekce umožňuje "projekt" data do tvaru, který odpovídá vašim potřebám, zachování relace tak, aby nástrojů, jako je Power BI může číst data s žádné další úsilí. 

Projekce může být tabulkový, s daty uloženými v řádků a sloupců ve službě Azure Table storage nebo objekty JSON uložené v úložišti objektů Blob v Azure. Více projekce vašich dat můžete definovat, jak se rozšiřují. To je užitečné, pokud chcete ve tvaru odlišně pro individuální použití případů stejná data. 

Znalostní báze úložiště podporuje dva typy projekce:

+ **Tabulky**: Pro data, která nejlépe představuje jako řádky a sloupce tabulky projekce umožňují definovat schematizovanými obrazec nebo projekce v úložišti tabulek. 

+ **Objekty**: Když budete potřebovat JSON s reprezentací provedených vaše data a obohacení, objekt projekce se ukládají jako objekty BLOB.

Zobrazit projekce, které jsou definované v kontextu, krokovat [jak začít pracovat s úložištěm znalostní báze](knowledge-store-howto.md)

## <a name="projection-groups"></a>Projekce skupiny

V některých případech je potřeba vaše bohatších možností data v různých tvarů splnit různé cíle projektu. Znalostní báze úložiště můžete zadat více skupin projekcí. Projekce skupiny mají následující vlastnosti klíče vzájemné exkluzivita a příbuznost.

### <a name="mutually-exclusivity"></a>Vzájemně exkluzivitu, kterou

Veškerý obsah promítnout do jedné skupiny je nezávislá data promítnout do jiných skupin projekce. Z toho vyplývá, že můžete mít stejná data ve tvaru jinak, ale opakovat v každé skupině projekce. 

Jedno omezení vynucená v projekci skupiny je vzájemné exkluzivitu, kterou projekce typů se skupinou projekce. Lze definovat pouze projekce tabulky nebo objektu projekce v rámci jedné skupiny. Pokud chcete tabulek a objektů, definujte jednu skupinu projekce pro tabulky a druhý projekce skupiny objektů.

### <a name="relatedness"></a>Příbuznosti

Veškerý obsah plánovaný v rámci jednoho projekce skupiny zachová relace v datech. Jsou relace založené na vygenerovaný klíč a každý podřízený uzel uchovává odkaz na nadřazený uzel. Relace není span projekce skupin a tabulky nebo objekty vytvořené v jedné skupině projekce nemají žádný vztah ke data generovaná v jiných skupinách projekce.

## <a name="input-shaping"></a>Vstup tvarování
Získávají se vaše data do správného tvaru nebo struktury je použití klíčů k efektivní, už to jsou tabulky nebo objekty. Schopnost tvarovat nebo strukturovat data podle jak plánujete přistupovat a používat ho je klíčová funkce, jako **Shaper** dovedností v rámci zkušenostech.  

Projekce jsou lépe definovat až budete mít objekt ve stromové struktuře rozšíření, která odpovídá schématu projekce. Aktualizovaný [Shaper dovednosti](cognitive-search-skill-shaper.md) vám umožní vytvořit objekt z různých uzlů stromu rozšiřování a nadřazené nového uzlu. **Shaper** dovednosti můžete zadat komplexní typy s vnořené objekty.

Až budete mít nový tvar definované, který obsahuje všechny prvky, které potřebujete k projektu navýšení kapacity, teď můžete tento obrazec jako zdroj pro vaše projekce nebo jako vstup pro jiné dovednosti.

## <a name="table-projections"></a>Tabulka projekce

Vzhledem k tomu je snazší import, doporučujeme projekce tabulky pro zkoumání dat pomocí Power BI. Kromě toho projekce tabulky umožňují změna Kardinalita mezi tabulkami. 

Jednotlivý dokument v indexu můžete promítnout do několika tabulek, zachování vztahy. Při projekci k několika tabulkám, dokončení tvar promítat do každé tabulky, pokud podřízený uzel je zdrojem jiné tabulky ve stejné skupině.

### <a name="defining-a-table-projection"></a>Definující projekci tabulky

Při definování tabulky projekce v rámci `knowledgeStore` element z vaše dovednosti, začněte tím, že uzel ve stromu obohacování mapování na zdroje tabulky. Obvykle je tento uzel výstup **Shaper** dovednosti, které jste přidali do seznamu dovednosti k vytvoření konkrétní tvar, který je třeba projekt do tabulek. Můžete dělená podle uzlu rozhodnete projekt na projekt do několika tabulek. Definice tabulek je seznam tabulek, které chcete do projektu. 

Každá tabulka vyžaduje tři vlastnosti:

+ Název tabulky: Název tabulky ve službě Azure Storage.

+ generatedKeyName: Název sloupce pro klíč, který jednoznačně identifikuje tento řádek.

+ Zdroj: Uzel ve stromu rozšíření jsou sourcing vaše obohacení z. To je obvykle výstup shaper, ale může být výstup dovednosti.

Tady je příklad tabulky projekcí.

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
Jak je ukázáno v tomto příkladu, klíčové fráze a entity jsou modelovány do různých tabulek a bude obsahovat odkaz na nadřazený (%{maintable/) pro každý řádek. 

Na následujícím obrázku je odkaz na judikatura cvičení v [jak začít pracovat s úložištěm znalostní báze](knowledge-store-howto.md). Ve scénáři případ má více názory, kde každý názor je obohaceného o určení entit, které jsou v něm obsažena může model projekce, jak je znázorněno zde.

![Entit a vztahů v tabulkách](media/knowledge-store-projection-overview/TableRelationships.png "modelovat vztahy v tabulce projekce")

## <a name="object-projections"></a>Objekt projekce

Projekce objektu jsou reprezentaci JSON, který může pocházet z libovolného uzlu stromu rozšíření. V mnoha případech stejný **Shaper** dovednosti, která vytvoří tabulku projekce lze generovat projekce objektu. 

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

Generování objektu projekci vyžaduje několik atributů specifický pro objekt:

+ storageContainer: Kontejner, ve kterém se uloží objekty
+ Zdroj: Cesta k uzlu stromu rozšíření, která je kořenem projekce
+ Klíč: Cesta, která představuje jedinečný klíč pro objekt, který má být uložena. Se použije k vytvoření názvu objektu blob v kontejneru.

## <a name="projection-lifecycle"></a>Životní cyklus projekce

Vaše projekce mají životní cyklus, který je vázán na zdroj dat ve zdroji dat. Jako vaše data se aktualizují a znovu indexovat, výsledky obohacení zajistit, že vaše projekce jsou konzistentní s daty ve zdroji dat aktualizují vaše projekce. Projekce dědit odstranit zásadu, kterou jste nakonfigurovali pro indexu. 

## <a name="using-projections"></a>Použití projekce

Po spuštění indexeru byste si přečíst očekávaná data v kontejnerech nebo tabulek, které jste zadali prostřednictvím projekce. 

Pro účely analýzy je stejně jednoduché jako nastavení služby Azure Table storage jako zdroj dat pro zkoumání v Power BI. Na datech využití relace v rámci lze velmi snadno vytvořit sadu vizualizací.

Případně, pokud je potřeba použít bohatších možností data v kanálu vědeckého zpracování dat, můžete [načíst data z objektů BLOB do Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Nakonec pokud je potřeba exportovat data z úložiště znalostní báze, Azure Data Factory obsahuje konektory pro export dat a přímo do databáze podle vašeho výběru. 

## <a name="next-steps"></a>Další postup

V dalším kroku vytvoření vaší první úložiště znalostní báze pomocí ukázkových dat a pokyny.

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze úložiště](knowledge-store-howto.md).