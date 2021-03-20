---
title: Koncepce projekce
titleSuffix: Azure Cognitive Search
description: Uložte obohacená data z kanálu indexování pro rozšíření AI do úložiště znalostí pro použití ve scénářích, které nejsou fulltextovým vyhledáváním.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85565183"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Projekce ve znalostní bázi ve službě Azure Kognitivní hledání

Azure Kognitivní hledání v rámci indexování umožňuje rozšíření obsahu prostřednictvím integrovaných schopností rozpoznávání a vlastní dovednosti. Obohacení vytvoří nové informace tam, kde už dříve neexistovaly: extrakce informací z obrázků, detekce mínění, klíčových slov a entit z textu, na několik názvů. Rozšíření také přidává strukturu na nerozlišený text. Výsledkem všech těchto procesů jsou dokumenty, které usnadňují vyhledávání fulltextového vyhledávání. V mnoha instancích jsou obohacené dokumenty užitečné pro jiné scénáře, než je hledání, například pro dolování znalostí.

Projekce, součást služby [Knowledge Store](knowledge-store-concept-intro.md), představují zobrazení obohacených dokumentů, které je možné uložit do fyzického úložiště pro účely dolování v rámci vědomostí. Projekce umožňuje "projekt" vašich dat do tvaru, který se podle vašich potřeb sjednotí, zachovávání vztahů, aby nástroje, jako Power BI, mohly data číst bez dalšího úsilí.

Projekce můžou být tabulkové, s daty uloženými v řádcích a sloupcích ve službě Azure Table Storage nebo objekty JSON uložené ve službě Azure Blob Storage. Můžete definovat více výčnělků vašich dat při jejich obohacení. Více projekcí je užitečné, pokud chcete, aby stejný tvar dat byl pro jednotlivé případy použití odlišný.

Znalostní databáze podporuje tři typy projekce:

+ **Tabulky**: u dat, která jsou nejlépe reprezentovaná jako řádky a sloupce, vám umožňují definovat schematized tvar nebo projekci v úložišti tabulek. Jako tabulky lze promítnout pouze platné objekty JSON, obohacený dokument může obsahovat uzly, které nejsou pojmenované objekty JSON, a při projekci těchto objektů vytvořit platný objekt JSON s dovedností Shaper nebo s vloženým tvarem.

+ **Objekty**: Pokud potřebujete reprezentace JSON vašich dat a rozšíření, jsou projekce objektů uloženy jako objekty blob. Pouze platné objekty JSON mohou být probíhají jako objekty, obohacený dokument může obsahovat uzly, které nejsou pojmenované objekty JSON, a při projekci těchto objektů vytvořit platný objekt JSON s Shaper dovedností nebo včleněným tvarem.

+ **Soubory**: Pokud potřebujete uložit obrázky extrahované z dokumentů, projekce souborů vám umožní ukládat normalizované image do úložiště objektů BLOB.

