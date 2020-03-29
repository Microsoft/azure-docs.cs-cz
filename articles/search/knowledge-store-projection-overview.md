---
title: Projekce v úložišti znalostí (náhled)
titleSuffix: Azure Cognitive Search
description: Uložte a tvarovat obohacená data z kanálu indexování obohacení ai do úložiště znalostí pro použití v jiných scénářích než fulltextové vyhledávání. Úložiště znalostí je v současné době ve verzi Public Preview.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942975"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projekce v úložišti znalostí v Azure Cognitive Search

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Azure Cognitive Search umožňuje obohacení obsahu prostřednictvím integrovaných kognitivních dovedností a vlastních dovedností jako součást indexování. Obohacení vytvořit nové informace, kde žádný dříve neexistoval: extrahování informací z obrázků, detekce mínění, klíčové fráze a entity z textu, abychom jmenovali několik. Obohacení také přidat strukturu nediferencovaný text. Výsledkem všech těchto procesů jsou dokumenty, které zefektivní fulltextové vyhledávání. V mnoha případech jsou rozšířené dokumenty užitečné pro jiné scénáře než hledání, například pro dolování znalostí.

Projekce, součást [úložiště znalostí](knowledge-store-concept-intro.md), jsou pohledy na obohacené dokumenty, které lze uložit do fyzického úložiště pro účely dolování znalostí. Projekce umožňuje "promítat" data do obrazce, který odpovídá vašim potřebám, a zachovat tak vztahy, aby nástroje jako Power BI mohly data číst bez dalšího úsilí.

Projekce můžou být tabulkové, s daty uloženými v řádcích a sloupcích v úložišti Azure Table storage nebo s objekty JSON uloženými v úložišti objektů blob Azure. Můžete definovat více projekcí dat, jak je obohacována. Více projekcí jsou užitečné, pokud chcete, aby stejná data ve tvaru odlišně pro jednotlivé případy použití.

Úložiště znalostí podporuje tři typy projekcí:

+ **Tabulky**: Pro data, která jsou nejlépe reprezentována jako řádky a sloupce, umožňují projekce tabulek definovat schematizovaný tvar nebo projekci v úložišti tabulky. Jako tabulky lze promítat pouze platné objekty JSON, obohacený dokument může obsahovat uzly, které nemají název JSON objekty, a při promítání těchto objektů vytvořte platný objekt JSON s dovedností tvarovače nebo inline tvarování.

+ **Objekty**: Když potřebujete json reprezentaci dat a obohacení, objekt projekce jsou uloženy jako objekty BLOB. Pouze platné objekty JSON mohou být promítány jako objekty, obohacený dokument může obsahovat uzly, které nemají název JSON objekty a při promítání těchto objektů vytvořte platný objekt JSON s dovedností tvarovače nebo inline tvarování.

+ **Soubory**: Když potřebujete uložit obrazy extrahované z dokumentů, projekce souborů umožňují uložit normalizované obrazy do úložiště objektů blob.

