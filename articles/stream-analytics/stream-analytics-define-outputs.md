---
title: Principy výstupů z Azure Stream Analytics
description: Tento článek popisuje možnosti výstupu dat dostupné ve Službě Azure Stream Analytics, včetně Power BI pro výsledky analýzy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e0b4bcac8494f136dde21b03422e12b72cecb8f3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366434"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Principy výstupů z Azure Stream Analytics

Tento článek popisuje typy výstupů, které jsou k dispozici pro úlohu Azure Stream Analytics. Výstupy umožňují ukládat a ukládat výsledky úlohy Stream Analytics. Pomocí výstupních dat můžete provést další obchodní analýzy a ukládání dat.

Při návrhu dotazu Stream Analytics, odkazovat na název výstupu pomocí [into klauzule](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování (pokud je potřebujete) poskytnutím více klauzulí INTO v dotazu.

Chcete-li vytvářet, upravovat a testovat výstupy úloh Stream Analytics, můžete použít [portál Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), ROZHRANÍ REST [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)a [Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy výstupů podporují [dělení](#partitioning). [Velikosti výstupních dávek](#output-batch-size) se liší pro optimalizaci propustnost.


## <a name="azure-data-lake-storage-gen-1"></a>Úložiště datového jezera Azure Gen 1

Stream Analytics podporuje [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage je celopodnikové úložiště s hyperškálovat, které se nachází v rámci analýzy velkých objemů dat. Úložiště data lake můžete použít k ukládání dat libovolné velikosti, typu a rychlosti ingestování pro provozní a průzkumné analýzy. Stream Analytics musí mít oprávnění k přístupu k úložišti Datových jezer.

Výstup Azure Data Lake Storage z Stream Analytics teď není dostupný v oblastech Azure China 21Vianet a Azure Germany (T-Systems International).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci výstupu úložiště datového jezera Gen 1.   

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech k přesměrování výstupu dotazu na úložiště Data Lake Store. |
| Předplatné | Předplatné, které obsahuje váš účet Azure Data Lake Storage. |
| Název účtu | Název účtu Úložiště datového jezera, do kterého odesíláte výstup. Zobrazí se rozevírací seznam účtů úložiště Data Lake Store, které jsou k dispozici ve vašem předplatném. |
| Vzorek předpony cesty | Cesta k souboru, která se používá k zápisu souborů v rámci zadaného účtu Data Lake Store. Můžete zadat jednu nebo více instancí proměnných {date} a {time}:<br /><ul><li>Příklad 1: folder1/logs/{date}/{time}</li><li>Příklad 2: folder1/logs/{date}</li></ul><br />Časové razítko vytvořené struktury složek následuje podle času UTC a nikoli místního času.<br /><br />Pokud vzorek cesty k souboru neobsahuje koncové lomítko (/), bude poslední vzorek v cestě k souboru považován za předponu názvu souboru. <br /><br />Za těchto okolností jsou vytvářeny nové soubory:<ul><li>Změna schématu výstupu</li><li>Externí nebo interní restartování úlohy</li></ul> |
| Formát data | Nepovinný parametr. Pokud je v cestě předpony použit token data, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: Yyyy/MM/DD |
|Formát času | Nepovinný parametr. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV a Avro jsou podporovány.|
| Kódování | Pokud používáte formát CSV nebo JSON, musí být zadáno kódování. UTF-8 je v současné době jediným podporovaným formátem kódování.|
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislý pruh.|
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tím, že každý objekt JSON je oddělen novým řádkem. Pokud vyberete **Oddělené čáry**, JSON se čte po jednom objektu. Celý obsah sám o sobě by nebyl platný JSON.  **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo služba Stream Analytics přešla do dalšího časového okna. Obecně je vhodnější použít json oddělený od řádku, protože nevyžaduje žádné zvláštní zpracování, zatímco výstupní soubor je stále zapisován.|
| Režim ověřování | Přístup k účtu úložiště datového jezera můžete autorizovat pomocí [spravované identity](stream-analytics-managed-identities-adls.md) nebo uživatelského tokenu. Jakmile udělíte přístup, můžete odvolat přístup změnou hesla uživatelského účtu, odstraněním výstupu úložiště datového jezera pro tuto úlohu nebo odstraněním úlohy Stream Analytics. |

## <a name="sql-database"></a>Databáze SQL

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) můžete použít jako výstup pro data, která jsou relační povahy nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Úlohy Analýzy datových proudů zapisují do existující tabulky v databázi SQL. Schéma tabulky se musí přesně shodovat s poli a jejich typy ve výstupu úlohy. Můžete také zadat [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako výstup prostřednictvím možnosti výstupu sql database. Další informace o způsobech, jak zlepšit propustnost zápisu, najdete [v článku Stream Analytics s Azure SQL Database jako výstupní](stream-analytics-sql-output-perf.md) článek.

Jako výstup můžete taky použít [spravovanou instanci Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) Musíte [nakonfigurovat veřejný koncový bod ve spravované instanci Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) a pak ručně nakonfigurovat následující nastavení v Azure Stream Analytics. Virtuální počítač Azure se systémem SQL Server s připojenou databází je také podporován ruční konfigurací níže uvedených nastavení.

V následující tabulce jsou uvedeny názvy vlastností a jejich popis pro vytvoření výstupu databáze SQL.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech k nasměrování výstupu dotazu do této databáze. |
| Databáze | Název databáze, do které odesíláte výstup. |
| Název serveru | Název serveru SQL Database. Pro azure sql database spravované instance je nutné zadat port 3342. Například *sampleserver.public.database.windows.net,3342* |
| Uživatelské jméno | Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Table | Název tabulky, kde je zapsán výstup. Název tabulky rozlišuje malá a velká písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typů, které generuje výstup úlohy. |
|Zdědit schéma oddílu| Možnost pro dědění schéma dělení předchozí krok dotazu, povolit plně paralelní topologie s více zapisovače do tabulky. Další informace najdete v tématu [Výstup Azure Stream Analytics do Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximální počet dávek| Doporučený horní limit počtu záznamů odeslaných s každou transakcí hromadného vložení.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Úložiště objektů blob a Azure Data Lake Gen2

Data Lake Storage Gen2 dělá Azure Storage základem pro vytváření podnikových datových jezer v Azure. Aplikace Data Lake Storage Gen2, navržená od začátku, aby obsluhovala více petabajtů informací a zároveň udržovala stovky gigabitů propustností, umožňuje snadnospravovat obrovské množství dat. Základní součástí Data Lake Storage Gen2 je přidání hierarchického oboru názvů do úložiště objektů Blob.

Azure Blob storage nabízí cenově výhodné a škálovatelné řešení pro ukládání velkého množství nestrukturovaných dat v cloudu. Úvod k úložišti objektů Blob a jeho využití najdete v článku [Nahrávání, stahování a seznam objektů BLOB s portálem Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu objektu BLOB nebo ADLS Gen2.

| Název vlastnosti       | Popis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název používaný v dotazech k přesměrování výstupu dotazu na toto úložiště objektů blob. |
| Účet úložiště     | Název účtu úložiště, kam odesíláte výstup.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště.                              |
| Skladovací kontejner   | Logické seskupení pro objekty BLOB uložené ve službě Azure Blob. Při nahrávání objektu blob do služby objektů blob, musíte zadat kontejner pro tento objekt blob. |
| Vzor cesty | Nepovinný parametr. Vzorek cesty k souboru, který se používá k zápisu objektů BLOB v rámci zadaného kontejneru. <br /><br /> Ve vzorku cesty můžete použít jednu nebo více instancí proměnných data a času k určení četnosti zápisu objektů BLOB: <br /> {date}, {time} <br /><br />Vlastní dělení objektu blob můžete zadat jeden vlastní název {field} z dat události do objektů blob oddílu. Název pole je alfanumerický a může obsahovat mezery, spojovníky a podtržítka. Omezení vlastních polí zahrnují následující: <ul><li>Názvy polí nerozlišují malá a velká písmena. Služba například nemůže rozlišovat mezi sloupcem "ID" a sloupcem "id".</li><li>Vnořená pole nejsou povolena. Místo toho použijte alias v dotazu na úlohu k "sloučení" pole.</li><li>Výrazy nelze použít jako název pole.</li></ul> <br />Tato funkce umožňuje použití vlastních konfigurací specifikátoru formátu data a času v cestě. Vlastní formáty data a času musí být zadány po jednom,\<ohraničené matné klíčové slovo {datetime: specifier>}. Přípustné vstupy \<pro> specifikátoru jsou yyyy, MM, M, dd, d, HH, H, mm, m, ss nebo s. Klíčové slovo {datetime:\<specifier>} lze použít vícekrát v cestě k vytvoření vlastních konfigurací data a času. <br /><br />Příklady: <ul><li>Příklad 1: cluster1/logs/{date}/{time}</li><li>Příklad 2: cluster1/logs/{date}</li><li>Příklad 3: cluster1/{client_id}/{date}/{time}</li><li>Příklad 4: cluster1/{datetime:ss}/{myField}, kde je dotaz: SELECT data.myField AS myField FROM Input;</li><li>Příklad 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Časové razítko vytvořené struktury složek následuje podle času UTC a nikoli místního času.<br /><br />Pojmenování souborů používá následující konvenci: <br /><br />{Vzorek předpony cesty}/schemaHashcode_Guid_Number.extension<br /><br />Příklad výstupních souborů:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Další informace o této funkci najdete v [tématu Azure Stream Analytics vlastní dělení výstupu objektu blob](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Nepovinný parametr. Pokud je v cestě předpony použit token data, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: Yyyy/MM/DD |
| Formát času | Nepovinný parametr. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV, Avro a parkety jsou podporovány. |
|Minimální počet řádků (pouze parkety)|Počet minimálních řádků na dávku. Pro parkety každá dávka vytvoří nový soubor. Aktuální výchozí hodnota je 2 000 řádků a povolené maximum je 10 000 řádků.|
|Maximální doba (pouze parkety)|Maximální čekací doba na dávku. Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že není splněn požadavek na minimální řádky. Aktuální výchozí hodnota je 1 minuta a povolené maximum je 2 hodiny. Pokud má výstup objektu blob frekvenci vzorku cesty, doba čekání nemůže být vyšší než časový rozsah oddílu.|
| Kódování    | Pokud používáte formát CSV nebo JSON, musí být zadáno kódování. UTF-8 je v současné době jediným podporovaným formátem kódování. |
| Oddělovač   | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislý pruh. |
| Formát      | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tím, že každý objekt JSON je oddělen novým řádkem. Pokud vyberete **Oddělené čáry**, JSON se čte po jednom objektu. Celý obsah sám o sobě by nebyl platný JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo služba Stream Analytics přešla do dalšího časového okna. Obecně je vhodnější použít json oddělený od řádku, protože nevyžaduje žádné zvláštní zpracování, zatímco výstupní soubor je stále zapisován. |

Když používáte úložiště objektů Blob jako výstup, vytvoří se v objektu blob nový soubor v následujících případech:

* Pokud soubor překročí maximální počet povolených bloků (aktuálně 50 000). Můžete dosáhnout maximální povolený počet bloků bez dosažení maximální povolené velikosti objektu blob. Pokud je například výstupní rychlost vysoká, můžete zobrazit více bajtů na blok a velikost souboru je větší. Pokud je výstupní rychlost nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud dojde ke změně schématu ve výstupu a výstupní formát vyžaduje pevné schéma (CSV a Avro).
* Pokud je úloha restartována, buď externě uživatelem, který ji zastaví a spustí, nebo interně pro údržbu systému nebo obnovení chyb.
* Pokud je dotaz plně rozdělen a je vytvořen nový soubor pro každý výstupní oddíl.
* Pokud uživatel odstraní soubor nebo kontejner účtu úložiště.
* Pokud je výstup čas rozdělen pomocí vzoru předpony cesty a nový objekt blob se používá při přesunu dotazu na další hodinu.
* Pokud je výstup rozdělen na vlastní pole a nový objekt blob je vytvořen na klíč oddílu, pokud neexistuje.
* Pokud je výstup rozdělen na vlastní pole, kde mohutnost klíče oddílu přesahuje 8 000 a vytvoří se nový objekt blob na klíč oddílu.

## <a name="event-hubs"></a>Event Hubs

Služba [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) je vysoce škálovatelný příkaz pro publikování a přihlášení k odběru událostí. Může shromažďovat miliony událostí za sekundu. Jedním z použití centra událostí jako výstup je, když výstup úlohy Stream Analytics se stane vstupem jiné úlohy streamování. Informace o maximální velikosti zprávy a optimalizaci velikosti dávky naleznete v části [velikost i dávce výstupu.](#output-batch-size)

Potřebujete několik parametrů ke konfiguraci datových proudů z rozbočovačů událostí jako výstupu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech k přesměrování výstupu dotazu do tohoto centra událostí. |
| Obor názvů centra událostí | Kontejner pro sadu entit zasílání zpráv. Když jste vytvořili nové centrum událostí, vytvořili jste také obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásad centra událostí | Zásady sdíleného přístupu, které můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů centra událostí. |
| Sloupec klíče oddílu | Nepovinný parametr. Sloupec, který obsahuje klíč oddílu pro výstup centra událostí. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV a Avro jsou podporovány. |
| Kódování | Pro CSV a JSON, UTF-8 je pouze podporovaný formát kódování v tomto okamžiku. |
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislý pruh. |
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tím, že každý objekt JSON je oddělen novým řádkem. Pokud vyberete **Oddělené čáry**, JSON se čte po jednom objektu. Celý obsah sám o sobě by nebyl platný JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON.  |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako uživatelské vlastnosti odchozí zprávy namísto datové části. Další informace o této funkci jsou v části [Vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) můžete použít jako výstup pro úlohu Stream Analytics, abyste zajistili bohaté vizualizace výsledků analýzy. Tuto funkci můžete použít pro provozní řídicí panely, generování sestav a vytváření metrik.

Výstup Power BI z Stream Analytics teď není dostupný v oblastech Azure China 21Vianet a Azure Germany (T-Systems International).

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci výstupu Power BI.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Zadejte popisný název, který se používá v dotazech k přesměrování výstupu dotazu na tento výstup Power BI. |
| Skupinový pracovní prostor |Pokud chcete povolit sdílení dat s ostatními uživateli Power BI, můžete vybrat skupiny uvnitř účtu Power BI nebo zvolit **Můj pracovní prostor,** pokud nechcete psát do skupiny. Aktualizace existující skupiny vyžaduje obnovení ověřování power bi. |
| Název datové sady |Zadejte název datové sady, který chcete použít pro výstup Power BI. |
| Název tabulky |Zadejte název tabulky pod datovou sadou výstupu Power BI. V současné době může mít výstup Power BI z úloh Stream Analytics v datové sadě jenom jednu tabulku. |
| Autorizovat připojení | Ke konfiguraci nastavení výstupu je potřeba autorizovat s Power BI. Jakmile tomuto výstupnímu přístupu udělíte řídicí muřídicí panel Power BI, můžete odvolat přístup změnou hesla uživatelského účtu, odstraněním výstupu úlohy nebo odstraněním úlohy Stream Analytics. | 

Návod konfigurace výstupu a řídicího panelu Power BI najdete v kurzu [Azure Stream Analytics a Power BI.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Na řídicím panelu Power BI explicitně nevytvářejte datovou sadu a tabulku. Datová sada a tabulka se automaticky naplní při spuštění úlohy a úloha začne pumpovat výstup do Power BI. Pokud dotaz na úlohu negeneruje žádné výsledky, datová sada a tabulka se nevytvoří. Pokud už Power BI měl datovou sadu a tabulku se stejným názvem, jako je ta, která je k dispozici v této úloze Stream Analytics, stávající data se přepíšou.
>

### <a name="create-a-schema"></a>Vytvoření schématu
Azure Stream Analytics vytvoří pro uživatele datovou sadu Power BI a schéma tabulky, pokud ještě neexistují. Ve všech ostatních případech je tabulka aktualizována novými hodnotami. V současné době může existovat pouze jedna tabulka v rámci datové sady. 

Power BI používá zásady uchovávání informací fifo (first-in, first-out). Data se budou shromažďovat v tabulce, dokud nedosáhne 200 000 řádků.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Převod datového typu z Stream Analytics na Power BI
Azure Stream Analytics aktualizuje datový model dynamicky za běhu, pokud se změní výstupní schéma. Změny názvu sloupce, změny typu sloupce a přidání nebo odebrání sloupců jsou sledovány.

Tato tabulka popisuje převody datových typů z [datových typů Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) na typy dat entit Power BI [(EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), pokud datová sada a tabulka Power BI neexistují.

Z analýzy streamu | K Power BI
-----|-----
bigint | Int64
Nvarchar(max) | Řetězec
datetime | Datum a čas
float | Double
Pole záznamů | Typ řetězce, konstantní hodnota "IRecord" nebo "IArray"

### <a name="update-the-schema"></a>Aktualizace schématu
Stream Analytics odvodí schéma datového modelu na základě první sady událostí ve výstupu. Později, pokud je to nutné, schéma datového modelu je aktualizován tak, aby vyhovovaly příchozí události, které se nemusí vejít do původního schématu.

Vyhněte se dotazu, `SELECT *` abyste zabránili aktualizaci dynamického schématu napříč řádky. Kromě potenciální důsledky pro výkon, to může mít za následek nejistotu času potřebnýpro výsledky. Vyberte přesná pole, která se mají zobrazit na řídicím panelu Power BI. Kromě toho by měly být hodnoty dat kompatibilní s vybraným datovým typem.


Předchozí/aktuální | Int64 | Řetězec | Datum a čas | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | Double
Double | Double | Řetězec | Řetězec | Double
Řetězec | Řetězec | Řetězec | Řetězec | Řetězec 
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec

## <a name="table-storage"></a>Úložiště Table

[Azure Table storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné, masivně škálovatelné úložiště, takže aplikace může automaticky škálovat tak, aby vyhovovala požadavkům uživatelů. Úložiště tabulek je úložiště klíčů/atributů NoSQL společnosti Microsoft, které můžete použít pro strukturovaná data s menšími omezeními schématu. Azure Table storage lze použít k ukládání dat pro trvalost a efektivní načítání.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu tabulky.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech k přesměrování výstupu dotazu do tohoto úložiště tabulky. |
| Účet úložiště |Název účtu úložiště, kam odesíláte výstup. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště. |
| Název tabulky |Název tabulky. Tabulka se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce, který obsahuje klíč oddílu. Klíč oddílu je jedinečný identifikátor pro oddíl v tabulce, který tvoří první část primárního klíče entity. Je to hodnota řetězce, která může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce, který obsahuje klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je hodnota řetězce, která může mít velikost až 1 kb. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí hodnota (100) je dostatečná pro většinu úloh. Další podrobnosti o úpravách tohoto nastavení naleznete ve [specifikaci Operace dávky tabulky.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) |

## <a name="service-bus-queues"></a>Fronty služby Service Bus

[Fronty sběrnice služby](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) service bus nabízejí doručení zprávy FIFO jednomu nebo více konkurenčním spotřebitelům. Obvykle zprávy jsou přijímány a zpracovávány příjemci v časovém pořadí, ve kterém byly přidány do fronty. Každá zpráva je přijata a zpracována pouze jedním příjemcem zprávy.

V [úrovni kompatibility 1.2](stream-analytics-compatibility-level.md)používá Azure Stream Analytics protokol zasílání zpráv [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) k zápisu do front a témat sběrnice. AMQP umožňuje vytvářet hybridní aplikace napříč platformami pomocí otevřeného standardního protokolu.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu fronty.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech k přesměrování výstupu dotazu do této fronty služby Service Bus. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit zasílání zpráv. |
| Název fronty |Název fronty service bus. |
| Název zásady fronty |Při vytváření fronty můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** fronty. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV a Avro jsou podporovány. |
| Kódování |Pro CSV a JSON, UTF-8 je pouze podporovaný formát kódování v tomto okamžiku. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislý pruh. |
| Formát |Platí pouze pro typ JSON. **Oddělený řádek** určuje, že výstup je formátován tím, že každý objekt JSON je oddělen novým řádkem. Pokud vyberete **Oddělené čáry**, JSON se čte po jednom objektu. Celý obsah sám o sobě by nebyl platný JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON. |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako uživatelské vlastnosti odchozí zprávy namísto datové části. Další informace o této funkci jsou v části [Vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce Vlastnosti systému | Nepovinný parametr. Dvojice hodnot klíče vlastnosti systému a odpovídající názvy sloupců, které je třeba připojit k odchozí zprávy namísto datové části. Další informace o této funkci jsou v části [Vlastnosti systému pro výstupy fronty a tématu sběrnice](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Počet oddílů je [založen na skladové jednotce a velikosti služby Service Bus](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celá hodnota pro každý oddíl.

## <a name="service-bus-topics"></a>Témata služby Service Bus
Fronty služby Service Bus poskytují metodu komunikace 1:1 od odesílatele k příjemci. [Témata služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) poskytují formu komunikace 1:N.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu tématu služby Service Bus.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech k přesměrování výstupu dotazu na toto téma sběrnice. |
| Obor názvů služby Service Bus |Kontejner pro sadu entit zasílání zpráv. Když jste vytvořili nové centrum událostí, vytvořili jste také obor názvů Service Bus. |
| Název tématu |Témata jsou entity zasílání zpráv, podobně jako centra událostí a fronty. Jsou navrženy tak, aby shromažďovaly streamy událostí ze zařízení a služeb. Když je téma vytvořeno, je také uveden konkrétní název. Zprávy odeslané na téma nejsou k dispozici, pokud není vytvořeno předplatné, takže se ujistěte, že je v rámci tématu jedno nebo více předplatných. |
| Název zásady tématu |Při vytváření tématu služby Service Bus můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** tématu. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. |
| Klíč zásad tématu |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV a Avro jsou podporovány. |
| Kódování |Pokud používáte formát CSV nebo JSON, musí být zadáno kódování. UTF-8 je v současné době jediným podporovaným formátem kódování. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislý pruh. |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako uživatelské vlastnosti odchozí zprávy namísto datové části. Další informace o této funkci jsou v části [Vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce Vlastnosti systému | Nepovinný parametr. Dvojice hodnot klíče vlastnosti systému a odpovídající názvy sloupců, které je třeba připojit k odchozí zprávy namísto datové části. Další informace o této funkci jsou v části [Vlastnosti systému pro výstupy fronty a tématu sběrnice](#system-properties-for-service-bus-queue-and-topic-outputs) |

Počet oddílů je [založen na skladové jednotce a velikosti služby Service Bus](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celá hodnota pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná databázová služba, která nabízí neomezené elastické škálování po celém světě, bohaté dotazy a automatické indexování přes datové modely bez ohledu na schéma. Další informace o možnostech kontejneru Azure Cosmos DB pro Stream Analytics najdete v [článku Stream Analytics s Azure Cosmos DB jako výstupní](stream-analytics-documentdb-output.md) článek.

Výstup Azure Cosmos DB z Stream Analytics není momentálně dostupný v oblastech Azure China 21Vianet a Azure Germany (T-Systems International).

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje jenom připojení k Azure Cosmos DB pomocí rozhraní SQL API.
> Ostatní azure cosmos DB API ještě nejsou podporovány. Pokud naminete Azure Stream Analytics na účty Azure Cosmos DB vytvořené pomocí jiných api, data nemusí být správně uložená.

Následující tabulka popisuje vlastnosti pro vytvoření výstupu Azure Cosmos DB.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Alias odkazující tento výstup v dotazu Stream Analytics. |
| Jímka | Azure Cosmos DB. |
| Možnost importu | Zvolte vybrat **Cosmos DB z předplatného** nebo **poskytněte nastavení Cosmos DB ručně**.
| Account ID | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB. |
| Klíč účtu | Sdílený přístupový klíč pro účet Azure Cosmos DB. |
| Databáze | Název databáze Azure Cosmos DB. |
| Název kontejneru | Název kontejneru, který má být použit, který musí existovat v Cosmos DB. Příklad:  <br /><ul><li> _MyContainer_: Kontejner s názvem "MyContainer" musí existovat.</li>|
| ID dokumentu |Nepovinný parametr. Název pole ve výstupních událostech, který se používá k určení primárního klíče, na kterém jsou založeny operace vložení nebo aktualizace.

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní služba bez serveru, kterou můžete použít ke spuštění kódu na vyžádání, aniž byste museli explicitně zřídit nebo spravovat infrastrukturu. Umožňuje implementovat kód, který se aktivuje událostmi, ke kterým dochází v Azure nebo partnerských službách. Díky této možnosti funkce Azure reagovat na aktivační události je přirozený výstup pro Azure Stream Analytics. Tento výstupní adaptér umožňuje uživatelům připojit Stream Analytics k funkcím Azure a spustit skript nebo část kódu v reakci na různé události.

Výstup Azure Functions z Stream Analytics momentálně není dostupný v oblastech Azure China 21Vianet a Azure Germany (T-Systems International).

Azure Stream Analytics vyvolá funkce Azure prostřednictvím aktivačních událostí HTTP. Výstupní adaptér Azure Functions je k dispozici s následujícími konfigurovatelnými vlastnostmi:

| Název vlastnosti | Popis |
| --- | --- |
| Function App |Název aplikace Azure Functions. |
| Funkce |Název funkce v aplikaci Azure Functions. |
| Klíč |Pokud chcete použít funkci Azure z jiného předplatného, můžete tak učinit poskytnutím klíče pro přístup k vaší funkci. |
| Maximální velikost dávky |Vlastnost, která umožňuje nastavit maximální velikost pro každou výstupní dávku, která je odeslána do funkce Azure. Vstupní jednotka je v bajtů. Ve výchozím nastavení je tato hodnota 262 144 bajtů (256 kB). |
| Maximální počet dávek  |Vlastnost, která umožňuje určit maximální počet událostí v každé dávce, která je odeslána do Funkce Azure. Výchozí hodnota je 100. |

Azure Stream Analytics očekává stav HTTP 200 z aplikace Funkce pro dávky, které byly úspěšně zpracovány.

Když Azure Stream Analytics obdrží výjimku 413 ("http Request Entity Too Large") z funkce Azure, zmenší velikost dávek, které odesílá do funkcí Azure. V kódu funkce Azure použijte tuto výjimku a ujistěte se, že Azure Stream Analytics neodesílá nadrozměrné dávky. Také se ujistěte, že maximální počet dávek a hodnoty velikosti použité ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

> [!NOTE]
> Během testovacího připojení Stream Analytics odešle prázdnou dávku do Funkce Azure k testování, pokud připojení mezi těmito dvěma funguje. Ujistěte se, že vaše aplikace Functions zpracovává prázdné dávkové požadavky, abyste se ujistili, že testovací připojení projde.

Také v situaci, kdy není žádná událost přistání v časovém okně, žádný výstup je generován. V důsledku toho **computeResult** funkce není volána. Toto chování je konzistentní s vestavěné funkce agregace v okně.

## <a name="custom-metadata-properties-for-output"></a>Vlastní vlastnosti metadat pro výstup 

Ke odchozím zprávám můžete připojit sloupce dotazu jako vlastnosti uživatele. Tyto sloupce nejdou do datové části. Vlastnosti jsou k dispozici ve formě slovníku na výstupní zprávě. *Klíč* je název sloupce a *hodnota* je hodnota sloupce ve slovníku vlastností. Všechny datové typy Stream Analytics jsou podporovány kromě record a array.  

Podporované výstupy: 
* Fronta služby Service Bus 
* Téma služby Service Bus 
* Centrum událostí 

V následujícím příkladu přidáme `DeviceId` dvě `DeviceStatus` pole a metadata. 
* Dotazu:`select *, DeviceId, DeviceStatus from iotHubInput`
* Konfigurace výstupu:`DeviceId,DeviceStatus`

![Sloupce vlastností](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Následující snímek obrazovky ukazuje vlastnosti výstupní zprávy kontrolované v EventHub prostřednictvím [Aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Vlastní vlastnosti události](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Vlastnosti systému pro výstupy fronty a tématu sběrnice 
Sloupce dotazu můžete připojit jako [vlastnosti systému](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) ke zprávám fronty odchozí sběrnice nebo zprávy Tématu. Tyto sloupce nejdou do datové části místo toho odpovídající BrokeredMessage [systémvlastnost](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) je naplněna hodnoty sloupce dotazu.
Tyto vlastnosti systému jsou podporovány - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Řetězcové hodnoty těchto sloupců jsou analyzovány jako odpovídající typ hodnoty vlastnosti systému a všechny chyby analýzy jsou považovány za chyby dat.
Toto pole je k dispozici jako formát objektu JSON. Podrobnosti o tomto formátu jsou následující -
* Obklopený složených závorkami {}.
* Napsáno v párech klíč/hodnota.
* Klíče a hodnoty musí být řetězce.
* Klíč je název vlastnosti systému a hodnota je název sloupce dotazu.
* Klíče a hodnoty jsou odděleny dvojtečkou.
* Každý pár klíč/hodnota je oddělen čárkou.

To ukazuje, jak používat tuto vlastnost –

* Dotazu:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Sloupce vlastností systému:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Tím se `MessageId` nastaví zprávy `column1`fronty sběrnice na hodnoty `column2`'s a PartitionKey je nastaven s hodnotami 's.

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílu a počet zapisovačů výstupu pro každý typ výstupu:

| Typ výstupu | Podpora dělení | Klíč oddílu  | Počet zapisovačů výstupu |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Ve vzoru předpony cesty použijte tokeny {date} a {time}. Zvolte formát data, například YYYY/MM/DD, DD/MM/YYYY nebo MM-DD-YYYY. HH se používá pro formát času. | Následuje vstupní dělení pro [plně paralelizovatelné dotazy](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ano, musí být povoleno. | Na základě partition by klauzule v dotazu. | Pokud je povolena možnost Zdědit dělení, následuje vstupní dělení pro [plně paralelizovatelné dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lepšího výkonu propustnost zápisu při načítání dat do Azure SQL Database najdete v článku [výstup Azure Stream Analytics do Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ano | Ve vzoru cesty použijte tokeny {date} a {time} z polí událostí. Zvolte formát data, například YYYY/MM/DD, DD/MM/YYYY nebo MM-DD-YYYY. HH se používá pro formát času. Výstup objektu blob lze rozdělit pomocí jednoho atributu vlastní události\<{fieldname} nebo {datetime: specifikátor>}. | Následuje vstupní dělení pro [plně paralelizovatelné dotazy](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ano | Ano | Liší se v závislosti na zarovnání oddílu.<br /> Pokud je klíč oddílu pro výstup centra událostí stejně zarovnán s krokem upstream (předchozí) dotaz, počet zapisovačů je stejný jako počet oddílů ve výstupu centra událostí. Každý zapisovatel používá [třídu EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) k odesílání událostí do konkrétního oddílu. <br /> Pokud klíč oddílu pro výstup centra událostí není zarovnán s krok dotazu proti proudu (předchozí), počet zapisovačů je stejný jako počet oddílů v tomto předchozím kroku. Každý zapisovatel používá [sendBatchAsync třídy](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) v **EventHubClient** k odeslání událostí do všech výstupních oddílů. |
| Power BI | Ne | Žádný | Neužívá se. |
| Azure Table Storage | Ano | Libovolný výstupní sloupec.  | Následuje vstupní dělení pro [plně paralelizované dotazy](stream-analytics-scale-jobs.md). |
| Téma služby Azure Service Bus | Ano | Automaticky vybráno. Počet oddílů je založen na [skladové jednotce a velikosti služby Service Bus](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celá hodnota pro každý oddíl.| Stejný jako počet oddílů ve výstupním tématu.  |
| Fronta služby Azure Service Bus | Ano | Automaticky vybráno. Počet oddílů je založen na [skladové jednotce a velikosti služby Service Bus](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celá hodnota pro každý oddíl.| Stejný jako počet oddílů ve výstupní frontě. |
| Azure Cosmos DB | Ano | Na základě partition by klauzule v dotazu. | Následuje vstupní dělení pro [plně paralelizované dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ano | Na základě partition by klauzule v dotazu. | Následuje vstupní dělení pro [plně paralelizované dotazy](stream-analytics-scale-jobs.md). |

Počet výstupů zapisovače `INTO <partition count>` lze také řídit pomocí (viz [INTO)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)klauzule v dotazu, které mohou být užitečné při dosahování požadované topologie úlohy. Pokud není výstupní adaptér rozdělen, nedostatek dat v jednom vstupním oddílu způsobí zpoždění až do pozdního času doručení. V takových případech je výstup sloučen do jednoho zapisovatele, což může způsobit kritické body v kanálu. Další informace o zásadách pozdního doručení najdete v [tématu Aspekty objednávky událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky
Azure Stream Analytics používá dávky proměnné velikosti ke zpracování událostí a zápisu do výstupů. Modul Stream Analytics obvykle nepíše jednu zprávu najednou a používá dávky pro efektivitu. Pokud je rychlost příchozích i odchozích událostí vysoká, Stream Analytics používá větší dávky. Když je rychlost odchozího přenosu nízká, používá menší dávky k udržení nízké latence.

Následující tabulka vysvětluje některé důležité informace pro dávkování výstupu:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikosti dávky |
| :--- | :--- | :--- |
| Azure Data Lake Store | Viz [Limity úložiště datových jezer](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Použijte až 4 MB na operaci zápisu. |
| Azure SQL Database | Konfigurovatelné pomocí maximálního počtu dávek. Ve výchozím nastavení je 10 000 a 100 minimálních řádků na jednu hromadnou vložení.<br />Viz [Limity Azure SQL](../sql-database/sql-database-resource-limits.md). |  Každá dávka je zpočátku hromadně vložena s maximálním počtem dávek. Dávka je rozdělena na polovinu (do minimálního počtu dávek) na základě opakovatelných chyb z SQL. |
| Azure Blob Storage | Viz [Limity úložiště Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Maximální velikost bloku objektů blob je 4 MB.<br />Maximální počet objektů blob je 50 000. |
| Azure Event Hubs  | 256 KB nebo 1 MB na zprávu. <br />Viz [Omezení centra událostí](../event-hubs/event-hubs-quotas.md). |  Pokud není zarovnáno dělení vstupu a výstupu, každá `EventData` událost je zabalena jednotlivě a odeslána v dávce až do maximální velikosti zprávy. K tomu dochází také v [případě, že jsou použity vlastní vlastnosti metadat.](#custom-metadata-properties-for-output) <br /><br />  Když je zarovnáno dělení vstupu a výstupu, `EventData` je do jedné instance zabaleno více událostí až do maximální velikosti zprávy a odesláno. |
| Power BI | Viz [Limity rozhraní API pro úložiště Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Viz [Limity úložiště Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Výchozí hodnota je 100 entit na jednu transakci. Podle potřeby ji můžete nakonfigurovat na menší hodnotu. |
| Fronta služby Azure Service Bus   | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [Omezení sběrnice](../service-bus-messaging/service-bus-quotas.md). | Pro každou zprávu použijte jednu událost. |
| Téma služby Azure Service Bus | 256 KB za zprávu pro úroveň Standard, 1 MB pro úroveň Premium.<br /> Viz [Omezení sběrnice](../service-bus-messaging/service-bus-quotas.md). | Pro každou zprávu použijte jednu událost. |
| Azure Cosmos DB   | Viz [Limity Db služby Azure Cosmos](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Velikost dávky a četnost zápisu se dynamicky upravují na základě odpovědí Azure Cosmos DB. <br /> Z Stream Analytics neexistují žádná předem stanovená omezení. |
| Azure Functions   | | Výchozí velikost dávky je 262 144 bajtů (256 kB). <br /> Výchozí počet událostí na dávku je 100. <br /> Velikost dávky je konfigurovatelná a lze ji zvýšit nebo snížit v [možnostech výstupu](#azure-functions)Stream Analytics .

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> 
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