Pokud chcete zobrazit projekce definované v kontextu, Projděte si téma [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Skupiny projekce

V některých případech budete muset promítnout obohacená data v různých tvarech, aby splňovala různé cíle. Znalostní báze umožňuje definovat více skupin projekce. Skupiny projekce mají následující klíčové charakteristiky vzájemného výhradního práva a příbuznosti.

### <a name="mutual-exclusivity"></a>Vzájemná výlučná práva

Veškerý obsah, který je promítnut do jedné skupiny, je nezávislý na datech, které jsou promítnuty do jiných skupin projekce.
Tato nezávislost znamená, že můžete mít stejný tvar dat, který se v každé skupině projekce opakuje jinak, ale opakuje se.

### <a name="relatedness"></a>Příbuznost

Skupiny projekce teď umožňují při zachovávání vztahů mezi typy projekce promítnout dokumenty mezi typy projekce. Veškerý obsah, který se promítá v rámci jedné skupiny projekce, zachovává vztahy v rámci dat napříč typy projekcí. V tabulkách jsou relace založeny na vygenerovaném klíči a každý podřízený uzel uchovává odkaz na nadřazený uzel. V různých typech (tabulky, objekty a soubory) se vztahy uchovávají, pokud je jeden uzel rozložený napříč různými typy. Představte si například scénář, ve kterém máte dokument obsahující obrázky a text. Text můžete promítnout do tabulek nebo objektů a obrázků do souborů, kde tabulky nebo objekty mají sloupec nebo vlastnost obsahující adresu URL souboru.

## <a name="input-shaping"></a>Vstupní tvarování

Načtení dat do pravého tvaru nebo struktury je klíčem k efektivnímu použití, jedná se o tabulky nebo objekty. Schopnost tvarovat nebo strukturovat data na základě toho, jak plánujete přístup a používat, je klíčovou funkcí, která je vystavena jako **Shaper** dovednost v rámci dovednosti.  

Pokud máte objekt ve stromu obohacení, který odpovídá schématu projekce, je snazší definovat projekce. Aktualizovaná [Shaper dovednost](cognitive-search-skill-shaper.md) umožňuje vytvořit objekt z různých uzlů stromu obohacení a jejich nadřazených objektů v rámci nového uzlu. **Shaper** dovednost umožňuje definovat komplexní typy s vnořenými objekty.

Když máte definovaný nový tvar, který obsahuje všechny prvky, které potřebujete k vyzkoušení projektu, můžete teď tento tvar použít jako zdroj pro vaše projekce nebo jako vstup pro jinou dovednost.

## <a name="projection-slicing"></a>Průřez projekce

Při definování skupiny projekce lze jeden uzel ve stromu rozšíření rozřezat na více souvisejících tabulek nebo objektů. Přidáním projekce se zdrojovou cestou, která je podřízenou položkou existující projekce, bude mít za následek vyřazení podřízeného uzlu mimo nadřazený uzel a promítnut do nového související tabulky nebo objektu. Tato technika vám umožní definovat jeden uzel v Shaper dovednosti, která může být zdrojem všech vašich projekce.

## <a name="table-projections"></a>Projekce tabulek

Vzhledem k tomu, že usnadňuje import, doporučujeme pro zkoumání dat pomocí Power BI použít projekce tabulek. Kromě toho mohou projekce tabulek umožňovat změnu mohutnosti mezi vztahy mezi tabulkami. 

Jeden dokument v indexu můžete promítnout do několika tabulek a zachovat vztahy. Když procházíte na více tabulek, celý tvar bude v každé tabulce projekt, pokud podřízený uzel není zdrojem jiné tabulky ve stejné skupině.

### <a name="defining-a-table-projection"></a>Definování projekce tabulky

Při definování projekce tabulky v rámci `knowledgeStore` prvku dovednosti začněte mapováním uzlu ve stromu rozšíření na zdroj tabulky. Obvykle je tento uzel výstupem **Shaper** dovednosti, kterou jste přidali do seznamu dovedností k vytvoření konkrétního tvaru, který potřebujete k Projectu v tabulkách. Uzel, který se rozhodnete pro projekt, lze rozdělit na více tabulek. Definice tabulek je seznam tabulek, které chcete projektovat.

Každá tabulka vyžaduje tři vlastnosti:

+ tableName: název tabulky v Azure Storage.

+ generatedKeyName: název sloupce pro klíč, který jednoznačně identifikuje tento řádek.

+ Zdroj: uzel ze stromu obohacení, ze kterého spravujete vaše rozšíření. Tento uzel je obvykle výstupem Shaper, ale může to být výstup kterékoli dovednosti.

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
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Jak je znázorněno v tomto příkladu, klíčové fráze a entity jsou modelovány do různých tabulek a budou obsahovat odkaz zpátky na nadřazenou (hlavní tabulku) pro každý řádek.

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
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Generování projekce objektu vyžaduje několik atributů specifických pro objekt:

+ storageContainer: kontejner objektů blob, do kterého se uloží objekty
+ Zdroj: cesta k uzlu stromu obohacení, který je kořenem projekce.

## <a name="file-projection"></a>Projekce souboru

Projekce souborů jsou podobné projekcím objektů a pracují pouze s `normalized_images` kolekcí. Podobně jako u projekcí objektů jsou projekce souborů uloženy v kontejneru objektů BLOB s předponou složky s hodnotou kódování Base64 ID dokumentu. Výčnělky souborů nemůžou sdílet stejný kontejner jako projekce objektů a musí se promítnout do jiného kontejneru.

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
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Životní cyklus projekce

Vaše projekce mají životní cyklus, který je svázán se zdrojovými daty ve zdroji dat. Jak jsou vaše data aktualizována a znovu indexována, vaše projekce se aktualizují o výsledky rozšíření, která zajišťují, že vaše projekce jsou nakonec konzistentní s daty ve zdroji dat. Výčnělky dědí zásadu odstranění, kterou jste nakonfigurovali pro váš index. Při odstranění indexeru nebo samotné vyhledávací služby se projekce neodstraňují.

## <a name="using-projections"></a>Použití projekce

Po spuštění indexeru můžete číst provedená data v kontejnerech nebo tabulkách, které jste určili pomocí projekce.

V případě analýz je průzkum v Power BI jednoduchý jako nastavení úložiště tabulek Azure jako zdroje dat. Pomocí vztahů v rámci můžete snadno vytvořit sadu vizualizací dat.

Případně, pokud potřebujete použít obohacená data v kanálu pro datové vědy, můžete [načíst data z objektů blob do PANDAS dataframe](../machine-learning/team-data-science-process/explore-data-blob.md).

Nakonec, pokud potřebujete exportovat data z znalostní báze Knowledge Store, Azure Data Factory obsahuje konektory pro export dat a pozemky v databázi podle vašeho výběru. 

## <a name="next-steps"></a>Další kroky

Jako další krok si vytvořte svoje první úložiště znalostní báze s použitím ukázkových dat a pokynů.

> [!div class="nextstepaction"]
> [Vytvořte znalostní bázi v klidovém úložišti](knowledge-store-create-rest.md).

Kurz týkající se pokročilých konceptů projekce, jako je například vytváření řezů, line Shaping a Relationships, Začínáme s [definováním projekce ve znalostní bázi Knowledge Store](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definování projekce ve znalostní bázi Knowledge Store](knowledge-store-projections-examples.md)