Chcete-li zobrazit projekce definované v kontextu, krok ovat [Vytvořit úložiště znalostí v REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Projekční skupiny

V některých případech budete muset promítat obohacená data v různých tvarech, abyste splnili různé cíle. Úložiště znalostí umožňuje definovat více skupin projekcí. Projekční skupiny mají následující klíčové charakteristiky vzájemné exkluzivity a příbuznosti.

### <a name="mutual-exclusivity"></a>Vzájemná exkluzivita

Veškerý obsah promítaný do jedné skupiny je nezávislý na datech promítaných do jiných projekčních skupin.
Tato nezávislost znamená, že můžete mít stejná data ve tvaru jinak, ale opakovat v každé projekční skupině.

### <a name="relatedness"></a>Příbuznost

Projekční skupiny nyní umožňují promítat dokumenty mezi typy projekcí při zachování vztahů mezi typy projekce. Veškerý obsah promítaný v rámci jedné projekční skupiny zachovává vztahy v rámci dat napříč typy projekce. V rámci tabulek jsou relace založeny na generovaném klíči a každý podřízený uzel si zachová odkaz na nadřazený uzel. Napříč typy (tabulky, objekty a soubory) jsou relace zachovány, když je jeden uzel promítán napříč různými typy. Zvažte například scénář, ve kterém máte dokument obsahující obrázky a text. Text můžete promítat do tabulek nebo objektů a obrázků do souborů, kde tabulky nebo objekty mají sloupec nebo vlastnost obsahující adresu URL souboru.

## <a name="input-shaping"></a>Vstupní tvarování

Získání dat do správného tvaru nebo struktury je klíčem k efektivnímu využití, ať už se jedná o tabulky nebo objekty. Schopnost tvarovat nebo strukturovat data na základě toho, jak je plánujete přistupovat a používat je klíčovou schopností, která je vystavena jako dovednost **Shaper** v rámci skillset.  

Projekce jsou snadněji definovat, když máte objekt ve stromu obohacení, který odpovídá schématu projekce. Aktualizované [Shaper dovednosti](cognitive-search-skill-shaper.md) umožňuje vytvořit objekt z různých uzlů stromu obohacení a nadřazený je pod novým uzlem. Dovednost **Shaper** umožňuje definovat složité typy s vnořenými objekty.

Pokud máte nový tvar definovaný, který obsahuje všechny prvky, které potřebujete promítnout, můžete nyní použít tento obrazec jako zdroj pro vaše projekce nebo jako vstup do jiné dovednosti.

## <a name="projection-slicing"></a>Projekční krájení

Při definování projekční skupiny lze jeden uzel ve stromu obohacení rozdělit do více souvisejících tabulek nebo objektů. Přidání projekce se zdrojovou cestou, která je podřízenou existující projekcí, bude mít za následek, že podřízený uzel bude rozdělen z nadřazeného uzlu a promítnut do nové, ale související tabulky nebo objektu. Tato technika umožňuje definovat jeden uzel v shaper dovednost, která může být zdrojem pro všechny vaše projekce.

## <a name="table-projections"></a>Projekce stolů

Protože usnadňuje import, doporučujeme projekci tabulek pro zkoumání dat pomocí Power BI. Projekce tabulky navíc umožňují změnu mohutnosti mezi relacemi mezi tabulkami. 

Můžete promítnout jeden dokument v indexu do více tabulek, zachování relací. Při promítání do více tabulek bude celý obrazec promítán do každé tabulky, pokud podřízený uzel není zdrojem jiné tabulky ve stejné skupině.

### <a name="defining-a-table-projection"></a>Definování projekce tabulky

Při definování projekce tabulky `knowledgeStore` v rámci prvku vaší skillset začněte mapováním uzlu ve stromu obohacení na zdroj tabulky. Obvykle je tento uzel výstupem **shaper** dovednosti, které jste přidali do seznamu dovedností k vytvoření konkrétní ho tvaru, který je třeba promítnout do tabulek. Uzel, který zvolíte k promítu, lze rozdělit na projekt do více tabulek. Definice tabulek je seznam tabulek, které chcete promítnout.

Každá tabulka vyžaduje tři vlastnosti:

+ název tabulky ve službě Azure Storage.

+ generatedKeyName: Název sloupce pro klíč, který jednoznačně identifikuje tento řádek.

+ zdroj: Uzel ze stromu obohacení, ze kterých získáváte obohacení. Tento uzel je obvykle výstup shaper, ale může být výstup emitovaného.

Zde je příklad projekcí tabulek.

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

Jak je znázorněno v tomto příkladu, klíčové fráze a entity jsou modelovány do různých tabulek a bude obsahovat odkaz zpět na nadřazené (MainTable) pro každý řádek.

## <a name="object-projections"></a>Objektové projekce

Objekt projekce jsou JSON reprezentace stromu obohacení, které mohou být získávány z libovolného uzlu. V mnoha případech lze stejnou dovednost **Shaper,** která vytvoří projekci tabulky, použít ke generování projekce objektu. 

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

+ storageContainer: Kontejner objektů blob, kde budou uloženy objekty
+ zdroj: Cesta k uzlu stromu obohacení, který je kořenem projekce

## <a name="file-projection"></a>Projekce souborů

Projekce souborů jsou podobné projekce objektu a `normalized_images` působí pouze na kolekci. Podobně jako projekce objektů jsou projekce souborů uloženy v kontejneru objektů blob s předponou složky kódované hodnoty id dokumentu base64. Projekce souborů nemohou sdílet stejný kontejner jako projekce objektů a je třeba je promítnout do jiného kontejneru.

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

Vaše projekce mají životní cyklus, který je vázán na zdrojová data ve zdroji dat. Při aktualizaci a přeindexování dat jsou vaše projekce aktualizovány s výsledky obohacení, které zajišťují, že vaše projekce budou nakonec konzistentní s daty ve zdroji dat. Projekce dědí zásady odstranění, které jste nakonfigurovali pro index. Projekce nejsou odstraněny při odstranění indexeru nebo samotné vyhledávací služby.

## <a name="using-projections"></a>Použití projekcí

Po spuštění indexeru můžete číst promítaná data v kontejnerech nebo tabulkách, které jste zadali prostřednictvím projekcí.

Pro analýzy je průzkum v Power BI stejně jednoduchý jako nastavení úložiště Azure Table jako zdroj dat. Pomocí relací uvnitř můžete snadno vytvořit sadu vizualizací na datech.

Případně pokud potřebujete použít obohacená data v kanálu datové [vědy, můžete načíst data z objektů BLOB do datového rámce Pandy](../machine-learning/team-data-science-process/explore-data-blob.md).

A konečně, pokud potřebujete exportovat data z úložiště znalostí, Azure Data Factory má konektory pro export dat a přiložte je do databáze podle vašeho výběru. 

## <a name="next-steps"></a>Další kroky

Jako další krok vytvořte první úložiště znalostí pomocí ukázkových dat a pokynů.

> [!div class="nextstepaction"]
> [Vytvořte úložiště znalostí v REST](knowledge-store-create-rest.md).

Pro kurz zahrnující pokročilé projekce pojmy, jako je krájení, inline tvarování a vztahy, začít s [definovat projekce v úložišti znalostí](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definování projekcí v úložišti znalostí](knowledge-store-projections-examples.md)
