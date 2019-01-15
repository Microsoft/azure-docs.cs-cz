---
title: Využití referenčních dat pro vyhledávání ve službě Azure Stream Analytics
description: Tento článek popisuje, jak použít referenční data vyhledávání identifikátoru objektu nebo korelaci dat v návrhu dotazu úlohu Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 905ea05d2b3bc58428831ae815238de818912928
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304427"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Pomocí referenčních dat pro vyhledávání ve službě Stream Analytics
Referenční data (označované také jako vyhledávací tabulky) je konečná datová sada, které jsou statické nebo s pomalou změnou ze své podstaty, používá k vyhledávání a korelaci s datovým proudem. Azure Stream Analytics načítá referenčních dat v paměti, zpracování streamů s nízkou latencí. Chcete-li pomocí referenčních dat v úloze Azure Stream Analytics, budete obvykle používat [referenční Data připojení](https://msdn.microsoft.com/library/azure/dn949258.aspx) v dotazu. Stream Analytics využívá úložiště objektů Blob v Azure jako vrstvu úložiště pro referenční Data, a s Azure Data Factory – referenční informace může transformovat data nebo zkopírovat do úložiště objektů Blob v Azure, pro použití jako referenční Data z [libovolný počet založené na cloudu a v místním úložišti dat](../data-factory/copy-activity-overview.md). Referenční data je modelovaná jako řadu objektů BLOB (definovaný ve vstupní konfiguraci) ve vzestupném pořadí datum a čas zadaný v názvu objektu blob. To **pouze** podporuje přidávání na konci sekvence s použitím datum/čas **větší** než je uvedeno v pořadí poslední objekt blob.

Stream Analytics podporuje referenční data s **maximální velikost 300 MB**. 300 MB limit maximální velikosti referenčních dat je pouze s jednoduchých dotazů. Jak se zvyšuje složitost dotazu zahrnout stavové zpracování, jako jsou například agregace v okně, dočasné spojení a dočasné analytických funkcí, očekává se, že maximální podporovaná velikost snížení referenční data. Pokud Azure Stream Analytics nejde načíst referenčních dat a provádění složitých operací, úlohy budou mít nedostatek paměti a selhání. V takových případech metriky využití SU dosáhne 100 %.    

|**Počet jednotek streamování**  |**Podporované asi maximální velikost (v MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 a novější   |300   |

Zvýšení počtu jednotek streamování úlohy nad rámec 6 nezvyšuje maximální podporovaná velikost referenční data.

Podpora pro kompresi není k dispozici pro referenční data. 

## <a name="configuring-reference-data"></a>Konfigurace referenčních dat
Pokud chcete nakonfigurovat referenční data, musíte nejprve vytvořit vstup, který je typu **referenčních dat**. Následující tabulka popisuje každý vlastnost, která budete muset zadat při vytváření referenčních dat, zadejte jeho popis:

|**Název vlastnosti**  |**Popis**  |
|---------|---------|
|Alias pro vstup   | Popisný název, který se použije k odkazování tento vstup v dotazu úlohy.   |
|Účet úložiště   | Název účtu úložiště, kde se nachází objektů BLOB. Pokud je ve stejném předplatném jako vaší úlohy Stream Analytics, můžete ji vybrat z rozevíracího seznamu.   |
|Klíč účtu úložiště   | Tajný klíč přidružený k účtu úložiště. To automaticky naplní, pokud je účet úložiště ve stejném předplatném jako vaší úlohy Stream Analytics.   |
|Kontejner úložiště   | Kontejnery poskytují možnost logického seskupování u objektů BLOB uložených ve službě Microsoft Azure Blob. Při nahrání objektu blob do služby Blob service, je nutné zadat kontejner pro tohoto objektu blob.   |
|Vzor cesty   | Cesta používaná k nalezení objektů BLOB v zadaném kontejneru. V této cestě můžete zadat jednu nebo víc instancí následujících proměnných 2:<BR>{date} {time}<BR>Příklad 1: products/{date}/{time}/product-list.csv<BR>Příklad 2: products/{date}/product-list.csv<BR>Příklad 3: seznam-produktu.csv<BR><br> Pokud objekt blob neexistuje v zadané cestě, úlohy Stream Analytics bude čekat bez omezení pro tento objekt blob k dispozici.   |
|Formát data [volitelný]   | Pokud jste použili {date} v rámci vzor cesty, který jste zadali, můžete vybrat formát data, ve kterém jsou uspořádány objektů blob z rozevíracího seznamu podporovaných formátů.<BR>Příklad: RRRR/MM/DD, MM/DD/RRRR, atd.   |
|Formát času [volitelný]   | Pokud jste použili {time} v rámci vzor cesty, který jste zadali, můžete vybrat formát času, ve kterém jsou uspořádány objektů blob z rozevíracího seznamu podporovaných formátů.<BR>Příklad: HH, HH/mm nebo HH mm.  |
|Formát serializace události   | Aby dotazy fungovaly podle očekávání, potřebuje Stream Analytics vědět, který formát serializace používáte pro příchozí datové streamy. Podporované formáty pro referenční Data jsou CSV a JSON.  |
|Kódování   | V tuto chvíli je jediným podporovaným formátem kódování UTF-8.  |

## <a name="static-reference-data"></a>Statická referenční data
Pokud referenční data se neočekává změna, podporu pro statická referenční data je povoleno zadat cestu ke statické ve vstupní konfiguraci. Azure Stream Analytics převezme objekt blob ze zadané cesty. {date} a {time} nahrazování tokenů se nevyžadují. Referenční data jsou neměnné ve službě Stream Analytics. Proto se nedoporučuje přepsání statická referenční data objektu blob.

## <a name="generating-reference-data-on-a-schedule"></a>Generuje se referenční data podle plánu
Pokud referenční data jsou pomalu se měnící datové sady, podporu pro aktualizaci odkaz, který je povolené rozhraní data tak, že zadáte vzor cesty ve vstupní konfiguraci pomocí {date} a {time} nahrazování tokenů. Stream Analytics převezme definice aktualizovaná referenční data, založené na tento vzor cesty. Například vzor `sample/{date}/{time}/products.csv` s formátem data **"Rrrr-MM-DD"** a formát času **"HH mm"** dává pokyn Stream Analytics ke sbírání aktualizované blob `sample/2015-04-16/17-30/products.csv` v 17:30:00 na 16. dubna , Časové pásmo UTC 2015.

Azure Stream Analytics automaticky kontroluje pro objekty BLOB aktualizace referenčních dat v minutových intervalech.

> [!NOTE]
> Aktuálně úlohy Stream Analytics vyhledejte aktualizace objektů blob jenom v případě, že čas počítače přejde do doby kódovaný v názvu objektu blob. Například bude hledat úlohy `sample/2015-04-16/17-30/products.csv` ihned poté, co možná, ale ne starší než 17:30:00 na 16. dubna 2015 UTC časové pásmo. Bude *nikdy* vyhledejte objekt blob s kódováním časem starší než poslední z nich, který je zjištěn.
> 
> Například Jakmile úloha nalezne objekt blob `sample/2015-04-16/17-30/products.csv` všech souborů s kódováním data starší než 17:30:00 16. dubna 2015 se bude ignorovat, pokud opožděné přicházejících `sample/2015-04-16/17-25/products.csv` objektů blob se vytvoří ve stejném kontejneru úloha nebude používat.
> 
> Podobně pokud `sample/2015-04-16/17-30/products.csv` je vytvořen pouze ve 22:03:00 16. dubna 2015, ale žádný objekt blob s dřívějšímu datu se nachází v kontejneru, úloha použije tento soubor se spouští ve 22:03:00 16. dubnem 2015 a použít předchozí referenční data, dokud to neuděláte.
> 
> Jedinou výjimkou je to když to úloha potřebuje znovu zpracovat data zpět v čase, nebo po prvním spuštění. Při spuštění úlohy hledá nejnovější blob vytvořené před určený čas spuštění úlohy. To se provádí, aby se zajistilo, že **neprázdný** referenční sady dat při spuštění úlohy. Pokud nelze nalézt jeden, úlohy se zobrazí následující Diagnostika: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) je použít k orchestrování úlohy vytvoření aktualizované objekty BLOB vyžaduje Stream Analytics k aktualizaci definic referenční data. Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Data Factory podporuje [připojení velkého počtu cloudu na základě a místních úložišť dat](../data-factory/copy-activity-overview.md) a snadno přesun dat v pravidelných intervalech, který zadáte. Další informace a podrobné pokyny, jak vytvořit kanál služby Data Factory pro generování referenčních dat pro Stream Analytics, která aktualizuje podle předem daného plánu, podívejte se na to [Githubu ukázky](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Tipy pro aktualizace referenčních dat
1. Nepřepisovat objekty BLOB referenčních dat, jako jsou neměnné.
2. Je doporučeným způsobem, jak aktualizovat referenční data:
    * Použijte {date} / {time} v vzor cesty
    * Přidání nového objektu blob s využitím stejného kontejneru a cestu vzorku definovanému v vstup úlohy
    * Použijte datum a čas **větší** než je uvedeno v pořadí poslední objekt blob.
3. Objekty BLOB referenčních dat jsou **není** seřazené podle času "Naposledy upraveno" Tento objekt blob, ale pouze čas a datum zadané v objektu blob pojmenovat pomocí {date} a {time} náhrady.
3. Aby se nemusela seznamu velkého počtu objektů BLOB, zvažte odstranění starých objekty BLOB, pro které se již provádí zpracování. Mějte prosím na paměti, že Azure Stream Analytics je možné dát muset znovu zpracovat malé množství v některých scénářích, jako je restartování.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
