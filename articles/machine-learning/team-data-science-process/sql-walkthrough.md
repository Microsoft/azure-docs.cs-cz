---
title: Sestavení a nasazení modelu ve virtuálním virtuálním ms sql serveru – proces vědecké vědy o týmových datech
description: Vytvářejte a nasazujte model strojového učení pomocí SQL Serveru na virtuálním počítači Azure s veřejně dostupnou datovou sadou.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251580"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Proces vědecké ho procesu týmových dat v akci: pomocí serveru SQL Server
V tomto kurzu procházíte procesem vytváření a nasazování modelu strojového učení pomocí SERVERU SQL Server a veřejně dostupné datové sady – datové sady [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Postup se řídí standardním pracovním postupem pro datové vědy: ingestuje a zkoumá data, vymýšlejí funkce pro usnadnění učení a pak vytvoří a nasadí model.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Výlety Dataset Popis
NYC Taxi Trip data je asi 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimované), zahrnující více než 173 milionů jednotlivých cest a tarify zaplacené za každou cestu. Každý záznam cesty obsahuje místo vyzvednutí a předání a čas, anonymizované číslo licence hack (řidiče) a medailon (jedinečné číslo taxi( jedinečné číslo taxíku). Údaje se týkají všech cest v roce 2013 a jsou uvedeny v následujících dvou datových souborech za každý měsíc:

1. CsV "trip_data" obsahuje podrobnosti o jízdě, jako je počet cestujících, místa vyzvednutí a předání, doba trvání cesty a délka cesty. Zde je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. "trip_fare" CSV obsahuje podrobnosti o jízdném zaplacené za každou cestu, jako je typ platby, částka jízdného, příplatek a daně, tipy a mýtné a celková zaplacená částka. Zde je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro\_připojení údajů\_o cestě a jízdného se\_skládá\_z polí: medailon, hack licence a vyzvednutí datetime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady predikčních úloh
Budeme formulovat tři predikce problémy na základě *výše tip\_*, a to:

* Binární klasifikace: Předpovědět, zda tip byl zaplacen za cestu, to znamená, že *\_tip částka,* která je větší než $0 je pozitivní příklad, zatímco *tip\_částka* 0 dolarů je negativní příklad.
* Klasifikace více tříd: Předpovědět rozsah tipu placeného za cestu. *Množství spropitného\_* rozdělíme do pěti přihrádek nebo tříd:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regresní úkol: Předpovědět množství tipu zaplacené za cestu.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Nastavení prostředí Azure pro datové vědy pro pokročilé analýzy
Jak můžete vidět z průvodce [Plán prostředí,](plan-your-environment.md) existuje několik možností pro práci s datovou sadou NYC Taxi Trips v Azure:

* Práce s daty v objektech BLOB Azure a pak modelv Azure Machine Learning
* Načtení dat do databáze SERVERU SQL Server a pak model v Azure Machine Learning

V tomto kurzu budeme demonstrovat paralelní hromadný import dat na SQL Server, zkoumání dat, funkce inženýrství a dolů vzorkování pomocí SQL Server Management Studio, stejně jako pomocí IPython Notebook. [Ukázkové skripty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) a [poznámkové bloky IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) jsou sdíleny v GitHubu. Ukázkový poznámkový blok IPython pro práci s daty v objektech BLOB Azure je také k dispozici ve stejném umístění.

Nastavení prostředí Azure Data Science:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)
2. [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md)
3. [Zřídit virtuální počítač pro datové vědy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), který poskytuje SQL Server a server Poznámkového bloku IPython.
   
   > [!NOTE]
   > Ukázkové skripty a poznámkové bloky IPython se stáhnou do virtuálního počítače datové vědy během procesu instalace. Po dokončení skriptu virtuálního počítače po instalaci budou ukázky v knihovně dokumentů virtuálního počítače:  
   > 
   > * Ukázkové skripty:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Ukázkové poznámkové bloky IPython:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   kde `<user_name>` je přihlašovací jméno vašeho virtuálního počítače pro Windows. Budeme odkazovat na ukázkové složky jako **ukázkové skripty** a **ukázkové poznámkové bloky IPython**.
   > 
   > 

