---
title: Použití referenčních dat pro vyhledávání ve službě Azure Stream Analytics
description: Tento článek popisuje, jak použít referenční data k vyhledávání nebo korelaci dat v návrhu dotazu úlohy Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267284"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Použití referenčních dat pro vyhledávání v Stream Analytics

Referenční data (označovaná také jako vyhledávací tabulka) je omezená datová sada, která se mění staticky nebo se pomalu mění, používá se k provedení vyhledávání nebo k rozšíření datových proudů. Například ve scénáři IoT můžete ukládat metadata o senzorech (které se často nemění) v referenčních datech a spojit je s datovými proudy IoT v reálném čase. Azure Stream Analytics načítá referenční data do paměti, aby bylo dosaženo zpracování datového proudu s nízkou latencí. Chcete-li využít referenční data v úloze Azure Stream Analytics, budete obecně používat [referenční data připojit v](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) dotazu. 

Stream Analytics podporuje azure blob úložiště a Azure SQL Database jako vrstvu úložiště pro referenční data. Můžete také transformovat nebo zkopírovat referenční data do úložiště objektů Blob z Azure Data Factory a použít [libovolný počet cloudových a místních úložišť dat](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referenční data jsou modelována jako posloupnost objektů BLOB (definované ve vstupní konfiguraci) ve vzestupném pořadí data a času určeného v názvu objektu blob. Podporuje **pouze** přidání na konec sekvence pomocí data a času **větší** než je zadaný poslední objekt blob v pořadí.

### <a name="configure-blob-reference-data"></a>Konfigurace referenčních dat objektu blob

Chcete-li nakonfigurovat referenční data, musíte nejprve vytvořit vstup, který je typu **Referenční data**. Níže uvedená tabulka vysvětluje každou vlastnost, kterou budete muset poskytnout při vytváření vstupu referenčních dat s jeho popisem:

|**Název vlastnosti**  |**Popis**  |
|---------|---------|
|Alias pro vstup   | Popisný název, který bude použit v dotazu na úlohu odkazovat na tento vstup.   |
|Účet úložiště   | Název účtu úložiště, kde jsou umístěny objekty BLOB. Pokud je ve stejném předplatném jako vaše úloha Stream Analytics, můžete ji vybrat z rozbalovací nabídky.   |
|Klíč účtu úložiště   | Tajný klíč přidružený k účtu úložiště. To se automaticky naplní, pokud je účet úložiště ve stejném předplatném jako vaše úloha Stream Analytics.   |
|Kontejner úložiště   | Kontejnery poskytují logické seskupení pro objekty BLOB uložené ve službě Objektů blob Microsoft Azure. Při nahrávání objektu blob do služby objektů blob, musíte zadat kontejner pro tento objekt blob.   |
|Vzor cesty   | Cesta slouží k vyhledání objektů BLOB v rámci zadaného kontejneru. V rámci cesty můžete určit jednu nebo více instancí následujících 2 proměnných:<BR>{date}, {time}<BR>Příklad 1: products/{date}/{time}/product-list.csv<BR>Příklad 2: products/{date}/product-list.csv<BR>Příklad 3: product-list.csv<BR><br> Pokud objekt blob neexistuje v zadané cestě, úloha Analýzy streamování bude čekat neomezeně dlouho, než bude objekt blob k dispozici.   |
|Formát data [nepovinné]   | Pokud jste v zadaném vzoru cesty použili {date}, můžete vybrat formát data, ve kterém jsou objekty BLOB uspořádány, z rozevíracího souboru podporovaných formátů.<BR>Příklad: YYYY/MM/DD, MM/DD/YYYY atd.   |
|Formát času [nepovinné]   | Pokud jste použili {time} v rámci vzoru cesty, který jste zadali, můžete vybrat formát času, ve kterém jsou objekty BLOB uspořádány z rozevíracího souboru podporovaných formátů.<BR>Příklad: HH, HH/mm nebo HH-mm.  |
|Formát serializace událostí   | Chcete-li zajistit, aby vaše dotazy fungovaly očekávaným způsobem, stream analytics potřebuje vědět, který formát serializace používáte pro příchozí datové proudy. Pro referenční data jsou podporované formáty CSV a JSON.  |
|Kódování   | UTF-8 je v současné době jediným podporovaným formátem kódování.  |

### <a name="static-reference-data"></a>Statická referenční data

Pokud se neočekává, že by se referenční data změnila, je povolena podpora statických referenčních dat zadáním statické cesty ve vstupní konfiguraci. Azure Stream Analytics vyzvedne objekt blob ze zadané cesty. {date} a {time} substituční tokeny nejsou povinné. Vzhledem k tomu, že referenční data jsou v Stream Analytics neměnná, není doporučeno přepsat objekt blob statických referenčních dat.

### <a name="generate-reference-data-on-a-schedule"></a>Generovat referenční data podle plánu

Pokud jsou referenční data pomalu se měnící datovou sadou, je povolena podpora pro aktualizaci referenčních dat zadáním vzoru cesty ve vstupní konfiguraci pomocí substitučních tokenů {date} a {time}. Stream Analytics vyzvedne aktualizované definice referenčních dat na základě tohoto vzoru cesty. `sample/{date}/{time}/products.csv` Například vzor s formátem data **"YYYY-MM-DD"** a časový formát **"HH-mm"** instruuje Stream Analytics k vyzvednutí aktualizovaného objektu blob `sample/2015-04-16/17-30/products.csv` v 17:30 v dubnu 16th, 2015 UTC časové pásmo.

Azure Stream Analytics automaticky hledá objekty BLOB aktualizovaná referenční data v intervalu jedné minuty. Pokud se objekt blob s časovým razítkem 10:30:00 nahraje s malým zpožděním (například 10:30:30), všimnete si malého zpoždění v úloze Stream Analytics odkazující na tento objekt blob. Chcete-li se takovým scénářům vyhnout, doporučujeme nahrát objekt blob dříve, než je cílový efektivní čas (v tomto příkladu 10:30:00), aby úloha Analýzy datového proudu měla dostatek času na zjištění a načtení do paměti a provádění operací. 

> [!NOTE]
> V současné době úlohy Stream Analytics hledat aktualizace objektu blob pouze v případě, že čas počítače přejde na čas kódovaný v názvu objektu blob. Například úloha bude `sample/2015-04-16/17-30/products.csv` hledat co nejdříve, ale ne dříve než 5:30 PM na 16 dubna 2015 UTC časové pásmo. *Nikdy* nebude hledat objekt blob s kódovaný čas dříve než poslední, který je objeven.
> 
> Například jakmile úloha najde `sample/2015-04-16/17-30/products.csv` objekt blob, bude ignorovat všechny soubory s kódované datum dříve než 5:30 PM 16.dubna 2015, takže pokud pozdní příchozí `sample/2015-04-16/17-25/products.csv` objekt blob získá vytvořen ve stejném kontejneru úloha nebude používat.
> 
> Podobně pokud `sample/2015-04-16/17-30/products.csv` je pouze vyrobeno v 10:03 PM 16.dubna 2015, ale žádný objekt blob s dřívější datum je k dispozici v kontejneru, úloha bude používat tento soubor začíná v 10:03 PM 16.dubna 2015 a používat předchozí referenční data do té doby.
> 
> Výjimkou je, když úloha potřebuje znovu zpracovat data zpět v čase nebo při prvním spuštění úlohy. V době zahájení úloha hledá nejnovější objekt blob vytvořený před zadaným časem zahájení úlohy. To se provádí, aby bylo zajištěno, že při spuštění úlohy existuje **neprázdná** sada referenčních dat. Pokud nelze nalézt, úloha zobrazí následující `Initializing input without a valid reference data blob for UTC time <start time>`diagnostiku: .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) se dá použít k orchestraci úlohy vytváření aktualizovaných objektů BLOB vyžadovaných službou Stream Analytics k aktualizaci definic referenčních dat. Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Data Factory podporuje [připojení k velkému počtu cloudových a místních úložišť dat](../data-factory/copy-activity-overview.md) a přesouvání dat snadno v pravidelných intervalech, které zadáte. Další informace a podrobné pokyny k nastavení kanálu Data Factory pro generování referenčních dat pro Stream Analytics, která se aktualizují podle předem definovaného plánu, najdete v této [ukázce GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tipy pro aktualizaci referenčních dat objektů blob

1. Nepřepisovat objekty BLOB referenčních dat, protože jsou neměnné.
2. Doporučený způsob aktualizace referenčních dat je:
    * Použít {date}/{time} ve vzoru cesty
    * Přidání nového objektu blob pomocí stejného kontejneru a vzoru cesty definovaného ve vstupu úlohy
    * Použijte datum a čas **větší** než datum a čas určený posledním objektem blob v sekvenci.
3. Objekty BLOB referenčních dat **nejsou** seřazeny podle času poslední změny objektu blob, ale pouze podle času a data určeného v názvu objektu blob pomocí nahrazení {date} a {time}.
3. Chcete-li se vyhnout nutnosti vypsat velký počet objektů BLOB, zvažte odstranění velmi starých objektů BLOB, pro které již nebude prováděno zpracování. Vezměte prosím na vědomí, že ASA může jít muset znovu zpracovat malé množství v některých scénářích, jako je restartování.

## <a name="azure-sql-database"></a>Azure SQL Database

Referenční data azure sql database se načítají úlohou Stream Analytics a ukládají se jako snímek v paměti pro zpracování. Snímek referenčních dat je také uložen v kontejneru v účtu úložiště, který zadáte v nastavení konfigurace. Kontejner je automaticky vytvořen při spuštění úlohy. Pokud je úloha zastavena nebo zadá neúspěšný stav, automaticky vytvořené kontejnery budou odstraněny při restartování úlohy.  

Pokud jsou referenční data pomalu se měnící datovou sadou, je třeba pravidelně aktualizovat snímek, který se používá ve vaší úloze. Stream Analytics umožňuje nastavit obnovovací frekvenci při konfiguraci vstupního připojení azure SQL database. Modul runtime Stream Analytics bude dotazovat na vaši azure SQL databázi v intervalu určeném obnovovací frekvencí. Nejrychlejší obnovovací frekvence je jednou za minutu. Pro každou aktualizaci stream analytics ukládá nový snímek v účtu úložiště k dispozici.

Stream Analytics poskytuje dvě možnosti pro dotazování na azure sql database. Snímek dotaz je povinné a musí být zahrnuty v každé úloze. Stream Analytics spouští dotaz na snímek pravidelně na základě intervalu aktualizace a používá výsledek dotazu (snímek) jako sadu referenčních dat. Dotaz na snímek by měl odpovídat většině scénářů, ale pokud narazíte na problémy s výkonem s velkými sadami dat a rychlými obnovovacími frekvencemi, můžete použít možnost rozdílového dotazu. Dotazy, které trvá více než 60 sekund vrátit referenční sadu dat bude mít za následek časový rozsah.

S možností rozdílového dotazu Stream Analytics spustí dotaz na snímek zpočátku získat sadu dat odkazů směrného plánu. Poté Stream Analytics pravidelně spouští rozdílový dotaz na základě intervalu aktualizace, aby načetl přírůstkové změny. Tyto přírůstkové změny jsou průběžně použity na sadu referenčních dat, aby byla aktualizována. Použití rozdílového dotazu může pomoci snížit náklady na úložiště a operace vstupně-videa v síti.

### <a name="configure-sql-database-reference"></a>Konfigurace odkazu na databázi SQL

Chcete-li nakonfigurovat referenční data databáze SQL, musíte nejprve vytvořit vstup **referenčních dat.** Níže uvedená tabulka vysvětluje každou vlastnost, kterou budete muset poskytnout při vytváření vstupu referenčních dat s jeho popisem. Další informace najdete [v tématu Použití referenčních dat z databáze SQL pro úlohu Azure Stream Analytics](sql-reference-data.md).

Jako referenční datový vstup můžete použít [spravovanou instanci Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) Musíte [nakonfigurovat veřejný koncový bod ve spravované instanci Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a pak ručně nakonfigurovat následující nastavení v Azure Stream Analytics. Virtuální počítač Azure se systémem SQL Server s připojenou databází je také podporován ruční konfigurací níže uvedených nastavení.

|**Název vlastnosti**|**Popis**  |
|---------|---------|
|Alias vstupu|Popisný název, který bude použit v dotazu na úlohu odkazovat na tento vstup.|
|Předplatné|Zvolte vaše předplatné.|
|Databáze|Databáze Azure SQL, která obsahuje vaše referenční data. Pro azure sql database spravované instance je nutné zadat port 3342. Například *sampleserver.public.database.windows.net,3342*|
|Uživatelské jméno|Uživatelské jméno přidružené k databázi Azure SQL Database.|
|Heslo|Heslo přidružené k vaší Azure SQL Database.|
|Pravidelně aktualizovat|Tato možnost umožňuje zvolit obnovovací frekvenci. Volba "Zapnuto" vám umožní určit obnovovací frekvenci v DD:HH:MM.|
|Dotaz na snímek|Toto je výchozí možnost dotazu, která načte referenční data z databáze SQL.|
|Rozdílový dotaz|Pro pokročilé scénáře s velkými sadami dat a krátkou obnovovací frekvencí zvolte přidání rozdílového dotazu.|

## <a name="size-limitation"></a>Omezení velikosti

Stream Analytics podporuje referenční data s **maximální velikostí 300 MB**. Limit 300 MB maximální velikost referenčních dat je dosažitelný pouze s jednoduchými dotazy. Vzhledem k tomu, že složitost dotazu se zvyšuje tak, aby zahrnovala stavové zpracování, jako jsou agregace v okně, časová spojení a časové analytické funkce, očekává se, že maximální podporovaná velikost referenčních dat se sníží. Pokud Azure Stream Analytics nelze načíst referenční data a provádět složité operace, úloha se vyběhne z paměti a nezdaří. V takových případech metrika využití SU % dosáhne 100 %.    

|**Počet jednotek streamování**  |**Přibližně maximální podporovaná velikost (v MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 a dále   |300   |

Zvýšení počtu jednotek streamování úlohy nad 6 nezvýší maximální podporovanou velikost referenčních dat.

Podpora komprese není k dispozici pro referenční data. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
