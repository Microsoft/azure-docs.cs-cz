---
title: Sestavit a nasadit model strojového učení pomocí SQL serveru na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Proces pokročilých analýz a technologie v akci
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: cad56d2e8de071feb9a02e0cfc6bcc884eebe91a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445459"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Vědecké zpracování týmových dat v akci: použití serveru SQL Server
V tomto kurzu vás provede procesem sestavování a nasazování modelu strojového učení pomocí SQL serveru a veřejně dostupné datové sady – [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datové sady. Postup následující pracovní postup standardních datových věd: ingestování a zkoumání dat, navrhování funkcí usnadnění vzdělávání, pak sestavit a nasadit model.

## <a name="dataset"></a>NYC taxislužby přepne popis datové sady
Data o jízdách taxislužby NYC je přibližně 20GB komprimované soubory CSV (nekomprimovaný ~ 48GB), zahrnující 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách obsahuje číslo řidičského sbírat míčky a dropoff umístění a čas, anonymizované hack (ovladače) a číslo Medailon (jedinečné id taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

1. "Trip_data" CSV obsahuje podrobnosti o jízdách, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. "trip_fare" CSV obsahuje podrobné informace o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek za a daní, tipy a mýtné a celkové částky zaplacené. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení o jízdách\_a dat o jízdách\_tarif se skládá z polí: medailonu, najděte, co je\_licence a vyzvednutí\_data a času.

## <a name="mltasks"></a>Příklady úloh Predikcí
Jsme se mohli formulovat tři problémy předpovědi na základě *tip\_částka*, konkrétně:

1. Binární klasifikace: předpovědět, jestli byl tip placené cesty, to znamená *tip\_velikost* , který je větší než 0 USD je kladné příklad, zatímco *tip\_velikost* $ 0 je Příklad záporné.
2. Klasifikace víc tříd: K předpovědi rozsahu tip placené pro cestu. Doporučujeme rozdělit *tip\_částka* do pěti přihrádky nebo třídy:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Úloha regrese: odhadnout množství tip placené cesty.  

## <a name="setup"></a>Prostředí pro datové vědy nastavení si Azure pro pokročilou analýzu
Jak je vidět [plánování vašeho prostředí](plan-your-environment.md) průvodce, máte několik možností pro práci s datovou sadou NYC taxislužby zkracuje dobu odezvy v Azure:

* Práce s data v objektech BLOB Azure a modelu ve službě Azure Machine Learning
* Načtení dat do databáze serveru SQL Server a model ve službě Azure Machine Learning

V tomto kurzu si předvedeme paralelní hromadný import dat do SQL serveru, zkoumání dat, funkce inženýrství a dolů vzorkování pomocí SQL Server Management Studio a jak používat IPython Notebook. [Ukázky skriptů](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) a [IPython notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) jsou sdílené v Githubu. Ukázka IPython notebook, a to pro práci s daty v objektech BLOB Azure je také k dispozici ve stejném umístění.

Nastavení prostředí Azure pro datové vědy:

1. [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md)
2. [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md)
3. [Zřízení virtuálního počítače pro datové vědy](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), která poskytuje systému SQL Server a serveru IPython Notebook.
   
   > [!NOTE]
   > Ukázkové skripty a IPython notebook se stáhne do vašeho virtuálního počítače pro datové vědy během procesu instalace. Po dokončení skriptu po instalaci virtuálního počítače, ukázky budou v knihovně dokumentů pro váš virtuální počítač:  
   > 
   > * Ukázky skriptů: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Ukázka IPython notebook: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   kde `<user_name>` je váš virtuální počítač Windows přihlašovací jméno. Jsme bude odkazovat na ukázkové složky jako **ukázkové skripty** a **ukázka IPython notebook**.
   > 
   > 

Na základě velikost datové sady, umístění zdroje dat a vybrané Azure cílového prostředí, tento scénář je podobný [scénář \#5: velkou datovou sadu v místních souborů cílit na SQL Server na virtuálním počítači Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Získávají Data z veřejných zdrojů
Chcete-li získat [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datové sady z veřejné umístění, můžete použít některou z metod popsaných v [přesun dat do a z úložiště objektů Blob v Azure](move-azure-blob.md) ke zkopírování dat do nového virtuálního počítače.

Kopírování dat pomocí AzCopy:

1. Přihlaste se ke svému virtuálnímu počítači (VM)
2. Vytvořte nový adresář v datového disku Virtuálního počítače (Poznámka: Nepoužívejte dočasný Disk, který se dodává s virtuálním Počítačem jako datový Disk).
3. V okně příkazového řádku spusťte následující příkazový řádek Azcopy nahrazení < path_to_data_folder > vaše data složky vytvořené v (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po dokončení AzCopy celkem 24 CSV soubory ZIP (12 pro cestu\_dat a 12 pro cestu\_tarif) by měl být ve složce data.
4. Rozbalte stažené soubory. Poznamenejte si složku, ve kterém jsou umístěny dekomprimovaných souborů. Tato složka se označuje jako < cesta\_k\_data\_soubory\>.

## <a name="dbload"></a>Hromadného importu dat do databáze SQL serveru
Výkon načítání/přenos velkých objemů dat do služby SQL database a následné dotazů lze zvýšit pomocí *dělené tabulky a zobrazení*. V této části budeme postupovat podle pokynů v tématu [paralelní hromadných dat Import pomocí tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md) k vytvoření nové databáze a načíst data do dělené tabulky paralelně.

1. Když jste přihlášení k virtuálnímu počítači, spusťte **SQL Server Management Studio**.
2. Připojení pomocí ověřování Windows.
   
    ![Připojení přes SSMS][12]
3. Pokud máte ještě změnit režim ověřování systému SQL Server a vytvoření nové přihlašovací jméno uživatele SQL, otevření souboru skriptu s názvem **změnit\_auth.sql** v **ukázkové skripty** složky. Změňte výchozí uživatelské jméno a heslo. Klikněte na tlačítko **! Spustit** na panelu nástrojů pro spuštění skriptu.
   
    ![Spuštění skriptu][13]
4. Ověřit nebo změnit systému SQL Server výchozí databáze a protokolu složky k zajištění, které nově vytvořené databáze budou uloženy ve datový Disk. Image virtuálního počítače s SQL serverem, který je optimalizovaný pro načtení datawarehousing je předem nakonfigurovaný s disky protokolu a data. Pokud se váš virtuální počítač neobsahuje datový Disk a přidat nový virtuální pevné disky během procesu instalace virtuální počítač, změňte výchozí složky následujícím způsobem:
   
   * Klikněte pravým tlačítkem na název serveru SQL Server na levém panelu a klikněte na tlačítko **vlastnosti**.
     
       ![Vlastnosti serveru SQL][14]
   * Vyberte **nastavení databáze** z **vybrat stránku** seznamu na levé straně.
   * Ověřit nebo změnit **databáze výchozí umístění** k **datový Disk** umístění podle vašeho výběru. Je to, kde jsou umístěny nové databáze, pokud vytvořený s výchozím nastavením umístění.
     
       ![Výchozí nastavení databáze SQL][15]  
5. Chcete-li vytvořit novou databázi a sadu skupin souborů pro uchování dělené tabulky, otevřete ukázkový skript **vytvořit\_db\_default.sql**. Tento skript vytvoří novou databázi s názvem **TaxiNYC** a 12 skupiny souborů ve výchozím umístění data. Každá skupina souborů bude obsahovat jeden měsíc o jízdách\_a dat o jízdách\_jízdenky data. Změňte název databáze, v případě potřeby. Klikněte na tlačítko **! Spustit** pro spuštění skriptu.
6. V dalším kroku vytvoření dvou tabulek oddílů, jeden pro cestu\_data a druhý pro cestu\_tarif. Otevřete ukázkový skript **vytvořit\_dělené\_table.sql**, který bude:
   
   * Vytvořte funkci oddílu rozdělit data podle měsíce.
   * Vytvořte schéma oddílu mapuje každý měsíc data na různých souborů.
   * Vytvoření dvou dělené tabulky, které jsou mapovány na schéma oddílu: **nyctaxi\_o jízdách** bude obsahovat cestu\_dat a **nyctaxi\_tarif** bude obsahovat o jízdách\_jízdenky data.
     
     Klikněte na tlačítko **! Spustit** spusťte skript a vytvořit dělené tabulky.
7. V **ukázkové skripty** složky, existují dva ukázkové skripty Powershellu k předvedení paralelní hromadný importuje dat do tabulek systému SQL Server k dispozici.
   
   * **BCP\_paralelní\_generic.ps1** je obecný skript pro paralelní hromadný import dat do tabulky. Upravte tento skript k nastavení proměnných vstup a cíl, jak je uvedeno v řádcích komentáře ve skriptu.
   * **BCP\_paralelní\_nyctaxi.ps1** je předem nakonfigurovaná verze obecného skriptu a slouží k načtení obou tabulek pro data NYC taxislužby zkracuje dobu odezvy.  
8. Klikněte pravým tlačítkem myši **bcp\_paralelní\_nyctaxi.ps1** název skriptu a klikněte na tlačítko **upravit** a otevře se v prostředí PowerShell. Zkontrolujte předvolby proměnných a měnit podle název vybrané databáze, vstupní data složky, cílové složky protokolu a cesty k souborům vzorek formátu **nyctaxi_trip.xml** a **nyctaxi\_fare.xml** (k dispozici v **ukázkové skripty** složky).
   
    ![Hromadné Import dat][16]
   
    Můžete také vybrat režim ověřování, výchozí hodnota je ověřování Windows. Klikněte na zelenou šipku na panelu nástrojů ke spuštění. Skript se spustí 24 operace hromadného importu v paralelní, 12 pro každý dělenou tabulku. Můžete monitorovat průběh importování dat tak, že otevřete výchozí složce dat serveru SQL Server jako výše.
9. Skript prostředí PowerShell hlásí počáteční a koncové časy. Když všechny importy kompletní hromadně, se použije v hlášení koncový čas. Zkontrolujte cílové složky protokolu k ověření, že hromadné importy byla úspěšná, například v cílové složce protokol nebyly hlášeny žádné chyby.
10. Vaše databáze je nyní připravena pro zkoumání, vytváření funkcí a dalších operací podle potřeby. Protože tabulky jsou rozdělené podle **vyzvednutí\_data a času** pole dotazů, které zahrnují **vyzvednutí\_data a času** podmínky ve **kde** klauzule výhodu schéma oddílu.
11. V **SQL Server Management Studio**, prozkoumejte poskytnutý ukázkový skript **ukázka\_queries.sql**. Chcete-li spustit některý z ukázkových dotazů, označte řádky dotazu a poté klikněte na tlačítko **! Spustit** na panelu nástrojů.
12. Načtení dat cesty taxíkem NYC ve dvou samostatných tabulkách. Pokud chcete zlepšit operace spojení, důrazně doporučujeme k indexování tabulky. Ukázkový skript **vytvořit\_dělené\_index.sql** vytváří indexy dělené spojení složený klíč **medailonu, najděte, co je\_licence a vyzvednutí\_ Datum a čas**.

## <a name="dbexplore"></a>Zkoumání dat a vytváření funkcí v systému SQL Server
V této části budeme provádět zkoumání a funkce generování dat spuštěním dotazy SQL přímo **SQL Server Management Studio** použití databáze systému SQL Server vytvořili dříve. Ukázkový skript s názvem **ukázka\_queries.sql** je součástí **ukázkové skripty** složky. Upravit skript, který chcete změnit název databáze, pokud se liší od výchozí hodnota: **TaxiNYC**.

V tomto cvičení provedeme následující:

* Připojte se k **SQL Server Management Studio** pomocí jedno z ověření Windows nebo pomocí ověřování SQL a přihlašovací jméno SQL a heslo.
* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data kvality zeměpisnou šířku a délku pole.
* Generovat popisky klasifikace binární a víc tříd na základě **tip\_částka**.
* Generovat funkce a výpočetní/porovnání vzdálenosti o jízdách.
* Spojení dvou tabulek a extrahovat náhodného vzorku, který se použije k vytvoření modelů.

Až budete připravení přejít k Azure Machine Learning, můžete se buď:  

1. Poslední dotaz SQL extrahovat ukázková data a kopírování a vkládání dotaz přímo do uložit [Import dat] [ import-data] modulu ve službě Azure Machine Learning, nebo
2. Zachovat vzorku a analýzou dat máte v úmyslu použít pro model vytvářet nové databáze tabulky a používat nová tabulka v [Import dat] [ import-data] modulu ve službě Azure Machine Learning.

V této části jsme se uloží konečný dotaz a extrahovat ukázková data. Druhý způsob je patrné [zkoumání dat a funkce Engineering v IPython Notebook](#ipnb) oddílu.

Pro rychlé ověření počtu řádků a sloupců v tabulkách vyplněny dříve paralelní hromadný import

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
V tomto příkladu identifikuje Medailon (taxislužby čísla) s více než 100 zkracuje dobu odezvy v daném časovém období. Dotaz je výhodná dělenou tabulku přístupu od náležitého schéma oddílů **vyzvednutí\_data a času**. Dotazování úplnou datovou sadu se také provést pomocí dělené tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Zkoumání: Distribuce latence podle Medailon a hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Hodnocení kvality dat: Ověřte záznamy s nesprávné délky a šířky
V tomto příkladu prověří, pokud jakýkoli z polí zeměpisná délka a/nebo zeměpisnou šířku buď obsahuje neplatnou hodnotu (stupně radián by měl být od -90 do 90), nebo máte (0, 0) souřadnic.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Zkoumání: Šikmý vs. Není distribuční šikmý zkracuje dobu odezvy
Tento příklad zjistí počet cest, které byly šikmý šikmý není v daném časovém období (nebo se v celé datové sadě, pokud pokrývající celý rok). Toto rozdělení odráží distribuce binární označení později použitého pro binární klasifikaci modelování.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Zkoumání: Tip třídy a rozsahu distribuce
Tento příklad vypočítá distribuci tip rozsahů v daném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok). To je distribuce popisek tříd, které se později použijí pro modelování klasifikace víc tříd.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Zkoumání: Compute a porovnat vzdálenost o jízdách
Tento příklad převede sbírat míčky a dropoff délky a šířky do SQL zeměpisné oblasti odkazuje, vypočítá vzdálenost o jízdách pomocí SQL zeměpisné oblasti bodů rozdíl a vrací náhodného vzorku výsledky pro porovnání. V příkladu omezuje výsledky na platné souřadnice pouze pomocí dotazu hodnocení kvality dat věnovali výše.

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

#### <a name="feature-engineering-in-sql-queries"></a>Vytváření funkcí v dotazech SQL
Popisek generování a geografického umístění převod zkoumání dotazy lze také Generovat popisky/funkce tak, že odeberete počítání část. Další funkce engineering SQL příklady jsou uvedeny v [zkoumání dat a funkce Engineering v IPython Notebook](#ipnb) oddílu. To je mnohem efektivnější spustit funkci generování dotazy na úplnou datovou sadu nebo velká podmnožina pomocí dotazů SQL, které běží přímo v instanci databáze SQL serveru. Dotazy mohou být provedeny v **SQL Server Management Studio**, IPython Notebook nebo všechny vývojové nástroje/prostředí, které máte přístup k databázi, místně nebo vzdáleně.

#### <a name="preparing-data-for-model-building"></a>Připravují se Data pro vytváření modelů
Následující dotaz spojení **nyctaxi\_o jízdách** a **nyctaxi\_tarif** tabulky, generuje binární klasifikační popisek **šikmý**, Popisek klasifikace roc **tip\_třída**a extrahuje náhodného vzorku 1 % z celé datové sady připojené k doméně. Tento dotaz můžete zkopírovat a vložit přímo v [Azure Machine Learning Studio](https://studio.azureml.net) [Import dat] [ import-data] modul pro zpracování dat s přímým přístupem v databázi serveru SQL Server instance v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnic.

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


## <a name="ipnb"></a>Zkoumání dat a vytváření funkcí v IPython Notebook
V této části budeme provádět zkoumání dat a vytváření funkce pomocí Pythonu a SQL dotazy na databázi systému SQL Server vytvořili dříve. IPython notebook vzorku, s názvem **machine-Learning-data-science-process-sql-story.ipynb** je součástí **ukázka IPython notebook** složky. Tento poznámkový blok je také k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Doporučené pořadí při práci s velkými objemy dat je následující:

* Přečtěte si v malým vzorkem dat do rámečku data v paměti.
* Provedení některých vizualizací a průzkumy pomocí jen Vzorkovaná data.
* Můžete experimentovat s vytváření funkcí pomocí jen Vzorkovaná data.
* Pro větší zkoumání dat, manipulaci s daty a vytváření funkcí posílat dotazy SQL přímo na databázi systému SQL Server na virtuálním počítači Azure pomocí Pythonu.
* Rozhodněte, velikost vzorku pro vytváření modelů Azure Machine Learning.

Až budete připraveni přejít k Azure Machine Learning, můžete se buď:  

1. Poslední dotaz SQL extrahovat ukázková data a kopírování a vkládání dotaz přímo do uložit [Import dat] [ import-data] modulu ve službě Azure Machine Learning. Tato metoda je patrné [vytváření modelů ve službě Azure Machine Learning](#mlmodel) části.    
2. Zachovat vzorky a analýzou dat máte v úmyslu použít pro model v sestavování v nové databázové tabulky, a pak pomocí nové tabulky v [Import dat] [ import-data] modulu.

Následuje několik zkoumání, vizualizaci dat a funkce technické příklady. Další příklady najdete v ukázkové SQL IPython notebook v **ukázka IPython notebook** složky.

#### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Inicializace nastavení připojení k databázi v následující proměnné:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Vytvoření připojení k databázi
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Sestava počet řádků a sloupců v tabulce nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Čtení ve vzorku malá data z databáze serveru SQL
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
Počet řádků a sloupců načtení = (84952, 21)

#### <a name="descriptive-statistics"></a>Popisný statistiky
Nyní jsou připraveni na zkoumání jen Vzorkovaná data. Začneme s vyhledáváním statistiku popisný **o jízdách\_vzdálenost** (nebo jakékoli jiné) pole s požadovanými:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Vizualizace: Příklad diagramu pole
Dále podíváme na diagramu pole pro cestu vzdálenost k vizualizaci quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Vykreslení #1][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizace: Příklad diagram distribuce
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Vykreslení #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizace: Panelu a řádku grafy
V tomto příkladu jsme bin vzdálenost výlet do pěti přihrádek a vizualizaci výsledků binningu.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Jsme vykreslení výše uvedené přihrádky distribuce v pruhu nebo čáry vykreslení, jak je uvedeno níže

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Vykreslení #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Vykreslení #4][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizace: Příklad diagnostického
Ukážeme korelačního diagramu mezi **o jízdách\_čas\_v\_sekundy** a **o jízdách\_vzdálenost** zobrazíte, pokud se jakákoli korelace

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Vykreslení #6][6]

Podobně lze kontrolujeme vztah mezi **míra\_kód** a **o jízdách\_vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Vykreslení #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Vzorkování dat v SQL
Při přípravě dat pro model v sestavování v [Azure Machine Learning Studio](https://studio.azureml.net), můžete rozhodnout buď na **dotaz SQL pro použití přímo v modulu Import dat** nebo zachovat data inženýrství a vzorky v novém Tabulka, která můžete použít v [Import dat] [ import-data] modul s jednoduchou **vyberte * FROM < vaší\_nové\_tabulky\_název >** .

V této části vytvoříme novou tabulku pro ukládání dat vzorky a analýzou. Příklad přímý dotaz SQL pro vytváření modelů najdete v [zkoumání dat a technické funkce v systému SQL Server](#dbexplore) oddílu.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Vytvořením ukázkového tabulky a naplnění spojené tabulky % 1. Odstranit tabulku první, pokud existuje.
V této části jsme spojení tabulek **nyctaxi\_o jízdách** a **nyctaxi\_tarif**, extrahovat náhodného vzorku 1 %, a zachovat vzorkovaných dat. v názvu nové tabulky  **nyctaxi\_jeden\_procent**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat pomocí dotazů SQL v IPython Notebook
V této části se podíváme na distribuce dat pomocí % 1 Vzorkovaná data, která se ukládají do nové tabulky, kterou jsme vytvořili výše. Všimněte si, že podobné průzkumy je možné provádět pomocí původní tabulky, případně můžete použít **klauzule TABLESAMPLE** omezit zkoumání ukázkové nebo tím, že omezí výsledky na daném časovém období pomocí **vyzvednutí\_datetime** oddíly, jak je znázorněno [zkoumání dat a technické funkce v systému SQL Server](#dbexplore) oddílu.

#### <a name="exploration-daily-distribution-of-trips"></a>Zkoumání: Denní distribuci zkracuje dobu odezvy
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Zkoumání: Distribuční výlet za Medailon
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Funkce generování pomocí příkazů jazyka SQL v IPython Notebook
V této části vygenerujeme nové popisky a funkce přímo pomocí příkazů jazyka SQL, fungujícími v tabulce 1 % jsme vytvořili v předchozí části.

#### <a name="label-generation-generate-class-labels"></a>Popisek generace: Generovat třídy popisky
V následujícím příkladu se vygeneruje dvě sady popisky pro modelování:

1. Binární třída popisky **šikmý** (předpověď, pokud se zadaný tip)
2. Víc tříd popisky **tip\_třídy** (předpověď tip bin nebo oblast)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Vytváření funkcí: Počet funkcí pro sloupce s kategorií
V tomto příkladu pole kategorií transformuje na číselné pole nahrazením jednotlivých kategorií počet jeho výskyty v datech.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Vytváření funkcí: Funkce Bin pro číselné sloupce
V tomto příkladu transformuje průběžné číselné pole na předvolby kategorie rozsahy, například transformace číselné pole do pole kategorií.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Vytváření funkcí: Extrahování umístění funkce z desítkové zeměpisnou šířkou/délkou
V tomto příkladu boří desítkový zápis zeměpisná šířka a zeměpisná délka pole do více polí oblast různých členitosti a jako země, Město, Město, blok, atd. Všimněte si, že nové geografická pole nejsou namapované na skutečné umístění. Informace o umístění geokód mapování, naleznete v tématu [REST služby Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Zkontrolujte poslední formulář natrénuje tabulky
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Jsme připraveni přejít k vytváření modelů a nasazení modelů v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravena pro některý z předpovědi problémy identifikovat dříve, a to:

1. Binární klasifikace: předpovědět, zda je či není tip byla zaplacena cesty.
2. Klasifikace víc tříd: K předpovědi rozsahu tip placené podle dříve definovaných tříd.
3. Úloha regrese: odhadnout množství tip placené cesty.  

## <a name="mlmodel"></a>Vytváření modelů ve službě Azure Machine Learning
Začněte cvičení modelování, přihlaste se do pracovního prostoru Azure Machine Learning. Pokud jste ještě nevytvořili pracovního prostoru machine learning, přečtěte si téma [vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md).

1. Začínáme s Azure Machine Learning, najdete v článku [co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Na domovskou stránku Studio nabízí celou řadu informací, videa, kurzy, odkazy k odkazu moduly a dalším prostředkům. Další informace o službě Azure Machine Learning, najdete [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukového experimentu se skládá z následujících akcí:

1. Vytvoření **+ nová** experimentovat.
2. Získání dat pro Azure Machine Learning.
3. Předběžné zpracování, transformovat a zpracovávat data podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do datové sady školení/ověření/testování (nebo máte rozdělování datových sad pro každou).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na učení problém vyřešit. Například binární klasifikace klasifikace víc tříd, regrese.
7. Trénování jeden nebo více modelů pomocí trénovací datové sady.
8. Ověření datové sady, natrénované modely pomocí skóre.
9. Vyhodnoťte modely pro výpočet důležité metriky pro výuky.
10. Bez problémů můžete vyladit modely a vybrat nejlepší model nasadit.

V tomto cvičení jsme mít již prozkoumali a analýzou dat v systému SQL Server a rozhodli na velikost vzorku ingestování ve službě Azure Machine Learning. K sestavení některých prediktivní modely, které jsme se rozhodli:

1. Získání dat pomocí Azure Machine Learning [Import dat] [ import-data] modulu, k dispozici v **datový vstup a výstup** oddílu. Další informace najdete v tématu [Import dat] [ import-data] stránce odkaz na modul.
   
    ![Azure Machine Learning umožňuje importovat Data][17]
2. Vyberte **Azure SQL Database** jako **zdroj dat** v **vlastnosti** panelu.
3. Zadejte název DNS databáze **název databázového serveru** pole. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Zadejte **název_databáze** v odpovídajícím poli.
5. Zadejte **uživatelské jméno SQL** v **název uživatelského účtu serveru**a **heslo** v **heslo uživatelského účtu serveru**.
7. V **databázový dotaz** upravit textovou oblast, vložte dotaz, který extrahuje nezbytné databázová pole (včetně jakékoli vypočítaná pole, jako jsou štítky) a dolů vzorkuje data, která mají velikost požadovaného vzorku.

Na následujícím obrázku je příklad binární klasifikace: experiment čtení dat přímo z databáze SQL serveru. Podobně jako experimenty může být vytvořen pro klasifikace víc tříd a regresní problémy.

![Azure Machine Learning trénování][10]

> [!IMPORTANT]
> Modelování dat extrakce a vzorkování Příklady dotazů uvedené v předchozích částech, **všechny popisky pro tři cvičení modelování jsou obsažena v dotazu**. Důležitým krokem (povinné) v každém modelování cvičení je **vyloučit** zbytečné popisky pro dva problémy a jakékoli jiné **cílit nevracení**. Pro například při použití binární klasifikace, použijte popisek **šikmý** a vyloučit pole **tip\_třídy**, **tip\_částka**a **celkový\_částka**. Druhá možnost se cílového únikům protože implikují tip placené.
> 
> K vyloučení nepotřebných sloupců a/nebo cílové nevracení, můžete použít [výběr sloupců v datové sadě] [ select-columns] modulu nebo [upravit Metadata][edit-metadata]. Další informace najdete v tématu [výběr sloupců v datové sadě] [ select-columns] a [upravit Metadata] [ edit-metadata] odkazují na stránky.
> 
> 

## <a name="mldeploy"></a>Nasazení modelů ve službě Azure Machine Learning
Když je model hotový, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení webové služby Azure Machine Learning najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Pokud chcete nasadit nové webové služby, budete muset:

1. Vytvoření experimentu bodování.
2. Nasazení webové služby.

K vytvoření bodování experiment z **dokončeno** školení experiment, klikněte na tlačítko **vytvořit vyhodnocování EXPERIMENTOVAT** na dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit bodování experiment založené na součástech výukového experimentu. Konkrétně se tyto akce:

1. Uložení naučeného modelu a odeberte moduly trénování modelu.
2. Identifikujte logické **vstupním portem** představující schéma očekávaný vstupní data.
3. Identifikujte logické **výstupní port** představující schéma výstupu očekávané webové služby.

Při vyhodnocování experiment, zkontrolujte a podle potřeby upravte. Typické nastavení je k nahrazení vstupní datové sady a/nebo dotazu s jedním, který vyloučí popisek pole, tyto nebudou k dispozici při volání služby. Je také vhodné zmenšit velikost vstupní datové sady a/nebo dotazu na několik záznamů, právě takové, aby označuje vstupní schéma. Pro výstupní port je společné pro všechny vstupní pole vyloučit a obsahovat jenom **popisky vyhodnocení** a **skóre pravděpodobnosti** ve výstupu pomocí [výběr sloupců v datové sadě] [ select-columns] modulu.

Ukázka vyhodnocování experimentu je na následujícím obrázku. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** tlačítko na panelu akcí nižší.

![Publikování Azure Machine Learning][11]

Rekapitulace v tomto kurzu návodu jste vytvořili Azure prostředí pro datové vědy, pracoval s velkou datovou sadu veřejné od získání dat pro modelování školení a nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a jeho doprovodném skripty a IPython notebook(s) sdílí Microsoft v rámci licence MIT. Zkontrolujte prosím soubor LICENSE.txt v adresáři ukázkového kódu na Githubu pro další podrobnosti.

### <a name="references"></a>Odkazy
• [Cesty taxíkem NYC Andrés Monroy stránce pro stažení](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi Data o jízdách podle Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxislužby a Limousine Komise výzkumu a statistiky](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

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
