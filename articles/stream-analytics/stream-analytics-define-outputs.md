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
ms.openlocfilehash: ef2a55b377c2ca48b9417310926a014a82f679d7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621881"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Vysvětlení vytvořené jako výstupy z Azure Stream Analytics

Tento článek popisuje typy výstupy, které jsou k dispozici pro úlohy Azure Stream Analytics. Výstupy umožňují ukládat a uložit výsledky úlohy Stream Analytics. Když použijete výstupní data, můžete provést další obchodní analýzy a skladování dat vaše data.

Při návrhu dotazu Stream Analytics, odkazovat na název výstupu pomocí [klauzule INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování (Pokud je potřebujete) tím, že poskytuje více klauzulí INTO v dotazu.

K vytváření, úpravám a testovací úlohy Stream Analytics výstupy, můžete použít [webu Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [prostředí Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [rozhraní REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), a [sady Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy podporu výstupy [dělení](#partitioning). [Výstup velikosti dávky](#output-batch-size) umožňuje optimalizovat propustnost se liší.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen1

Stream Analytics podporuje [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage je podnikové úrovni velkokapacitní úložiště pro analytické úlohy s velkými objemy dat. Data Lake Storage můžete použít k ukládání dat libovolné velikosti, typu a rychlosti příjmu pro provozní a zjišťovací analýzy. Stream Analytics musí mít oprávnění k přístupu k Data Lake Storage.

Azure Data Lake Storage výstup ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China 21Vianet.

Následující tabulka uvádí názvy vlastností a jejich popisy nakonfigurovat výstup do Data Lake Storage Gen 1.   

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přímé dotazování výstup do Data Lake Store. |
| Subscription | Předplatné obsahující účet Azure Data Lake Storage. |
| Název účtu | Název účtu Data Lake Store, které e-mail posíláte výstupu. Zobrazí se rozevírací seznam účtů Data Lake Store, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru, který se používá k zápisu souborů v rámci zadaného účtu Data Lake Store. Můžete zadat jednu nebo víc instancí {date} a {time} proměnné:<br /><ul><li>Příklad 1: složku1/logs / {date} / {time}</li><li>Příklad 2: složku1/logs / {date}</li></ul><br />Časové razítko struktura vytvořená složka se řídí není místním časem a UTC.<br /><br />Pokud vzor cesty souboru nemůže obsahovat koncové lomítko (/), je považován za předponu názvu souboru poslední vzorek v cestě k souboru. <br /><br />Vytvoří se nové soubory v těchto případech:<ul><li>Změna v schéma výstupu</li><li>Externí nebo interní restartovat úlohy</li></ul> |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
|Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány.|
| Kódování | Pokud používáte formát CSV nebo JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8.|
| Oddělovač | Vztahuje se pouze pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára.|
| Formát | Vztahuje se pouze pro serializaci JSON. **Řádcích** : Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. **Pole** : Určuje takový formát výstupu jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít oddělené řádku JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do.|
| Režim ověřování | Můžete povolit přístup ke svému účtu Data Lake Storage pomocí [Identity spravované](stream-analytics-managed-identities-adls.md) nebo token uživatele. Jakmile povolíte přístup, můžete odvolat přístup ve změně hesla uživatelského účtu, odstranění výstupní Data Lake Storage pro tuto úlohu nebo odstranění úlohy Stream Analytics. |

## <a name="sql-database"></a>SQL Database

Můžete použít [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako výstup pro data, která jsou svou povahou relační, nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Úlohy Stream Analytics zapisovat do existující tabulky ve službě SQL Database. Schéma tabulky musí přesně odpovídat pole a jejich typy ve výstupu vaší úlohy. Můžete také určit [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) možnost výstupu jako výstup prostřednictvím SQL Database. Další informace o způsoby, jak zlepšit propustnost zápis, najdete v článku [Stream Analytics se službou Azure SQL Database jako výstup](stream-analytics-sql-output-perf.md) článku.

Následující tabulka uvádí názvy vlastností a jejich popis vytvářejícím výstupem SQL Database.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu k této databázi. |
| Databáze | Název databáze, které e-mail posíláte výstupu. |
| Název serveru | Název databáze SQL serveru. |
| Uživatelské jméno | Uživatelské jméno, který má oprávnění k zápisu do databáze. Stream Analytics podporuje jenom ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Table | Název tabulky, kde je zapsán výstup. Název tabulky je velká a malá písmena. Schéma této tabulky by měl přesně shodovat s počtem pole a jejich typy, které generuje výstup úlohy. |
|Dědit schéma oddílu| Možnost pro dědění schéma rozdělení oddílů z předchozího kroku dotazu, umožňují plně paralelní topologie s více zapisovačů do tabulky. Další informace najdete v tématu [výstupu Azure Stream Analytics ke službě Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximální počet v dávce| Doporučené horní limit počtu záznamy odeslané s každou hromadné vložení transakce.|

> [!NOTE]
> Azure SQL Database nabízí, je podporováno pro výstup úlohy ve Stream Analytics, ale virtuální počítač Azure, které používají verzi SQL Server s databází připojen nebo spravované Instance SQL Azure se ještě nepodporuje. To se může v budoucích verzích změnit.

## <a name="blob-storage"></a>Blob Storage

Azure Blob storage nabízí nákladově efektivní a škálovatelné řešení pro ukládání velkých objemů nestrukturovaných dat v cloudu. Úvod do úložiště objektů Blob a jeho použití, naleznete v tématu [nahrávání, stahování a výpis objektů BLOB pomocí webu Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md).

Následující tabulka uvádí názvy vlastností a jejich popisy pro vytvoření výstupního objektu blob.

| Název vlastnosti       | Popis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto úložiště objektů blob. |
| Účet úložiště     | Název účtu úložiště, které e-mail posíláte výstupu.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště.                              |
| Kontejner úložiště   | Logické seskupení pro objekty BLOB uložené ve službě Azure Blob service. Při nahrání objektu blob do služby Blob service, je nutné zadat kontejner pro tohoto objektu blob. |
| Vzor cesty | Volitelné. Vzor cesty souboru, který se používá k zápisu objektů BLOB v zadaném kontejneru. <br /><br /> V vzor cesty můžete použít jednu nebo víc instancí proměnných datum a čas pro určení četnosti, které jsou napsané objektů blob: <br /> {date} {time} <br /><br />Dělení vlastních objektů blob můžete zadat jeden název vlastní {pole} ze svých dat událostí do oddílu objektů BLOB. Název pole je alfanumerický a může obsahovat mezery, pomlčky a podtržítka. Omezení na vlastních polích, patří: <ul><li>Názvy polí nejsou malá a velká písmena. Například nelze službu rozlišovat mezi sloupci "ID" a "id sloupce."</li><li>Vnořená pole nejsou povolené. Místo toho použijte alias v dotazu úlohy k "vyrovnání" pole.</li><li>Výrazy nelze použít jako název pole.</li></ul> <br />Tato funkce umožňuje, aby použití konfigurací specifikátor formátu vlastní data a času v cestě. Vlastní datum a čas formátu musí být zadaný jeden po druhém, ohraničená {data a času:\<specifikátor >} – klíčové slovo. Povolené vstupy pro \<specifikátor > jsou rrrr, MM, M, dd, d, HH, H, mm, m, ss nebo s. {Data a času:\<specifikátor >} – klíčové slovo lze použít více než jednou v cestě k vytvoření konfigurace vlastní data a času. <br /><br />Příklady: <ul><li>Příklad 1: cluster1/logs / {date} / {time}</li><li>Příklad 2: cluster1/logs / {date}</li><li>Příklad 3: cluster1 / {client_id} / {date} / {time}</li><li>Příklad 4: cluster1 / {datetime:ss} / {myField} dotaz, kde je: Vyberte data.myField jako myField ze vstupu;</li><li>Příklad 5: cluster1/rok = {datetime:yyyy} / měsíc = {datetime:MM} za den = {datetime:dd}</ul><br />Časové razítko struktura vytvořená složka se řídí není místním časem a UTC.<br /><br />Pojmenovávání souborů používá tato konvence: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Příklad výstupní soubory:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Další informace o této funkci najdete v tématu [objektů blob Azure Stream Analytics, vlastní, výstup dělení](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
| Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování    | Pokud používáte formát CSV nebo JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač   | Vztahuje se pouze pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát      | Vztahuje se pouze pro serializaci JSON. **Řádcích** : Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. **Pole** : Určuje takový formát výstupu jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít oddělené řádku JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do. |

Při použití úložiště objektů Blob jako výstup, je vytvořen nový soubor v objektu blob v následujících případech:

* Pokud soubor překračuje maximální počet povolených bloků (aktuálně 50 000). Maximální povolený počet bloků může dosáhnout nedorazí velikost objektu blob maximální povolené. Při vysoká míra výstupních uvidíte počet bajtů za bloku a velikost souboru je větší. Pokud je míra výstupních nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud dojde ke změně schématu ve výstupu a výstupní formát vyžaduje pevného schématu (CSV nebo Avro).
* Pokud restartování úlohy, externí uživatele, zastavte ho a potom ji spustit, nebo interně údržby nebo Chyba obnovení systému.
* Pokud je dotaz dělený plně a je vytvořen nový soubor pro každý oddíl výstup.
* Pokud uživatel odstraní soubor nebo kontejner účtu úložiště.
* Pokud výstupem je čas rozdělit na oddíly pomocí vzor předpony cesty a nový objekt blob se používá, pokud se dotaz přesune do příští hodiny.
* Pokud výstup je rozdělený podle vlastní pole a za if klíče oddílu je vytvořen nový objekt blob neexistuje.
* Pokud výstup je rozdělená na oddíly pomocí vlastního pole, kde klíč oddílu Kardinalita větší než 8 000 a nový objekt blob se vytvoří každý klíč oddílu.

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) služba je vysoce škálovatelná publikování a odběr schopná. Může shromažďovat miliony událostí za sekundu. Jedno použití centra událostí jako výstup při vstupu jiná úloha streamování se stane výstup úlohy Stream Analytics.

Budete potřebovat několik parametrů ke konfiguraci datové proudy ze služby event hubs jako výstup.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do centra událostí. |
| Obor názvů centra událostí | Kontejner pro sadu entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásady centra událostí | Zásady sdíleného přístupu, které vytvoříte v Centru událostí **konfigurovat** kartu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k obor názvů centra událostí. |
| Sloupec klíče oddílu | Volitelné. Sloupec, který obsahuje klíč oddílu výstup z centra událostí. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování | CSV a JSON UTF-8 je jediný podporovaný formát kódování v tuto chvíli. |
| Oddělovač | Vztahuje se pouze pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát | Vztahuje se pouze pro serializaci JSON. **Řádcích** : Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. **Pole** : Určuje takový formát výstupu jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít oddělené řádku JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do. |
| Sloupce vlastností | Volitelné. Sloupce oddělených čárkou, které musí být připojené jako vlastnosti uživatele odchozí zprávy místo datové části. Další informace o této funkci se v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Můžete použít [Power BI](https://powerbi.microsoft.com/) jako výstup pro úlohu Stream Analytics k poskytování bohatých funkcí vizualizace poznatky o výsledky analýzy. Tuto funkci můžete použít pro provozní řídicích panelů, generování sestav a vytváření sestav řízené metriky.

Výstup Power BI ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China 21Vianet.

Následující tabulka uvádí názvy vlastností a jejich popisy nakonfigurovat výstup do Power BI.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Zadejte popisný název, který se používá v dotazech na přímé výstup dotazu na tento výstup Power BI. |
| Pracovní prostor skupiny |Pokud chcete povolit sdílení dat s dalšími uživateli Power BI, můžete vybrat skupiny, do účtu Power BI nebo jej vybrat **pracovní prostor** Pokud nechcete, aby k zápisu do skupiny. Aktualizuje se existující skupina vyžaduje obnovování ověřování Power BI. |
| Název datové sady |Zadejte název datové sady, který se má výstup Power BI používat. |
| Název tabulky |Zadejte název tabulky v datové sadě Power BI výstupu. V současné době Power BI výstup z úlohy Stream Analytics může mít pouze jednu tabulku v datové sadě. |
| Autorizovat připojení | Musíte se autorizovat s Power BI mohli konfigurovat svá nastavení výstupu. Jakmile udělit přístup výstup na řídicí panel Power BI, můžete odvolat přístup ve změně hesla uživatelského účtu, odstranění výstup úlohy nebo odstranění úlohy Stream Analytics. | 

Postup konfigurace výstup Power BI a řídicí panel, najdete v části [Azure Stream Analytics a Power BI](stream-analytics-power-bi-dashboard.md) kurzu.

> [!NOTE]
> Nevytvářejí explicitně datová sada a tabulky v řídicím panelu Power BI. Datová sada a tabulky se automaticky naplní při spuštění úlohy a úlohy spustí čerpací výstup do Power BI. Pokud dotaz úlohy negeneruje žádné výsledky, nevytvoří se datová sada a tabulky. Pokud Power BI už je datová sada a tabulky se stejným názvem, jaký byl zadán v této úloze Stream Analytics, je existující data přepsána.
>

### <a name="create-a-schema"></a>Vytvořte schéma
Azure Stream Analytics vytvoří schéma datové sady a tabulky Power BI pro uživatele, pokud ještě neexistují. Ve všech ostatních případech se aktualizuje tabulku s novými hodnotami. V současné době může existovat pouze jednu tabulku v datové sadě. 

Power BI používá first-in FIFO (FIFO) zásady uchovávání informací. V tabulce bude shromažďovat data, dokud nedosáhne 200 000 řádků.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Převést typ dat ze služby Stream Analytics na Power BI
Azure Stream Analytics aktualizuje datový model dynamicky za běhu, pokud se změní schéma výstupu. Změny názvů sloupců, změny typu sloupce a přidání nebo odebrání sloupců jsou všechny sledovány.

Tato tabulka popisuje převody typů dat z [Stream Analytics datové typy](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [typy Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), pokud neexistují tabulky a datová sada Power BI.

Ze služby Stream Analytics | Do Power BI
-----|-----
bigint | Int64
nvarchar(max) | Řetězec
datetime | Datum a čas
float | Double
Pole záznamu | Řetězec typu, konstanta "IRecord" nebo "IArray"

### <a name="update-the-schema"></a>Aktualizace schématu
Stream Analytics odvodí schéma modelu dat založené na první sadu událostí. ve výstupu. Později v případě potřeby se aktualizuje schéma modelu dat tak, aby vyhovovaly příchozí události, které nemusí odpovídat do původního schématu.

Vyhněte `SELECT *` dotaz, který brání dynamické schéma aktualizace více řádků. Kromě potenciální vliv na výkon může dojít k nejistoty čas potřebný pro výsledky. Vyberte přesně pole, které je potřeba zobrazený na řídicím panelu Power BI. Kromě toho musí být hodnoty dat kompatibilní s vybraným datovým typem.


Předchozí nebo aktuální | Int64 | Řetězec | Datum a čas | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | Double
Double | Double | Řetězec | Řetězec | Double
Řetězec | String | String | String | Řetězec 
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec

## <a name="table-storage"></a>Úložiště Table

[Azure Table storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné a široce škálovatelné úložiště, takže aplikace může automaticky škálovat podle požadavků uživatelů. Úložiště Table je úložiště klíčů/atributů typu NoSQL od Microsoftu, který můžete použít pro strukturovaná data s méně omezeními ve schématu. Azure Table storage můžete použít k ukládání dat pro trvalost a efektivní načtení.

Následující tabulka uvádí názvy vlastností a jejich popisy pro vytvoření výstupu tabulky.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech pro přesměrování výstupu dotazu do této tabulky úložiště. |
| Účet úložiště |Název účtu úložiště, které e-mail posíláte výstupu. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště. |
| Název tabulky |Název tabulky. V tabulce se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce obsahujícího klíč oddílu. Klíč oddílu je jedinečný identifikátor pro oddíl v tabulce, která tvoří první část primárního klíče entity. Je řetězec, který může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce obsahujícího klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí hodnota (100) je dostatečné pro většinu úloh. Najdete v článku [tabulky dávkové operace specifikace](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) další podrobnosti ke změně tohoto nastavení. |

## <a name="service-bus-queues"></a>Fronty služby Service Bus

[Fronty služby Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) nabízejí doručení zpráv FIFO na jeden nebo několik konkurenčních spotřebitelů. Zprávy jsou obvykle přijata a zpracovávají v pořadí, ve kterém byly přidány do fronty. Je každou zprávu přijme a zpracuje jenom jeden spotřebitel zprávy.

Následující tabulka uvádí názvy vlastností a jejich popisy pro vytvoření výstupní fronty.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech pro přesměrování výstupu dotazu do této fronty služby Service Bus. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit pro zasílání zpráv. |
| Název fronty |Název fronty služby Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete vytvořit taky zásady sdíleného přístupu do fronty **konfigurovat** kartu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |CSV a JSON UTF-8 je jediný podporovaný formát kódování v tuto chvíli. |
| Oddělovač |Vztahuje se pouze pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát |Vztahuje se jenom typ formátu JSON. **Řádcích** : Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. **Pole** : Určuje takový formát výstupu jako pole objektů JSON. |
| Sloupce vlastností | Volitelné. Sloupce oddělených čárkou, které musí být připojené jako vlastnosti uživatele odchozí zprávy místo datové části. Další informace o této funkci se v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.

## <a name="service-bus-topics"></a>Témata služby Service Bus
Fronty Service Bus poskytují způsob komunikace 1: 1 z odesílatele příjemci. [Témata služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zadejte jeden mnoho forma komunikace.

Následující tabulka uvádí názvy vlastností a jejich popisy pro vytvoření výstupních téma služby Service Bus.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu na toto téma služby Service Bus. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor názvů Service Bus. |
| Název tématu |Témata jsou entity pro zasílání zpráv, podobné centrům událostí a frontám. Jsou navržené ke shromažďování streamů událostí z zařízení a služeb. Když se na téma, udělil taky určitý název. Zprávy odeslané do tématu nejsou k dispozici, pokud není vytvořené předplatné, zajistěte proto je jeden nebo více předplatných v rámci tématu. |
| Název zásad tématu |Při vytváření tématu služby Service Bus můžete vytvořit taky zásady sdíleného přístupu k danému tématu **konfigurovat** kartu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad tématu |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |Pokud používáte formát CSV nebo JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač |Vztahuje se pouze pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Sloupce vlastností | Volitelné. Sloupce oddělených čárkou, které musí být připojené jako vlastnosti uživatele odchozí zprávy místo datové části. Další informace o této funkci se v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná databázová služba, která nabízí neomezené elastické škálování po celém světě, bohaté možnosti dotazů a automatického indexování přes dogmaticky na schématu datové modely. Další informace o možnostech kontejneru Azure Cosmos DB pro Stream Analytics, najdete v článku [Stream Analytics pomocí služby Azure Cosmos DB jako výstup](stream-analytics-documentdb-output.md) článku.

Výstup Azure Cosmos DB ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China 21Vianet.

> [!Note]
> V tuto chvíli je v Azure Stream Analytics podporuje jenom připojení ke službě Azure Cosmos DB pomocí rozhraní SQL API.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně.

Následující tabulka popisuje vlastnosti pro vytvoření výstup Azure Cosmos DB.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Alias pro najdete na tomto výstup v dotazu Stream Analytics. |
| Jímka | Azure Cosmos DB. |
| Možnost importu | Zvolte buď **vyberte Cosmos DB ze svého předplatného** nebo **nastavení zadejte Cosmos DB ručně**.
| Account ID | Název nebo identifikátor URI účtu služby Azure Cosmos DB koncového bodu. |
| Klíč účtu | Sdílený přístupový klíč pro účet služby Azure Cosmos DB. |
| Databáze | Název databáze Azure Cosmos DB. |
| Název kontejneru | Název kontejneru se použije, která musí existovat ve službě Cosmos DB. Příklad:  <br /><ul><li> _MyContainer_: Kontejner s názvem "MyContainer", musí existovat.</li>|
| ID dokumentu |Volitelné. Název pole ve výstupních událostech používaný k určení primárního klíče, na které insert nebo update jsou založené operace.

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní prostředí služby, který používáte ke spuštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. To umožňuje implementaci kódu aktivovaného událostmi, ke kterým dochází v Azure nebo partner služeb. Díky schopnosti služby Azure Functions reagovat na triggery je přirozeným výstupem pro Azure Stream Analytics. Tento adaptér pro výstup umožňuje uživatelům připojit Stream Analytics k Azure Functions a spuštění skriptu nebo kusu kódu v reakci na různé události.

Výstup Azure Functions ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China 21Vianet.

Azure Stream Analytics volá Azure Functions prostřednictvím triggerů HTTP. Adaptér pro výstup Azure Functions je k dispozici následující konfigurovatelné vlastnosti:

| Název vlastnosti | Popis |
| --- | --- |
| Function App |Název aplikace Azure Functions. |
| Funkce |Název funkce v aplikaci Azure Functions. |
| Klíč |Pokud chcete používat funkce Azure z jiného předplatného, provést zadáním klíče pro přístup k funkci. |
| Maximální velikost dávky |Vlastnost, která umožňuje nastavit maximální velikost pro každou výstupní dávku, která se odesílá do dané funkce Azure. Vstupní jednotka je v bajtech. Ve výchozím nastavení tato hodnota je 262 144 bajtů (256 KB). |
| Maximální počet v dávce  |Vlastnost, která umožňuje určit maximální počet událostí v každé dávce, která je odeslána do služby Azure Functions. Výchozí hodnota je 100. |

Když Azure Stream Analytics přijímá 413 ("http příliš velký požadavek Entity") výjimky z funkce Azure, snižuje velikost dávky, která se odešle do služby Azure Functions. V kódu funkce Azure Ujistěte se, že Azure Stream Analytics neodesílá dávky nadměrné velikosti neposílá pomocí této výjimky. Také se ujistěte, že počet a velikost hodnoty maximální batch použít ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

Také v situaci, kde není žádná událost doručení časový interval, nebude vygenerován žádný výstup. V důsledku toho **computeResult** funkce není volána. Toto chování je konzistentní s integrovanou oddílové agregační funkce.

## <a name="custom-metadata-properties-for-output"></a>Vlastnosti vlastních metadat pro výstup 

Sloupce dotaz jako vlastnosti uživatele může připojit k odchozí zprávy. Tyto sloupce nenavazují do datové části. Vlastnosti jsou k dispozici v podobě slovníku na výstupní zprávu. *Klíč* název sloupce a *hodnotu* je hodnota sloupce ve slovníku vlastností. Všechny typy dat Stream Analytics jsou podporované s výjimkou záznamu a pole.  

Podporované výstupy: 
* Fronty služby Service Bus 
* Téma služby Service Bus 
* Centrum událostí 

V následujícím příkladu přidáme dvě pole `DeviceId` a `DeviceStatus` v metadatech. 
* Dotaz: `select *, DeviceId, DeviceStatus from iotHubInput`
* Konfigurace výstupu: `DeviceId,DeviceStatus`

![Sloupce vlastností](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Následující snímek obrazovky ukazuje výstup kontrole EventHub prostřednictvím vlastnosti zprávy [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

![Vlastní vlastnosti události](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílu a počet modulů pro zápis výstupu pro každý typ výstupu:

| Typ výstupu | Dělení podpory | Klíč oddílu  | Počet modulů pro zápis výstupu |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Použijte {date} a {time} tokeny v vzor předpony cesty. Vyberte formát data, jako je rrrr/MM/DD, DD/MM/rrrr nebo MM-DD-RRRR. HH se používá pro formát času. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ano, je potřeba povolena. | Podle v klauzuli PARTITION BY v dotazu. | Když je povolena volba dědit dělení, následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lépe zápisu propustnost při načítání dat do Azure SQL Database najdete v tématu [výstupu Azure Stream Analytics ke službě Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ano | Použijte {date} a {time} tokeny z polí událostí v vzor cesty. Vyberte formát data, jako je rrrr/MM/DD, DD/MM/rrrr nebo MM-DD-RRRR. HH se používá pro formát času. Výstupní objekt BLOB lze rozdělit pomocí jedné události vlastní atribut {pole fieldname} nebo {data a času:\<specifikátor >}. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ano | Ano | Se liší v závislosti na zarovnání oddílu.<br /> Když klíč oddílu výstup z centra událostí je stejně v souladu s nadřazeného (předchozí) kroku dotazu, počet zapisovače je stejný jako počet oddílů ve výstupu centra událostí. Každý writer používá [EventHubSender třídy](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) k odesílání událostí do konkrétních oddílů. <br /> Při klíče oddílu pro výstupu centra událostí není zarovnána s nadřazeného (předchozí) kroku dotazu, počet zapisovače je stejný jako počet oddílů v tomto dřívějším kroku. Každý writer používá [SendBatchAsync třídy](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) v **EventHubClient** k odesílání událostí do všech oddílů výstup. |
| Power BI | Ne | Žádný | Není k dispozici. |
| Azure Table Storage | Ano | Výstup je sloupec.  | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Azure tématu služby Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.| Stejný jako počet oddílů tématu výstup.  |
| Fronty Azure Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.| Stejný jako počet oddílů ve výstupní frontě. |
| Azure Cosmos DB | Ano | Podle v klauzuli PARTITION BY v dotazu. | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ne | Žádný | Není k dispozici. |

Počet modulů pro zápis výstupu je možné řídit také pomocí `INTO <partition count>` (viz [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) klauzule v dotazu, které mohou být užitečné při dosažení topologie požadované úlohy. Pokud adaptér pro výstup není rozdělena na oddíly, chybějící data v jednom oddílu vstupní způsobí zpoždění až pozdní doručení množství času. V takovém případě je výstup sloučeny s jedním zapisujícím procesem, což by mohlo způsobit problémová místa ve vašem kanálu. Další informace o pozdní přijetí zásad najdete v tématu [aspekty pořadí událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost dávky výstupu
Azure Stream Analytics používá proměnné velikosti dávky pro zpracování událostí a zápis do výstupů. Obvykle modul Stream Analytics nepodporuje zápis zpráv najednou a používá dávky efektivitu. Když je vysoká míra příchozích a odchozích událostí, Stream Analytics používá větších dávek. Po nízká frekvence odchozího přenosu dat se používá po menších dávkách zachovat s nízkou latencí.

Následující tabulka popisuje některé důležité informace pro dávkové zpracování výstupu:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikosti dávky |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobrazit [Data Lake Storage omezuje](../azure-subscription-service-limits.md#data-lake-store-limits). | Použijte až 4 MB za operace zápisu. |
| Azure SQL Database | Možnost konfigurace maximální počet v dávce pomocí. 10 000 maximální a 100 minimální řádků na jeden hromadné vložení ve výchozím nastavení.<br />Zobrazit [Azure SQL omezuje](../sql-database/sql-database-resource-limits.md). |  Hromadné vložení pomocí služby batch maximální počet je zpočátku každou dávku. Batch je rozdělen na polovinu (až do služby batch minimální počet) podle opakovatelná chyby SQL. |
| Azure Blob Storage | Zobrazit [omezení služby Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Objekt blob maximální velikost bloku je 4 MB.<br />Počet bock maximální objektů blob je 50 000. |
| Azure Event Hubs  | 1 MB za zprávy nebo 256 KB. <br />Zobrazit [omezuje služby Event Hubs](../event-hubs/event-hubs-quotas.md). |  Při dělení vstup/výstup nejsou zarovnána, každé události je zabalena jednotlivě v `EventData` a odeslány až do maximální velikosti dávky. To se také stane, když [vlastní metadata vlastnosti](#custom-metadata-properties-for-output) se používají. <br /><br />  Pokud vstupní/výstupní dělení je zarovnán, více událostí jsou zkomprimována do jednoho `EventData` instance až maximální velikost zprávy a odeslat. |
| Power BI | Zobrazit [omezuje rozhraní Rest API Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Zobrazit [omezení služby Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Výchozí hodnota je 100 entit na jedné transakce. Můžete ho nakonfigurovat na menší hodnotu, podle potřeby. |
| Fronty Azure Service Bus   | 256 KB na zprávu pro úroveň Standard, 1MB pro úroveň Premium.<br /> Zobrazit [omezuje služby Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost za každou zprávu. |
| Azure tématu služby Service Bus | 256 KB na zprávu pro úroveň Standard, 1MB pro úroveň Premium.<br /> Zobrazit [omezuje služby Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost za každou zprávu. |
| Azure Cosmos DB   | Zobrazit [služby Azure Cosmos DB omezí](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Velikost dávky a zápis jsou upraveny frekvence dynamicky podle odpovědi služby Azure Cosmos DB. <br /> Neexistují žádná předem omezení ze Stream Analytics. |
| Azure Functions   | | Výchozí velikost dávky je 262 144 bajtů (256 KB). <br /> Výchozí počet událostí za služby batch je 100. <br /> Velikost dávky je možné konfigurovat a můžete se zvýší nebo sníží ve službě Stream Analytics [výstup možnosti](#azure-functions).

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> 
> [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
