---
title: Porozumění výstupům z Azure Stream Analytics
description: Tento článek popisuje možnosti výstupu dat dostupné v Azure Stream Analytics, včetně Power BI pro výsledky analýzy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: d867cceb3e7261f658e2406617144c9150e36f2a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173439"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Porozumění výstupům z Azure Stream Analytics

Tento článek popisuje typy výstupů, které jsou k dispozici pro úlohu Azure Stream Analytics. Výstupy umožňují ukládat a ukládat výsledky Stream Analytics úlohy. Pomocí výstupních dat můžete provádět další obchodní analýzy a datové sklady vašich dat.

Pokud navrhujete Stream Analytics dotaz, přečtěte si název výstupu pomocí [klauzule into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování (Pokud je potřebujete) zadáním více klauzulí INTO v dotazu.

Chcete-li vytvářet, upravovat a testovat Stream Analytics výstupy úlohy, můžete použít [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), rozhraní [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)a [Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy výstupů podporují [dělení](#partitioning). [Velikosti výstupních dávek](#output-batch-size) se liší pro optimalizaci propustnosti.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics podporuje [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage je škálovatelné úložiště s velkými objemy dat v celé organizaci. Data Lake Storage můžete použít k ukládání dat libovolné velikosti, typu a rychlosti příjmu pro provozní a průzkumné analýzy. Stream Analytics musí mít oprávnění pro přístup k Data Lake Storage.

Azure Data Lake Storage výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Data Lake Storageho výstupu 1. generace.   

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název, který se v dotazech používá k přímému nasměrování výstupu dotazu na Data Lake Store. |
| Formě | Předplatné, které obsahuje váš účet Azure Data Lake Storage. |
| Název účtu | Název účtu Data Lake Store, do kterého posíláte výstup. Zobrazí se rozevírací seznam Data Lake Store účtů, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru, která se používá k zápisu souborů v rámci zadaného Data Lake Store účtu. Můžete zadat jednu nebo více instancí proměnných {Date} a {Time}:<br /><ul><li>Příklad 1: složku1/logs/{Date}/{Time}</li><li>Příklad 2: složku1/logs/{Date}</li></ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pokud vzor cesty k souboru neobsahuje koncové lomítko (/), považuje se poslední vzor v cestě k souboru za předponu názvu souboru. <br /><br />V těchto případech se vytvoří nové soubory:<ul><li>Změna ve schématu výstupu</li><li>Externí nebo interní restartování úlohy</li></ul> |
| Formát data | Volitelné. Pokud je token data použit v cestě předpony, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: RRRR/MM/DD |
|Formát času | Volitelné. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro.|
| Kódování | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8.|
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára.|
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo Stream Analytics přesunula k následujícímu časovému intervalu. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu.|
| Režim ověřování | Přístup k účtu Data Lake Storage můžete autorizovat pomocí [spravované identity](stream-analytics-managed-identities-adls.md) nebo tokenu uživatele. Po udělení přístupu můžete přístup odvolat změnou hesla uživatelského účtu, odstraněním Data Lake Storageho výstupu této úlohy nebo odstraněním úlohy Stream Analytics. |

## <a name="sql-database"></a>SQL Database

Můžete použít [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako výstup pro data, která jsou v podstatě relační nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Stream Analytics úlohy zapisují do existující tabulky v SQL Database. Schéma tabulky musí přesně odpovídat polím a jejich typům ve výstupu vaší úlohy. Můžete také zadat [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako výstup pomocí možnosti SQL Database Output. Další informace o způsobech zvýšení propustnosti zápisu naleznete v článku [Stream Analytics s Azure SQL Database jako výstup](stream-analytics-sql-output-perf.md) .

[Azure SQL Database spravovanou instanci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) můžete použít také jako výstup. Musíte [nakonfigurovat veřejný koncový bod v Azure SQL Database Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a pak ručně nakonfigurovat následující nastavení v Azure Stream Analytics. Virtuální počítač Azure se spuštěným SQL Server s připojenou databází je také podporován ruční konfigurací nastavení níže.

Následující tabulka uvádí seznam názvů vlastností a jejich popis pro vytvoření výstupu SQL Database.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k směrování výstupu dotazu do této databáze. |
| Databáze | Název databáze, do které posíláte výstup. |
| Název serveru | Název serveru SQL Database. Pro Azure SQL Database Managed instance je nutné zadat port 3342. Například *sampleserver. Public. Database. Windows. NET, 3342* |
| jmen | Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Tabulka | Název tabulky, do které se zapisuje výstup V názvu tabulky se rozlišují velká a malá písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typům vygenerovaných výstupem úlohy. |
|Zdědit schéma oddílu| Možnost dědění schématu dělení vašeho předchozího dotazu, která umožňuje úplnou paralelní topologii s více zapisovači v tabulce. Další informace najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximální počet dávek| Doporučený horní limit počtu záznamů odeslaných při každé hromadné vložené transakci.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Úložiště objektů BLOB a Azure Data Lake Gen2

Výstup do Azure Data Lake Gen2 se nabízí jako funkce ve verzi Preview v omezených oblastech po celém světě. Můžete požádat o přístup k verzi Preview poskytnutím dalších podrobností ve [formuláři žádosti](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).

Azure Blob Storage nabízí nákladově efektivní a škálovatelné řešení pro ukládání velkých objemů nestrukturovaných dat v cloudu. Úvod do úložiště objektů BLOB a jeho použití najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu objektu BLOB.

| Název vlastnosti       | Popis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název, který se používá v dotazech k směrování výstupu dotazu do tohoto úložiště objektů BLOB. |
| Účet úložiště     | Název účtu úložiště, do kterého posíláte výstup.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště                              |
| Kontejner úložiště   | Logické seskupení pro objekty blob uložené v Azure Blob service. Po nahrání objektu blob do Blob service musíte zadat kontejner pro tento objekt BLOB. |
| Vzor cesty | Volitelné. Vzor cesty k souboru, který se používá k zápisu objektů BLOB v zadaném kontejneru. <br /><br /> Ve vzoru cesty se můžete rozhodnout použít jednu nebo více instancí proměnných data a času k určení četnosti zápisu objektů BLOB: <br /> {Date}, {Time} <br /><br />Vlastní dělení objektů blob můžete použít k určení jednoho vlastního názvu {Field} z dat události pro vytvoření oddílů objektů BLOB. Název pole je alfanumerický a může obsahovat mezery, spojovníky a podtržítka. Mezi vlastní pole patří tato omezení: <ul><li>V názvech polí se nerozlišují malá a velká písmena. Například služba nemůže odlišit sloupce "ID" a "ID".</li><li>Vnořená pole nejsou povolena. Místo toho použijte alias v dotazu úlohy na možnost "sloučit" pole.</li><li>Výrazy nelze použít jako název pole.</li></ul> <br />Tato funkce povoluje použití vlastních konfigurací specifikátoru formátu data a času v cestě. Vlastní formáty data a času musí být zadány po jednom a uzavřeny klíčovým slovem {DateTime: \<specifier >}. Povolené vstupy pro \<specifier > jsou rrrr, MM, M, DD, d, HH, H, mm, m, SS nebo s. Klíčové slovo {DateTime: \<specifier >} lze v cestě použít několikrát k vytvoření vlastních konfigurací data a času. <br /><br />Příklady: <ul><li>Příklad 1: Cluster1/logs/{Date}/{Time}</li><li>Příklad 2: Cluster1/logs/{Date}</li><li>Příklad 3: Cluster1/{client_id}/{Date}/{Time}</li><li>Příklad 4: Cluster1/{DateTime: SS}/{myField}, kde je dotaz: Vyberte data. myField jako myField ze vstupu;</li><li>Příklad 5: Cluster1/Year = {DateTime: rrrr}/month = {DateTime: MM}/Day = {DateTime: DD}</ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pojmenovávání souborů používá následující konvenci: <br /><br />{Vzor předpony cesty}/schemaHashcode_Guid_Number.extension<br /><br />Příklady výstupních souborů:<ul><li>MyOutput/20170901/00/45434_gguid_1. csv</li>  <li>MyOutput/20170901/01/45434_gguid_1. csv</li></ul> <br />Další informace o této funkci najdete v tématu [Azure Stream Analytics vlastního dělení výstupu objektů BLOB](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Volitelné. Pokud je token data použit v cestě předpony, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: RRRR/MM/DD |
| Formát času | Volitelné. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV, Avro a Parquet. |
|Minimální počet řádků (pouze Parquet)|Počet minimálních řádků na dávku V případě Parquet bude každá dávka vytvářet nový soubor. Aktuální výchozí hodnota je 2 000 řádků a povolené maximum je 10 000 řádků.|
|Maximální doba (jenom Parquet)|Maximální doba čekání na dávku. Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že požadavek na minimální řádky není splněn. Aktuální výchozí hodnota je 1 minuta a povolené maximum je 2 hodiny. Pokud váš výstup objektu BLOB má četnost vzorů cesty, doba čekání nesmí být vyšší než časový rozsah oddílu.|
| Kódování    | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8. |
| Oddělovač   | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát      | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo Stream Analytics přesunula k následujícímu časovému intervalu. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu. |

Pokud jako výstup používáte úložiště objektů blob, vytvoří se v objektu BLOB nový soubor v následujících případech:

* Pokud soubor překračuje maximální povolený počet bloků (aktuálně 50 000). Můžete dosáhnout maximálního povoleného počtu bloků, aniž byste dosáhli maximální povolené velikosti objektu BLOB. Například pokud je výstupní frekvence vysoká, můžete zobrazit více bajtů na blok a velikost souboru je větší. Pokud je výstupní frekvence nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud se ve výstupu změní schéma a výstupní formát vyžaduje pevné schéma (CSV a Avro).
* Pokud se úloha restartuje, buď ji uživatel zastavuje, nebo ji zahájí, nebo je interně údržba systému nebo zotavení po chybě.
* Pokud je dotaz plně rozdělený a vytvoří se nový soubor pro každý výstupní oddíl.
* Pokud uživatel odstraní soubor nebo kontejner účtu úložiště.
* Pokud je výstupem čas rozdělení na oddíly pomocí vzoru předpony cesty a nový objekt BLOB se použije, když se dotaz přesune na další hodinu.
* Pokud je výstup rozdělený vlastním polem, vytvoří se nový objekt BLOB pro každý klíč oddílu, pokud neexistuje.
* Pokud je výstup rozdělený vlastním polem, u kterého klíč oddílu překračuje 8 000, a pro každý klíč oddílu se vytvoří nový objekt BLOB.

## <a name="event-hubs"></a>Event Hubs

Služba [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) je vysoce škálovatelný ingestování událostí publikování a odběru. Může shromažďovat miliony událostí za sekundu. Jedním z nich je použití centra událostí jako výstup, když se výstup Stream Analytics úlohy stal vstupem jiné úlohy streamování.

Pro konfiguraci datových proudů z Center událostí jako výstupu potřebujete několik parametrů.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název, který se používá v dotazech k nasměrování výstupu dotazu do tohoto centra událostí. |
| Obor názvů centra událostí | Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste taky vytvořili obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásad centra událostí | Zásady sdíleného přístupu, které můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů centra událostí. |
| Sloupec klíče oddílu | Volitelné. Sloupec obsahující klíč oddílu pro výstup centra událostí |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Kódování | V případě CSV a JSON je v tuto chvíli jediným podporovaným formátem kódování UTF-8. |
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo Stream Analytics přesunula k následujícímu časovému intervalu. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu. Další informace najdete v části [velikost výstupní dávky](#output-batch-size) . |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) můžete použít jako výstup pro úlohu Stream Analytics a poskytnout tak bohatou vizualizaci výsledků analýzy. Tuto možnost můžete použít pro provozní řídicí panely, generování sestav a vytváření sestav řízených metrikami.

Power BI výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Power BIho výstupu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Zadejte popisný název, který se použije v dotazech k nasměrování výstupu dotazu do tohoto Power BI výstupu. |
| Pracovní prostor skupiny |Pokud chcete povolit sdílení dat s jinými Power BI uživateli, můžete vybrat skupiny v rámci účtu Power BI nebo zvolit **pracovní prostor** , pokud ho nechcete zapisovat do skupiny. Aktualizace existující skupiny vyžaduje obnovení Power BI ověřování. |
| Název datové sady |Zadejte název datové sady, který má výstup Power BI použít. |
| Název tabulky |Pod datovou sadou výstupu Power BI zadejte název tabulky. V současné době může Power BI výstup z úloh Stream Analytics obsahovat pouze jednu tabulku v datové sadě. |
| Autorizovat připojení | Abyste mohli konfigurovat nastavení výstupu, musíte se autorizovat s Power BI. Po udělení tohoto výstupního přístupu vašemu řídicímu panelu Power BI můžete odvolat přístup změnou hesla uživatelského účtu, odstraněním výstupu úlohy nebo odstraněním úlohy Stream Analytics. | 

Návod ke konfiguraci Power BIho výstupu a řídicího panelu najdete v kurzu [Azure Stream Analytics a Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Datovou sadu a tabulku nevytvářejte explicitně na řídicím panelu Power BI. Datová sada a tabulka se automaticky naplní při zahájení úlohy a úloha začne vyčerpat výstup do Power BI. Pokud dotaz úlohy negeneruje žádné výsledky, datová sada a tabulka se nevytvoří. Pokud Power BI již měla datovou sadu a tabulku se stejným názvem jako v této Stream Analytics úlohy, stávající data budou přepsána.
>

### <a name="create-a-schema"></a>Vytvoření schématu
Azure Stream Analytics vytvoří datovou sadu Power BI a schéma tabulky pro uživatele, pokud ještě neexistují. Ve všech ostatních případech je tabulka aktualizována o nové hodnoty. V současné době může existovat pouze jedna tabulka v rámci datové sady. 

Power BI používá zásady uchovávání dat first in, First-out (FIFO). Data se budou shromažďovat v tabulce, dokud nebude mít 200 000 řádků.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Převod datového typu z Stream Analytics na Power BI
Pokud se změní výstupní schéma, Azure Stream Analytics aktualizuje datový model dynamicky za běhu. Změny názvu sloupce, změny typu sloupce a přidání nebo odebrání sloupců jsou sledovány.

Tato tabulka popisuje převody datových typů z [Stream Analytics typy dat](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) do Power BI [model EDM (Entity Data Model) (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), pokud Power BI datová sada a tabulka neexistuje.

Z Stream Analytics | Pro Power BI
-----|-----
bigint | Int64
nvarchar (max) | String
Hodnotu | Hodnotu
float | Double
Pole záznamu | Typ řetězce, konstantní hodnota "IRecord" nebo "IArray"

### <a name="update-the-schema"></a>Aktualizace schématu
Stream Analytics odvodí schéma datového modelu na základě první sady událostí ve výstupu. Později v případě potřeby se schéma datového modelu aktualizuje tak, aby odpovídal příchozím událostem, které se nemusí vejít do původního schématu.

Nepoužívejte dotaz `SELECT *`, abyste zabránili aktualizaci dynamického schématu napříč řádky. Kromě potenciálních dopadů na výkon může být výsledkem nejistota doba trvání výsledků. Vyberte přesná pole, která se musí zobrazit na řídicím panelu Power BI. Kromě toho musí být hodnoty dat kompatibilní se zvoleným datovým typem.


Předchozí/aktuální | Int64 | String | Hodnotu | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Hodnotu | String | String |  Hodnotu | String

## <a name="table-storage"></a>Table Storage

[Azure Table Storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné, široce škálovatelné úložiště, aby se aplikace mohla automaticky škálovat tak, aby splňovala požadavky uživatelů. Table Storage je úložiště NoSQLch klíčů a atributů od Microsoftu, které můžete použít pro strukturovaná data s menším omezením schématu. Služba Azure Table Storage se dá použít k ukládání dat do trvalých a efektivních načtení.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu tabulky.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do tohoto úložiště tabulek. |
| Účet úložiště |Název účtu úložiště, do kterého posíláte výstup. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště |
| Název tabulky |Název tabulky Tabulka se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce, který obsahuje klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v tabulce, který tvoří první část primárního klíče entity. Je to hodnota řetězce, která může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce, který obsahuje klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí (100) je pro většinu úloh dostačující. Další informace o úpravách tohoto nastavení najdete v článku [specifikace operace dávky tabulky](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) . |

## <a name="service-bus-queues"></a>Fronty Service Bus

[Fronty Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) nabízejí doručování zpráv typu FIFO jednomu nebo více konkurenčním spotřebitelům. Obvykle jsou zprávy přijímány a zpracovávány příjemci v dočasném pořadí, ve kterém byly přidány do fronty. Každá zpráva je přijata a zpracována pouze jedním příjemcem zprávy.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu fronty.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do této Service Bus fronty. |
| Obor názvů Service Bus |Kontejner pro sadu entit zasílání zpráv. |
| Název fronty |Název fronty Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** fronty. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Kódování |V případě CSV a JSON je v tuto chvíli jediným podporovaným formátem kódování UTF-8. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát |Platí pouze pro typ JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. **Pole** určuje, že výstup je formátován jako pole objektů JSON. |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce systémových vlastností | Volitelné. Páry klíč-hodnota vlastností systému a odpovídajících názvů sloupců, které je třeba připojit k odchozí zprávě místo datové části. Další informace o této funkci najdete v části [Vlastnosti systému pro Service Bus fronty a výstupy témat](#system-properties-for-service-bus-queue-and-topic-outputs) .  |

Počet oddílů je [založený na Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.

## <a name="service-bus-topics"></a>Service Bus témata
Fronty Service Bus poskytují metodu komunikace 1:1 od odesílatele k přijímači. [Service Bus témata](https://msdn.microsoft.com/library/azure/hh367516.aspx) poskytují vzájemnou formu komunikace 1: n.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu Service Bus tématu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do tohoto Service Bus tématu. |
| Obor názvů Service Bus |Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste také vytvořili Service Bus obor názvů. |
| Název tématu |Témata jsou entity zasílání zpráv, podobně jako centra událostí a fronty. Jsou navržené ke shromažďování datových proudů událostí ze zařízení a služeb. Při vytvoření tématu se také zobrazí konkrétní název. Zprávy odeslané do tématu nejsou k dispozici, dokud nevytvoříte odběr, a zajistěte, aby bylo v tématu jedno nebo více předplatných. |
| Název zásad tématu |Když vytvoříte Service Bus téma, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** tématu. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad tématu |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Kódování |Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Sloupce vlastností | Volitelné. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce systémových vlastností | Volitelné. Páry klíč-hodnota vlastností systému a odpovídajících názvů sloupců, které je třeba připojit k odchozí zprávě místo datové části. Další informace o této funkci najdete v části [Vlastnosti systému pro Service Bus fronty a výstupy témat](#system-properties-for-service-bus-queue-and-topic-outputs) . |

Počet oddílů je [založený na Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná databázová služba, která nabízí neomezené Elastické škálování po celém světě, bohatě vydaný dotaz a automatické indexování prostřednictvím datových modelů nezávislá schématu. Další informace o možnostech kontejneru Azure Cosmos DB pro Stream Analytics naleznete v článku [Stream Analytics s Azure Cosmos DB jako výstup](stream-analytics-documentdb-output.md) .

Azure Cosmos DB výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje připojení pouze k Azure Cosmos DB pomocí rozhraní SQL API.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovaná. Pokud přejdete Azure Stream Analytics na účty Azure Cosmos DB vytvořené pomocí jiných rozhraní API, data nemusí být správně uložená.

Následující tabulka popisuje vlastnosti pro vytvoření výstupu Azure Cosmos DB.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Alias pro odkazování na tento výstup v dotazu Stream Analytics. |
| Jímkou | Azure Cosmos DB. |
| Možnost importu | Zvolte buď **možnost vybrat Cosmos DB z předplatného** , nebo **Zadejte Cosmos DB nastavení ručně**.
| ID účtu | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB. |
| Klíč účtu | Sdílený přístupový klíč pro účet Azure Cosmos DB. |
| Databáze | Azure Cosmos DB název databáze. |
| Název kontejneru | Název kontejneru, který se má použít, který musí existovat v Cosmos DB. Příklad:  <br /><ul><li> _MyContainer_: musí existovat kontejner s názvem "MyContainer".</li>|
| ID dokumentu |Volitelné. Název pole ve výstupních událostech, které slouží k určení primárního klíče, na kterém jsou založeny operace INSERT nebo Update.

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní služba bez serveru, kterou můžete použít ke spouštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Umožňuje implementovat kód, který se aktivuje událostmi, ke kterým dochází v Azure nebo v partnerských službách. Tato schopnost Azure Functions reagovat na triggery, vytvoří přirozený výstup pro Azure Stream Analytics. Tento výstupní adaptér umožňuje uživatelům připojení Stream Analytics k Azure Functions a spuštění skriptu nebo části kódu v reakci na nejrůznější události.

Azure Functions výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

Azure Stream Analytics vyvolá Azure Functions prostřednictvím triggerů HTTP. Azure Functions výstupní adaptér je k dispozici s následujícími konfigurovatelnými vlastnostmi:

| Název vlastnosti | Popis |
| --- | --- |
| Aplikace Function App |Název vaší aplikace Azure Functions. |
| Funkce |Název funkce v aplikaci Azure Functions. |
| Key |Pokud chcete používat funkci Azure Functions z jiného předplatného, můžete to udělat tak, že pro přístup k funkci poskytnete klíč. |
| Maximální velikost dávky |Vlastnost, která umožňuje nastavit maximální velikost pro každou výstupní dávku, která se odesílá do funkce Azure Functions. Vstupní jednotka je v bajtech. Ve výchozím nastavení je tato hodnota 262 144 bajtů (256 KB). |
| Maximální počet dávek  |Vlastnost, která umožňuje určit maximální počet událostí v každé dávce, která je odeslána do Azure Functions. Výchozí hodnota je 100. |

Když Azure Stream Analytics obdrží výjimku ("413 je příliš velká entita požadavku HTTP) z funkce Azure, zmenší velikost balíků, které posílá do Azure Functions. V kódu funkce Azure použijte tuto výjimku k tomu, abyste se ujistili, že Azure Stream Analytics neodesílají dávky s větší velikostí. Také se ujistěte, že maximální počet dávek a hodnoty velikosti použité ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

> [!NOTE]
> Během testovacího připojení Stream Analytics odešle prázdnou dávku, aby Azure Functions testování, pokud propojení mezi nimi funguje. Ujistěte se, že vaše aplikace Functions zpracovává prázdné požadavky na dávky, aby bylo zajištěno, že test připojení projde.

V situaci, kdy není žádné přistání události v časovém intervalu, není vygenerován žádný výstup. V důsledku toho není volána funkce **computeResult** . Toto chování je konzistentní s předdefinovanými agregačními funkcemi v okně.

## <a name="custom-metadata-properties-for-output"></a>Vlastnosti vlastních metadat pro výstup 

Sloupce dotazu můžete k odchozím zprávám připojit jako vlastnosti uživatele. Tyto sloupce neobsahují datovou část. Vlastnosti jsou k dispozici ve formě slovníku ve výstupní zprávě. *Klíč* je název sloupce a *hodnota* je hodnota sloupce ve slovníku Properties (vlastnosti). Všechny datové typy Stream Analytics jsou podporovány kromě záznamu a pole.  

Podporované výstupy: 
* Fronta Service Bus 
* Service Bus téma 
* Centrum událostí 

V následujícím příkladu přidáme dvě pole `DeviceId` a `DeviceStatus` do metadat. 
* Dotaz: `select *, DeviceId, DeviceStatus from iotHubInput`
* Konfigurace výstupu: `DeviceId,DeviceStatus`

![Sloupce vlastností](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Následující snímek obrazovky ukazuje vlastnosti výstupní zprávy, které byly zkontrolovány v centru EventHub prostřednictvím [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

![Vlastní vlastnosti události](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Systémové vlastnosti pro Service Bus front a témat 
Sloupce dotazu můžete připojit jako [systémové vlastnosti](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) do odchozí fronty služby Service Bus nebo do zpráv tématu. Tyto sloupce neobsahují datovou část, místo toho se naplní odpovídající [vlastnost BrokeredMessage systému](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) hodnotami sloupce dotazu.
Podporované vlastnosti systému jsou `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Řetězcové hodnoty těchto sloupců se analyzují jako odpovídající typ hodnoty vlastnosti systému a jakékoli selhání při analýze se považují za chyby dat.
Toto pole je k dispozici jako formát objektu JSON. Podrobnosti o tomto formátu jsou následující:
* Uzavřeno složenými závorkami {}.
* Napsané páry klíč/hodnota.
* Klíče a hodnoty musí být řetězce.
* Klíč je název vlastnosti systému a hodnota je název sloupce dotazu.
* Klíče a hodnoty jsou oddělené dvojtečkou.
* Jednotlivé páry klíč/hodnota jsou oddělené čárkou.

Ukazuje, jak používat tuto vlastnost –

* Dotaz: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Sloupce systémových vlastností: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Tím se nastaví `MessageId` na zprávy fronty Service Bus s hodnotami `column1` a PartitionKey se nastaví s hodnotami `column2`.

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílů a počet výstupních modulů pro každý typ výstupu:

| Typ výstupu | Podpora dělení | Klíč oddílu  | Počet zapisovačů výstupu |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Použijte {Date} a {Time} tokens ve vzoru předpony cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. HH se používá pro formát času. | Sleduje vstupní oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ano, je nutné povolit. | Na základě klauzule PARTITION BY v dotazu. | Když je povolená možnost zdědit rozdělení na oddíly, řídí se vstupní oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lepšího výkonu propustnosti zápisu při načítání dat do Azure SQL Database naleznete v tématu [Azure Stream Analytics Output to Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Úložiště objektů BLOB v Azure | Ano | Použijte {Date} a {Time} tokeny z polí události ve vzoru cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. HH se používá pro formát času. Výstup objektu BLOB se dá rozdělit na oddíly jediným vlastním atributem události {FieldName} nebo {DateTime: \<specifier >}. | Sleduje vstupní oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Event Hubs Azure | Ano | Ano | Liší se v závislosti na zarovnání oddílů.<br /> Když je klíč oddílu pro výstup centra událostí rovnoměrně zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů ve výstupu centra událostí. Každý zapisovač používá ke posílání událostí do konkrétního oddílu [třídu EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) . <br /> Pokud klíč oddílu pro výstup centra událostí není zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů v předchozím kroku. Každý zapisovač používá v **EventHubClient** [třídu SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) k odesílání událostí do všech výstupních oddílů. |
| Power BI | Ne | Žádné | Nelze použít. |
| Úložiště tabulek v Azure | Ano | Libovolný výstupní sloupec.  | Sleduje vstupní dělení pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). |
| Azure Service Bus téma | Ano | Automaticky zvoleno. Počet oddílů je založený na [Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.| Stejné jako počet oddílů v tématu výstupu.  |
| Fronta Azure Service Bus | Ano | Automaticky zvoleno. Počet oddílů je založený na [Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.| Stejné jako počet oddílů ve výstupní frontě. |
| Azure Cosmos DB | Ano | Na základě klauzule PARTITION BY v dotazu. | Sleduje vstupní dělení pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ano | Na základě klauzule PARTITION BY v dotazu. | Sleduje vstupní dělení pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). |

Počet výstupních zapisovačů lze také ovládat pomocí klauzule `INTO <partition count>` (viz [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) v dotazu, což může být užitečné při dosahování požadované topologie úlohy. Pokud váš výstupní adaptér není rozdělený na oddíly, nedostatečné množství dat v jednom vstupním oddílu způsobí zpoždění až do doby doručení. V takových případech se výstup sloučí do jediného zapisovače, což může způsobit kritické body ve vašem kanálu. Další informace o zásadách pozdního doručení najdete v tématu [Azure Stream Analytics požadavky na pořadí událostí](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky
Azure Stream Analytics používá dávky variabilní velikosti pro zpracování událostí a zápis do výstupů. Modul Stream Analytics obvykle nezapisuje jednu zprávu najednou a využívá dávky k efektivitě. Když je vysoká míra příchozích i odchozích událostí, Stream Analytics využívá větší počet dávek. Když je přenosová rychlost nízká, používá menší dávky k udržení nízké latence.

Následující tabulka popisuje některé z důležitých informací pro výstup dávkování:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikosti dávky |
| :--- | :--- | :--- |
| Azure Data Lake Store | Viz [omezení Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits). | Pro každou operaci zápisu použijte až 4 MB. |
| Azure SQL Database | Konfigurovatelné pomocí maximálního počtu dávek. 10 000 maximální a 100 minimální počet řádků na jedno hromadné vložení ve výchozím nastavení.<br />Viz [omezení Azure SQL](../sql-database/sql-database-resource-limits.md). |  Každá dávka je zpočátku hromadně vložena s maximálním počtem dávek. Dávka je rozdělena na polovinu (až do minimálního počtu dávek) na základě opakovaných chyb z SQL. |
| Úložiště objektů BLOB v Azure | Viz [omezení Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Maximální velikost bloku objektu BLOB je 4 MB.<br />Maximální počet Bock objektů BLOB je 50 000. |
| Event Hubs Azure  | 256 KB nebo 1 MB na zprávu <br />Viz [omezení Event Hubs](../event-hubs/event-hubs-quotas.md). |  Když není zarovnaný vstup/výstup dělení, každá událost je zabalená jednotlivě do `EventData` a odeslaná v dávce až do maximální velikosti zprávy. K tomu dojde také v případě, že se používají [vlastní vlastnosti metadat](#custom-metadata-properties-for-output) . <br /><br />  Při zarovnávání vstupních/výstupních dělení se více událostí zabalí do jedné instance @no__t 0, až do maximální velikosti zprávy a pošle se. |
| Power BI | Viz [omezení Power BI rozhraní REST API](https://msdn.microsoft.com/library/dn950053.aspx). |
| Úložiště tabulek v Azure | Viz [omezení Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Výchozí hodnota je 100 entit na jednu transakci. V případě potřeby ho můžete nakonfigurovat na menší hodnotu. |
| Fronta Azure Service Bus   | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [omezení Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost na zprávu. |
| Azure Service Bus téma | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [omezení Service Bus](../service-bus-messaging/service-bus-quotas.md). | Použijte jednu událost na zprávu. |
| Azure Cosmos DB   | Viz [omezení Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Velikost dávky a frekvence zápisu se dynamicky upravují na základě Azure Cosmos DBch odpovědí. <br /> Neexistují předem vymezená omezení Stream Analytics. |
| Azure Functions   | | Výchozí velikost dávky je 262 144 bajtů (256 KB). <br /> Výchozí počet událostí na jednu dávku je 100. <br /> Velikost dávky je konfigurovatelná a je možné ji zvýšit nebo snížit v [možnostech výstupu](#azure-functions)Stream Analytics.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> 
> [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
