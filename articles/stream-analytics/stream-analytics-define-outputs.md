---
title: Vysvětlení vytvořené jako výstupy z Azure Stream Analytics
description: Tento článek popisuje možnosti výstupní data k dispozici ve službě Azure Stream Analytics, včetně Power BI pro výsledky analýzy.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2018
ms.custom: seodec18
ms.openlocfilehash: 8ae55028bbc44a9383be6723f9bc6d39787cca45
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767299"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Vysvětlení vytvořené jako výstupy z Azure Stream Analytics
Tento článek popisuje různé typy výstupy, které jsou k dispozici pro úlohy Azure Stream Analytics. Výstupy umožňují ukládat a uložit výsledky úlohy Stream Analytics. Pomocí výstupní data, můžete provést další obchodní analýzy a skladování dat vaše data.

Při návrhu dotazu Stream Analytics, odkazovat na název výstupu pomocí [klauzule INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování, pokud potřebujete tím, že poskytuje více klauzulí INTO v dotazu.

K vytváření, úpravám a testovací úlohy Stream Analytics výstupy, můžete použít [webu Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [prostředí Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [rozhraní REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), a [sady Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy podporu výstupy [dělení](#partitioning), a [výstup velikosti dávky](#output-batch-size) umožňuje optimalizovat propustnost se liší.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics podporuje [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store je celopodnikové, flexibilně škálovatelné úložiště pro analytické úlohy s velkými objemy dat. Data Lake Store umožňuje ukládat data libovolné velikosti, typu a rychlosti příjmu pro provozní a zjišťovací analýzy. Stream Analytics musí mít oprávnění k přístupu Data Lake Store.

Azure Data Lake Store výstup ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Povolit účet Azure Data Lake Store

1. Při výběru úložiště Data Lake jako výstup na webu Azure Portal, zobrazí se výzva k autorizaci připojení existující Data Lake Store.

   ![Autorizovat připojení pro Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Pokud již máte přístup k Data Lake Store, vyberte **povolit teď** a zobrazí stránku určující **přesměrování na autorizaci**. Po úspěšném ověření, zobrazí se stránka, která můžete konfigurovat výstupní Data Lake Store.

3. Jakmile budete mít účet Data Lake Store, ověření, můžete konfigurovat vlastnosti pro výstup do Data Lake Store. V následující tabulce je seznam názvů vlastností a jejich popis nakonfigurovat výstup do Data Lake Store.

   ![Definovat Data Lake Store jako výstupní Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto Data Lake Store. |
| Název účtu | Název účtu Data Lake Storage Pokud odesíláte výstupu. Zobrazí se rozevírací seznam účtů Data Lake Store, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru používaná k zápisu souborů v rámci zadaného účtu Data Lake Store. Můžete zadat jednu nebo víc instancí {date} a {time} proměnné.<br /><ul><li>Příklad 1: složku1/logs / {date} / {time}</li><li>Příklad 2: složku1/logs / {date}</li></ul><br />Časové razítko struktury složek, vytvoří se řídí není místním časem a UTC.<br /><br />Pokud vzor cesty souboru neobsahuje koncový znak "/", poslední vzorek v cestě k souboru je považován za předponu názvu souboru. <br /><br />Vytvoří se nové soubory v těchto případech:<ul><li>Změna v schéma výstupu</li><li>Externí nebo interní restartovat úlohy.</li></ul> |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
|Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány.|
| Kódování | Pokud používáte formát CSV nebo JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8.|
| Oddělovač | Platí jenom pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, prostor, karty a svislá čára.|
| Formát | Platí jenom pro serializaci JSON. Řádcích: Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. Pole určuje, že je výstup naformátovaný jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít řádku oddělené JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do.|

### <a name="renew-data-lake-store-authorization"></a>Obnovit autorizaci Data Lake Store
Je nutné donutit účtu Data Lake Store, pokud od úlohy byl vytvořen nebo poslední ověřený změnila jeho heslo. Pokud nemáte donutit, vaše úloha nevytvoří výstup a zobrazí chybu s informacemi potřebu opakované v protokoly operací. V současné době neexistuje omezení kde ověřovací token je potřeba ručně aktualizovat každých 90 dní pro všechny úlohy s výstupem Data Lake Store.

Chcete obnovit autorizaci, **Zastavit** úlohy > přejděte na výstup do Data Lake Store > klikněte na tlačítko **obnovit autorizaci** propojit a po krátkou dobu a stránky objeví označující **přesměrování autorizace...**. Na stránce automaticky zavře a v případě úspěchu, označuje **autorizaci úspěšně obnovil**. Pak budete muset kliknout na **Uložit** v dolní části stránky a můžete pokračovat restartováním úlohy z **posledním zastavení** k zamezení ztrátě.

![Obnovit autorizaci Data Lake Store ve výstupu](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) může sloužit jako výstup pro data, která jsou svou povahou relační, nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Úlohy Stream Analytics zapisovat do existující tabulky ve službě Azure SQL Database. Schéma tabulky musí přesně odpovídat pole a jejich typy se výstup z vaší úlohy. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) je taky možné specifikovat jako výstup prostřednictvím také možnost výstupu SQL Database. Další informace o způsobech, jak zlepšit propustnost zápisu, naleznete [Stream Analytics pomocí služby Azure SQL DB jako výstup](stream-analytics-sql-output-perf.md) článku. Následující tabulka uvádí názvy vlastností a jejich popis vytvářejícím výstupem SQL Database.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu k této databázi. |
| Databáze | Název databáze, kde jsou odesílání výstupu. |
| Název serveru | Název databáze SQL serveru. |
| Uživatelské jméno | Uživatelské jméno, která má přístup k zápisu do databáze. Stream Analytics podporuje jenom ověřování SQL. |
| Heslo | Heslo pro připojení k databázi. |
| Table | Název tabulky, kde je zapsán výstup. Název tabulky je velká a malá písmena a schéma této tabulky musí přesně shodovat počet polí a jejich typy generovaná výstupu úlohy. |
|Dědit schéma oddílu| To umožňuje dědit schéma rozdělení oddílů v předchozím kroku dotazu umožňuje paralelní plně topologie s více zapisovačů do tabulky. Další informace najdete v tématu [výstupu Azure Stream Analytics ke službě Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Odpovídá počet v dávce| Několik doporučených horní limit záznamů odeslané s každou hromadné vložení transakce.|

> [!NOTE]
> Nabídka Azure SQL Database je aktuálně podporovány pro výstup úlohy ve Stream Analytics. Ale není podporované systémem SQL Server s databází připojený virtuální počítač Azure. To se může v budoucích verzích změnit.
>

## <a name="blob-storage"></a>Blob Storage
Úložiště objektů BLOB nabízí cenově výhodné a škálovatelné řešení pro ukládání velkých objemů nestrukturovaných dat v cloudu. Úvod do Azure Blob storage a jeho použití, naleznete v dokumentaci na [jak používat objekty BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Následující tabulka uvádí názvy vlastností a jejich popis pro vytvoření výstupního objektu blob.

| Název vlastnosti       | Popis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto úložiště objektů blob. |
| Účet úložiště     | Název účtu úložiště, ve kterém jsou odesílání výstupu.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště.                              |
| Kontejner úložiště   | Kontejnery poskytují možnost logického seskupování u objektů BLOB uložených ve službě Microsoft Azure Blob. Při nahrání objektu blob do služby Blob service, je nutné zadat kontejner pro tohoto objektu blob. |
| Vzor cesty | Volitelné. Vzor cesty souboru použitý k zápisu objektů BLOB v zadaném kontejneru. <br /><br /> V vzor cesty můžete použít jednu nebo víc instancí proměnných datum čas pro určení četnosti, které jsou napsané objektů blob: <br /> {date} {time} <br /><br />Dělení vlastních objektů blob můžete zadat jeden název vlastní {pole} ze svých dat událostí do oddílu objektů BLOB. Název pole je alfanumerický a může obsahovat mezery, pomlčky a podtržítka. Omezení na vlastních polích, patří: <ul><li>Malá a velká nerozlišování velikosti písmen (nelze rozlišit mezi sloupci "ID" a sloupec "id")</li><li>Vnořená pole nejsou povolené (místo toho použít alias v dotazu úlohy k "vyrovnání" pole)</li><li>Výrazy nelze použít jako název pole.</li></ul> <br /><br /> Tato funkce umožňuje, aby použití konfigurací specifikátor formátu vlastní data a času v cestě. Vlastní datum a čas formátu musí být zadaný jeden po druhém, ohraničená {data a času:\<specifikátor >} – klíčové slovo. Povolené vstupy \<specifikátor > jsou rrrr, MM, M, dd, d, HH, H, mm, m, ss nebo s. {Data a času:\<specifikátor >} – klíčové slovo slouží více než jednou v cestě k vytvoření konfigurace vlastní data a času. <br /><br />Příklady: <ul><li>Příklad 1: cluster1/logs / {date} / {time}</li><li>Příklad 2: cluster1/logs / {date}</li><li>Příklad 3: cluster1 / {client_id} / {date} / {time}</li><li>Příklad 4: cluster1 / {datetime:ss} / {myField} dotaz, kde je: Vyberte data.myField jako myField ze vstupu;</li><li>Příklad 5: cluster1/rok = {datetime:yyyy} / měsíc = {datetime:MM} za den = {datetime:dd}</ul><br /><br />Časové razítko struktury složek, vytvoří se řídí není místním časem a UTC.<br /><br />Tato konvence pojmenování souboru následující: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Příklad výstupní soubory:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Další informace o této funkci najdete [objektů blob Azure Stream Analytics, vlastní, výstup dělení](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Volitelné. Pokud v předponová cesta se používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány souborů. Příklad: RRRR/MM/DD |
| Formát času | Volitelné. Pokud token čas, který se používá v předponovou cestu, zadejte formát času, ve kterém jsou uspořádány souborů. Momentálně je jediná podporovaná hodnota HH. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování    | Pokud používáte formát CSV nebo JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač   | Platí jenom pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadou běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát      | Platí jenom pro serializaci JSON. Řádcích: Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. Pole určuje, že je výstup naformátovaný jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít řádku oddělené JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do. |

Při použití úložiště objektů blob jako výstup, je vytvořen nový soubor v objektu blob v následujících případech:

* Pokud soubor překračuje maximální počet povolených bloků (aktuálně 50 000). Maximální povolený počet bloků může dosaženo nedorazí velikost objektu blob maximální povolené. Při vysoká míra výstupních uvidíte počet bajtů za bloku a velikost souboru je větší. Pokud je míra výstupních nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud dojde ke změně schématu ve výstupu a výstupní formát vyžaduje pevného schématu (CSV nebo Avro).
* Pokud restartování úlohy, externí uživatele, zastavte ho a potom ji spustit, nebo interně údržby nebo Chyba obnovení systému.
* Pokud je dotaz dělený plně, je vytvořen nový soubor pro každý oddíl výstup.
* Pokud soubor nebo kontejner účtu úložiště je odstraněno uživatelem.
* Pokud ve výstupu se čas rozdělit na oddíly pomocí vzor předpony cesty, nový objekt blob se používá při dotazu přesune do příští hodiny.
* Pokud výstup je rozdělená na oddíly pomocí vlastního pole, vytvoří se nový objekt blob každý klíč oddílu, pokud neexistuje.
* Pokud výstup je rozdělená na oddíly pomocí vlastního pole přesahuje 8000 mohutnost klíče oddílu, může být vytvořen nový objekt blob každý klíč oddílu.

## <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) služba je vysoce škálovatelná publikování a odběr schopná. Může shromažďovat miliony událostí za sekundu. Jedno použití centra událostí jako výstup při vstupu jiná úloha streamování se stane výstup úlohy Stream Analytics.

Existuje několik parametrů, které jsou potřeba ke konfiguraci centra událostí datových proudů jako výstup.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do centra událostí. |
| Obor názvů centra událostí |Obor názvů služby Event Hub je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor obor názvů centra událostí. |
| Název centra událostí | Název centra událostí výstupu. |
| Název zásad centra událostí | Zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč používá k ověření přístupu pro obor názvů centra událostí. |
| Sloupec klíče oddílu [volitelný] | Tento sloupec obsahuje klíč oddílu centra událostí výstupu. |
| Formát serializace události | Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování | CSV a JSON UTF-8 je jediný podporovaný formát kódování v tuto chvíli. |
| Oddělovač | Platí jenom pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát | Platí jenom pro serializaci JSON. Řádcích: Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. Pole určuje, že je výstup naformátovaný jako pole objektů JSON. Toto pole se zavře, když je úloha pozastavena nebo Stream Analytics je přesunout další časový interval. Obecně je vhodnější použít řádku oddělené JSON, protože nevyžaduje žádným zvláštním způsobem, zatímco výstupní soubor je stále se zapisují do. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) může sloužit jako výstup pro úlohu Stream Analytics k poskytování bohatých funkcí vizualizace poznatky o výsledky analýzy. Tato funkce je možné pro provozní řídicích panelů, generování sestav a metriku řízené generování sestav.

Výstup Power BI ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Povolit účet Power BI
1. Když Power BI je vybrán jako výstup na webu Azure Portal, zobrazí se výzva k autorizaci stávajícího uživatele Power BI nebo vytvořte nový účet Power BI.
   
   ![Povolit uživatelům Power BI konfigurace výstupu](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Vytvořte nový účet, pokud není ještě mít jeden a pak klikněte na tlačítko Povolit. Můžete vidět na následující stránce:
   
   ![Ověření ve službě Power BI z účtu Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. V tomto kroku zadejte pracovní nebo školní účet pro autorizaci výstup Power BI. Pokud jste se už zaregistrovali službu Power BI, vyberte znaménko. Může být odlišné od účtu předplatného Azure, který jste aktuálně přihlášení pomocí pracovního nebo školního účtu, který používáte pro Power BI.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurace vlastností výstup Power BI
Jakmile budete mít účet Power BI, ověření, můžete konfigurovat vlastnosti pro výstup do Power BI. V následující tabulce je seznam názvů vlastností a jejich popis nakonfigurovat výstup do Power BI.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu na tento výstup Power BI. |
| Pracovní prostor skupiny |Povolit sdílení dat s dalšími uživateli Power BI můžete vybrat skupiny, do účtu Power BI nebo zvolte možnost "My Workspace", pokud nechcete k zápisu do skupiny. Aktualizuje se existující skupina vyžaduje obnovování ověřování Power BI. |
| Název datové sady |Zadejte název datové sady, který je požadován pro výstup Power BI k použití |
| Název tabulky |Zadejte název tabulky v datové sadě Power BI výstupu. V současné době Power BI výstup z úlohy Stream Analytics může mít pouze jednu tabulku v datové sadě |

Návod, jak nakonfigurovat výstup Power BI a řídicí panel, najdete v článku [Azure Stream Analytics a Power BI](stream-analytics-power-bi-dashboard.md) článku.

> [!NOTE]
> Nevytvářejte explicitně datová sada a tabulky v řídicím panelu Power BI. Datová sada a tabulky se automaticky vyplní při spuštění úlohy a úlohy spustí čerpací výstup do Power BI. Poznámka: Pokud dotaz úlohy negeneruje žádné výsledky, datové sady, a není vytvořena tabulka. Všimněte si, že pokud Power BI už je datová sada a tabulky se stejným názvem jako ten, který je k dispozici v této úloze Stream Analytics, existující data přepsána.
>

### <a name="schema-creation"></a>Vytvoření schématu
Azure Stream Analytics vytvoří datová sada Power BI a tabulka jménem uživatele, pokud ještě neexistuje. Ve všech ostatních případech se aktualizuje tabulku s novými hodnotami. V současné době neexistuje omezení může existovat pouze jeden tabulky v datové sadě. Power BI používá FIFO zásady uchovávání informací. Při povolené, shromáždí data v tabulce, dokud nedosáhne 200 000 řádků.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Převod ze Stream Analytics pro Power BI datového typu
Azure Stream Analytics aktualizuje datový model dynamicky za běhu, pokud se změní schéma výstupu. Změny názvů sloupců, změny typu sloupce a přidání nebo odebrání sloupců jsou všechny sledovány.

Tato tabulka popisuje převody typů dat z [Stream Analytics datové typy](https://msdn.microsoft.com/library/azure/dn835065.aspx) na napájení BIs [typy Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) Pokud neexistuje datová sada POWER BI a tabulky.

Ze služby Stream Analytics | Do Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Řetězec
datetime | Datum a čas
float | Double
Pole záznamu | Řetězec typu, konstanta "IRecord" nebo "IArray"

### <a name="schema-update"></a>Aktualizace schématu
Stream Analytics odvodí schéma modelu dat založené na první sadu událostí. ve výstupu. Později v případě potřeby se aktualizuje schéma modelu dat tak, aby vyhovovaly příchozí události, které nemusí vejde do původního schématu.

`SELECT *` Dotazu třeba se jim vyhnout, aby se zabránilo dynamické schéma aktualizace více řádků. Kromě potenciální vliv na výkon může také vést k nejistoty čas potřebný pro výsledky. Musí být vybrána přesně definovat pole, které je potřeba zobrazený na řídicí panel Power BI. Kromě toho musí být hodnoty dat kompatibilní s vybraným datovým typem.


Předchozí nebo aktuální | Int64 | Řetězec | Datum a čas | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | Double
Double | Double | Řetězec | Řetězec | Double
Řetězec | Řetězec | Řetězec | Řetězec |  | Řetězec |
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec


### <a name="renew-power-bi-authorization"></a>Obnovit autorizaci Power BI
Pokud po vytvoření nebo posledního ověření vaší úlohy Stream Analytics se změní heslo ke svému účtu Power BI, budete muset donutit Stream Analytics. Pokud Vícefaktorové ověřování (MFA) je nakonfigurován na tenantovi služby Azure Active Directory (AAD), musíte také obnovit autorizaci Power BI každé dva týdny. Příznaky tohoto problému je žádné výstupy a "ověřit uživatele chybu" v protokoly operací:

  ![Power BI ověření chyba uživatele](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Chcete-li vyřešit tento problém, zastavte spuštěné úlohy a přejděte na výstup do Power BI. Vyberte **obnovit autorizaci** propojit a restartujte úlohu z **posledním zastavení** k zamezení ztrátě.

  ![Obnovit autorizaci Power BI pro výstup](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Azure Table storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné a široce škálovatelné úložiště, takže aplikace může automaticky škálovat podle požadavků uživatelů. Úložiště Table je úložiště klíčů/atributů typu NoSQL od Microsoftu, který můžete využít pro strukturovaná data s méně omezeními ve schématu. Azure Table storage můžete použít k ukládání dat pro trvalost a efektivní načtení.

Následující tabulka uvádí názvy vlastností a jejich popis pro vytvoření výstupu tabulky.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech pro přesměrování výstupu dotazu do této tabulky úložiště. |
| Účet úložiště |Název účtu úložiště, ve kterém jsou odesílání výstupu. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště. |
| Název tabulky |Název tabulky. V tabulce se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce obsahujícího klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v dané tabulce, která tvoří první část primárního klíče entity. Je řetězec, který může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce obsahujícího klíč řádku. Klíč řádku je jedinečný identifikátor pro entitu v rámci daného oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkovou operaci. Výchozí hodnota (100) je dostatečné pro většinu úloh. Odkazovat [tabulky dávkové operace specifikace](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) další podrobnosti ke změně tohoto nastavení. |

## <a name="service-bus-queues"></a>Fronty služby Service Bus
[Fronty služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) nabízejí první aplikace, doručování zpráv první Out (FIFO) na jeden nebo několik konkurenčních spotřebitelů. Zprávy jsou obvykle přijímají a zpracovávají v pořadí, ve kterém byly přidány do fronty, a je každou zprávu přijme a zpracuje jenom jeden spotřebitel zprávy.

Následující tabulka uvádí názvy vlastností a jejich popis vytváření výstupní fronty.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech pro přesměrování výstupu dotazu do této fronty služby Service Bus. |
| Obor názvů služby Service Bus |Obor názvů služby Service Bus je kontejner sady entit pro zasílání zpráv. |
| Název fronty |Název fronty Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete vytvořit taky zásady sdíleného přístupu na kartě Konfigurace fronty. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč používá k ověření přístupu k oboru názvů služby Service Bus |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |CSV a JSON UTF-8 v tuto chvíli je jediný podporovaný formát kódování |
| Oddělovač |Platí jenom pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |
| Formát |Platí jenom pro typ formátu JSON. Řádcích: Určuje takový formát výstupu tak, že každý objekt JSON oddělené novým řádkem. Pole určuje, že je výstup naformátovaný jako pole objektů JSON. |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.

## <a name="service-bus-topics"></a>Témata služby Service Bus
Fronty služby Service Bus nabízejí způsob komunikace 1: 1 z odesílatele příjemci, [témata služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zadejte jeden mnoho forma komunikace.

Následující tabulka uvádí názvy vlastností a jejich popis pro vytvoření výstupu tabulky.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přímé výstup dotazu na toto téma služby Service Bus. |
| Obor názvů služby Service Bus |Obor názvů služby Service Bus je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor názvů Service Bus |
| Název tématu |Témata jsou entity pro zasílání zpráv, podobné centrům událostí a frontám. Jsou navržené ke shromažďování streamů událostí z nejrůznějších zařízení a služeb. Když se na téma, dostane taky určitý název. Zprávy odeslané do tématu není k dispozici, pokud není vytvořené předplatné, zajistěte proto existují jednoho nebo více předplatných v rámci tématu |
| Název zásad tématu |Když vytvoříte téma, můžete vytvořit taky zásady sdíleného přístupu na kartě konfigurace tématu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče |
| Klíč zásad tématu |Sdílený přístupový klíč používá k ověření přístupu k oboru názvů služby Service Bus |
| Formát serializace události |Formát serializace pro výstupní data. JSON, CSV nebo Avro, jsou podporovány. |
| Kódování |Pokud používáte formát CSV nebo JSON, kódování musí být zadán. UTF-8 v tuto chvíli je jediný podporovaný formát kódování |
| Oddělovač |Platí jenom pro serializaci sdíleného svazku clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, místa, karty a svislá čára. |

Počet oddílů je [na základě skladové položky služby Service Bus a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná a vícemodelová databázová služba, že nabízí neomezené elastické škálování po celém světě, bohaté možnosti dotazů a automatického indexování přes dogmaticky na schématu datové modely, garance nízké latence a špičkové komplexní smlouvy SLA. Další informace o možnostech kolekce Cosmos DB pro Stream Analytics, naleznete [Stream Analytics pomocí služby Cosmos DB jako výstup](stream-analytics-documentdb-output.md) článku.

Výstup Azure Cosmos DB ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China (21Vianet).

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje jenom připojení do služby cosmos DB pomocí **rozhraní SQL API**.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně.

Následující tabulka popisuje vlastnosti pro vytvoření výstup Azure Cosmos DB.
| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup | Alias pro najdete na tomto výstup v dotazu Stream Analytics. |
| Jímka | Databáze Cosmos |
| Možnost importu | Zvolte možnost "Vybrat ze svého předplatného služby Cosmos DB", nebo "nastavení zadejte Cosmos DB ručně".
| ID účtu | Název nebo identifikátor URI účtu Cosmos DB koncového bodu. |
| Klíč účtu | Sdílený přístupový klíč pro účet služby Cosmos DB. |
| Databáze | Název databáze Cosmos DB. |
| Vzor názvu kolekce | Název kolekce nebo jejich vzor pro kolekce, který se má použít. <br />Formát názvu kolekce lze sestavit pomocí nepovinného tokenu {partition}, ve kterém oddíly začínají od 0. Dva příklady:  <br />1. _Mojekolekce_ – jednu kolekci s názvem "Mojekolekce", musí existovat.  <br />2. _Mojekolekce {partition}_ – podle dělicí sloupec. <br />Rozdělení sloupce kolekce je potřeba vytvořit – "MyCollection0", "MyCollection1", "MyCollection2" a tak dále. |
| Klíč oddílu | Volitelné. To je potřeba pouze v případě, že používáte tokenu {partition} ve vaší vzor názvu kolekce.<br /> Klíč oddílu je název pole ve výstupních událostech používaný k určení klíče pro rozdělení výstupu do kolekcí.<br /> Pro výstup jedné kolekce lze použít libovolný sloupec libovolného výstup. Například ID oddílu. |
| ID dokumentu |Volitelné. Název pole ve výstupních událostech používaný k určení primárního klíče, na které insert nebo update jsou založené operace.

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní služba bez serveru umožňující spouštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. To umožňuje implementaci kódu aktivovaného událostmi, ke kterým dochází v Azure nebo služby třetích stran. Díky schopnosti služby Azure Functions reagovat na triggery je přirozeným výstupem pro Azure Stream Analytics. Tento adaptér pro výstup umožňuje uživatelům připojit Stream Analytics k Azure Functions a spuštění skriptu nebo kusu kódu v reakci na různé události.

Výstup Azure Functions ze Stream Analytics není aktuálně dostupná v oblastech Azure Germany (T-Systems International) a Azure China (21Vianet).

Azure Stream Analytics volá Azure Functions prostřednictvím triggerů HTTP. Nový adaptér pro výstup funkce Azure je k dispozici následující konfigurovatelné vlastnosti:

| Název vlastnosti | description |
| --- | --- |
| Function App |Název aplikace Azure Functions |
| Funkce |Název funkce ve vaší aplikaci funkcí Azure |
| Klíč |Pokud chcete použít funkci z jiného předplatného Azure, lze provést zadáním klíče pro přístup k funkci |
| Maximální velikost dávky |Tato vlastnost slouží k nastavení maximální velikosti pro každou výstupní dávku, která se odesílá do dané funkce Azure. Vstupní jednotka je v bajtech. Ve výchozím nastavení tato hodnota je 262 144 bajtů (256 KB) |
| Maximální počet v dávce  |Jak název naznačuje, tato vlastnost umožňuje určit maximální počet událostí v každé dávce, která se odesílá do služby Azure Functions. Počet maximální batch výchozí hodnota je 100 |

Když Azure Stream Analytics přijímá 413 (příliš velký požadavek Entity http) výjimky z funkce Azure Functions, snižuje velikost dávky, který odesílá do služby Azure Functions. V kódu funkce Azure Ujistěte se, že Azure Stream Analytics neodesílá dávky nadměrné velikosti neposílá pomocí této výjimky. Také se ujistěte, že počet a velikost hodnoty maximální batch použít ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

Také v situaci, kde není žádná událost doručení časový interval, nebude vygenerován žádný výstup. V důsledku toho computeResult funkce není volána. Toto chování je konzistentní s integrovanou oddílové agregační funkce.

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílu a počet modulů pro zápis výstupu pro každý typ výstupu:

| Typ výstupu | Dělení podpory | Klíč oddílu  | Počet modulů pro zápis výstupu |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Použijte {date} a {time} tokeny v vzor předpony cesty. Vyberte formát data, jako je rrrr/MM/DD, DD/MM/RRRR MM-DD-RRRR. HH se používá pro formát času. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ano | Podle v klauzuli PARTITION BY v dotazu | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). Další informace o dosažení lépe zápisu propustnost při načítání dat do databáze SQL Azure najdete v tématu [výstupu Azure Stream Analytics ke službě Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ano | Použijte {date} a {time} tokeny z polí událostí v vzor cesty. Vyberte formát data, jako je rrrr/MM/DD, DD/MM/RRRR MM-DD-RRRR. HH se používá pro formát času. Výstupní objekt BLOB lze rozdělit pomocí jedné události vlastní atribut {pole fieldname} nebo {data a času:\<specifikátor >}. | Následuje vstupní dělení pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md). |
| Centrum událostí Azure | Ano | Ano | Se liší v závislosti na zarovnání oddílu.<br /> Výstupem, který klíč oddílu je stejně v souladu s nadřazeného (předchozí) kroku dotazu, počet zapisovače centra událostí je stejný počet výstupu oddílů centra událostí. Každý writer používá pro EventHub [EventHubSender třídy](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) k odesílání událostí do konkrétních oddílů. <br /> Pokud výstup Eventhub klíč oddílu není zarovnána s nadřazeného (předchozí) kroku dotazu, počet modulů pro zápis je stejný jako počet oddílů v tomto dřívějším kroku. Každý writer používá EventHubClient [SendBatchAsync třídy](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) k odesílání událostí do všech oddílů výstup. |
| Power BI | Ne | Žádný | Není k dispozici. |
| Azure Table Storage | Ano | Výstup je sloupec.  | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Služby Azure Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.| Stejný jako počet oddílů tématu výstup.  |
| Fronty Azure Service Bus | Ano | Automaticky zvolí. Počet oddílů je založen na [SKU služby Service Bus a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečné celé číslo pro každý oddíl.| Stejný jako počet oddílů ve výstupní frontě. |
| Azure Cosmos DB | Ano | Pomocí tokenu {partition} v vzor názvu kolekce. Hodnota {partition} je založena na klauzuli PARTITION BY v dotazu. | Následuje vstupní dělení pro [plně paralelizovaná dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ne | Žádný | Není k dispozici. |

Pokud adaptér pro výstup není rozdělena na oddíly, chybějící data v jednom oddílu vstupní způsobí zpoždění až pozdní doručení množství času.  V takovém případě je výstupem sloučeny s jedním zapisujícím procesem, který může způsobit problémová místa ve vašem kanálu. Další informace o pozdní přijetí zásad najdete v tématu [aspekty pořadí událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost dávky výstupu
Azure Stream Analytics používá proměnné velikosti dávky pro zpracování událostí a zápis do výstupů. Obvykle modul Stream Analytics nezapisuje jednu zprávu v čase a používá dávky efektivitu. Příchozí a odchozí frekvence událostí je vysoké, využívá větších dávek. Po nízká frekvence odchozího přenosu dat se používá po menších dávkách zachovat s nízkou latencí.

Následující tabulka popisuje některé důležité informace pro výstup do dávek:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikosti dávky |
| :--- | :--- | :--- |
| Azure Data Lake Store | Zobrazit [omezuje úložiště Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits) | Až 4 MB za operace zápisu |
| Azure SQL Database | 10 000 maximální počet řádků na jeden hromadné vložení<br />Minimum 100 řádků na jeden příkaz bulk insert <br />Viz také [omezuje Azure SQL](../sql-database/sql-database-resource-limits.md) |  Každé dávky je zpočátku hromadné vložení pomocí maximální velikost dávky a batch může rozdělit na polovinu (až do velikosti dávky Min) podle opakovatelná chyby SQL. |
| Azure Blob Storage | Zobrazit [omezení služby Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maximální velikost objektu Blob bloku je 4 MB<br />Maximální počet objektů Blob bock je 50000 |
| Centrum událostí Azure   | 256 KB na zprávu <br />Viz také [omezuje služby Event Hubs](../event-hubs/event-hubs-quotas.md) |   Při vstupní výstupní dělení není zarovnání, každé události je jednotlivě zabalené v EventData a odeslány dávku až do maximální počet zpráv velikosti (1 MB pro SKU úrovně Premium). <br /><br />  Při dělení vstupně-výstupní zarovnán, více událostí jsou zkomprimována do jednoho EventData až do maximální velikost a odeslat.  |
| Power BI | Zobrazit [omezuje rozhraní Rest API Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Zobrazit [omezení služby Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Výchozí je 100 entit na jedné transakce a lze nastavit na hodnotu menší, podle potřeby. |
| Fronty Azure Service Bus   | 256 KB na zprávu<br /> Viz také [omezuje služby Service Bus](../service-bus-messaging/service-bus-quotas.md) | Jednu událost za správu. |
| Azure tématu služby Service Bus | 256 KB na zprávu<br /> Viz také [omezuje služby Service Bus](../service-bus-messaging/service-bus-quotas.md) | Jednu událost za správu. |
| Azure Cosmos DB   | Zobrazit [omezení služby Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Batch velikost a zápis, je frekvence upravené dynamicky na základě odpovědi služby cosmos DB. <br /> Žádné předem omezení ze Stream Analytics. |
| Azure Functions   | | Výchozí velikost dávky je 262 144 bajtů (256 KB). <br /> Výchozí počet událostí za služby batch je 100. <br /> Velikost dávky je možné konfigurovat a můžete se zvýší nebo sníží ve službě Stream Analytics [výstup možnosti](#azure-functions).

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]

> [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
