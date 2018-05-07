---
title: Sestavení a nasazení modelu strojového učení pomocí systému SQL Server na virtuálním počítači Azure | Microsoft Docs
description: Proces pokročilou analýzu a technologie v akci
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: deguhath
ms.openlocfilehash: 1e53814c0c0598380944d576e1937e38d1e1d792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Proces Team dat. vědecké účely v akci: pomocí SQL serveru
V tomto kurzu vás provede procesem vytváření a nasazování modelu strojového učení pomocí systému SQL Server a veřejně dostupné datové sady – [NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/) datovou sadu. Postup dodržovat standardní data vědecké účely pracovního postupu: ingestování a zkoumat data, pracovníka funkce pro usnadnění učení se pak sestavení a nasazení modelu.

## <a name="dataset"></a>NYC taxíkem cest v popis datové sady
Data NYC taxíkem cesty je přibližně 20GB komprimovaných souborů CSV (nekomprimovaným ~ 48GB), skládající se z více než 173 milionů jednotlivých cest a tarify placené pro každou cestu. Každý záznam cestě zahrnuje číslo licence vyzvednutí a odkládací umístění a čas, anonymizovaná hackerský (ovladač) a číslo Medailon (taxi na jedinečné id). Data obsahuje všechny služebních cest v roku 2013 a je dostupné pro každý měsíc následující dvě datové sady:

1. 'trip_data' CSV obsahuje podrobnosti o cestě, například na počtu cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Trip_fare CSV obsahující podrobnosti o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek a daně, tipy a mýtné, a celkovou velikost placené. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení k cestě\_dat a cesty\_tarif se skládá z pole: medailonu, hackerský\_licence a vyzvednutí\_data a času.

## <a name="mltasks"></a>Příklady předpovědi úlohy
Jsme se formulovali tři předpovědi problémů na základě *tip\_velikost*, konkrétně:

1. Binární klasifikace: předpovědět, zda byl tip placené cesty, tj. *tip\_velikost* větší než $0 je pozitivní příklad, při *tip\_velikost* $ 0 je Příklad záporné.
2. Více třídami klasifikace: K předpovědi rozsahu tipu placené pro cestu. Jsme rozdělit *tip\_velikost* do pěti přihrádek nebo třídy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Úloha regrese: K předvídání množství tip placené cesty.  

## <a name="setup"></a>Nastavení si Azure datové vědy prostředí pro pokročilou analýzu
Jak vidíte z [plánování vašeho prostředí](plan-your-environment.md) průvodce, máte několik možností pro práci s datovou sadu NYC taxíkem cest v Azure:

* Práce s data do objektů BLOB služby Azure a modelu v Azure Machine Learning
* Načíst data do databáze systému SQL Server a modelu v Azure Machine Learning

V tomto kurzu si předvedeme paralelní hromadného importu dat do SQL Server, který zkoumání dat, funkce technici a dolů vzorkování pomocí SQL Server Management Studio a také pomocí IPython Poznámkový blok. [Ukázkové skripty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) a [poznámkových bloků IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) jsou sdíleny v Githubu. Ukázkový IPython Poznámkový blok pro práci s daty v Azure blob je k dispozici také ve stejném umístění.

Nastavení prostředí vědecké zpracování dat Azure:

1. [vytvořit účet úložiště](../../storage/common/storage-create-storage-account.md)
2. [Vytvořit pracovní prostor služby Azure Machine Learning](../studio/create-workspace.md)
3. [Zřízení virtuálního počítače s datové vědy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), který poskytuje systému SQL Server a server služby IPython Poznámkový blok.
   
   > [!NOTE]
   > Ukázkové skripty a poznámkových bloků IPython bude stažen do virtuálního počítače vědecké zpracování dat během procesu instalace. Po dokončení skriptu po instalaci virtuálního počítače, bude mít ukázky knihovna dokumentů Virtuálního počítače:  
   > 
   > * Ukázku skripty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Ukázka IPython poznámkových bloků: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   kde `<user_name>` je váš virtuální počítač Windows přihlašovací jméno. Bude označujeme ukázkové složky jako **ukázkové skripty** a **poznámkových bloků IPython ukázka**.
   > 
   > 

