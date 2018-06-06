---
title: Pochopení výstupy z Azure Stream Analytics
description: Tento článek popisuje možnosti výstup dat, které jsou k dispozici v Azure Stream Analytics, včetně Power BI pro analysis výsledky.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: f2f616c5908d8583764425b62acd1650283d0695
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701713"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Pochopení výstupy z Azure Stream Analytics
Tento článek popisuje různé typy výstupů, které jsou k dispozici pro úlohu služby Azure Stream Analytics. Výstupy umožňují ukládat a uložte výsledky úlohy Stream Analytics. Pomocí výstupní data, můžete provést další obchodní analýza a datových skladů vaše data. 

Při návrhu dotazu Stream Analytics odkazovat na název výstupu pomocí [klauzule INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Můžete použít jednu výstupní na úlohu nebo několik výstupů za úlohu streamování, pokud potřebujete tím, že poskytuje více klauzulí INTO v dotazu.

K vytváření, úpravám a testovací úlohy služby Stream Analytics výstupy, můžete použít [portál Azure](stream-analytics-quick-create-portal.md#configure-output-to-the-job), [prostředí Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-output), a [sady Visual Studio](stream-analytics-tools-for-visual-studio.md).

Některé typy podporu výstupy [dělení](#partitioning), a [výstup velikosti dávky](#output-batch-size) optimalizovat výkon se liší.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics podporuje [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store je celopodnikové, flexibilně škálovatelné úložiště pro analytické úlohy s velkými objemy dat. Data Lake Store umožňuje ukládání dat z jakékoli velikosti, typu a rychlosti příjmu pro provozní a zjišťovací analýzy. Stream Analytics musí mít oprávnění pro přístup k Data Lake Store.

Azure Data Lake Store výstup ze služby Stream Analytics není momentálně k dispozici v Azure China (21Vianet) a oblastech Azure v Německu (T-systémy mezinárodní).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizace účtu Azure Data Lake Store

1. Pokud Data Lake Storage je vybraná jako výstupu na portálu Azure, zobrazí se výzva k autorizaci připojení k existující Data Lake Store.  

   ![Autorizovat Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Pokud již máte přístup do Data Lake Store, vyberte **autorizovat teď** a na stránce se zobrazí, která určuje **přesměrování na autorizace**. Po úspěšné ověřování, se zobrazí stránka, která můžete konfigurovat výstupní Data Lake Store.

3. Jakmile máte účet Data Lake Store, ověření, můžete konfigurovat vlastnosti pro výstup do Data Lake Store. V následující tabulce je seznam názvů vlastností a jejich popis konfigurace výstup do Data Lake Store.

   ![Autorizovat Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| Název vlastnosti | Popis | 
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto Data Lake Store. | 
| Název účtu | Název účtu Data Lake Storage, kde jsou odesílání výstupu. Zobrazí rozevírací seznam účtů Data Lake Store, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru používaná k zápisu souborů v rámci zadaného účtu Data Lake Store. Můžete určit jeden nebo více instancí {date} a {time} proměnné.</br><ul><li>Příklad 1: složku1/logs / {date} / {time}</li><li>Příklad 2: složku1/logs / {date}</li></ul><br>Časové razítko vytvoření struktura složek následuje místním ČASEM a ne.</br><br>Pokud se vzorek cesty soubor neobsahuje koncový znak "/", poslední vzor v cestě k souboru je považován za předponu názvu souboru. </br></br>Za těchto okolností se vytváří nové soubory:<ul><li>Změnit ve výstupu schématu</li><li>Vnější nebo vnitřní restartujte úlohy.</li></ul> |
| Formát data | Volitelné. Pokud se v cestě předponu používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány soubory. Příklad: Rrrr/MM/DD |
|Formát času | Volitelné. Pokud token čas se používá v cestě předponu, zadejte formát času, ve kterém jsou uspořádány soubory. Aktuálně jedinou podporovanou hodnotou je HH. |
| Formát serializace události | Formát serializace pro výstupní data. Jsou podporovány JSON, CSV a Avro.| 
| Kódování | Pokud používáte formát CSV nebo formátu JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8.|
| Oddělovač | Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat sdíleného svazku clusteru. Podporované hodnoty jsou čárku, středník, místo, karta a, svislá čára.|
| Formát | Platí jenom pro serializaci JSON. Řádcích: Určuje, že výstup je formátován tak, že každý objekt JSON oddělených nový řádek. Pole určuje, zda je výstup naformátovaný jako pole objektů JSON. Toto pole je zavřený jenom v případě, že je úloha pozastavena nebo Stream Analytics se přesunul další časový interval. Obecně platí, je vhodnější použít řádku oddělené formát JSON, protože nevyžaduje žádné zvláštní zpracování, při výstupní soubor je stále zápisu do.|

### <a name="renew-data-lake-store-authorization"></a>Obnovit autorizační Data Lake Store
Je potřeba novému ověření účtu Data Lake Store, pokud došlo ke změně jeho heslo vzhledem k tomu, že vaše úlohy vytvoření nebo poslední ověření. Pokud nemáte novému ověření, vaše úloha nevytváří výstup výsledků a zobrazuje chyba oznamující potřebu opětovné ověření v protokoly operací. V současné době není omezení kde ověřovací token, je potřeba ručně aktualizovat každých 90 dní pro všechny úlohy s výstupem Data Lake Store. 

Obnovit autorizace, **Zastavit** úlohu > přejděte na výstup do Data Lake Store > klikněte na tlačítko **obnovit autorizace** propojit a po krátkou dobu a stránky objeví se označující **přesměrování na autorizace...** . Na stránce automaticky zavře a v případě úspěšného označuje **autorizace byl úspěšně obnoven**. Pak je třeba kliknout na **Uložit** v dolní části stránky a můžete pokračovat restartováním úlohu z **naposledy Zastaveno** ztráty dat.

![Autorizovat Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) lze použít jako výstup pro data, která je povahou relační, nebo pro aplikace, které závisí na obsahu hostovaném v relační databázi. Úlohy Stream Analytics k zápisu do existující tabulky v databázi SQL Azure.  Schématu tabulky se musí přesně shodovat, pole a jejich typy se výstup z úlohy. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) dá se zadat taky jako výstupu prostřednictvím také možnost output databáze SQL. Následující tabulka uvádí názvy vlastností a jejich popis pro vytvoření databáze SQL výstup.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přesměrujte výstup dotazu do této databáze. |
| Databáze | Název databáze, kde jsou odesílání výstupu. |
| Název serveru | Název databáze SQL serveru. |
| Uživatelské jméno | Uživatelské jméno, který má přístup k zápisu do databáze. |
| Heslo | Heslo pro připojení k databas.e |
| Table | Název tabulky, kde je zapsán výstup. Název tabulky je malá a velká písmena a schéma této tabulky by se měl shodovat přesně počet polí a jejich typy generovaných výstupu úlohy. |

> [!NOTE]
> Pro výstup úlohy v Stream Analytics je aktuálně podporuje nabídku Azure SQL Database. Virtuální počítač Azure s systému SQL Server databáze připojené však není podporován. Toto je může v budoucích verzích změnit.
> 

## <a name="blob-storage"></a>Blob Storage
Úložiště BLOB nabízí cenově výhodné a škálovatelné řešení pro ukládání velkého objemu nestrukturovaných dat v cloudu.  Úvod do Azure Blob storage a jeho použití, najdete v dokumentaci na [jak používat objekty BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Následující tabulka uvádí názvy vlastností a jejich popis vytvoření výstupního objektu blob.

| Název vlastnosti | Popis | 
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto úložiště objektů blob. |
| Účet úložiště | Název účtu úložiště, kde jsou odesílání výstupu. |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště. |
| Kontejner úložiště | Kontejnery poskytují možnost logického seskupování pro objekty BLOB uložené ve službě Microsoft Azure Blob. Při nahrávání do objektu blob ve službě Blob, je nutné zadat kontejner pro tento objekt blob. |
| Vzor cesty | Volitelné. Vzor cesty souborů používá k zápisu objektů BLOB v rámci zadaného kontejneru. </br></br> Ve vzoru cestu můžete použít jeden nebo více instancí datum čas proměnných pro určení četnosti, které jsou napsané objekty BLOB: </br> {date}, {time} </br> </br>Pokud si nejste [preview](https://aka.ms/ASAPreview), můžete také určit vlastní {pole} názvů z vašich dat událostí na objekty BLOB oddílu, kde název pole je alfanumerické znaky a může obsahovat mezery, pomlčky a podtržítka. Omezení na vlastních polích, patří: <ul><li>Případ nezaložené (nelze rozlišit mezi sloupci "ID" a sloupec "id")</li><li>Vnořená pole nejsou povolené (místo toho použít alias v dotazu úlohy k "vyrovnání" pole)</li><li>Výrazy nelze použít jako název pole</li></ul>Příklady: <ul><li>Příklad 1: cluster1/logs / {date} / {time}</li><li>Příklad 2: cluster1/logs / {date}</li><li>Příklad 3 (preview): cluster1 / {client_id} / {date} / {time}</li><li>Příklad 4 (preview): cluster1 / {myField} kde je dotaz: Vyberte data.myField jako myField vstup z;</li></ul><br>Časové razítko vytvoření struktura složek následuje místním ČASEM a ne.</br><BR> Pojmenovávání souborů zahrnuje následující konvence: </br> {Path Prefix Pattern}/schemaHashcode_Guid_Number.extension </br></br> Příklad výstupní soubory: </br><ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li><li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul><br/>
| Formát data | Volitelné. Pokud se v cestě předponu používá token kalendářního data, můžete vybrat formát data, ve kterém jsou uspořádány soubory. Příklad: Rrrr/MM/DD |
| Formát času | Volitelné. Pokud token čas se používá v cestě předponu, zadejte formát času, ve kterém jsou uspořádány soubory. Aktuálně jedinou podporovanou hodnotou je HH. |
| Formát serializace události | Formát serializace pro výstupní data.  Jsou podporovány JSON, CSV a Avro.
| Kódování | Pokud používáte formát CSV nebo formátu JSON, kódování musí být zadán. V tuto chvíli je jediným podporovaným formátem kódování UTF-8. |
| Oddělovač | Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat sdíleného svazku clusteru. Podporované hodnoty jsou čárkami, středník, adresní prostor, karta a svislá čára. |
| Formát | Platí jenom pro serializaci JSON. Řádcích: Určuje, že výstup je formátován tak, že každý objekt JSON oddělených nový řádek. Pole určuje, zda je výstup naformátovaný jako pole objektů JSON. Toto pole je zavřený jenom v případě, že je úloha pozastavena nebo Stream Analytics se přesunul další časový interval. Obecně platí, je vhodnější použít řádku oddělené formát JSON, protože nevyžaduje žádné zvláštní zpracování, při výstupní soubor je stále zápisu do. |

Při použití úložiště objektů blob jako výstup, je vytvořen nový soubor do objektu BLOB v následujících případech:

* Pokud velikost souboru přesahuje maximální počet povolených bloků (aktuálně 50 000). Maximální povolený počet bloků může dosaženo bez dosažení objektů blob maximální povolenou velikost. Pokud je vysoká míra výstupních, zobrazí se další bajtů za bloku a velikost souboru je větší. Pokud je míra výstupních nízké, každý blok má méně dat a velikost souboru je menší.
* Pokud dojde ke změně schématu ve výstupu a formát výstupu vyžaduje pevného schématu (sdíleného svazku clusteru a Avro).  
* Pokud se restartuje úlohu, buď externě uživatelem ji zastavuje a potom ji spustit, nebo interně pro obnovení systému údržby nebo chyba.  
* Pokud dotaz je plně rozdělena na oddíly, nový soubor se vytvoří pro každý oddíl výstup.  
* Pokud soubor nebo kontejneru účtu úložiště je odstraněno uživatelem.  
* Je-li výstup čas oddíly pomocí předpony vzorek cesty, nový objekt blob se používá při dotaz přesune do příští hodiny.
* Pokud výstup je rozdělena na oddíly pomocí vlastních polí, vytvoří se nový objekt blob za klíč oddílu, pokud neexistuje.
* Pokud výstup je rozdělena na oddíly pomocí vlastního pole, které překročí mohutnost klíče oddílu 8000, mohou být vytvořeny nové objektů blob na klíč oddílu.

## <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) služba je vysoce škálovatelná publikování a odběru na přijímač událostí. Ho může shromažďovat miliony událostí za sekundu. Jedno použití centra událostí jako výstup je, když se stane vstup streamování jiné úlohy, výstup úlohy Stream Analytics.

Existuje několik parametrů, které jsou potřeba ke konfiguraci datové proudy centra událostí jako výstup.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto centra událostí. |
| Obor názvů centra událostí |Na obor názvů Centrum událostí je kontejner sady entit pro zasílání zpráv. Při vytváření nového centra událostí vytvořit taky na obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásad centra událostí | Zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč použitý k ověření přístupu k oboru názvů centra událostí. |
| Sloupec klíče oddílu [Nepovinné] | Tento sloupec obsahuje klíč oddílu centra událostí výstupu. |
| Formát serializace události | Formát serializace pro výstupní data.  Jsou podporovány JSON, CSV a Avro. |
| Kódování | Znakové sady UTF-8 pro sdílený svazek clusteru a JSON, je jediným podporovaným formátem kódování v tuto chvíli. |
| Oddělovač | Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárkami, středník, adresní prostor, karta a svislá čára. |
| Formát | Platí jenom pro serializaci JSON. Řádcích: Určuje, že výstup je formátován tak, že každý objekt JSON oddělených nový řádek. Pole určuje, zda je výstup naformátovaný jako pole objektů JSON. Toto pole je zavřený jenom v případě, že je úloha pozastavena nebo Stream Analytics se přesunul další časový interval. Obecně platí, je vhodnější použít řádku oddělené formát JSON, protože nevyžaduje žádné zvláštní zpracování, při výstupní soubor je stále zápisu do. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) lze použít jako výstup úlohy Stream Analytics k poskytování prostředí bohaté vizualizace výsledků analýzy. Tato funkce slouží pro provozní řídicí panely, generování sestav a metrika řízené generování sestav.

Výstup Power BI ze služby Stream Analytics není momentálně k dispozici v Azure China (21Vianet) a oblastech Azure v Německu (T-systémy mezinárodní).

### <a name="authorize-a-power-bi-account"></a>Autorizovat účet Power BI
1. Pokud Power BI je vybraná jako výstupu na portálu Azure, zobrazí se výzva k autorizaci stávajícímu uživateli Power BI nebo vytvořte nový účet Power BI.  
   
   ![Autorizace uživatele Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. Vytvořte nový účet, pokud nemáte ještě mít jeden a pak klikněte na autorizovat.  Na následující stránce se zobrazí:
   
   ![Účet Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. V tomto kroku zadejte pracovní nebo školní účet pro ověřování výstup Power BI. Pokud nejsou se už přihlásili pro Power BI, zvolte přihlašovací nahoru. Pracovní nebo školní účet, který použijete pro Power BI může lišit od účtu předplatného Azure, který jste právě přihlášení s.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurovat vlastnosti výstup Power BI
Až budete mít účet Power BI ověřený, můžete konfigurovat vlastnosti pro výstup Power BI. V následující tabulce je seznam názvů vlastností a jejich popis konfigurace výstupu Power BI.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přesměrujte výstup dotaz na tento výstup PowerBI. |
| Pracovní prostor skupiny |Chcete-li povolit sdílení dat s dalšími uživateli Power BI můžete vybrat skupiny uvnitř svůj účet Power BI nebo zvolte "Pracovní prostor", pokud nechcete pro zápis do skupiny.  Aktualizuje existující skupinu vyžaduje obnovování ověřování Power BI. |
| Název datové sady |Zadejte název datové sady, který je požadován pro výstup Power BI k použití |
| Název tabulky |Zadejte název tabulky v rámci datové sady výstup Power BI. V současné době Power BI výstup z úlohy Stream Analytics může mít pouze jednu tabulku v datové sadě |

Návod konfigurace výstup Power BI a řídicí panel, najdete v článku [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) článku.

> [!NOTE]
> Nevytvářejte explicitně datovou sadu a tabulkou v řídicím panelu Power BI. Datovou sadu a tabulky se automaticky vyplní při zahájení úlohy a úlohy spustí čerpací výstup do Power BI. Pamatujte, že pokud dotaz úlohy není generovat žádné výsledky, datové sady a tabulky nebyl vytvořen. Všimněte si, že pokud Power BI již bylo datovou sadu a tabulka se stejným názvem jako ten, který je součástí této úlohy Stream Analytics, se přepíše stávající data.
> 

### <a name="schema-creation"></a>Vytvoření schématu
Azure Stream Analytics vytvoří na datovou sadu Power BI a tabulku jménem uživatele, pokud ještě neexistuje. Ve všech ostatních případech se v tabulce aktualizuje s novými hodnotami. V současné době není omezení že pouze jedna tabulka může existovat v rámci datové sady.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Datový typ – převod ze služby Stream Analytics k Power BI.
Azure Stream Analytics aktualizuje datový model dynamicky za běhu, pokud se změní schéma výstupu. Změny názvu sloupce, sloupec typu změny a přidání nebo odebrání sloupců sledovány všechny.

Tato tabulka obsahuje konverze datových typů z [Stream Analytics datové typy](https://msdn.microsoft.com/library/azure/dn835065.aspx) k Power BIs [Entity Data Model (EDM) typy](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) Pokud na datovou sadu POWER BI a tabulku neexistují.

Ze služby Stream Analytics | K Power BI.
-----|-----|------------
bigint | Int64
nvarchar(max) | Řetězec
datetime | Datum a čas
float | Double
Pole záznamu | Řetězec typu konstantní hodnoty "IRecord" nebo "IArray"

### <a name="schema-update"></a>Aktualizace schématu
Stream Analytics odvodí schéma modelu dat podle první sadu událostí ve výstupu. Později v případě potřeby schéma modelu dat se aktualizuje zohlednit příchozí události, které nemusí začlenit do původní schématu.

`SELECT *` Dotazu je nutno, aby se zabránilo aktualizace dynamické schématu více řádků. Kromě potenciální ovlivnit výkon to může také způsobit nejistoty čas potřebný pro dané výsledky. Měla by být vybrána přesný pole, které je potřeba se zobrazí na řídicí panel Power BI. Kromě toho musí být hodnoty data kompatibilní s vybraným datovým typem.


Předchozí/aktuální | Int64 | Řetězec | Datum a čas | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Řetězec | Řetězec | Double
Double | Double | Řetězec | Řetězec | Double
Řetězec | Řetězec | Řetězec | Řetězec |  | Řetězec | 
Datum a čas | Řetězec | Řetězec |  Datum a čas | Řetězec


### <a name="renew-power-bi-authorization"></a>Obnovit autorizace Power BI
Pokud se heslo ke svému účtu Power BI změní po vytvoření nebo poslední ověřit vaše úloha Stream Analytics, musíte k novému ověření, Stream Analytics. Pokud je na vašeho klienta Azure Active Directory (AAD) nakonfigurovaná služba Multi-Factor Authentication (MFA), musíte také obnovit Power BI autorizace každé dva týdny. Příznakem tohoto problému je žádný výstup úlohy a "ověřit uživatele chyba" v protokoly operací:

  ![Chyba aktualizace tokenu Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Chcete-li vyřešit tento problém, přejděte na výstupu Power BI a zastavit spuštěná úloha.  Vyberte **obnovit autorizace** propojit a restartujte úlohu z **naposledy Zastaveno** ztráty dat.

  ![Power BI obnovuje autorizace](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure Table storage](../storage/common/storage-introduction.md) nabízí vysoce dostupné, enormně škálovatelné úložiště, takže aplikace může automaticky škálovat podle požadavků uživatelů. Úložiště Table je úložiště klíčů/atributů NoSQL společnosti Microsoft, která můžete využít pro strukturovaná data s méně omezení na schéma. Azure Table storage můžete použít k ukládání dat pro trvalosti a efektivní načtení.

Následující tabulka uvádí názvy vlastností a jejich popis vytváření výstupní tabulka.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přesměrujte výstup dotazu do této tabulky úložiště. |
| Účet úložiště |Název účtu úložiště, kde jsou odesílání výstupu. |
| Klíč účtu úložiště |Přístupový klíč přidružený k účtu úložiště. |
| Název tabulky |Název tabulky. Tabulka se vytvoří, pokud neexistuje. |
| Klíč oddílu |Název výstupního sloupce obsahujícího klíč oddílu. Klíč oddílu je jedinečný identifikátor pro oddíl v dané tabulce, která tvoří první část primárního klíče entity. Je řetězcovou hodnotu, která může mít velikost až 1 KB. |
| Klíč řádku |Název výstupního sloupce obsahujícího klíč řádku. Klíč řádku je jedinečný identifikátor entity v daném oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. |
| Velikost dávky |Počet záznamů pro dávkové operace. Výchozí hodnota (100) je dostatečné pro většinu úloh. Odkazovat [tabulky dávkovou operaci specifikace](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) další podrobnosti o úpravě tohoto nastavení. |
 
## <a name="service-bus-queues"></a>Fronty služby Service Bus
[Fronty služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) nabízejí First In doručení zpráv první Out (FIFO) na jeden nebo několik konkurenčních spotřebitelů. Obvykle se očekává zprávy, přijímají a zpracovávají v pořadí, ve kterém byly přidány do fronty, a každou zprávu přijme a zpracuje jenom jeden příjemce zprávy.

Následující tabulka uvádí názvy vlastností a jejich popis vytváření výstupní fronty.

| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přesměrujte výstup dotazu do této fronty Service Bus. |
| Obor názvů služby Service Bus |Obor názvů sběrnice je kontejner sady entit pro zasílání zpráv. |
| Název fronty |Název fronty Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete také vytvořit zásady sdíleného přístupu na kartě Konfigurace fronty. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč použitý k ověření přístupu k oboru názvů Service Bus |
| Formát serializace události |Formát serializace pro výstupní data.  Jsou podporovány JSON, CSV a Avro. |
| Kódování |Pro sdílený svazek clusteru a JSON UTF-8 v tuto chvíli je jediným podporovaným formátem kódování |
| Oddělovač |Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárkami, středník, adresní prostor, karta a svislá čára. |
| Formát |Platí jenom pro typ formátu JSON. Řádcích: Určuje, že výstup je formátován tak, že každý objekt JSON oddělených nový řádek. Pole určuje, zda je výstup naformátovaný jako pole objektů JSON. |

Počet oddílů je [na základě Service Bus SKU a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečný celočíselnou hodnotu pro každý oddíl.

## <a name="service-bus-topics"></a>Témata služby Service Bus
Zatímco fronty služby Service Bus poskytují způsob komunikace k jedné z odesílatele k příjemce, [témata služby Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) zadejte formuláři na více komunikace.

Následující tabulka uvádí názvy vlastností a jejich popis vytváření výstupní tabulka.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název používaný v dotazech na přesměrujte výstup dotazu k tomuto tématu Service Bus. |
| Obor názvů služby Service Bus |Obor názvů sběrnice je kontejner sady entit pro zasílání zpráv. Při vytváření nového centra událostí taky vytvoříte obor názvů sběrnice |
| Název tématu |Témata jsou entity pro zasílání zpráv podobné centrům událostí a frontám. Jsou navržené ke shromažďování streamů událostí z mnoha různých zařízení a služeb. Při vytváření téma je rovněž dán určitý název. Zprávy odeslané do tématu není k dispozici, pokud není vytvořená předplatné, zajistěte proto jsou jeden nebo více odběrů v tématu |
| Název zásad tématu |Když vytvoříte téma, můžete také vytvořit zásady sdíleného přístupu na kartě konfigurace tématu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče |
| Klíč zásad tématu |Sdílený přístupový klíč použitý k ověření přístupu k oboru názvů Service Bus |
| Formát serializace události |Formát serializace pro výstupní data.  Jsou podporovány JSON, CSV a Avro. |
| Kódování |Pokud používáte formát CSV nebo formátu JSON, kódování musí být zadán. Znakové sady UTF-8 v tuto chvíli je jediným podporovaným formátem kódování |
| Oddělovač |Platí jenom pro serializaci sdílených svazků clusteru. Stream Analytics podporuje celou řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárkami, středník, adresní prostor, karta a svislá čára. |

Počet oddílů je [na základě Service Bus SKU a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečný celočíselnou hodnotu pro každý oddíl.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuované databáze, více modelu služby, aby neomezenou elastické škálování nabízí kolem celém světě, plnohodnotný dotazovací a automatické indexování přes vázané na schéma datové modely, garantované s nízkou latencí a špičkový komplexní SLA. Další informace o možnosti kolekce Cosmos DB Stream Analytics, naleznete [Stream Analytics s DB Cosmos jako výstup](stream-analytics-documentdb-output.md) článku.

Azure Cosmos DB výstup ze služby Stream Analytics není momentálně k dispozici v Azure China (21Vianet) a oblastech Azure v Německu (T-systémy mezinárodní).

> [!Note]
> V tomto okamžiku Azure Stream Analytics podporuje jenom připojení pomocí CosmosDB **rozhraní SQL API**.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovány. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, data nemusí být uložen správně. 

Následující tabulka popisuje vlastnosti pro vytvoření výstupu Azure Cosmos DB.
| Název vlastnosti | description |
| --- | --- |
| Alias pro výstup | Alias odkazoval tento výstup v dotazu Stream Analytics. |
| Jímka | Databáze Cosmos |
| Možnost importu | Vyberte buď "Vybrat ze svého předplatného Cosmos DB", nebo "Zadejte Cosmos DB nastavení ručně".
| ID účtu | Název nebo identifikátor URI účtu Cosmos DB koncového bodu. |
| Klíč účtu | Sdílený přístupový klíč pro účet Cosmos DB. |
| Databáze | Název databáze Cosmos DB. |
| Vzor názvu kolekce | Název kolekce nebo jejich vzor pro kolekce, který se má použít. <br/>Formát názvu kolekce se dá vytvořit pomocí tokenu volitelná {partition}, na kterém oddíly začínají od 0. Dva příklady:  <br/>1. _Kolekce_ – jednu kolekci s názvem "Kolekce", musí existovat.  <br/>2. _Kolekce {partition}_ – na základě dělicí sloupce. <br/>Musí existovat kolekce dělicí sloupec – "MyCollection0", "MyCollection1", "MyCollection2" a tak dále. |
| Klíč oddílu | Volitelné. To je potřeba jenom Pokud používáte tokenu {partition} ve vzoru názvu vaší kolekce.<br/> Klíč oddílu je název pole ve výstupních událostech používaný k určení klíče pro rozdělení výstupu do kolekcí.<br/> Jedinou kolekci výstupu lze použít všechny libovolný výstupního sloupce. Například ID oddílu. |
| ID dokumentu |Volitelné. Název pole ve výstupních událostech používaný k určení primárního klíče, na které insert nebo update jsou založené operace.  

## <a name="azure-functions"></a>Azure Functions
Azure Functions je výpočetní služba bez serveru umožňující spouštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Umožňuje implementovat kód, který je aktivovaného událostmi, ke kterým dochází v Azure nebo služby třetích stran.  Tato schopnost Azure Functions reagovat na aktivační události usnadňuje přirozené výstup Azure Stream Analytics. Tento výstup adaptér umožňuje uživatelům připojení k Azure Functions Stream Analytics a spusťte skript nebo část kódu v reakci na celou řadu událostí.

Výstup funkce Azure Stream Analytics není momentálně k dispozici v Azure China (21Vianet) a oblastech Azure v Německu (T-systémy mezinárodní).

Azure Stream Analytics vyvolá Azure Functions prostřednictvím protokolu HTTP aktivační události. Nový adaptér výstup funkce Azure je k dispozici následující konfigurovatelné vlastnosti:

| Název vlastnosti | description |
| --- | --- |
| Function App |Název aplikace Azure Functions |
| Funkce |Název funkce v aplikaci funkce Azure |
| Klíč |Pokud chcete používat funkci Azure z jiné předplatné, můžete tak učinit zadáním klíče pro přístup k vaší – funkce |
| Maximální velikost dávky |Tuto vlastnost lze nastavit maximální velikost každé dávky výstup, které je odesláno funkce Azure. Ve výchozím nastavení tato hodnota je 256 KB |
| Maximální počet v dávce  |Jako název značí, tato vlastnost umožňuje určit maximální počet událostí v každé dávce, která se odešlou do Azure Functions. Počet maximální dávky výchozí hodnota je 100 |

Když Azure Stream Analytics obdrží 413 (http požadavku Entity příliš velký) výjimky z funkce Azure, snižuje velikost dávky, který odesílá Azure Functions. V kódu funkce Azure používejte výjimku a ujistěte se, že Azure Stream Analytics neodešle nadměrné velikosti dávky. Taky se ujistěte, že jsou konzistentní s hodnotou zadanou v portálu Stream Analytics batch maximální počet a velikost hodnoty používané ve funkci. 

Také v situaci, kde není žádná událost cílová stránka časové okno, nevygeneruje žádný výstup. V důsledku toho není computeResult funkce volána. Toto chování je konzistentní s integrovanou agregační funkce vracející.

## <a name="partitioning"></a>Dělení

Následující tabulka shrnuje podporu oddílu a počet výstupních zapisovače pro každý typ výstupu:

| Typ výstupu | Vytváření oddílů podpory | Klíč oddílu  | Počet modulů pro zápis výstupu | 
| --- | --- | --- | --- |
| Azure Data Lake Store | Ano | Použití {date} a {time} tokeny v předponu vzorek cesty. Vyberte formát, datum, jako je například RRRR/MM/DD, DD/MM/RRRR MM-DD-RRRR. HH se používá pro formát času. | Následuje vstupní vytváření oddílů pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). | 
| Azure SQL Database | Ne | Žádný | Není k dispozici. | 
| Azure Blob Storage | Ano | Použití {date} a {time} tokeny od vaší pole událostí v vzorek cesty. Vyberte formát, datum, jako je například RRRR/MM/DD, DD/MM/RRRR MM-DD-RRRR. HH se používá pro formát času. Jako součást [preview](https://aka.ms/ASAPreview), můžou být dělené výstupního objektu blob jedna událost vlastní atribut {fieldname}. | Následuje vstupní vytváření oddílů pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). | 
| Centrum událostí Azure | Ano | Ano | Se liší v závislosti na zarovnání oddílu.</br> Pokud výstup, který nadřazeného (předchozí) dotazu krok počet zapisovače stejným způsobem je zarovnán klíč oddílu centra událostí je stejný počet výstupní oddíly centra událostí. Každý zapisovače používá pro EventHub [EventHubSender třída](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) k odesílání událostí na konkrétní oddíl. </br> Pokud je klíč oddílu není v souladu s nadřazeného (předchozí) dotazu krok počet zapisovače centra událostí výstup stejný jako počet oddílů v této předchozí krok. Každý zapisovače používá EventHubClient [SendBatchAsync třída](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) k odesílání událostí na všechny oddíly výstup. |
| Power BI | Ne | Žádný | Není k dispozici. | 
| Azure Table Storage | Ano | Všechny výstupního sloupce.  | Následuje vstupní vytváření oddílů pro [plně paralelizovaná málo dotazy](stream-analytics-scale-jobs.md). | 
| Témata sběrnice Azure | Ano | Automaticky vybrali. Počet oddílů je založen na [Service Bus SKU a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečný celočíselnou hodnotu pro každý oddíl.| Stejný jako počet oddílů v tématu výstup.  |
| Fronty Azure Service Bus | Ano | Automaticky vybrali. Počet oddílů je založen na [Service Bus SKU a velikost](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečný celočíselnou hodnotu pro každý oddíl.| Stejný jako počet oddílů ve výstupní fronty. |
| Azure Cosmos DB | Ano | Ve vzoru názvu kolekce pomocí tokenu {partition}. Hodnota {partition} je založena na klauzuli PARTITION BY v dotazu. | Následuje vstupní vytváření oddílů pro [plně paralelizovaná málo dotazy](stream-analytics-scale-jobs.md). |
| Azure Functions | Ne | Žádný | Není k dispozici. | 

## <a name="output-batch-size"></a>Velikost dávky výstup
Azure Stream Analytics používá proměnnou velikost dávky zpracovat události a zapisovat do výstupů. Obvykle modul Stream Analytics nelze zapsat jednu zprávu najednou a používá dávky pro efektivitu. Když je vysoká příchozí a odchozí rychlost událostí, používá větší dávky. Jestliže je míra odchozí nízkou, používá menší dávky zachovat nízkou latencí. 

Následující tabulka popisuje některé aspekty výstup dávkování:

| Typ výstupu | Maximální velikost zprávy | Optimalizace velikost dávky |
| :--- | :--- | :--- | 
| Azure Data Lake Store | V tématu [omezuje Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits) | Až 4 MB za operace zápisu |
| Azure SQL Database | 10 000 maximální počet řádků na jeden hromadné vložení</br>100 minimální počet řádků na jeden jedné hromadné vložení </br>Viz také [Azure SQL omezuje](../sql-database/sql-database-resource-limits.md) |  Každou dávku je původně hromadné s maximální velikost dávky vložit a batch může rozdělit na polovinu (dokud minimální velikost dávky) podle opakovatelné chyby SQL. |
| Azure Blob Storage | V tématu [omezuje Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maximální velikost objektu Blob bloku je 4 MB volného místa</br>Maximální počet bock objektů Blob je 50000 |
| Centrum událostí Azure   | 256 KB jednotlivých zpráv </br>Viz také [omezuje služby Event Hubs](../event-hubs/event-hubs-quotas.md) |    Při vytváření oddílů vstupní výstupní není zarovnat, každá událost je zabaleny jednotlivě EventData a odeslány dávky až po zprávy maximální velikost (1 MB pro skladová položka Premium). </br></br>  Při vytváření oddílů vstup a výstup je zarovnán, jsou více událostí balí do jednoho EventData až zprávy maximální velikost a odeslat.    |
| Power BI | V tématu [omezuje Power BI Rest API](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | V tématu [omezuje Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Výchozí je 100 entit na jedné transakce a je možné nakonfigurovat na menší hodnotu podle potřeby. |
| Fronty Azure Service Bus   | 256 KB jednotlivých zpráv</br> Viz také [omezuje Service Bus](../service-bus-messaging/service-bus-quotas.md) | Jedna událost za správu. |
| Azure tématu Service Bus | 256 KB jednotlivých zpráv</br> Viz také [omezuje Service Bus](../service-bus-messaging/service-bus-quotas.md) | Jedna událost za správu. |
| Azure Cosmos DB   | V tématu [omezuje Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Batch velikost a zápisem frekvence je upravenou dynamicky na základě CosmosDB odpovědi. </br> Žádná předem určený omezení ze služby Stream Analytics. |
| Azure Functions   | | Výchozí velikost dávky je 246 KB. </br> Výchozí počet událostí na jednu dávku je 100. </br> Velikost dávky je možné konfigurovat a můžete zvětšit nebo zmenšit v Stream Analytics [výstup možnosti](#azure-functions). 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Rychlý úvod: Vytvoření úlohy Stream Analytics pomocí portálu Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
