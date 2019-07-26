---
title: Vysvětlení vytvořené jako výstupy z Azure Stream Analytics
description: Tento článek popisuje možnosti výstupní data k dispozici ve službě Azure Stream Analytics, včetně Power BI pro výsledky analýzy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: a0da13e82811d500dee50c2231500245c7e011a6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383444"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Vysvětlení vytvořené jako výstupy z Azure Stream Analytics

Tento článek popisuje typy výstupů, které jsou k dispozici pro úlohu Azure Stream Analytics. Výstupy umožňují ukládat a uložit výsledky úlohy Stream Analytics. Pomocí výstupních dat můžete provádět další obchodní analýzy a datové sklady vašich dat.

Pokud navrhujete Stream Analytics dotaz, přečtěte si název výstupu pomocí [klauzule into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování (Pokud je potřebujete) zadáním více klauzulí INTO v dotazu.

Chcete-li vytvářet, upravovat a testovat Stream Analytics výstupy úlohy, můžete použít [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), rozhraní [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)a [Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy výstupů podporují [dělení](#partitioning). [Velikosti výstupních dávek](#output-batch-size) se liší pro optimalizaci propustnosti.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen1

Stream Analytics podporuje [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage je škálovatelné úložiště s velkými objemy dat v celé organizaci. Data Lake Storage můžete použít k ukládání dat libovolné velikosti, typu a rychlosti příjmu pro provozní a průzkumné analýzy. Stream Analytics musí mít oprávnění pro přístup k Data Lake Storage.

Azure Data Lake Storage výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Data Lake Storageho výstupu 1. generace.   

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název, který se v dotazech používá k přímému nasměrování výstupu dotazu na Data Lake Store. |
| Subscription | Předplatné, které obsahuje váš účet Azure Data Lake Storage. |
| Název účtu | Název účtu Data Lake Store, do kterého posíláte výstup. Zobrazí se rozevírací seznam Data Lake Store účtů, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru, která se používá k zápisu souborů v rámci zadaného Data Lake Store účtu. Můžete zadat jednu nebo více instancí proměnných {Date} a {Time}:<br /><ul><li>Příklad 1: složku1/logs / {date} / {time}</li><li>Příklad 2: složku1/logs / {date}</li></ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pokud vzor cesty k souboru neobsahuje koncové lomítko (/), považuje se poslední vzor v cestě k souboru za předponu názvu souboru. <br /><br />Vytvoří se nové soubory v těchto případech:<ul><li>Změna v schéma výstupu</li><li>Externí nebo interní restartování úlohy</li></ul> |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
|Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány.|
| Kódování | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. V tuto chvíli je jediným podporovaným formátem kódování UTF-8.|
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára.|
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu.|
| Režim ověřování | Přístup k účtu Data Lake Storage můžete autorizovat pomocí [spravované identity](stream-analytics-managed-identities-adls.md) nebo tokenu uživatele. Po udělení přístupu můžete přístup odvolat změnou hesla uživatelského účtu, odstraněním Data Lake Storageho výstupu této úlohy nebo odstraněním úlohy Stream Analytics. |

## <a name="sql-database"></a>SQL Database

Můžete použít [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako výstup pro data, která jsou v podstatě relační nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Stream Analytics úlohy zapisují do existující tabulky v SQL Database. Schéma tabulky musí přesně odpovídat polím a jejich typům ve výstupu vaší úlohy. Můžete také zadat [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako výstup pomocí možnosti SQL Database Output. Další informace o způsobech zvýšení propustnosti zápisu naleznete v článku [Stream Analytics s Azure SQL Database jako výstup](stream-analytics-sql-output-perf.md) .

Následující tabulka uvádí seznam názvů vlastností a jejich popis pro vytvoření výstupu SQL Database.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu k této databázi. |
| Databáze | Název databáze, do které posíláte výstup. |
| Název serveru | Název databáze SQL serveru. |
| Uživatelské jméno | Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Table | Název tabulky, kde je zapsán výstup. V názvu tabulky se rozlišují velká a malá písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typům vygenerovaných výstupem úlohy. |
|Zdědit schéma oddílu| Možnost dědění schématu dělení vašeho předchozího dotazu, která umožňuje úplnou paralelní topologii s více zapisovači v tabulce. Další informace najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximální počet v dávce| Doporučený horní limit počtu záznamů odeslaných při každé hromadné vložené transakci.|

> [!NOTE]
> Nabídka Azure SQL Database je podporovaná pro výstup úlohy v Stream Analytics, ale virtuální počítač Azure se spuštěným SQL Server s připojenou databází nebo ve spravované instanci SQL Azure ještě není podporovaný. To se může v budoucích verzích změnit.

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Úložiště objektů BLOB a Azure Data Lake Gen2

Výstup do Azure Data Lake Gen2 se nabízí jako funkce ve verzi Preview v omezených oblastech po celém světě. Můžete požádat o přístup k verzi Preview poskytnutím dalších podrobností ve [formuláři žádosti](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).

Azure Blob Storage nabízí nákladově efektivní a škálovatelné řešení pro ukládání velkých objemů nestrukturovaných dat v cloudu. Úvod do úložiště objektů BLOB a jeho použití najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu objektu BLOB.

| Název vlastnosti       | Popis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto úložiště objektů blob. |
| Účet úložiště     | Název účtu úložiště, do kterého posíláte výstup.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště.                              |
| Kontejner úložiště   | Logické seskupení pro objekty blob uložené v Azure Blob service. Při nahrání objektu blob do služby Blob service, je nutné zadat kontejner pro tohoto objektu blob. |
| Vzor cesty | Volitelné. Vzor cesty k souboru, který se používá k zápisu objektů BLOB v zadaném kontejneru. <br /><br /> Ve vzoru cesty se můžete rozhodnout použít jednu nebo více instancí proměnných data a času k určení četnosti zápisu objektů BLOB: <br /> {date} {time} <br /><br />Vlastní dělení objektů blob můžete použít k určení jednoho vlastního názvu {Field} z dat události pro vytvoření oddílů objektů BLOB. Název pole je alfanumerický a může obsahovat mezery, pomlčky a podtržítka. Omezení na vlastních polích, patří: <ul><li>V názvech polí se nerozlišují malá a velká písmena. Například služba nemůže odlišit sloupce "ID" a "ID".</li><li>Vnořená pole nejsou povolena. Místo toho použijte alias v dotazu úlohy na možnost "sloučit" pole.</li><li>Výrazy nelze použít jako název pole.</li></ul> <br />Tato funkce povoluje použití vlastních konfigurací specifikátoru formátu data a času v cestě. Vlastní datum a čas formátu musí být zadaný jeden po druhém, ohraničená {data a času:\<specifikátor >} – klíčové slovo. Povolené vstupy pro \<specifikátor > jsou rrrr, mm, m, DD, d, hh, H, mm, m, SS nebo s. Klíčové slovo {DateTime\<: specifikátor >} lze v cestě použít několikrát k vytvoření vlastních konfigurací data a času. <br /><br />Příklady: <ul><li>Příklad 1: cluster1/logs / {date} / {time}</li><li>Příklad 2: cluster1/logs / {date}</li><li>Příklad 3: Cluster1/{client_id}/{Date}/{Time}</li><li>Příklad 4: Cluster1/{DateTime: SS}/{myField}, kde je dotaz: Vyberte data. myField jako myField ze vstupu;</li><li>Příklad 5: Cluster1/Year = {DateTime: rrrr}/month = {DateTime: MM}/Day = {DateTime: DD}</ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pojmenovávání souborů používá následující konvenci: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Příklad výstupní soubory:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Další informace o této funkci najdete v tématu [Azure Stream Analytics vlastního dělení výstupu objektů BLOB](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
| Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV, Avro a Parquet. |
|Minimální počet řádků (pouze Parquet)|Počet minimálních řádků na dávku V případě Parquet bude každá dávka vytvářet nový soubor. Aktuální výchozí hodnota je 2 000 řádků a povolené maximum je 10 000 řádků.|
|Maximální doba (jenom Parquet)|Maximální doba čekání na dávku. Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že požadavek na minimální řádky není splněn. Aktuální výchozí hodnota je 1 minuta a povolené maximum je 2 hodiny. Pokud váš výstup objektu BLOB má četnost vzorů cesty, doba čekání nesmí být vyšší než časový rozsah oddílu.|
| Kódování    | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač   | Platí pouze pro serializaci CSV. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát      | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu. |

Pokud jako výstup používáte úložiště objektů blob, vytvoří se v objektu BLOB nový soubor v následujících případech:

* Pokud soubor překračuje maximální počet povolených bloků (aktuálně 50 000). Můžete dosáhnout maximálního povoleného počtu bloků, aniž byste dosáhli maximální povolené velikosti objektu BLOB. Při vysoká míra výstupních uvidíte počet bajtů za bloku a velikost souboru je větší. Pokud je míra výstupních nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud se ve výstupu změní schéma a výstupní formát vyžaduje pevné schéma (CSV a Avro).
* Pokud restartování úlohy, externí uživatele, zastavte ho a potom ji spustit, nebo interně údržby nebo Chyba obnovení systému.
* Pokud je dotaz plně rozdělený a vytvoří se nový soubor pro každý výstupní oddíl.
* Pokud uživatel odstraní soubor nebo kontejner účtu úložiště.
* Pokud je výstupem čas rozdělení na oddíly pomocí vzoru předpony cesty a nový objekt BLOB se použije, když se dotaz přesune na další hodinu.
* Pokud je výstup rozdělený vlastním polem, vytvoří se nový objekt BLOB pro každý klíč oddílu, pokud neexistuje.
* Pokud je výstup rozdělený vlastním polem, u kterého klíč oddílu překračuje 8 000, a pro každý klíč oddílu se vytvoří nový objekt BLOB.

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) služba je vysoce škálovatelná publikování a odběr schopná. Může shromažďovat miliony událostí za sekundu. Jedním z nich je použití centra událostí jako výstup, když se výstup Stream Analytics úlohy stal vstupem jiné úlohy streamování.

Pro konfiguraci datových proudů z Center událostí jako výstupu potřebujete několik parametrů.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název, který se používá v dotazech k nasměrování výstupu dotazu do tohoto centra událostí. |
| Obor názvů centra událostí | Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste taky vytvořili obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásady centra událostí | Zásady sdíleného přístupu, které můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů centra událostí. |
| Sloupec s klíčem oddílu | Volitelné. Sloupec obsahující klíč oddílu pro výstup centra událostí |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování | CSV a JSON UTF-8 je jediný podporovaný formát kódování v tuto chvíli. |
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu. |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) můžete použít jako výstup pro úlohu Stream Analytics a poskytnout tak bohatou vizualizaci výsledků analýzy. Tuto možnost můžete použít pro provozní řídicí panely, generování sestav a vytváření sestav řízených metrikami.

Power BI výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Power BIho výstupu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Zadejte popisný název, který se použije v dotazech k nasměrování výstupu dotazu do tohoto Power BI výstupu. |
| Pracovní prostor skupiny |Pokud chcete povolit sdílení dat s jinými Power BI uživateli, můžete vybrat skupiny v rámci účtu Power BI nebo zvolit **pracovní prostor** , pokud ho nechcete zapisovat do skupiny. Aktualizuje se existující skupina vyžaduje obnovování ověřování Power BI. |
| Název datové sady |Zadejte název datové sady, který má výstup Power BI použít. |
| Název tabulky |Zadejte název tabulky v datové sadě Power BI výstupu. V současné době může Power BI výstup z úloh Stream Analytics obsahovat pouze jednu tabulku v datové sadě. |
| Autorizovat připojení | Abyste mohli konfigurovat nastavení výstupu, musíte se autorizovat s Power BI. Po udělení tohoto výstupního přístupu vašemu řídicímu panelu Power BI můžete odvolat přístup změnou hesla uživatelského účtu, odstraněním výstupu úlohy nebo odstraněním úlohy Stream Analytics. | 

Návod ke konfiguraci Power BIho výstupu a řídicího panelu najdete v kurzu [Azure Stream Analytics a Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Datovou sadu a tabulku nevytvářejte explicitně na řídicím panelu Power BI. Datová sada a tabulka se automaticky naplní při zahájení úlohy a úloha začne vyčerpat výstup do Power BI. Pokud dotaz úlohy negeneruje žádné výsledky, datová sada a tabulka se nevytvoří. Pokud Power BI již měla datovou sadu a tabulku se stejným názvem jako v této Stream Analytics úlohy, stávající data budou přepsána.
>

### <a name="create-a-schema"></a>Vytvořte schéma
Azure Stream Analytics vytvoří datovou sadu Power BI a schéma tabulky pro uživatele, pokud ještě neexistují. Ve všech ostatních případech se aktualizuje tabulku s novými hodnotami. V současné době může existovat pouze jedna tabulka v rámci datové sady. 

Power BI používá zásady uchovávání dat first in, First-out (FIFO). Data se budou shromažďovat v tabulce, dokud nebude mít 200 000 řádků.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Převod datového typu z Stream Analytics na Power BI
Azure Stream Analytics aktualizuje datový model dynamicky za běhu, pokud se změní schéma výstupu. Změny názvů sloupců, změny typu sloupce a přidání nebo odebrání sloupců jsou všechny sledovány.

Tato tabulka popisuje převody datových typů z [Stream Analytics typy dat](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [model EDM (Entity Data Model) (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), pokud Power BI datová sada a tabulka neexistuje.

Ze služby Stream Analytics | Do Power BI
-----|-----
bigint | Int64
nvarchar(max) | Řetězec
datetime | Datum a čas
float | Double
Pole záznamu | Typ řetězce, konstantní hodnota "IRecord" nebo "IArray"

### <a name="update-the-schema"></a>Aktualizace schématu
Stream Analytics odvodí schéma modelu dat založené na první sadu událostí. ve výstupu. Později v případě potřeby se schéma datového modelu aktualizuje tak, aby odpovídal příchozím událostem, které se nemusí vejít do původního schématu.

Nepoužívejte `SELECT *` dotaz, aby nedocházelo k dynamické aktualizaci schématu napříč řádky. Kromě potenciálních dopadů na výkon může být výsledkem nejistota doba trvání výsledků. Vyberte přesná pole, která se musí zobrazit na řídicím panelu Power BI. Kromě toho musí být hodnoty dat kompatibilní s vybraným datovým typem.


Předchozí/aktuální | Int64 | Řetězec | Datum a čas | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | Double
Double | Double | Řetězec | Řetězec | Double
Řetězec | String | String | String | Řetězec 
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec

## <a name="table-storage"></a>Úložiště tabulek

[Azure Table Storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné, široce škálovatelné úložiště, aby se aplikace mohla automaticky škálovat tak, aby splňovala požadavky uživatelů. Table Storage je úložiště NoSQLch klíčů a atributů od Microsoftu, které můžete použít pro strukturovaná data s menším omezením schématu. Azure Table storage můžete použít k ukládání dat pro trvalost a efektivní načtení.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu tabulky.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech pro přesměrování výstupu dotazu do této tabulky úložiště. |
| Účet úložiště |Název účtu úložiště, do kterého posíláte výstup. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště. |
| Název tabulky |Název tabulky. Tabulka se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce, který obsahuje klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v tabulce, který tvoří první část primárního klíče entity. Je to hodnota řetězce, která může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce, který obsahuje klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí hodnota (100) je dostatečné pro většinu úloh. Další informace o úpravách tohoto nastavení najdete v článku [specifikace operace dávky tabulky](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) . |

## <a name="service-bus-queues"></a>Fronty služby Service Bus

[Fronty Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) nabízejí doručování zpráv typu FIFO jednomu nebo více konkurenčním spotřebitelům. Obvykle jsou zprávy přijímány a zpracovávány příjemci v dočasném pořadí, ve kterém byly přidány do fronty. Každá zpráva je přijata a zpracována pouze jedním příjemcem zprávy.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu fronty.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do této Service Bus fronty. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit zasílání zpráv. |
| Název fronty |Název fronty Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** fronty. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |CSV a JSON UTF-8 je jediný podporovaný formát kódování v tuto chvíli. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát |Platí pouze pro typ JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.

## <a name="service-bus-topics"></a>Témata služby Service Bus
Fronty Service Bus poskytují metodu komunikace 1:1 od odesílatele k přijímači. [Service Bus témata](https://msdn.microsoft.com/library/azure/hh367516.aspx) poskytují vzájemnou formu komunikace 1: n.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu Service Bus tématu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do tohoto Service Bus tématu. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor názvů Service Bus. |
| Název tématu |Témata jsou entity pro zasílání zpráv, podobné centrům událostí a frontám. Jsou navržené ke shromažďování datových proudů událostí ze zařízení a služeb. Při vytvoření tématu se také zobrazí konkrétní název. Zprávy odeslané do tématu nejsou k dispozici, dokud nevytvoříte odběr, a zajistěte, aby bylo v tématu jedno nebo více předplatných. |
| Název zásad tématu |Když vytvoříte Service Bus téma, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** tématu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad tématu |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná databázová služba, která nabízí neomezené Elastické škálování po celém světě, bohatě vydaný dotaz a automatické indexování prostřednictvím datových modelů nezávislá schématu. Další informace o možnostech kontejneru Azure Cosmos DB pro Stream Analytics naleznete v článku [Stream Analytics s Azure Cosmos DB jako výstup](stream-analytics-documentdb-output.md) .

Azure Cosmos DB výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje připojení pouze k Azure Cosmos DB pomocí rozhraní SQL API.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně.

Následující tabulka popisuje vlastnosti pro vytvoření výstup Azure Cosmos DB.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Alias pro najdete na tomto výstup v dotazu Stream Analytics. |
| Jímka | Azure Cosmos DB. |
| Možnost importu | Zvolte buď **možnost vybrat Cosmos DB z** předplatného, nebo **Zadejte Cosmos DB nastavení ručně**.
| ID účtu | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB. |
| Klíč účtu | Sdílený přístupový klíč pro účet Azure Cosmos DB. |
| Databáze | Azure Cosmos DB název databáze. |
| Název kontejneru | Název kontejneru, který se má použít, který musí existovat v Cosmos DB. Příklad:  <br /><ul><li> _MyContainer_: Musí existovat kontejner s názvem "MyContainer".</li>|
| ID dokumentu |Volitelné. Název pole ve výstupních událostech, které slouží k určení primárního klíče, na kterém jsou založeny operace INSERT nebo Update.

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní služba bez serveru, kterou můžete použít ke spouštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Umožňuje implementovat kód, který se aktivuje událostmi, ke kterým dochází v Azure nebo v partnerských službách. Tato schopnost Azure Functions reagovat na triggery, vytvoří přirozený výstup pro Azure Stream Analytics. Tento výstupní adaptér umožňuje uživatelům připojení Stream Analytics k Azure Functions a spuštění skriptu nebo části kódu v reakci na nejrůznější události.

Azure Functions výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

Azure Stream Analytics volá Azure Functions prostřednictvím triggerů HTTP. Azure Functions výstupní adaptér je k dispozici s následujícími konfigurovatelnými vlastnostmi:

| Název vlastnosti | Popis |
| --- | --- |
| Function App |Název vaší aplikace Azure Functions. |
| Funkce |Název funkce v aplikaci Azure Functions. |
| Klíč |Pokud chcete používat funkci Azure Functions z jiného předplatného, můžete to udělat tak, že pro přístup k funkci poskytnete klíč. |
| Maximální velikost dávky |Vlastnost, která umožňuje nastavit maximální velikost pro každou výstupní dávku, která se odesílá do funkce Azure Functions. Vstupní jednotka je v bajtech. Ve výchozím nastavení je tato hodnota 262 144 bajtů (256 KB). |
| Maximální počet v dávce  |Vlastnost, která umožňuje určit maximální počet událostí v každé dávce, která je odeslána do Azure Functions. Výchozí hodnota je 100. |

Když Azure Stream Analytics obdrží výjimku ("413 je příliš velká entita požadavku HTTP) z funkce Azure, zmenší velikost balíků, které posílá do Azure Functions. V kódu funkce Azure Ujistěte se, že Azure Stream Analytics neodesílá dávky nadměrné velikosti neposílá pomocí této výjimky. Také se ujistěte, že maximální počet dávek a hodnoty velikosti použité ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

V situaci, kdy není žádné přistání události v časovém intervalu, není vygenerován žádný výstup. V důsledku toho není volána funkce **computeResult** . Toto chování je konzistentní s integrovanou oddílové agregační funkce.

## <a name="custom-metadata-properties-for-output"></a>Vlastnosti vlastních metadat pro výstup 

Sloupce dotazu můžete k odchozím zprávám připojit jako vlastnosti uživatele. Tyto sloupce neobsahují datovou část. Vlastnosti jsou k dispozici ve formě slovníku ve výstupní zprávě. *Klíč* je název sloupce a *hodnota* je hodnota sloupce ve slovníku Properties (vlastnosti). Všechny datové typy Stream Analytics jsou podporovány kromě záznamu a pole.  

Podporované výstupy: 
* Fronta služby Service Bus 
* Téma služby Service Bus 
* Centrum událostí 

V následujícím příkladu přidáme dvě pole `DeviceId` a `DeviceStatus` metadata. 
* Zadávání`select *, DeviceId, DeviceStatus from iotHubInput`
* Konfigurace výstupu:`DeviceId,DeviceStatus`

![Sloupce vlastností](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Následující snímek obrazovky ukazuje vlastnosti výstupní zprávy, které byly zkontrolovány v centru EventHub prostřednictvím [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

![Vlastní vlastnosti události](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílu a počet modulů pro zápis výstupu pro každý typ výstupu:

| Typ výstupu | Dělení podpory | Klíč oddílu  | Počet modulů pro zápis výstupu |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Použijte {Date} a {Time} tokens ve vzoru předpony cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. HH se používá pro formát času. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ano, je nutné povolit. | Na základě klauzule PARTITION BY v dotazu. | Když je povolená možnost zdědit rozdělení na oddíly, řídí se vstupní oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lepšího výkonu propustnosti zápisu při načítání dat do Azure SQL Database naleznete v tématu [Azure Stream Analytics Output to Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ano | Použijte {Date} a {Time} tokeny z polí události ve vzoru cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. HH se používá pro formát času. Výstup objektu BLOB se dá rozdělit na oddíly jedním vlastním atributem události {FieldName} nebo {DateTime\<: specifikátorem >}. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ano | Ano | Se liší v závislosti na zarovnání oddílu.<br /> Když je klíč oddílu pro výstup centra událostí rovnoměrně zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů ve výstupu centra událostí. Každý zapisovač používá ke posílání událostí do konkrétního oddílu [třídu EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) . <br /> Pokud klíč oddílu pro výstup centra událostí není zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů v předchozím kroku. Každý zapisovač používá v **EventHubClient** [třídu SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) k odesílání událostí do všech výstupních oddílů. |
| Power BI | Ne | Žádný | Není k dispozici. |
| Azure Table Storage | Ano | Výstup je sloupec.  | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Azure tématu služby Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.| Stejný jako počet oddílů tématu výstup.  |
| Fronty Azure Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.| Stejný jako počet oddílů ve výstupní frontě. |
| Azure Cosmos DB | Ano | Na základě klauzule PARTITION BY v dotazu. | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ne | Žádný | Není k dispozici. |

Počet výstupních zapisovačů lze také ovládat pomocí `INTO <partition count>` klauzule (viz [v](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)tématu) v dotazu, což může být užitečné při dosahování požadované topologie úlohy. Pokud adaptér pro výstup není rozdělena na oddíly, chybějící data v jednom oddílu vstupní způsobí zpoždění až pozdní doručení množství času. V takových případech se výstup sloučí do jediného zapisovače, což může způsobit kritické body ve vašem kanálu. Další informace o zásadách pozdního doručení najdete v tématu [Azure Stream Analytics požadavky na pořadí událostí](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost dávky výstupu
Azure Stream Analytics používá dávky variabilní velikosti pro zpracování událostí a zápis do výstupů. Modul Stream Analytics obvykle nezapisuje jednu zprávu najednou a využívá dávky k efektivitě. Když je vysoká míra příchozích i odchozích událostí, Stream Analytics využívá větší počet dávek. Po nízká frekvence odchozího přenosu dat se používá po menších dávkách zachovat s nízkou latencí.

Následující tabulka popisuje některé z důležitých informací pro výstup dávkování:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikosti dávky |
| :--- | :--- | :--- |
| Azure Data Lake Store | Viz [omezení Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits). | Pro každou operaci zápisu použijte až 4 MB. |
| Azure SQL Database | Konfigurovatelné pomocí maximálního počtu dávek. 10 000 maximální a 100 minimální počet řádků na jedno hromadné vložení ve výchozím nastavení.<br />Viz [omezení Azure SQL](../sql-database/sql-database-resource-limits.md). |  Každá dávka je zpočátku hromadně vložena s maximálním počtem dávek. Dávka je rozdělena na polovinu (až do minimálního počtu dávek) na základě opakovaných chyb z SQL. |
| Azure Blob Storage | Viz [omezení Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Maximální velikost bloku objektu BLOB je 4 MB.<br />Maximální počet Bock objektů BLOB je 50 000. |
| Azure Event Hubs  | 256 KB nebo 1 MB na zprávu <br />Viz [omezení Event Hubs](../event-hubs/event-hubs-quotas.md). |  Když není zarovnaný vstup/výstup dělení, každá událost se zabalí jednotlivě `EventData` v dávce a pošle se do maximální velikosti zprávy. K tomu dojde také v případě, že se používají [vlastní vlastnosti metadat](#custom-metadata-properties-for-output) . <br /><br />  Při zarovnávání vstupních/výstupních dělení se více událostí balí do jedné `EventData` instance, až do maximální velikosti zprávy a pošle se. |
| Power BI | Viz [omezení Power BI rozhraní REST API](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Viz [omezení Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Výchozí hodnota je 100 entit na jednu transakci. V případě potřeby ho můžete nakonfigurovat na menší hodnotu. |
| Fronty Azure Service Bus   | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [omezení Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost na zprávu. |
| Azure tématu služby Service Bus | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [omezení Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost na zprávu. |
| Azure Cosmos DB   | Viz [omezení Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Velikost dávky a frekvence zápisu se dynamicky upravují na základě Azure Cosmos DBch odpovědí. <br /> Neexistují předem vymezená omezení Stream Analytics. |
| Azure Functions   | | Výchozí velikost dávky je 262 144 bajtů (256 KB). <br /> Výchozí počet událostí na jednu dávku je 100. <br /> Velikost dávky je možné konfigurovat a můžete se zvýší nebo sníží ve službě Stream Analytics [výstup možnosti](#azure-functions).

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> 
> [Rychlé zprovoznění: Vytvoření Stream Analytics úlohy pomocí Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
