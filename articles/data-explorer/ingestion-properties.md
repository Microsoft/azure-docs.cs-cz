---
title: Vlastnosti ingestování dat pro Azure Data Explorer
description: Přečtěte si o různých vlastnostech ingestování dat podporovaných aplikací Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109573"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Vlastnosti při lání dat průzkumníka dat Azure 

Při jízání dat je proces, kterým se data přidávají do tabulky a jsou dostupná pro dotaz v Průzkumníku dat Azure. Vlastnosti přidat do příkazu ingestování za `with` klíčové slovo.

## <a name="ingestion-properties"></a>Vlastnosti požití

V následující tabulce jsou uvedeny vlastnosti podporované aplikací Azure Data Explorer, popisují je a obsahují příklady: 

|Vlastnost              |Popis                                              |Příklad                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Hodnota řetězce, která označuje, jak mapovat data ze zdrojového souboru do skutečných sloupců v tabulce. Definujte `format` hodnotu s příslušným typem mapování. Viz [mapování dat](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(zastaralé: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Hodnota řetězce, která označuje, jak mapovat data ze zdrojového souboru do skutečných sloupců v tabulce pomocí pojmenovaného objektu zásad mapování. Definujte `format` hodnotu s příslušným typem mapování. Viz [mapování dat](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(zastaralé: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Datetime hodnota (formátován jako řetězec ISO8601) použít v době vytvoření rozsahy ingestovaných dat. Pokud není zadán, bude`now()`použita aktuální hodnota ( ). Přepsání výchozí je užitečné při ingestování starších dat, takže zásady uchovávání informací budou použity správně.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Logická hodnota, která, pokud je zadána, instruuje příkaz k rozšíření `false`schématu tabulky (výchozí). Tato možnost se `.append` vztahuje `.set-or-append` pouze na příkazy a příkazy. Pouze povolené rozšíření schématu mají další sloupce přidané do tabulky na konci.|Pokud je původní schéma tabulky `(a:string, b:int)`, platné rozšíření schématu `(a:string, b:int, c:datetime, d:string)`by `(a:string, c:datetime)` bylo , ale nebylo by platné|
|`folder` |Pro [příkazy ingest-from-query,](/azure/kusto/management/data-ingestion/ingest-from-query) složku přiřadit k tabulce. Pokud tabulka již existuje, tato vlastnost přepíše složku tabulky.|`with (folder="Tables/Temporary")`|
|`format` |Formát dat (viz [podporované formáty dat](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Hodnota řetězce, která, pokud je zadána, zabraňuje úspěšnému ingestování, pokud `ingest-by:` tabulka již obsahuje data označená značkou se stejnou hodnotou. Tím je zajištěno idempotentní požití dat. Další informace naleznete [v tématu ingest-by: tags](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|Vlastnosti `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` označují, že pokud `ingest-by:Part0001` data se značkou již existuje, nevyplňujte aktuální ingestování. Pokud ještě neexistuje, toto nové ingestování by mělo mít tuto značku nastavenou (v případě, že se budoucí ingestování pokusí znovu singestovat stejná data.)|
|`ignoreFirstRecord` |Logická hodnota, která, `true`pokud je nastavena na , označuje, že požití by měl ignorovat první záznam každého souboru. Tato vlastnost je užitečná pro soubory v `CSV`podobných formátech, pokud první záznam v souboru jsou názvy sloupců. Ve výchozím `false` nastavení se předpokládá.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Logická hodnota, která, pokud je zadána, označuje, že příkaz by měl zachovat podrobné výsledky (i v případě úspěchu), aby je mohl načíst příkaz [.show operation details.](/azure/kusto/management/operations#show-operation-details) Výchozí hodnota `false`je na .|`with (persistDetails=true)`|
|`policy_ingestiontime`|Logická hodnota, která, pokud je zadána, popisuje, zda chcete povolit [zásady času ingestování](/azure/kusto/management/ingestiontimepolicy) v tabulce, která je vytvořena tímto příkazem. Výchozí formát je `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Logická hodnota, která, pokud je zadána, popisuje, zda příkaz může znovu vytvořit schéma tabulky. Tato vlastnost platí pouze `.set-or-replace` pro příkaz. Tato vlastnost má přednost `extend_schema` před vlastností, pokud jsou nastaveny obě.|`with (recreate_schema=true)`|
|`tags`|Seznam [značek, které](/azure/kusto/management/extents-overview#extent-tagging) chcete přidružit k ingovaným datům, formátovaný jako řetězec JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Řetězec JSON, který označuje, které ověření spustit během ingestování. Vysvětlení různých možností naleznete v tématu [Ingestion](/azure/kusto/management/data-ingestion/) data.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(to to je vlastně výchozí zásady)|
|`zipPattern`|Tuto vlastnost použijte při ingestování dat z úložiště, které má archiv ZIP. Jedná se o řetězcovou hodnotu označující regulární výraz, který se má použít při výběru souborů v archivu ZIP k ingestování.  Všechny ostatní soubory v archivu budou ignorovány.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Další kroky

* Další informace o [přijím dat](/azure/data-explorer/ingest-data-overview)
* Další informace o [podporovaných formátech dat](ingestion-supported-formats.md)