Na základě velikost datové sady, umístění zdroje dat a vybrané Azure cílovém prostředí, tento scénář je podobná [scénář \#5: velké datové sady v místních souborů cíle systému SQL Server ve virtuálním počítači Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Získat Data z veřejné zdroje
Chcete-li získat [NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/) datové sady z veřejného umístění, můžete použít některou z metod popsaných v [přesun dat do a z Azure Blob Storage](move-azure-blob.md) zkopírovat data do nového virtuálního počítače.

Ke zkopírování dat pomocí nástroje AzCopy:

1. Přihlaste se k virtuálnímu počítači (VM)
2. Vytvořte nový adresář v datový disk Virtuálního počítače (Poznámka: Nepoužívejte dočasné Disk, který se dodává s virtuální počítač jako datový Disk).
3. V okně příkazového řádku spusťte následující příkazového řádku Azcopy nahrazení < path_to_data_folder > vaše data složky vytvořené v (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po dokončení AzCopy CSV soubory ZIP celkem 24 (12 pro cestu\_dat a 12 pro cestu\_tarif) by měla být ve složce data.
4. Rozbalte stažené soubory. Všimněte si, složky, kde jsou umístěny nekomprimovaných souborů. Tato složka se označuje jako < cesta\_k\_data\_soubory\>.

## <a name="dbload"></a>Hromadný Import dat do databáze serveru SQL
Je možné zlepšit výkon načítání nebo přenos velkých objemů dat do databáze SQL a následné dotazy pomocí *rozdělena na oddíly tabulky a zobrazení*. V této části jsme postupujte podle pokynů v tématu [paralelní hromadný Import pomocí SQL oddílu tabulky dat](parallel-load-sql-partitioned-tables.md) vytvořit novou databázi a načítání dat do dělené tabulky paralelně.

1. Při přihlášení k virtuálnímu počítači, spusťte **SQL Server Management Studio**.
2. Připojte pomocí ověřování systému Windows.
   
    ![Připojení přes SSMS][12]
3. Pokud máte ještě změnit režim ověřování systému SQL Server a vytvořit nového uživatele SQL přihlášení, otevřete soubor skriptu s názvem **změnit\_auth.sql** v **ukázkové skripty** složky. Změňte výchozí uživatelské jméno a heslo. Klikněte na tlačítko **! Spuštění** na panelu nástrojů pro spuštění skriptu.
   
    ![Spuštění skriptu][13]
4. Ověřte nebo změnit systému SQL Server výchozí složky databáze a protokolu zajistit, které nově vytvořený databáze bude uložen v datový Disk. Virtuální počítač s SQL serverem bitovou kopii, která je optimalizovaná pro datawarehousing zatížení je předem nakonfigurovaný s disky protokolu a data. Pokud virtuální počítač neobsahuje datový Disk a přidat nové virtuální pevné disky během procesu instalace virtuálních počítačů, změňte výchozí složky takto:
   
   * Klikněte pravým tlačítkem na název serveru SQL Server na levém panelu a klikněte na tlačítko **vlastnosti**.
     
       ![Vlastnosti serveru SQL][14]
   * Vyberte **nastavení databáze** z **vybrat stránku** seznamu na levé straně.
   * Ověřte nebo změňte **databáze výchozí umístění** k **datový Disk** umístění podle vaší volby. Toto je, kde jsou umístěny nové databáze, pokud vytvořili pomocí výchozích nastavení umístění.
     
       ![Výchozí nastavení databáze SQL][15]  
5. Chcete-li vytvořit novou databázi a sadu skupin souborů pro uložení dělené tabulky, otevřete ukázkový skript **vytvořit\_db\_default.sql**. Skript se vytvoří novou databázi s názvem **TaxiNYC** a 12 skupiny souborů ve výchozím umístění data. Každou skupinu souborů bude obsahovat jeden měsíc cesty\_dat a cesty\_jízdenky data. Změňte název databáze, v případě potřeby. Klikněte na tlačítko **! Spuštění** pro spuštění skriptu.
6. Dále vytvořte dvě tabulky oddílů, jeden pro cestu\_dat a druhý pro cestu\_tarif. Otevřete ukázkový skript **vytvořit\_oddílů\_table.sql**, který bude:
   
   * Vytvořte funkci oddílu k rozdělení dat po měsících.
   * Vytvořte schéma oddílu mapování dat každý měsíc do jiné skupiny souborů.
   * Vytvořte dva dělené tabulky, které jsou mapovány na schéma oddílu: **nyctaxi\_cestě** bude obsahovat cestu\_dat a **nyctaxi\_tarif** bude obsahovat cestě\_jízdenky data.
     
     Klikněte na tlačítko **! Spuštění** spusťte skript a vytvořit dělené tabulky.
7. V **ukázkové skripty** složky, existují dva ukázkové skripty prostředí PowerShell poskytuje k předvedení paralelní hromadné importuje dat do tabulek systému SQL Server.
   
   * **BCP\_paralelní\_generic.ps1** je obecný skript k paralelní hromadně importovat data do tabulky. Upravte tento skript nastavit proměnné vstup a cíle, jak je uvedeno v řádcích komentáře ve skriptu.
   * **BCP\_paralelní\_nyctaxi.ps1** je předem nakonfigurovaný verze obecný skript a může sloužit k načtení obě tabulky pro data NYC taxíkem cest.  
8. Klikněte pravým tlačítkem myši **bcp\_paralelní\_nyctaxi.ps1** název skriptu a klikněte na tlačítko **upravit** a otevře se v prostředí PowerShell. Zkontrolujte přednastavené proměnné a upravte podle název vybrané databáze, složka vstupních dat, cílové složce protokolu a cesty k ukázkové soubory formátu **nyctaxi_trip.xml** a **nyctaxi\_fare.xml** (součástí **ukázkové skripty** složku).
   
    ![Hromadný Import dat][16]
   
    Můžete také vybrat režim ověřování, výchozí hodnota je ověřování systému Windows. Klikněte na zelenou šipku na panelu nástrojů ke spuštění. Skript se spustí 24 operace hromadného importu v paralelní, 12 pro každou tabulku oddílů. Probíhá import dat může sledovat otevřením složky dat SQL Server výchozí výše.
9. Skript prostředí PowerShell hlásí počáteční a koncový čas. Pokud všechny hromadně importy dokončení, uvede se čas dokončení. Zkontrolujte cílové složce protokol ověření, že hromadné importy byla úspěšná, tedy žádné chyby hlášené v cílové složce protokolu.
10. Vaše databáze je nyní připraven pro zkoumání, technici funkce a další operace podle potřeby. Vzhledem k tomu, že jsou rozděleny do oddílů tabulky podle **vyzvednutí\_data a času** pole, dotazy, které zahrnují **vyzvednutí\_data a času** podmínky v **kde** klauzule výhod schéma oddílu.
11. V **SQL Server Management Studio**, prozkoumejte zadaný ukázkový skript **ukázka\_queries.sql**. Pokud chcete spustit všechny ukázkové dotazy, označte řádky dotazu a poté klikněte na tlačítko **! Spuštění** na panelu nástrojů.
12. Data odezvy taxíkem NYC je načten do dvou samostatných tabulkách. Pokud chcete zlepšit operace spojení, důrazně doporučujeme k indexu tabulky. Ukázkový skript **vytvořit\_oddílů\_index.sql** vytváří indexy oddílů spojení složený klíč **medailonu, hackerský\_licence a vyzvednutí\_ data a času**.

## <a name="dbexplore"></a>Zkoumání dat a konstruování funkce v systému SQL Server
V této části, provedeme zkoumání a funkce generování dat přímo v spuštěním dotazů SQL **SQL Server Management Studio** pomocí databázi systému SQL Server vytvořili dříve. Ukázkový skript s názvem **ukázka\_queries.sql** je součástí **ukázkové skripty** složky. Upravte skript, který chcete změnit název databáze, pokud se liší od výchozí hodnota: **TaxiNYC**.

V tomto cvičení provedeme následující:

* Připojení k **SQL Server Management Studio** pomocí buď ověřování systému Windows nebo pomocí ověřování SQL a SQL přihlašovací jméno a heslo.
* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data quality polí zeměpisné šířky a délky.
* Generovat binární a více třídami klasifikační štítky na základě **tip\_velikost**.
* Generovat funkce a výpočetní nebo porovnat cestě vzdálenosti.
* Spojení dvou tabulek a extrahovat z náhodného vzorku, který bude použit k vytvoření modelů.

Jakmile budete připraveni pokračovat do Azure Machine Learning, můžete se buď:  

1. Poslední dotaz SQL k extrahování a ukázková data a způsobené kopírováním a vkládáním dotaz přímo do uložit [importovat Data] [ import-data] modulu v Azure Machine Learning, nebo
2. Zachovat jen Vzorkovaná a inženýrství dat, které máte v úmyslu použít pro model sestavování v nové databáze tabulky a používat nové tabulky v [importovat Data] [ import-data] modulu v Azure Machine Learning.

V této části jsme se uložit poslední dotaz k extrahování a ukázková data. Druhá metoda ukazují [zkoumání dat a funkce Engineering v poznámkovém bloku IPython](#ipnb) části.

Pro rychlé ověření počtu řádků a sloupců v tabulkách vyplněny dříve paralelní hromadného importu

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Cestě distribuce podle Medailon
Tento příklad identifikuje Medailon (taxi čísla) s více než 100 služebních cest v daném časovém období. Dotaz by využívat přístup dělenou tabulku vzhledem k tomu, že je podmíněno tím schéma oddílů **vyzvednutí\_data a času**. Dotazování úplné datové sadě také budou používat oddílů tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Zkoumání: Cestě distribuce podle Medailon a hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality dat: Ověřte záznamy s nesprávné délky a šířky
Tento příklad prověří, pokud jakýkoli z pole zeměpisné délky a šířky buď obsahuje neplatnou hodnotu (radián stupňů musí být mezi -90 a 90), nebo (0, 0) souřadnice.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Zkoumání: Šikmý vs. Není vysypávány služebních cest distribučního
Tento příklad vyhledá počet cest, které byly vysypávány oproti není vysypávány v daném časovém období (nebo pokud vztahující se na úplné rok úplné datové sady). Toto rozdělení odráží binární popisek distribuci do později použije pro modelování binární klasifikace.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Zkoumání: Třída nebo rozsah distribuční Tip
Tento příklad vypočítá distribuci rozsahy tip v daném časovém období (nebo pokud vztahující se na úplné rok úplné datové sady). Toto je distribuce popisek třídy, které se později použije pro modelování více třídami klasifikace.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Zkoumání: Výpočetní a porovnání vzdálenost cesty
Tento příklad převede vyzvednutí a odkládací zeměpisné délky a šířky na SQL geography body, vypočítá pomocí SQL geography body rozdíl vzdálenost cestě a vrátí z náhodného vzorku výsledky pro porovnání. V příkladu omezí výsledky do platná souřadnice jenom pomocí dotazu hodnocení kvality dat popsané výše.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Funkce analýzy v dotazy SQL
Popisek generování a zeměpisnou převod zkoumání dotazy mohou sloužit také ke generování popisků nebo funkcích odstraněním počítání části. Další funkce engineering SQL příklady jsou součástí [zkoumání dat a funkce Engineering v poznámkovém bloku IPython](#ipnb) části. Je efektivnější spustit funkci generování dotazy na úplné datové sady nebo velké podmnožinu pomocí dotazů SQL, které se spustit přímo na instanci databáze systému SQL Server. Dotazy může být spuštěna v **SQL Server Management Studio**, IPython Poznámkový blok nebo všechny vývoj nástroj/prostředí, které máte přístup k databázi, místně nebo vzdáleně.

#### <a name="preparing-data-for-model-building"></a>Připravují se Data k vytváření modelů
Následující dotaz spojení **nyctaxi\_cestě** a **nyctaxi\_tarif** tabulky, vygeneruje štítek binární klasifikace **vysypávány**, Popisek více třída klasifikace **tip\_třída**a extrahuje z náhodného vzorku 1 % z připojeného k úplné datové sadě. Tento dotaz můžete zkopírovat a vložit přímo v [Azure Machine Learning Studio](https://studio.azureml.net) [importovat Data] [ import-data] modul pro přijímání přímé dat z databáze serveru SQL instance v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnice.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Zkoumání dat a funkce technikům v poznámkovém bloku IPython
V této části provedeme zkoumání dat a funkce generování pomocí jazyka Python a SQL dotazů v databázi systému SQL Server vytvořili dříve. Ukázka IPython Poznámkový blok s názvem **machine-Learning-data-science-process-sql-story.ipynb** je součástí **poznámkových bloků IPython ukázka** složky. Tento poznámkový blok je také k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Doporučené pořadí při práci s velkým objemem dat je následující:

* Přečtěte si v malé ukázkové dat do data v paměti rámečku.
* Proveďte některé vizualizace a explorations pomocí jen Vzorkovaná data.
* Experimentujte s inženýrství funkce pomocí jen Vzorkovaná data.
* Pro větší zkoumání dat, manipulaci s daty a funkce technikům používejte jazyk Python k vydávání dotazů SQL přímo s databází systému SQL Server ve virtuálním počítači Azure.
* Rozhodněte, velikost vzorku, který chcete použít pro vytváření modelů Azure Machine Learning.

Až budete připraveni pokračovat Azure Machine Learning, můžete se buď:  

1. Poslední dotaz SQL k extrahování a ukázková data a způsobené kopírováním a vkládáním dotaz přímo do uložit [importovat Data] [ import-data] modulu v Azure Machine Learning. Tato metoda je znázorněn v [vytváření modelů v Azure Machine Learning](#mlmodel) části.    
2. Zachovat data jen Vzorkovaná a inženýrství máte v úmyslu použít pro model sestavování v nové databázové tabulky, potom použijte novou tabulku v [importovat Data] [ import-data] modulu.

Následuje několik zkoumání dat, vizualizace dat a funkce technici příklady. Další příklady najdete v tématu ukázkový poznámkový blok SQL IPython v **poznámkových bloků IPython ukázka** složky.

#### <a name="initialize-database-credentials"></a>Inicializace přihlašovací údaje databáze
Inicializace nastavení připojení k databázi v následující proměnné:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Vytvoření připojení k databázi
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sestava počtu řádků a sloupců v tabulce nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Celkový počet řádků = 173179759  
* Celkový počet sloupců = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Pro čtení ve vzorku malá data z databáze serveru SQL
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Doba čtení ukázkové tabulky je 6.492000 sekund  
Počet řádků a sloupců načíst = (84952, 21)

#### <a name="descriptive-statistics"></a>Popisný statistiky
Teď je připravena k prozkoumání jen Vzorkovaná data. Začneme s prohlížení popisný statistiku pro **cestě\_vzdálenost** (nebo libovolný jiný) polí:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad vykreslení pole
Potom se podíváme na Krabicový pro vzdálenost cesty k vizualizaci quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Vykreslení #1][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizaci: Příklad vykreslení distribuční
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Vykreslení #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizaci: Panelu a řádku pozemků
V tomto příkladu jsme bin vzdálenost cesty do pěti přihrádek a vizualizace binning výsledků.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Nemůžeme vykreslení výše uvedené distribuční bin panelu nebo řádek výkresu, jak je uvedeno níže

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Vykreslení #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Vykreslení #4][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizaci: Příklad Scatterplot
Ukážeme bodové vykreslení mezi **cestě\_čas\_v\_sekundy** a **cestě\_vzdálenost** jestli jsou všechny korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Vykreslení #6][6]

Podobně jsme můžete zkontrolovat vztah mezi **míra\_kód** a **cestě\_vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Vykreslení #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Vzorkování dat v systému SQL
Při přípravě dat pro model vytváření [Azure Machine Learning Studio](https://studio.azureml.net), můžete rozhodnout buď na **dotazu SQL pro použití přímo v modulu importovat Data** nebo zachovat v nové inženýrství a jen Vzorkovaná data tabulky a který můžete použít v [importovat Data] [ import-data] modul s jednoduchou **vyberte * FROM < vaší\_nové\_tabulky\_name >** .

V této části vytvoříme novou tabulku pro uložení dat jen Vzorkovaná a inženýrství. Je součástí příkladem přímý dotaz SQL pro vytváření modelů [zkoumání dat a funkce Engineering v systému SQL Server](#dbexplore) části.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Vytvořit vzorek, tabulky a naplnění 1 % spojené tabulky. Pokud existuje vyřaďte první tabulky.
V této části jsme připojení tabulky **nyctaxi\_cestě** a **nyctaxi\_tarif**, extrahujte náhodného vzorku 1 %, a zachovat jen Vzorkovaná data v názvu nové tabulky  **nyctaxi\_jeden\_procent**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat pomocí dotazů SQL v poznámkovém bloku IPython
V této části nám prozkoumat distribuce dat pomocí 1 % vzorků dat, který je uchován v nové tabulky, kterou jsme vytvořili výše. Všimněte si, že podobné explorations je možné provést pomocí původní tabulky, volitelně pomocí **TABLESAMPLE** omezit zkoumání ukázku nebo omezením výsledky do dané časové období pomocí **vyzvednutí\_data a času** oddíly, jak je znázorněno [zkoumání dat a funkce Engineering v systému SQL Server](#dbexplore) části.

#### <a name="exploration-daily-distribution-of-trips"></a>Zkoumání: Denní distribuce služebních cest
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Zkoumání: Distribuční cesty za Medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Funkce generování pomocí dotazů SQL v poznámkovém bloku IPython
V této části vygenerujeme nové štítky a funkce přímo pomocí dotazů SQL, fungujícími v tabulce 1 % jsme vytvořili v předchozí části.

#### <a name="label-generation-generate-class-labels"></a>Popisek generování: Generování popisků – třída
V následujícím příkladu se vygeneruje dvě sady popisky, které chcete použít pro modelování:

1. Binární třída popisky **vysypávány** (predikci, pokud bude mu udělená tip)
2. Více třídami popisky **tip\_třída** (predikci popisek přihrádku nebo rozsah)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funkce inženýrství: Počet funkcí pro kategorií sloupce
Tento příklad transformuje pole kategorií na číselné pole nahrazením každou kategorii počet jeho výskytů v datech.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funkce inženýrství: Funkce Koš pro číselné sloupce
Tento příklad transformuje průběžné číselné pole na rozsahy přednastavené kategorie, tedy transformace číselné pole do pole kategorií.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funkce inženýrství: Extrahování umístění funkce z Decimal zeměpisnou šířku a délku
Tento příklad rozpis decimal reprezentace zeměpisnou šířku a zeměpisnou délku pole do více polí oblasti jiné členitosti, jako země, Město, města, blok atd. Všimněte si, že nová geo pole nejsou namapované na skutečné umístění. Informace o mapování geocode umístění najdete v tématu [služby Bing Maps REST](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečné formuláře featurized tabulky
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Snažíme se teď přejít k vytváření modelů a modelu nasazení v [Azure Machine Learning](https://studio.azureml.net). Data je připraven pro některý z identifikované dříve, a to předpovědi problémy:

1. Binární klasifikace: K předvídání, jestli tip byl placené cesty.
2. Více třídami klasifikace: K předpovědi rozsahu tipu placené podle dříve definovaných tříd.
3. Úloha regrese: K předvídání množství tip placené cesty.  

## <a name="mlmodel"></a>Vytváření modelů v Azure Machine Learning
Pokud chcete začít cvičení modelování, přihlaste se do pracovního prostoru Azure Machine Learning. Pokud jste ještě nevytvořili machine learning pracovního prostoru, přečtěte si téma [vytvořit pracovní prostor služby Azure Machine Learning](../studio/create-workspace.md).

1. Chcete-li začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Na domovskou stránku Studio poskytuje širokou řadu informací, videa, kurzy, odkazů na odkaz na moduly a dalším prostředkům. Další informace o Azure Machine Learning, najdete [centru dokumentace Azure Machine Learning na](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukový experiment sestává z následujících akcí:

1. Vytvoření **+ nový** experiment.
2. Načíst data do Azure Machine Learning.
3. Předběžně zpracovat, transformace a manipulovat s daty, podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do školení, ověření nebo testování datové sady (nebo samostatné datové sady pro každou).
6. Vyberte jeden nebo více algoritmy strojového učení v závislosti na learning problém vyřešit. Například: binární klasifikace, klasifikace více třídami, regresi.
7. Cvičení jednoho nebo více modelů použití školení datové sady.
8. Stanovení skóre datovou sadu ověření pomocí vyškolení modely.
9. Vyhodnoťte modely k výpočtu relevantní metriky pro učení problém.
10. Bez problémů ladit modely a vyberte doporučené modelu pro nasazení.

V tomto cvičení jsme již prozkoumali a analýzou dat v systému SQL Server a rozhodli na velikost vzorku ingestování v Azure Machine Learning. K vytvoření jednoho nebo více modelů předpovědi jsme se rozhodli:

1. Získat data pomocí Azure Machine Learning [importovat Data] [ import-data] modulu, k dispozici v **vstupu a výstupu dat** části. Další informace najdete v tématu [importovat Data] [ import-data] stránka s referencemi modul.
   
    ![Azure Machine Learning Import dat][17]
2. Vyberte **Azure SQL Database** jako **zdroj dat** v **vlastnosti** panelu.
3. Zadejte název DNS databáze v **název databázového serveru** pole. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Zadejte **název databáze** v odpovídajícím poli.
5. Zadejte **uživatelské jméno SQL** v ** aqccount uživatelského jména a hesla **heslo uživatelského účtu serveru**.
7. V **databázový dotaz** upravit textová oblast, vložte dotaz, který extrahuje pole potřeby databáze (včetně všech počítané pole, jako je popisků) a nižší ukázky data na požadovanou velikost.

Příkladem binární klasifikace experimentů, čtení dat přímo z databáze serveru SQL je na obrázku níže. Podobně jako experimenty konstruovat pro více třídami klasifikaci a regresní problémy.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> V datech modelování extrakce a vzorkuje příklady dotazu zadaný v předchozích částech **všech popisků tři modelování cvičení jsou zahrnuty v dotazu**. Důležitým krokem (povinné) v každé cvičení modelování je **vyloučit** nepotřebné štítky pro dva problémy a všemi ostatními **cíle nevracení**. Pro například binární klasifikace, používejte návěští **vysypávány** a vyloučit pole **tip\_třída**, **tip\_velikost**a **celkový\_velikost**. K tomu jsou cílové nevracení vzhledem k tomu, že implikují tip placené.
> 
> Vyloučení nepotřebných sloupců nebo cíle nevracení, můžete použít [výběr sloupců v datové sadě] [ select-columns] modulu nebo [upravit Metadata][edit-metadata]. Další informace najdete v tématu [výběr sloupců v datové sadě] [ select-columns] a [upravit Metadata] [ edit-metadata] referenční stránky.
> 
> 

## <a name="mldeploy"></a>Nasazování modelů v Azure Machine Learning
Pokud váš model je připraven, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení webové služby Azure Machine Learning najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Chcete-li nasadit novou webovou službu, je potřeba:

1. Vytvoření experimentu vyhodnocování.
2. Nasazení webové služby.

K vytvoření vyhodnocování experimentu z **dokončeno** cvičení experiment, klikněte na tlačítko **vytvořit vyhodnocování EXPERIMENTOVAT** na dolním panelu akcí.

![Výpočet skóre Azure][18]

Azure Machine Learning se pokusí vytvořit vyhodnocování experimentu založené na součástech experimentu školení. Konkrétně se:

1. Uložení naučeného modelu a odeberte moduly školení modelu.
2. Identifikovat logickou **vstupní port** představují schéma očekávané vstupní data.
3. Identifikovat logickou **výstupní port** představují výstupního schématu očekávané webové služby.

Při vyhodnocování experimentu je vytvořen, zkontrolujte jej a podle potřeby upravte. Typické úpravu je nahradit vstupní datové sady nebo dotaz s jedním, který vyloučí pole štítek, jak tyto nebudete mít k dispozici při volání služby. Je také vhodné zmenšit velikost vstupní datové sady a/nebo dotaz na několik záznamů, akorát, aby znamenat vstupní schéma. Na výstupní port je společné pro všechny vstupní pole vyloučit a obsahovat jenom **popisky vyhodnocení** a **skóre pro Magnitudu Pravděpodobnostech** ve výstupu pomocí [výběr sloupců v datové sadě] [ select-columns] modulu.

Ukázka vyhodnocování experimentu je na obrázku níže. Až bude připravený k nasazení, klikněte na tlačítko **publikování webové služby** tlačítko na dolním panelu akcí.

![Azure Machine Learning publikování][11]

K recap v tomto kurzu návodu jste vytvořili prostředí vědecké účely dat Azure, práce s velké veřejné datové sady úplně z získávání dat pro modelování školení a nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento návod ukázka a jeho doplňujícími skripty a IPython notebook(s) sdílí Microsoft v rámci licencí MIT. Zkontrolujte prosím soubor LICENSE.txt v adresáři ukázkový kód na Githubu další podrobnosti.

### <a name="references"></a>Odkazy
• [Stránce pro stažení Andrés Monroy NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi cestě dat podle Whong Jan](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxíkem a Limousine Komise výzkum a statistiky](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