Na základě velikosti datové sady, umístění zdroje dat a vybraného cílového prostředí Azure je tento scénář podobný [ \#scénáři 5: Velká datová sada v místních souborech, cílový SQL Server v virtuálním počítači Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Získání dat z veřejného zdroje
Chcete-li získat [nyc taxi trips](https://www.andresmh.com/nyctaxitrips/) datové sady z jeho veřejné umístění, můžete použít některou z metod popsaných v [Přesunout data do a z Azure Blob Storage](move-azure-blob.md) zkopírovat data do nového virtuálního počítače.

Kopírování dat pomocí AzCopy:

1. Přihlášení k virtuálnímu počítači (VM)
2. Vytvořte nový adresář na datovém disku virtuálního počítače (Poznámka: Nepoužívejte dočasný disk, který je dodáván s virtuálním počítačem jako datový disk).
3. V okně příkazového řádku spusťte následující příkazový řádek Azcopy a nahraďte <path_to_data_folder> s datovou složkou vytvořenou v (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po dokončení AzCopy by mělo být ve složce dat celkem\_24 souborů\_CSV na zip (12 pro údaje o cestě a 12 pro jízdné.
4. Rozbalte stažené soubory. Všimněte si složky, ve které jsou umístěny nekomprimované soubory. Tato složka bude označována jako\_\_<\_\>cesta k datovým souborům .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Hromadné importy dat do databáze serveru SQL Server
Výkon načítání a přenosu velkého množství dat do databáze SQL a následných dotazů lze zlepšit pomocí *dělených tabulek a zobrazení*. V této části budeme postupovat podle pokynů popsaných v [paralelním hromadném importu dat pomocí tabulky oddílů SQL](parallel-load-sql-partitioned-tables.md) k vytvoření nové databáze a paralelnímu načtení dat do dělených tabulek.

1. Při přihlášení k virtuálnímu počítači spusťte **sql server management studio**.
2. Připojte se pomocí ověřování systému Windows.
   
    ![SSMS připojení][12]
3. Pokud jste ještě nezměnili režim ověřování serveru SQL Server a nevytvořili nového uživatele přihlášení sql, otevřete soubor skriptu s názvem **change\_auth.sql** ve složce **Ukázkové skripty.** Změňte výchozí uživatelské jméno a heslo. Kliknutím na **Spustit** na panelu nástrojů spusťte skript.
   
    ![Spustit skript][13]
4. Ověřte nebo změňte výchozí databázi serveru SQL Server a složky protokolu, abyste zajistili, že nově vytvořené databáze budou uloženy na datovém disku. Bitová kopie virtuálního počítače SQL Serveru, která je optimalizovaná pro zatížení datových skladů, je předem nakonfigurována s daty a disky protokolu. Pokud váš virtuální počítač neobsahuje datový disk a během procesu instalace virtuálního počítače jste přidali nové virtuální pevné disky, změňte výchozí složky takto:
   
   * Klepněte pravým tlačítkem myši na název serveru SQL Server v levém panelu a klepněte na příkaz **Vlastnosti**.
     
       ![Vlastnosti serveru SQL Server][14]
   * Ze seznamu **Vybrat stránku** vlevo vyberte **Nastavení databáze.**
   * Ověřte nebo změňte **výchozí umístění databáze** na umístění **datového disku** podle vašeho výběru. Toto umístění je místo, kde jsou umístěny nové databáze, pokud jsou vytvořeny s výchozím nastavením.
     
       ![Výchozí nastavení databáze SQL][15]  
5. Chcete-li vytvořit novou databázi a sadu skupin souborů pro uložení dělených tabulek, otevřete ukázkový skript **vytvořte\_soubor db\_default.sql**. Skript vytvoří novou databázi s názvem **TaxiNYC** a 12 filegroups ve výchozím umístění dat. Každá skupina souborů bude obsahovat data o cestě\_a tarifech za\_jeden měsíc. V případě potřeby upravte název databáze. Chcete-li spustit skript, klepněte na **tlačítko Spustit.**
6. Dále vytvořte dvě tabulky oddílů, jednu pro údaje o cestě\_a druhou pro jízdné.\_ Otevřete ukázkový skript **vytvořit\_partitioned\_table.sql**, který bude:
   
   * Vytvořte funkci oddílu a rozdělte data podle měsíce.
   * Vytvořte schéma oddílů pro mapování dat každého měsíce na jinou skupinu souborů.
   * Vytvořte dvě rozdělené tabulky mapované na schéma oddílů: **nyctaxi\_výlet** bude\_obsahovat data o cestě\_a **nyctaxi\_jízdné** bude obsahovat data o jízdném.
     
     Kliknutím na **Spustit** spusťte skript a vytvořte rozdělené tabulky.
7. Ve složce **Ukázkové skripty** jsou k dispozici dva ukázkové skripty prostředí PowerShell, které demonstrují paralelní hromadné importy dat do tabulek serveru SQL Server.
   
   * **bcp\_\_parallel generic.ps1** je obecný skript pro paralelní hromadné importdat do tabulky. Upravte tento skript a nastavte vstupní a cílové proměnné, jak je uvedeno v řádcích komentáře ve skriptu.
   * **bcp\_\_parallel nyctaxi.ps1** je předem nakonfigurovaná verze obecného skriptu a lze ji použít k načtení obou tabulek pro data NYC Taxi Trips.  
8. Klepněte pravým tlačítkem myši na název skriptu **bcp\_parallel\_nyctaxi.ps1** a klepnutím na tlačítko **Upravit** jej otevřete v prostředí PowerShell. Zkontrolujte přednastavené proměnné a upravte podle vybraného názvu databáze, vstupní datové složky, cílové složky protokolu a cest k ukázkovým souborům formátu **nyctaxi_trip.xml** a **\_nyctaxi fare.xml** (k dispozici ve složce **Ukázkové skripty).**
   
    ![Hromadná data importu][16]
   
    Můžete také vybrat režim ověřování, výchozí je ověřování systému Windows. Chcete-li spustit, klepněte na zelenou šipku na panelu nástrojů. Skript spustí souběžně 24 operací hromadného importu, 12 pro každou rozdělenou tabulku. Průběh importu dat můžete sledovat otevřením výchozí datové složky serveru SQL Server, jak je nastaveno výše.
9. Skript prostředí PowerShell hlásí počáteční a koncový čas. Po dokončení všech hromadných importů se oznamuje koncový čas. Zkontrolujte cílovou složku protokolu a ověřte, zda byl hromadný import úspěšný, to znamená, že ve složce cílového protokolu nebyly hlášeny žádné chyby.
10. Databáze je nyní připravena k průzkumu, inženýrské úpravě funkcí a dalším operacím podle potřeby. Vzhledem k tomu, že tabulky jsou rozděleny podle pole **datum\_vyzvednutí,** dotazy, které zahrnují podmínky data **vyzvednutí\_** v klauzuli **WHERE,** budou mít prospěch ze schématu oddílu.
11. V **aplikaci SQL Server Management Studio**prozkoumejte ukázkový **\_dotaz soubor queries.sql**. Chcete-li spustit některý z ukázkových dotazů, zvýrazněte řádky dotazu a klepněte na tlačítko **Spustit** na panelu nástrojů.
12. Data NYC Taxi Trips jsou načtena ve dvou samostatných tabulkách. Chcete-li zlepšit operace spojení, důrazně doporučujeme indexovat tabulky. Ukázkový skript **\_vytvořit\_partitioned index.sql** vytvoří rozdělené indexy na kompozitní spojit klíč **medailon, hack\_licence a vyzvednutí\_datetime**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Data Průzkum a funkce inženýrství v SQL Server
V této části provedeme zkoumání dat a generování funkcí spuštěním dotazů SQL přímo v **sql server management studio** pomocí databáze SQL Server vytvořené dříve. Ukázkový skript s názvem **\_Ukázkový soubor queries.sql** je k dispozici ve složce **Ukázkové skripty.** Chcete-li změnit název databáze, změňte skript, pokud se liší od výchozího: **TaxiNYC**.

V tomto cvičení:

* Připojte se k **aplikaci SQL Server Management Studio** pomocí ověřování systému Windows nebo pomocí ověřování SQL a přihlašovacího jména a hesla SQL.
* Prozkoumejte rozdělení dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat polí zeměpisné délky a zeměpisné šířky.
* Generovat binární a vícetřídní klasifikační popisky na základě **částky špičky\_**.
* Vytvářejte funkce a vypočítejte/porovnejte vzdálenosti jízdy.
* Připojte se ke dvěma tabulkám a extrahujte náhodný vzorek, který bude použit k sestavení modelů.

Až budete připraveni přejít na Azure Machine Learning, můžete buď:  

1. Uložte konečný dotaz SQL k extrahování a vzorkování dat a zkopírujte a vložte dotaz přímo do modulu [Import dat][import-data] v Azure Machine Learning nebo
2. Zachovat ukázková a navržená data, která chcete použít pro vytváření modelů v nové databázové tabulce, a použít novou tabulku v modulu [Import dat][import-data] v Azure Machine Learning.

V této části uložíme konečný dotaz k extrahování a vzorkování dat. Druhá metoda je demonstrována v části [Data Exploration and Feature Engineering in IPython Notebook.](#ipnb)

Pro rychlé ověření počtu řádků a sloupců v tabulkách naplněných dříve pomocí paralelního hromadného importu,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: Rozdělení cesty medailonem
Tento příklad identifikuje medailon (čísla taxi) s více než 100 cestami v daném časovém období. Dotaz by měl prospěch z přístupu k rozdělené tabulce, protože je podmíněn systémem oddílu **datatime vyzvednutí\_**. Dotazování úplné datové sady bude také využívat rozdělené tabulky nebo index prohledávání.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Průzkum: Rozdělení výletů medailonem a hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality údajů: Ověření záznamů s nesprávnou zeměpisnou délkou a šířkou
Tento příklad zkoumá, zda některé z polí zeměpisné délky nebo šířky obsahují neplatnou hodnotu (radiánní stupně by měly být mezi -90 a 90) nebo mají souřadnice (0, 0).

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Průzkum: Šikmá vs. distribuce nešipaných cest
Tento příklad vyhledá počet cest, které byly vychýleny vs. nevykládaného v daném časovém období (nebo v úplné datové sadě, pokud pokrývají celý rok). Toto rozdělení odráží binární distribuce popisků, které mají být později použity pro modelování binární klasifikace.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Průzkum: Tip třídy /rozsah distribuce
Tento příklad vypočítá rozdělení rozsahů špiček v daném časovém období (nebo v úplné datové sadě, pokud pokrývá celý rok). Toto rozdělení tříd popisků bude později použito pro modelování klasifikace více tříd.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Průzkum: Vypočítat a porovnat vzdálenost cesty
Tento příklad převede délku a zeměpisnou šířku vyzvednutí a šířky na body geografie SQL, vypočítá vzdálenost cesty pomocí rozdílu bodů zeměpisné polohy SQL a vrátí náhodný vzorek výsledků pro porovnání. Příklad omezuje výsledky na platné souřadnice pouze pomocí dotazu hodnocení kvality dat, který byl popsán dříve.

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

#### <a name="feature-engineering-in-sql-queries"></a>Inženýrské funkce v dotazech SQL
Generování popisků a dotazy průzkumu konverzí zeměpisné oblasti lze také použít ke generování popisků/funkcí odebráním části počítání. Další funkce inženýrství SQL příklady jsou k dispozici v zkoumání dat a funkce inženýrství v IPython notebook u [bádenec](#ipnb) části. Je efektivnější spustit dotazy generování funkcí na úplnou datovou sadu nebo její velkou podmnožinu pomocí dotazů SQL, které běží přímo na instanci databáze serveru SQL Server. Dotazy mohou být spouštěny v **SQL Server Management Studio**, IPython Notebook nebo jakýkoli vývojový nástroj nebo prostředí, které mohou přistupovat k databázi místně nebo vzdáleně.

#### <a name="preparing-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz spojuje **nyctaxi\_výlet** a **\_nyctaxi tarif** tabulky, generuje binární klasifikace popisek **hrot**, více třídy klasifikace popisek **\_tip třídy**a extrahuje 1 % náhodný vzorek z úplné spojené datové sady. Tento dotaz lze zkopírovat a pak vložit přímo do modulu Import [dat Aplikace][import-data] [Azure Machine Learning Studio](https://studio.azureml.net) pro přímé přiřazování dat z instance databáze SQL Serveru v Azure. Dotaz vyloučí záznamy s nesprávnými souřadnicemi (0, 0).

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


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Data Průzkum a funkce inženýrství v IPython notebooku
V této části budeme provádět zkoumání dat a generování funkcí pomocí Pythonu a SQL dotazy proti databázi SQL Server vytvořené dříve. Ukázkový poznámkový blok IPython s názvem **machine-Learning-data-science-process-sql-story.ipynb** je k dispozici ve složce **Ukázkové poznámkové bloky IPython.** Tento poznámkový blok je také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Při práci s velkými objemy dat postupujte podle této doporučené sekvence:

* Čtení v malém vzorku dat do datového rámce v paměti.
* Pomocí navzorkovaných dat provádějte některé vizualizace a průzkumy.
* Experimentujte s návrhem funkcí pomocí vzorkovaných dat.
* Pro větší zkoumání dat, manipulaci s daty a technické funkce, použijte Python k vydávání dotazů SQL přímo proti databázi SQL Serveru ve virtuálním počítači Azure.
* Rozhodněte velikost ukázky, která se má použít pro sestavení modelu Azure Machine Learning.

Až budete připraveni přejít na Azure Machine Learning, můžete buď:  

1. Uložte konečný dotaz SQL extrahovat a vzorkovat data a zkopírujte a vložte dotaz přímo do modulu [importu dat][import-data] v Azure Machine Learning. Tato metoda je demonstrována v části [Modely sestavení v Azure Machine Learning.](#mlmodel)    
2. Zachovat vzorkovaná a navržená data, která chcete použít pro vytváření modelů v nové databázové tabulce, a potom použít novou tabulku v modulu [Importovat data.][import-data]

Následuje několik příkladů zkoumání dat, vizualizace dat a technické funkce. Další příklady najdete v ukázkovém poznámkovém bloku SQL IPython ve složce **Ukázkové poznámkové bloky IPython.**

#### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Inicializovat nastavení připojení k databázi v následujících proměnných:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Vytvořit připojení k databázi
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Počet řádků a sloupců v tabulce nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Čtení malé ukázky dat z databáze serveru SQL Server
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

Čas čtení ukázkové tabulky je 6,492000 sekund  
Počet načtených řádků a sloupců = (84952, 21)

#### <a name="descriptive-statistics"></a>Popisná statistika
Nyní jsou připraveni prozkoumat vzorkovaná data. Začneme s pohledem na popisné statistiky pro **vzdálenost cesty\_** (nebo jiné) pole (y):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad vykreslení rámečku
Dále se podíváme na pole pozemku pro vzdálenost cesty vizualizovat kvantily

    df1.boxplot(column='trip_distance',return_type='dict')

![Vykreslení #1][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizace: Příklad distribučního vykreslování
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2 vykreslování][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizace: Obrázky pruhů a čar
V tomto příkladu jsme bin vzdálenost cesty do pěti přihrádek a vizualizovat binning výsledky.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Výše uvedenou distribuci přihrádky můžeme vykreslit v pruhu nebo v řádkovém grafu, jak je uvedeno níže

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Vykreslit #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 vykreslování][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizace: Příklad bodového grafu
Ukazujeme bodový graf mezi **dobou\_\_cesty\_v sekundách** a **vzdáleností,\_** abychom zjistili, zda existuje nějaká korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 vykreslování][6]

Podobně můžeme zkontrolovat vztah mezi **kódem sazby\_** a **vzdáleností cesty\_**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 vykreslování][8]

### <a name="sub-sampling-the-data-in-sql"></a>Dílčí vzorkování dat v SQL
Při přípravě dat pro sestavení modelu v [Azure Machine Learning Studio](https://studio.azureml.net), můžete buď rozhodnout o **dotazu SQL použít přímo v modulu Import dat** nebo zachovat navržená a vzorkovaná data v nové tabulce, které můžete použít v modulu Import [dat][import-data] s jednoduchým SELECT * FROM <**\_\_nový název tabulky\_>**.

V této části vytvoříme novou tabulku pro uložení vzorkovaných a navržených dat. Příklad přímého dotazu SQL pro vytváření modelů je uveden v části [Data Exploration and Feature Engineering in SQL Server.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Vytvořte ukázkovou tabulku a naplňte se 1 % spojených tabulek. Drop Tabulka První, pokud existuje.
V této části se připojujeme k tabulkám **\_nyctaxi trip** a **nyctaxi\_fare**, extrahujeme 1% náhodný vzorek a nadále zachováme vzorkovaná data v novém názvu tabulky **nyctaxi\_jedno\_procento**:

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
V této části zkoumáme distribuce dat pomocí 1 % vzorkovaných dat, která jsou trvalá v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy lze provést pomocí původní tabulky, volitelně pomocí **TABLESAMPLE** omezit ukázku průzkumu nebo omezením výsledků na dané časové období pomocí **oddílů\_datetime vyzvednutí,** jak je znázorněno v [data průzkumu a funkce inženýrství v sql server](#dbexplore) části.

#### <a name="exploration-daily-distribution-of-trips"></a>Průzkum: Denní distribuce výletů
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Průzkum: Rozdělení cesty na medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generování funkcí pomocí dotazů SQL v poznámkovém bloku IPython
V této sekci budeme generovat nové popisky a funkce přímo pomocí SQL dotazů, pracující chod na 1% ukázkové tabulce, kterou jsme vytvořili v předchozí části.

#### <a name="label-generation-generate-class-labels"></a>Generování popisků: Generování popisků tříd
V následujícím příkladu generujeme dvě sady popisků, které se použijí pro modelování:

1. Binární třídy Popisky **hrot (předpovídání,** zda tip bude uveden)
2. **Třída tipů\_** s vícetřídami (předvídání přihrádky nebo rozsahu spropitného)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Technické funkce: Počítat funkce pro kategorické sloupce
Tento příklad transformuje kategorické pole na číselné pole nahrazením každé kategorie počtem jeho výskytů v datech.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Technické funkce: Funkce přihrádky pro číselné sloupce
Tento příklad transformuje souvislé číselné pole na přednastavené rozsahy kategorií, tj.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Technické funkce: Extrahovat funkce umístění z desetinné šířky/délky
Tento příklad rozdělí desítkové znázornění pole zeměpisné šířky nebo délky do více polí oblasti s různou rozlišovací schopností, například země nebo oblast, město, město, blok atd. Nová geografická pole nejsou mapována na skutečná umístění. Informace o mapování umístění geokódů naleznete v [tématu Bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečné podoby tabulky featurized
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Teď jsme připraveni pokračovat v modelování vytváření a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravena pro všechny dříve identifikované problémy s predikcí, a to:

1. Binární klasifikace: Chcete-li předpovědět, zda byl za cestu zaplacen tip.
2. Klasifikace více tříd: Předpovědět rozsah placeného tipu podle dříve definovaných tříd.
3. Regresní úkol: Předpovědět množství tipu zaplacené za cestu.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Vytváření modelů v Azure Machine Learning
Chcete-li zahájit modelování cvičení, přihlaste se do pracovního prostoru Azure Machine Learning. Pokud jste ještě nevytvořili pracovní prostor strojového učení, přečtěte [si tématu Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

1. Pokud chcete začít s Azure Machine Learning, přečtěte [si, co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se do [Azure Machine Learning Studio](https://studio.azureml.net).
3. Domovská stránka studia poskytuje nepřeberné množství informací, videí, výukových programů, odkazů na odkaz na moduly a další chození. Další informace o Azure Machine Learning našlápnout na [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Typický tréninkový experiment se skládá z následujících kroků:

1. Vytvořte experiment **+NEW.**
2. Získejte data do Azure Machine Learning.
3. Předzpracováním, transformací a manipulací s daty podle potřeby.
4. Podle potřeby vygenerujte funkce.
5. Rozdělte data do trénovacích/validačních/testovacích datových sad (nebo mají samostatné datové sady pro každou z nich).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na problému učení, který chcete vyřešit. Například binární klasifikace, klasifikace více tříd, regrese.
7. Trénování jednoho nebo více modelů pomocí trénovací datové sady.
8. Skóre ověřovací datové sady pomocí trénovaného modelu (y).
9. Vyhodnoťte model (y) pro výpočet příslušné metriky pro problém učení.
10. Nalaďte model (y) a vyberte nejlepší model k nasazení.

V tomto cvičení jsme již prozkoumali a zkonstruovali data na SQL Serveru a rozhodli o velikosti ukázky ingestovat v Azure Machine Learning. Chcete-li vytvořit jeden nebo více predikčních modelů, rozhodli jsme se:

1. Získejte data do Azure Machine Learning pomocí modulu [Import dat,][import-data] který je k dispozici v části **Vstup dat a výstup.** Další informace naleznete na referenční stránce modulu [Import dat.][import-data]
   
    ![Data importu Azure Machine Learning][17]
2. V panelu **Vlastnosti** vyberte **Azure SQL Database** jako zdroj **dat.**
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formát:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Do odpovídajícího pole zadejte **název databáze.**
5. Zadejte **uživatelské jméno SQL** do názvu **uživatelského účtu serveru**a **heslo** do **hesla uživatelského účtu serveru**.
7. V oblasti úprav textu **databázového dotazu** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů ukažte data na požadovanou velikost vzorku.

Příklad experimentu binární klasifikace čtení dat přímo z databáze serveru SQL Server je na obrázku níže. Podobné experimenty lze sestavit pro vícetřídní klasifikace a regresní problémy.

![Vlak Azure Machine Learning][10]

> [!IMPORTANT]
> V příkladech extrakce dat modelování a vzorkování dotazu uvedených v předchozích částech **jsou do dotazu zahrnuty všechny popisky pro tři modelování**cvičení . Důležitým (povinným) krokem v každém z modelovacích cvičení je **vyloučit** zbytečné popisky pro další dva problémy a jakékoli další **úniky cílů**. Pro například při použití binární klasifikace použijte **popisek s hrotem** a vylučte **\_pole tip třídy**, **\_tip amount**a **\_celkové množství**. Ty jsou cílové úniky, protože znamenají tip placené.
> 
> Chcete-li vyloučit nepotřebné sloupce nebo cílové nevracení, můžete použít [select columns v modulu Dataset][select-columns] nebo [Upravit metadata][edit-metadata]. Další informace najdete [v tématech Výběr sloupců v datových sadách][select-columns] a [Úpravy referenčních][edit-metadata] stránek metadat.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je váš model připraven, můžete jej snadno nasadit jako webovou službu přímo z experimentu. Další informace o nasazení webových služeb Azure Machine Learning najdete v [tématu Nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Chcete-li nasadit novou webovou službu, je třeba:

1. Vytvořte experiment hodnocení.
2. Nasaďte webovou službu.

Chcete-li vytvořit experiment hodnocení z **experimentu dokončeného** tréninku, klepněte na tlačítko **VYTVOŘIT EXPERIMENT Bodování** v dolním panelu akcí.

![Azure vyhodnocování][18]

Azure Machine Learning se pokusí vytvořit vyhodnocovací experiment založený na součástech trénovacího experimentu. Zejména bude:

1. Uložte trénovaný model a odeberte tréninkové moduly modelu.
2. Identifikujte logický **vstupní port** představující schéma očekávaných vstupních dat.
3. Identifikujte logický **výstupní port** představující očekávané schéma výstupu webové služby.

Když je vytvořen bodovací experiment, zkontrolujte jej a podle potřeby upravte. Typická úprava je nahradit vstupní datovou sadu nebo dotaz tím, který vylučuje pole popisků, protože tyto popisky nebudou k dispozici ve schématu při volání služby. Je také vhodné zmenšit velikost vstupní datové sady nebo dotazu na několik záznamů, stačí k označení vstupního schématu. Pro výstupní port je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **scoredikované popisky** a **pravděpodobnosti s kinem** pomocí modulu [Vybrat sloupce v datové sadě.][select-columns]

Ukázkový bodovací experiment je na obrázku níže. Až budete připraveni k nasazení, klikněte na tlačítko **Publikovat webovou službu** v dolním panelu akcí.

![Publikování Azure Machine Learning][11]

Chcete-li rekapitulovat, v tomto návodu k onomu návodu jste vytvořili prostředí Azure pro datové vědy, pracoval s velkou veřejnou datovou sadou celou cestu od získávání dat na model školení a nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a jeho doprovodné skripty a poznámkové bloky IPython jsou sdíleny společností Microsoft pod licencí MIT. Další podrobnosti najdete v souboru LICENSE.txt v adresáři ukázkového kódu na GitHubu.

### <a name="references"></a>Odkazy
• [Andrés Monroy NYC Taxi Výlety Ke stažení Page](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taxi výlet data Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi a limuzína Komise výzkum a statistiky](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
