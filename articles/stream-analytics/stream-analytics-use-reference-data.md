---
title: Použití referenčních dat pro vyhledávání v Azure Stream Analytics
description: Tento článek popisuje, jak pomocí referenčních dat vyhledávat nebo korelovat data v návrhu dotazů Azure Stream Analytics úlohy.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e7f5b3ae0a4dc7faa67a361b210b1d014e1f1b93
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722126"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Použití referenčních dat pro vyhledávání v Stream Analytics

Referenční data (označovaná také jako vyhledávací tabulka) jsou konečnou datovou sadu, která je statická nebo pomalá, se mění v podstatě, která se používá k vyhledávání nebo rozšíření datových proudů. Například ve scénáři IoT můžete ukládat metadata o senzorech (které se často nemění) v referenčních datech a spojit je s datovými proudy IoT v reálném čase. Azure Stream Analytics načte referenční data v paměti, aby bylo možné zpracovat zpracování datových proudů s nízkou latencí. Pokud chcete používat referenční data v úloze Azure Stream Analytics, obecně se v dotazu použije [referenční datová připojení](/stream-analytics-query/reference-data-join-azure-stream-analytics) . 

## <a name="example"></a>Příklad  
V reálném čase můžete mít datový proud událostí vygenerovaných v případě, že automobily přejdou na svůj telefonní kabinu. V rámci telefonní kabiny může být v reálném čase zachycena licence a spojí se se statickou datovou sadou, která má registrační údaje, a identifikovat tak licenční štítky, jejichž platnost vypršela.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics podporuje službu Azure Blob Storage a Azure SQL Database jako vrstvu úložiště pro referenční data. Můžete také transformovat nebo kopírovat referenční data do úložiště objektů BLOB z Azure Data Factory, abyste mohli používat [libovolný počet cloudových a místních úložišť dat](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referenční data jsou modelována jako sekvence objektů BLOB (definovaných ve vstupní konfiguraci) ve vzestupném pořadí podle data a času zadaného v názvu objektu BLOB. Podporuje se **pouze** přidávání na konec sekvence pomocí data a času **většího** než ta, kterou Určuje poslední objekt BLOB v sekvenci.

### <a name="configure-blob-reference-data"></a>Konfigurace referenčních dat objektů BLOB

Chcete-li nakonfigurovat referenční data, musíte nejprve vytvořit vstup, který je typu **referenčních dat**. Následující tabulka vysvětluje každou vlastnost, kterou budete muset zadat při vytváření vstupních referenčních dat pomocí jejího popisu:

|**Název vlastnosti**  |**Popis**  |
|---------|---------|
|Alias pro vstup   | Popisný název, který bude použit v dotazu úlohy pro odkazování na tento vstup.   |
|Účet úložiště   | Název účtu úložiště, ve kterém jsou umístěné objekty blob Pokud je ve stejném předplatném jako vaše Stream Analytics úlohy, můžete ho vybrat z rozevírací nabídky.   |
|Klíč účtu úložiště   | Tajný klíč přidružený k účtu úložiště Tato funkce se automaticky naplní, pokud je účet úložiště ve stejném předplatném jako vaše úloha Stream Analytics.   |
|Kontejner úložiště   | Kontejnery poskytují logické seskupení pro objekty blob uložené v Blob service Microsoft Azure. Po nahrání objektu blob do Blob service musíte zadat kontejner pro tento objekt BLOB.   |
|Vzor cesty   | Tato vlastnost je povinná, která se používá k vyhledání objektů BLOB v zadaném kontejneru. V cestě se můžete rozhodnout, že chcete zadat jednu nebo více instancí následujících 2 proměnných:<BR>{Date}, {Time}<BR>Příklad 1: produkty/{Date}/{Time}/product-list.csv<BR>Příklad 2: produkty/{Date}/product-list.csv<BR>Příklad 3: product-list.csv<BR><br> Pokud objekt BLOB v zadané cestě neexistuje, bude úloha Stream Analytics čekat na neomezenou dobu, než se objekt BLOB stane dostupným.   |
|Formát data [nepovinné]   | Pokud jste {Date} použili v rámci zadaného vzoru cesty, můžete vybrat formát data, ve kterém jsou objekty blob seřazené z rozevírací nabídky podporovaných formátů.<BR>Příklad: RRRR/MM/DD, MM/DD/RRRR atd.   |
|Formát času [nepovinné]   | Pokud jste v rámci zadaného vzoru cesty použili {Time}, můžete vybrat formát času, ve kterém jsou objekty blob seřazené z rozevírací nabídky podporovaných formátů.<BR>Příklad: HH, HH/mm nebo HH-mm.  |
|Formát serializace události   | Chcete-li zajistit, aby dotazy fungovaly podle očekávání, Stream Analytics musí znát, který formát serializace používáte pro příchozí datové proudy. V případě referenčních dat jsou podporované formáty CSV a JSON.  |
|Encoding   | Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8.  |

### <a name="static-reference-data"></a>Statická referenční data

Pokud se nepředpokládá, že se vaše referenční data mění, je povolená podpora statických referenčních dat tím, že v konfiguraci vstupu určíte statickou cestu. Azure Stream Analytics převezme objekt BLOB ze zadané cesty. substituční tokeny {Date} a {Time} nejsou povinné. Vzhledem k tomu, že referenční data jsou v Stream Analytics neměnné, není doporučeno přepsat statický referenční datový objekt BLOB.

### <a name="generate-reference-data-on-a-schedule"></a>Generování referenčních dat podle plánu

Pokud jsou vaše referenční data s pomalou změnou datové sady, je povolená podpora pro aktualizaci referenčních dat zadáním vzoru cesty ve vstupní konfiguraci pomocí náhradních tokenů {Date} a {Time}. Stream Analytics převezme aktualizované definice referenčních dat na základě tohoto vzoru cesty. Například vzor `sample/{date}/{time}/products.csv` s formátem data **"rrrr-mm-dd"** a formát času **"HH-mm"** nastaví Stream Analytics k výběru aktualizovaného objektu blob `sample/2015-04-16/17-30/products.csv` v 5:30. dubna 2015 časové pásmo UTC.

Azure Stream Analytics automaticky hledá aktualizované objekty blob referenčních dat v jednom minutovém intervalu. Pokud se objekt BLOB s časovým razítkem 10:30:00 nahraje s malým zpožděním (například 10:30:30), všimnete se krátké prodlevy v Stream Analytics úlohy odkazující na tento objekt BLOB. Aby nedocházelo k takovým scénářům, doporučuje se nahrát objekt BLOB starší než cílový efektivní čas (10:30:00 v tomto příkladu), čímž umožníte Stream Analytics úlohy dostat dostatek času k tomu, aby se zjistila a načetla v paměti a prováděla operace. 

> [!NOTE]
> V současné době Stream Analytics úlohy hledají obnovení objektu BLOB pouze v případě, že čas počítače přejde k času zakódovanému v názvu objektu BLOB. Například úloha bude hledat co `sample/2015-04-16/17-30/products.csv` nejdříve, ale ne dříve než 5:30. dubna 2015 časová pásma UTC. *Nikdy* nebude Hledat objekt BLOB se zakódovaným časem starším než poslední zjištěný.
> 
> Když například úloha nalezne objekt blob, `sample/2015-04-16/17-30/products.csv` bude ignorovat všechny soubory s zakódovaným datem starším než 5:30. dubna 2015, takže pokud se pozdní příchozí `sample/2015-04-16/17-25/products.csv` objekt BLOB vytvoří ve stejném kontejneru, úloha ho nebude používat.
> 
> Podobně platí, že pokud `sample/2015-04-16/17-30/products.csv` je určena pouze v 10:03. dubna 2015, ale v kontejneru není přítomen žádný objekt BLOB se starším datem, úloha použije tento soubor od 10:03. dubna, 2015 a použijte předchozí referenční data do té doby.
> 
> Výjimkou je to, že úloha potřebuje znovu zpracovat data zpět v čase nebo při prvním spuštění úlohy. V okamžiku spuštění úlohy hledá nejaktuálnější objekt BLOB vytvořený před zadaným počátečním časem úlohy. K tomu je potřeba zajistit, aby při spuštění úlohy byla nastavena **neprázdná** referenční datová sada. Pokud ho nejde najít, úloha zobrazí následující diagnostiku: `Initializing input without a valid reference data blob for UTC time <start time>` .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) lze použít k orchestraci úlohy vytváření aktualizovaných objektů BLOB vyžadovaných Stream Analytics k aktualizaci definic referenčních dat. Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat. Data Factory podporuje [připojení k velkému počtu cloudových a místních úložišť dat](../data-factory/copy-activity-overview.md) a snadné přesouvání dat podle pravidelného plánu, který zadáte. Další informace a podrobné pokyny k nastavení kanálu Data Factory pro generování referenčních dat pro Stream Analytics, která se aktualizují podle předem definovaného plánu, najdete v této [ukázce GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tipy pro aktualizaci referenčních dat objektů BLOB

1. Nepřepište objekty blob referenčních dat, protože jsou neměnné.
2. Doporučený způsob, jak aktualizovat referenční data, je:
    * Použít {Date}/{Time} ve vzoru cesty
    * Přidat nový objekt BLOB s použitím stejného vzoru kontejneru a cesty definovaného ve vstupu úlohy
    * Použijte datum a čas **větší** než ta, která je určena posledním objektem BLOB v sekvenci.
3. Referenční datové objekty blob **se** neúčtují podle času poslední změny objektu blob, ale jenom podle času a data zadaného v názvu objektu BLOB pomocí nahrazení {Date} a {Time}.
3. Abyste se vyhnuli nutnosti vypsat velký počet objektů blob, zvažte odstranění velmi starých objektů blob, pro které se zpracování už nedokončí. Upozorňujeme, že ASA můžou v některých scénářích, jako je restartování, potřebovat znovu zpracovat malou část.

## <a name="azure-sql-database"></a>Databáze Azure SQL

Azure SQL Database referenční data jsou načítána úlohou Stream Analytics a jsou ukládána jako snímek v paměti ke zpracování. Snímek referenčních dat je uložený také v kontejneru v účtu úložiště, který zadáte v nastavení konfigurace. Kontejner se vytvoří automaticky při spuštění úlohy. Pokud se úloha zastaví nebo vstoupí do stavu selhání, automaticky vytvořené kontejnery se odstraní při restartování úlohy.  

Pokud jsou vaše referenční data datovou sadou s pomalou změnou, je třeba pravidelně aktualizovat snímek, který se používá ve vaší úloze. Stream Analytics umožňuje nastavit obnovovací frekvenci při konfiguraci Azure SQL Databaseho vstupního připojení. Modul runtime Stream Analytics se dotazuje Azure SQL Database v intervalu zadaném obnovovací frekvencí. Frekvence nejrychlejší aktualizace je podporovaná jednou za minutu. Pro každou aktualizaci Stream Analytics ukládá nový snímek v poskytnutém účtu úložiště.

Stream Analytics poskytuje dvě možnosti pro dotazování na Azure SQL Database. Dotaz na snímek je povinný a musí být zahrnutý v každé úloze. Stream Analytics spouští dotaz na snímek pravidelně v závislosti na intervalu aktualizace a používá výsledek dotazu (snímku) jako referenční sady dat. Dotaz na snímek by měl vyhovovat většině scénářů, ale pokud narazíte na problémy s výkonem s velkými sadami dat a rychlostmi rychlé aktualizace, můžete použít možnost rozdílového dotazu. Dotazy, které mají vrátit referenční datovou sadu více než 60 sekund, budou mít za následek časový limit.

Pomocí možnosti rozdílového dotazu Stream Analytics spustí nejprve dotaz snímku, který načte datovou sadu referenčních dat. Po Stream Analytics spustí rozdílový dotaz pravidelně na základě intervalu aktualizace, aby se načetly přírůstkové změny. Tyto přírůstkové změny se průběžně aplikují na referenční datovou sadu, aby se zachovala její aktualizace. Použití rozdílového dotazu může pomoci snižovat náklady na úložiště a vstupně-výstupní operace sítě.

### <a name="configure-sql-database-reference"></a>Konfigurace odkazu na SQL Database

Chcete-li nakonfigurovat referenční data SQL Database, musíte nejprve vytvořit **referenční vstupní data** . Následující tabulka vysvětluje každou vlastnost, kterou budete muset zadat při vytváření vstupních dat s popisem. Další informace najdete v tématu [použití referenčních dat z SQL Database pro úlohu Azure Stream Analytics](sql-reference-data.md).

Můžete použít [spravovanou instanci SQL Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) jako referenční datový vstup. Musíte [nakonfigurovat veřejný koncový bod ve spravované instanci SQL](../azure-sql/managed-instance/public-endpoint-configure.md) a pak ručně nakonfigurovat následující nastavení v Azure Stream Analytics. Virtuální počítač Azure se spuštěným SQL Server s připojenou databází je také podporován ruční konfigurací nastavení níže.

|**Název vlastnosti**|**Popis**  |
|---------|---------|
|Alias vstupu|Popisný název, který bude použit v dotazu úlohy pro odkazování na tento vstup.|
|Předplatné|Zvolte vaše předplatné.|
|Database|Azure SQL Database, která obsahuje vaše referenční data. Pro spravovanou instanci SQL je nutné zadat port 3342. Například *sampleserver. Public. Database. Windows. NET, 3342*|
|Uživatelské jméno|Uživatelské jméno přidružené k vašemu Azure SQL Database.|
|Heslo|Heslo přidružené k vašemu Azure SQL Database.|
|Pravidelně aktualizovat|Tato možnost umožňuje zvolit obnovovací frekvenci. Když zvolíte Zapnuto, budete moct zadat obnovovací frekvenci v DD: HH: MM.|
|Snímkový dotaz|Toto je výchozí možnost dotazu, která načte referenční data z vašeho SQL Database.|
|Rozdílový dotaz|V případě pokročilých scénářů s velkými datovými sadami a s frekvencí krátké aktualizace vyberte možnost Přidat rozdílový dotaz.|

## <a name="size-limitation"></a>Omezení velikosti

Pro nejlepší výkon se doporučuje použít referenční datové sady, které jsou menší než 300 MB. Referenční datové sady 5 GB nebo nižší jsou podporovány v úlohách s 6 nebo více službami SUs. Použití velmi velkých referenčních dat může mít vliv na koncovou latenci vaší úlohy. Vzhledem k tomu, že složitost dotazu se zvyšuje o zahrnutí stavového zpracování, jako jsou například agregace oken, dočasné spojení a dočasné analytické funkce, je očekáváno, že se maximální podporovaná velikost referenčních dat zkrátí. Pokud Azure Stream Analytics nemůže načíst referenční data a provádět složité operace, úloha nebude mít dostatek paměti a selže. V takových případech bude metrika využití% SU dostupná 100%.    

|**Počet jednotek streamování**  |**Doporučená velikost**  |
|---------|---------|
|1   |50 MB nebo méně   |
|3   |150 MB nebo méně   |
|6 a více   |5 GB nebo méně    |

Podpora komprese není pro referenční data k dispozici.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Propojení více datových sad odkazů v rámci úlohy
V jednom kroku dotazu se můžete spojit pouze s jedním vstupem datového proudu s jedním vstupem referenčních dat. Můžete však spojit více datových sad s více odkazy tím, že svůj dotaz rozdělíte do více kroků. Příklad najdete níže.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>Úlohy IoT Edge

Pro Stream Analytics hraničních úloh jsou podporována pouze místní referenční data. Při nasazení úlohy do IoT Edge zařízení načte referenční data z uživatelsky definované cesty k souboru. V zařízení musí být připravený referenční datový soubor. V případě kontejneru Windows umístěte soubor referenčních dat na místní disk a sdílejte místní disk s kontejnerem Docker. V případě kontejneru pro Linux vytvořte svazek Docker a naplňte do něj datový soubor.

Referenční data na IoT Edge Update se aktivují nasazením. Po aktivaci modul Stream Analytics vybere aktualizovaná data bez zastavení spuštěné úlohy.

Existují dva způsoby, jak aktualizovat referenční data:

* Aktualizujte cestu referenčních dat v Stream Analytics úlohy z Azure Portal.

* Aktualizujte nasazení IoT Edge.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
