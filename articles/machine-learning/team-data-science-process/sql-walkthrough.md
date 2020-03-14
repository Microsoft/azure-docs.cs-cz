---
title: Sestavit a nasadit model v SQL Server VM - vědecké zpracování týmových dat
description: Sestavujte a nasazujte pomocí systému SQL Server na Virtuálním počítači Azure s veřejně dostupnou datovou sadou model strojového učení.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251580"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Vědecké zpracování týmových dat v akci: použití serveru SQL Server
V tomto kurzu se seznámíte s procesem sestavení a nasazení modelu Machine Learning pomocí SQL Server a veřejně dostupné datové sady ( [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) ). Postup následující pracovní postup standardních datových věd: ingestování a zkoumání dat, navrhování funkcí usnadnění vzdělávání, pak sestavit a nasadit model.

## <a name="dataset"></a>NYC taxislužby TRIPS – popis datové sady
Data NYC taxislužby na cestách mají přibližně 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimovaných), které obsahují více než 173 000 000 jednotlivých cest a tarifů placených za každou cestu. Každý záznam o jízdách obsahuje číslo řidičského sbírat míčky a dropoff umístění a čas, anonymizované hack (ovladače) a číslo Medailon (jedinečné id taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

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

Jedinečný klíč pro připojení cesty\_dat a jízdě\_tarif se skládá z těchto polí: Medallion, napadení\_a vyzvednutí\_data a času.

## <a name="mltasks"></a>Příklady úkolů předpovědi
Budeme formulovat tři problémy předpovědi na základě *\_ho množství tipů*, a to znamená:

* Binární klasifikace: předpověď bez ohledu na to, jestli se pro cestu vyplatil tip, to znamená, že *tip\_množství* větší než $0 je pozitivní příklad, zatímco *tip\_hodnota* $0 je negativní příklad.
* Klasifikace víc tříd: K předpovědi rozsahu tip placené pro cestu. *\_velikost pro Tip* se rozdělí na pět přihrádek nebo tříd:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Úloha regrese: odhadnout množství tip placené cesty.  

## <a name="setup"></a>Nastavení prostředí pro datové vědy Azure pro pokročilou analýzu
Jak vidíte v příručce průvodce [plánováním prostředí](plan-your-environment.md) , existuje několik možností, jak pracovat s datovou sadou NYC taxislužby TRIPS v Azure:

* Práce s data v objektech BLOB Azure a modelu ve službě Azure Machine Learning
* Načtení dat do databáze serveru SQL Server a model ve službě Azure Machine Learning

V tomto kurzu provedeme paralelní hromadný import dat do SQL Server, zkoumání dat, strojírenství funkcí a vypnutí vzorkování pomocí SQL Server Management Studio a používání poznámkového bloku IPython. [Ukázkové skripty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) a [poznámkové bloky IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) se sdílejí na GitHubu. Ukázka IPython notebook, a to pro práci s daty v objektech BLOB Azure je také k dispozici ve stejném umístění.

Nastavení prostředí Azure pro datové vědy:

1. [vytvořit účet úložiště](../../storage/common/storage-account-create.md)
2. [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md)
3. [Zřídí Data Science Virtual Machine](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), který poskytuje SQL Server a server IPython notebook.
   
   > [!NOTE]
   > Ukázkové skripty a IPython notebook se stáhne do vašeho virtuálního počítače pro datové vědy během procesu instalace. Po dokončení skriptu po instalaci virtuálního počítače, ukázky budou v knihovně dokumentů pro váš virtuální počítač:  
   > 
   > * Ukázkové skripty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Ukázky IPython poznámkových bloků: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   kde `<user_name>` je přihlašovací jméno pro Windows vašeho virtuálního počítače. Jako **ukázkové skripty** a **ukázkové poznámkové bloky IPython**budeme odkazovat na vzorové složky.
   > 
   > 

V závislosti na velikosti datové sady, umístění zdroje dat a vybraném cílovém prostředí Azure se tento scénář podobá [scénáři \#5: velká datová sada v místních souborech, cílová SQL Server na virtuálním počítači Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Získat data z veřejného zdroje
Pokud chcete získat datovou sadu [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) z jejího veřejného umístění, můžete použít kteroukoli z metod popsaných v tématu [přesun dat do a z Azure Blob Storage](move-azure-blob.md) ke zkopírování dat do nového virtuálního počítače.

Kopírování dat pomocí AzCopy:

1. Přihlaste se ke svému virtuálnímu počítači (VM)
2. Vytvořte nový adresář na datovém disku virtuálního počítače (Poznámka: Nepoužívejte dočasný disk, který je součástí virtuálního počítače jako datový disk).
3. V okně příkazového řádku spusťte následující příkazový řádek Azcopy nahrazení < path_to_data_folder > vaše data složky vytvořené v (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Po dokončení AzCopy by měl být celkem 24 souborů. CSV (12 pro cestu\_dat a 12 pro Trip\_tarif) ve složce data.
4. Rozbalte stažené soubory. Poznamenejte si složku, ve kterém jsou umístěny dekomprimovaných souborů. Tato složka bude označována jako < cesta\_\_soubory\_dat\>.

## <a name="dbload"></a>Hromadný import dat do SQL Server databáze
Výkon načítání a přenosu velkých objemů dat na SQL Database a následné dotazy lze zlepšit pomocí *dělených tabulek a zobrazení*. V této části budeme postupovat podle pokynů popsaných v článku [paralelní hromadné importy dat pomocí tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md) k vytvoření nové databáze a načtení dat do dělených tabulek paralelně.

1. Při přihlášení ke svému VIRTUÁLNÍmu počítači spusťte **SQL Server Management Studio**.
2. Připojení pomocí ověřování Windows.
   
    ![Připojení přes SSMS][12]
3. Pokud jste ještě nezměnili režim ověřování SQL Server a vytvořili jste nového uživatele s přihlašovacími údaji SQL, otevřete soubor skriptu s názvem **change\_auth. SQL** ve složce **Sample Scripts** . Změňte výchozí uživatelské jméno a heslo. Spusťte skript kliknutím na tlačítko **Spustit** na panelu nástrojů.
   
    ![Spuštění skriptu][13]
4. Ověřit nebo změnit systému SQL Server výchozí databáze a protokolu složky k zajištění, které nově vytvořené databáze budou uloženy ve datový Disk. Image virtuálního počítače s SQL Server optimalizovaná pro načítání datových skladů je předem nakonfigurovaná s daty a disky protokolu. Pokud se váš virtuální počítač neobsahuje datový Disk a přidat nový virtuální pevné disky během procesu instalace virtuální počítač, změňte výchozí složky následujícím způsobem:
   
   * Na levém panelu klikněte pravým tlačítkem myši na název SQL Server a klikněte na **vlastnosti**.
     
       ![Vlastnosti serveru SQL][14]
   * V seznamu **Vybrat stránku** vlevo vyberte **nastavení databáze** .
   * Ověřte nebo změňte **výchozí umístění databáze** na umístění **datových disků** dle vašeho výběru. V tomto umístění se nacházejí nové databáze, pokud se vytvoří s výchozím nastavením.
     
       ![Výchozí nastavení databáze SQL][15]  
5. Pokud chcete vytvořit novou databázi a sadu skupin souborů pro ukládání dělených tabulek, otevřete vzorový skript **vytvoření\_db\_default. SQL**. Skript vytvoří ve výchozím umístění dat novou databázi nazvanou **TaxiNYC** a 12 skupin souborů. Každá skupina souborů bude uchovávat jeden měsíc cesty\_data a cesty\_dat tarifů. Změňte název databáze, v případě potřeby. Kliknutím na **Spustit** spusťte skript.
6. V dalším kroku vytvořte dvě tabulky oddílů, jednu pro cestu\_data a druhou pro služební\_tarif. Otevřete vzorový skript **vytvoření\_děleného\_Table. SQL**, který bude:
   
   * Vytvořte funkci oddílu rozdělit data podle měsíce.
   * Vytvořte schéma oddílu mapuje každý měsíc data na různých souborů.
   * Vytvoření dvou dělených tabulek mapovaných na schéma oddílu: **nyctaxi\_Trip** bude obsahovat\_data o jízdě a **nyctaxi\_tarif** bude obsahovat data o cestě\_tarify.
     
     Kliknutím na **Spustit** spusťte skript a vytvořte dělené tabulky.
7. Ve složce **Sample Scripts** jsou k dispozici dva ukázkové skripty PowerShellu, které ukazují paralelní hromadné importy dat do SQL Server tabulek.
   
   * **bcp\_parallel\_Generic. ps1** je obecný skript k paralelnímu hromadnému importu dat do tabulky. Upravte tento skript k nastavení proměnných vstup a cíl, jak je uvedeno v řádcích komentáře ve skriptu.
   * **bcp\_parallel\_nyctaxi. ps1** je předem nakonfigurovaná verze obecného skriptu a dá se použít k načtení obou tabulek pro data NYC taxislužby.  
8. Klikněte pravým tlačítkem na název skriptu **bcp\_parallel\_nyctaxi. ps1** a kliknutím na **Upravit** ho otevřete v PowerShellu. Zkontrolujte přednastavené proměnné a upravte je podle zvoleného názvu databáze, složky vstupních dat, cílové složky protokolu a cest k souborům ukázkových formátů **nyctaxi_trip. XML** a **nyctaxi\_jízdné. XML** (poskytované ve složce **Sample Scripts** ).
   
    ![Hromadné Import dat][16]
   
    Můžete také vybrat režim ověřování, výchozí hodnota je ověřování Windows. Klikněte na zelenou šipku na panelu nástrojů ke spuštění. Skript se spustí 24 operace hromadného importu v paralelní, 12 pro každý dělenou tabulku. Můžete monitorovat průběh importování dat tak, že otevřete výchozí složce dat serveru SQL Server jako výše.
9. Skript prostředí PowerShell hlásí počáteční a koncové časy. Když všechny importy kompletní hromadně, se použije v hlášení koncový čas. Zkontrolujte cílovou složku protokolu a ověřte, že hromadné importy byly úspěšné, tj. v cílové složce protokolu nejsou hlášeny žádné chyby.
10. Vaše databáze je nyní připravena pro zkoumání, vytváření funkcí a dalších operací podle potřeby. Vzhledem k tomu, že jsou tabulky rozdělené podle pole **data a času vyzvednutí\_** , budou se z schématu oddílu vytěžit dotazy, které zahrnují podmínky pro **vyzvednutí\_data a času** v klauzuli **WHERE** .
11. V **SQL Server Management Studio**Prozkoumejte uvedený ukázkový skript **Ukázka\_dotazů. SQL**. Chcete-li spustit některý z ukázkových dotazů, zvýrazněte řádky dotazu a klikněte na tlačítko **Spustit** na panelu nástrojů.
12. Načtení dat cesty taxíkem NYC ve dvou samostatných tabulkách. Pokud chcete zlepšit operace spojení, důrazně doporučujeme k indexování tabulky. Vzorový skript **vytvoří\_dělené\_index. SQL** vytvoří dělené indexy na složeném klíči Join **Medallion, napadení\_a vyzvednutí\_data a času**.

## <a name="dbexplore"></a>Zkoumání dat a strojírenství funkcí v SQL Server
V této části provedeme zkoumání dat a generování funkcí spuštěním dotazů SQL přímo v **SQL Server Management Studio** pomocí databáze SQL Server vytvořené dříve. Vzorový skript s názvem **sample\_dotazů. SQL** je k dispozici ve složce **Sample Scripts** . Úpravou skriptu změňte název databáze, pokud se liší od výchozího nastavení: **TaxiNYC**.

V tomto cvičení provedeme následující:

* Připojte se k **SQL Server Management Studio** buď pomocí ověřování systému Windows, nebo pomocí ověřování SQL a přihlašovacího jména SQL a hesla.
* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data kvality zeměpisnou šířku a délku pole.
* Vygenerujte binární a mezitřídní popisky klasifikace na základě **\_ho množství tipů**.
* Generovat funkce a výpočetní/porovnání vzdálenosti o jízdách.
* Spojení dvou tabulek a extrahovat náhodného vzorku, který se použije k vytvoření modelů.

Až budete připravení přejít k Azure Machine Learning, můžete se buď:  

1. Chcete-li extrahovat a vzorkovat data a zkopírovat dotaz přímo do modulu [Import dat][import-data] v Azure Machine Learning, uložte konečný dotaz SQL.
2. Pochovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové databázové tabulce a použijte novou tabulku v modulu [Import dat][import-data] v Azure Machine Learning.

V této části uložíme konečný dotaz pro extrakci a vzorkování dat. Druhá metoda je znázorněna v části pro [zkoumání dat a funkce pro vývoj funkcí v IPython poznámkovém bloku](#ipnb) .

Pro rychlé ověření počtu řádků a sloupců v tabulkách vyplněny dříve paralelní hromadný import

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
V tomto příkladu identifikuje Medailon (taxislužby čísla) s více než 100 zkracuje dobu odezvy v daném časovém období. Dotaz by měl těžit z přístupu do děleného tabulky, protože je určen schématem oddílu **pickup\_DateTime**. Dotazování úplnou datovou sadu se také provést pomocí dělené tabulky nebo indexu kontroly.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Zkoumání: Distribuce latence podle Medailon a hack_license
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

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Prozkoumávání: v nestejné distribuci cest
Tento příklad zjistí počet cest, které byly šikmý šikmý není v daném časovém období (nebo se v celé datové sadě, pokud pokrývající celý rok). Toto rozdělení odráží distribuce binární označení později použitého pro binární klasifikaci modelování.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Zkoumání: Tip třídy a rozsahu distribuce
Tento příklad vypočítá distribuci tip rozsahů v daném časovém období (nebo v celé datové sadě, pokud pokrývající celý rok). Tato distribuce tříd popisků se bude používat později pro modelování klasifikace s více třídami.

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
Popisek generování a geografického umístění převod zkoumání dotazy lze také Generovat popisky/funkce tak, že odeberete počítání část. Další příklady metodologie jazyka SQL najdete v části [zkoumání dat a funkce pro vývoj funkcí v poznámkovém bloku IPython](#ipnb) . Je efektivnější spustit dotazy generace funkcí na celé datové sadě nebo ve velké podmnožině pomocí dotazů jazyka SQL, které se spouštějí přímo na instanci databáze SQL Server. Dotazy mohou být spuštěny v **SQL Server Management Studio**, poznámkovém bloku IPython nebo jakémkoli vývojovém nástroji nebo prostředí, které má přístup k databázi místně nebo vzdáleně.

#### <a name="preparing-data-for-model-building"></a>Připravují se Data pro vytváření modelů
Následující dotaz se spojí s tabulkami **nyctaxi\_TRIPS** a **nyctaxi\_tarifs** , vygeneruje **binární popisek klasifikace, který je**popsán, jako **\_Tip**klasifikační klasifikace s více třídami a extrahuje 1% náhodný vzorek z plné připojené datové sady. Tento dotaz se dá zkopírovat přímo do [Azure Machine Learning Studio](https://studio.azureml.net) [importovat data][import-data] modul pro příjem přímých dat z instance databáze SQL Server v Azure. Dotaz vyloučí záznamy s nesprávnou (0, 0) souřadnic.

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


## <a name="ipnb"></a>Zkoumání dat a strojírenství funkcí v IPython poznámkovém bloku
V této části budeme provádět zkoumání dat a vytváření funkce pomocí Pythonu a SQL dotazy na databázi systému SQL Server vytvořili dříve. Ukázkový Poznámkový blok IPython nazvaný **Machine-Learning-data-věda-Process-SQL-Story. ipynb** je k dispozici ve složce **ukázek IPython poznámkových bloků** . Tento Poznámkový blok je také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Při práci s velkým objemem dat postupujte podle tohoto doporučeného pořadí:

* Přečtěte si v malým vzorkem dat do rámečku data v paměti.
* Provedení některých vizualizací a průzkumy pomocí jen Vzorkovaná data.
* Můžete experimentovat s vytváření funkcí pomocí jen Vzorkovaná data.
* Pro větší zkoumání dat, manipulaci s daty a vytváření funkcí posílat dotazy SQL přímo na databázi systému SQL Server na virtuálním počítači Azure pomocí Pythonu.
* Rozhodněte, velikost vzorku pro vytváření modelů Azure Machine Learning.

Až budete připraveni přejít k Azure Machine Learning, můžete se buď:  

1. Chcete-li extrahovat a vzorkovat data a zkopírovat dotaz přímo do modulu [Import dat][import-data] v Azure Machine Learning, uložte konečný dotaz SQL. Tato metoda je znázorněna v tématu [stavební modely v Azure Machine Learning](#mlmodel) .    
2. Zachovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové databázové tabulce, a pak použijte novou tabulku v modulu [Import dat][import-data] .

Následuje několik zkoumání, vizualizaci dat a funkce technické příklady. Další příklady najdete v ukázkovém poznámkovém bloku SQL IPython ve složce **ukázek poznámkových bloků IPython** .

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

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Sestava počet řádků a sloupců v tabulce nyctaxi_trip
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
Nyní jsou připraveni na zkoumání jen Vzorkovaná data. Začneme s přehledem o popisných statistikách **\_vzdálenosti** (nebo jiná) pole na cestách:

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
V bodovém grafu si ukážeme **dobu\_času\_v\_sekundách** a **cestě\_** , abyste zjistili, jestli existuje korelace.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Vykreslení #6][6]

Podobně je možné kontrolovat vztah mezi **sazbou\_kódem** a **cestou\_ou vzdálenost**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Vykreslení #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Vzorkování dat v SQL
Při přípravě dat pro sestavování modelu v [Azure Machine Learning Studio](https://studio.azureml.net)se můžete rozhodnout, že se má **dotaz SQL použít přímo v modulu import dat** nebo zachovat provedená inženýrská a ukázková data v nové tabulce, kterou můžete použít v modulu [Import dat][import-data] s jednoduchým **příkazem SELECT * z <\_nové\_\_název >** .

V této části vytvoříme novou tabulku, která bude obsahovat ukázková a inženýrská data. Příklad přímého dotazu SQL pro sestavování modelu je k dispozici v části [zkoumání dat a strojírenství funkcí v tématu SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Vytvořením ukázkového tabulky a naplnění spojené tabulky % 1. Odstranit tabulku první, pokud existuje.
V této části se spojíme s tabulkami **nyctaxi\_TRIPS** a **nyctaxi\_tarify**, extrahujete náhodný vzorek o velikosti 1% a zachovejte ukázková data v novém názvu tabulky **nyctaxi\_jednu\_procent**:

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
V této části prozkoumáme distribuci dat pomocí 1% ukázkových dat, která jsou trvalá v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy je možné provádět pomocí původních tabulek, volitelně můžete pomocí funkce **TABLESAMPLE** omezit ukázku průzkumu nebo omezením výsledků na dané časové období pomocí oddílů pro **vyzvednutí\_** data a času, jak je znázorněno v tématu [zkoumání dat a funkce v SQL Server](#dbexplore) části.

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

1. Popisky binárních tříd byly označeny (předpověď, jestli se má Tip předávat)
2. **\_třída** s popisem více tříd (předpověď přihrádky nebo rozsahu Tip)
   
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
Tento příklad převede souvislé číselné pole na předdefinované rozsahy kategorií, tj. transformuje číselné pole na pole kategorií.

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
V tomto příkladu dojde k rozdělení desítkové reprezentace pole Zeměpisná šířka a/nebo zeměpisná délka do několika polí oblastí různé členitosti, jako je například země/oblast, město, město, blok atd. Nová geografická pole nejsou namapovaná na skutečná umístění. Informace o mapování umístění pro různá místa v kódu najdete v tématu [služby pro službu REST pro mapy Bing](https://msdn.microsoft.com/library/ff701710.aspx).

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

Nyní je připraven pokračovat na sestavení modelu a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravena pro některý z předpovědi problémy identifikovat dříve, a to:

1. Binární klasifikace: předpovědět, zda je či není tip byla zaplacena cesty.
2. Klasifikace víc tříd: K předpovědi rozsahu tip placené podle dříve definovaných tříd.
3. Úloha regrese: odhadnout množství tip placené cesty.  

## <a name="mlmodel"></a>Vytváření modelů v Azure Machine Learning
Začněte cvičení modelování, přihlaste se do pracovního prostoru Azure Machine Learning. Pokud jste ještě nevytvořili pracovní prostor machine learningu, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](../studio/create-workspace.md).

1. Pokud chcete začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Na domovskou stránku Studio nabízí celou řadu informací, videa, kurzy, odkazy k odkazu moduly a dalším prostředkům. Další informace o Azure Machine Learning najdete v [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typické výukového experimentu se skládá z následujících kroků:

1. Vytvořte a **+ Nový** experiment.
2. Získání dat pro Azure Machine Learning.
3. Proveďte předběžné zpracování, transformaci a manipulaci s daty podle potřeby.
4. Funkce vygenerujte, podle potřeby.
5. Rozdělení dat do datové sady školení/ověření/testování (nebo máte rozdělování datových sad pro každou).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na učení problém vyřešit. Například binární klasifikace, klasifikace s více třídami, regrese.
7. Trénování jeden nebo více modelů pomocí trénovací datové sady.
8. Ověření datové sady, natrénované modely pomocí skóre.
9. Vyhodnoťte modely pro výpočet důležité metriky pro výuky.
10. Optimalizujte model (y) a vyberte nejlepší model, který chcete nasadit.

V tomto cvičení jsme mít již prozkoumali a analýzou dat v systému SQL Server a rozhodli na velikost vzorku ingestování ve službě Azure Machine Learning. Pro vytvoření jednoho nebo více předpovědí modelů jsme se rozhodli:

1. Získat data pro Azure Machine Learning pomocí modulu [Import dat][import-data] , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import datových][import-data] modulů.
   
    ![Azure Machine Learning umožňuje importovat Data][17]
2. Na panelu **vlastnosti** vyberte možnost **Azure SQL Database** jako **zdroj dat** .
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formát: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Do příslušného pole zadejte **název databáze** .
5. Zadejte **uživatelské jméno SQL** do pole **název uživatelského účtu serveru**a **heslo** v **hesle uživatelského účtu serveru**.
7. V textové oblasti **dotaz do databáze** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů vyvzorkuje data do požadované velikosti vzorku.

Na následujícím obrázku je příklad binární klasifikace: experiment čtení dat přímo z databáze SQL serveru. Podobně jako experimenty může být vytvořen pro klasifikace víc tříd a regresní problémy.

![Azure Machine Learning trénování][10]

> [!IMPORTANT]
> V ukázkách dotazů pro extrakci a vzorkování dat modelování, které jsou uvedené v předchozích částech, **jsou v dotazu zahrnuté všechny popisky pro tři cvičení modelování**. Důležitým (vyžadovaným) krokem v každé cvičení modelování je **vyloučení** zbytečných popisků pro ostatní dva problémy a jakékoli jiné **cíle nevracení**. Například při použití binární klasifikace **použijte popisek,** který vyloučí pole **\_třída**, **Tip\_** a **celkovou\_částku**. Druhá možnost se cílového únikům protože implikují tip placené.
> 
> Pokud chcete vyloučit nepotřebné sloupce nebo nevrácené cíle, můžete použít modul [Vybrat sloupce v datové sadě][select-columns] nebo [Upravit metadata][edit-metadata]. Další informace najdete v tématu [Výběr sloupců v datové sadě][select-columns] a úpravy odkazů na [metadata][edit-metadata] .
> 
> 

## <a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je model hotový, můžete snadno nasadit ho jako webovou službu přímo z experimentu. Další informace o nasazení Azure Machine Learning webové služby najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Pokud chcete nasadit nové webové služby, budete muset:

1. Vytvoření experimentu bodování.
2. Nasazení webové služby.

Pokud chcete vytvořit experiment bodování z **dokončeného** experimentu, klikněte na tlačítko **vytvořit bodování experimentu** v dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit bodování experiment založené na součástech výukového experimentu. Konkrétně se tyto akce:

1. Uložení naučeného modelu a odeberte moduly trénování modelu.
2. Identifikujte logický **vstupní port** , který bude představovat očekávané schéma vstupních dat.
3. Identifikujte logický **výstupní port** , který bude představovat očekávané výstupní schéma webové služby.

Při vyhodnocování experiment, zkontrolujte a podle potřeby upravte. Typickou úpravou je nahradit vstupní datovou sadu a/nebo dotazem, který vylučuje pole popisků, protože tyto popisky nebudou ve schématu při volání služby k dispozici. Je také vhodné omezit velikost vstupní datové sady nebo dotaz na několik záznamů, které jsou dostatečné pro indikaci vstupního schématu. V případě výstupního portu je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **popisky** s skóre a jejich **pravděpodobnosti** ve výstupu pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

Ukázka vyhodnocování experimentu je na následujícím obrázku. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** na dolním panelu akcí.

![Publikování Azure Machine Learning][11]

Rekapitulace v tomto kurzu návodu jste vytvořili Azure prostředí pro datové vědy, pracoval s velkou datovou sadu veřejné od získání dat pro modelování školení a nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a jeho doprovodném skripty a IPython notebook(s) sdílí Microsoft v rámci licence MIT. Další podrobnosti najdete v souboru LICENSE. txt v adresáři ukázkového kódu na GitHubu.

### <a name="references"></a>Reference
• [Stránka pro stažení Andrés MONROY NYC taxislužby](https://www.andresmh.com/nyctaxitrips/)  
• [Fólie NYC data taxislužby na cestách pomocí Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxislužby a Limousine výzkumu a statistiky Komise](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
