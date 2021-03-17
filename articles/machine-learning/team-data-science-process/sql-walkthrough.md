---
title: Sestavování a nasazování modelu ve SQL Serverm virtuálním počítači – vědecké zpracování týmových dat
description: Sestavte a nasaďte model strojového učení pomocí SQL Server na virtuálním počítači Azure s veřejně dostupnou datovou sadou.
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
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007405"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Vědecké zpracování týmových dat v akci: použití SQL Server
V tomto kurzu se seznámíte s procesem sestavení a nasazení modelu Machine Learning pomocí SQL Server a veřejně dostupné datové sady ( [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) ). Postup následuje za standardním pracovním postupem pro datové vědy: ingestování a zkoumání dat, inženýr funkcí usnadňujících učení a následné sestavení a nasazení modelu.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC taxislužby TRIPS – popis datové sady
Data NYC taxislužby na cestách mají přibližně 20 GB komprimovaných souborů CSV (~ 48 GB nekomprimovaných), které obsahují více než 173 000 000 jednotlivých cest a tarifů placených za každou cestu. Každý záznam na cestách zahrnuje vyzvednutí a dropoff umístění a čas, číslo licence v anonymního napadení (strojvedoucí) a číslo Medallion (jedinečné ID taxislužby). Data se týkají všech cest v roce 2013 a jsou k dispozici v následujících dvou datových sadách pro každý měsíc:

1. CSV trip_data obsahuje podrobnosti o cestě, jako je třeba počet cestujících, vyzvednutí a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. CSV trip_fare obsahuje podrobnosti o tarifu placeného za každou cestu, jako je typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Jedinečný klíč pro připojení k \_ datům cest a služební \_ tarif se skládá z těchto polí: Medallion, licence pro napadení \_ a \_ data a času vyzvednutí.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady úkolů předpovědi
Budeme formulovat tři problémy předpovědi na základě *\_ výše tipu*, konkrétně:

* Binární klasifikace: předpověď bez ohledu na to, zda byl pro cestu zaplacen tip, tj. *\_ hodnota tipu* větší než $0 je pozitivní příklad, zatímco *\_ hodnota tipu* $0 je negativním příkladem.
* Klasifikace více tříd: pro předpověď rozsahu tipu placeného pro danou cestu. *\_ Velikost tipu* rozdělíme na pět přihrádek nebo tříd:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Regresní úloha: pro předpověď množství tipu placeného pro cestu.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Nastavení prostředí pro datové vědy Azure pro pokročilou analýzu
Jak vidíte v příručce průvodce [plánováním prostředí](plan-your-environment.md) , existuje několik možností, jak pracovat s datovou sadou NYC taxislužby TRIPS v Azure:

* Pracujte s daty v objektech blob Azure a pak modelujte Azure Machine Learning
* Načtěte data do databáze SQL Server a potom model Azure Machine Learning

V tomto kurzu provedeme paralelní hromadný import dat do SQL Server, zkoumání dat, strojírenství funkcí a vypnutí vzorkování pomocí SQL Server Management Studio a používání poznámkového bloku IPython. [Ukázkové skripty](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) a [poznámkové bloky IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) se sdílejí na GitHubu. Ukázkový IPython Poznámkový blok pro práci s daty v Azure BLOBs je dostupný taky ve stejném umístění.

Nastavení prostředí pro datové vědy v Azure:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)
2. [Vytvoření pracovního prostoru Azure Machine Learning](../classic/create-workspace.md)
3. [Zřídí Data Science Virtual Machine](../data-science-virtual-machine/overview.md), který poskytuje SQL Server a server IPython notebook.
   
   > [!NOTE]
   > Ukázkové skripty a poznámkové bloky IPython se stáhnou do vašeho virtuálního počítače pro datové vědy během procesu instalace. Po dokončení skriptu po instalaci virtuálního počítače se ukázky zobrazí v knihovně dokumentů vašeho virtuálního počítače:  
   > 
   > * Ukázkové skripty: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Ukázkové poznámkové bloky IPython: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   kde `<user_name>` je přihlašovací jméno vašeho virtuálního počítače v systému Windows. Jako **ukázkové skripty** a **ukázkové poznámkové bloky IPython** budeme odkazovat na vzorové složky.
   > 
   > 

V závislosti na velikosti datové sady, umístění zdroje dat a vybraném cílovém prostředí Azure se tento scénář podobá [scénáři \# 5: velká datová sada v místních souborech, cílová SQL Server na virtuálním počítači Azure](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Získat data z veřejného zdroje
Pokud chcete získat datovou sadu [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) z jejího veřejného umístění, můžete použít kteroukoli z metod popsaných v tématu [přesun dat do a z Azure Blob Storage](move-azure-blob.md) ke zkopírování dat do nového virtuálního počítače.

Kopírování dat pomocí AzCopy:

1. Přihlaste se k virtuálnímu počítači (VM).
2. Vytvořte nový adresář na datovém disku virtuálního počítače (Poznámka: Nepoužívejte dočasný disk, který je součástí virtuálního počítače jako datový disk).
3. V okně příkazového řádku spusťte následující příkazový řádek AzCopy, ve kterém nahradíte <path_to_data_folder> s vaší datovou složkou vytvořenou v (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Po dokončení AzCopy by měl být celkem 24 souborů. CSV (12 pro data o služební cestě \_ a 12 pro služební \_ tarif) ve složce data.
4. Extrahování stažených souborů. Poznamenejte si složku, do které se nacházejí nekomprimované soubory. Tato složka bude označována jako <cesta \_ k \_ datovým \_ souborům \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Hromadný import dat do SQL Server databáze
Výkon načítání a přenosu velkých objemů dat na SQL Database a následné dotazy lze zlepšit pomocí *dělených tabulek a zobrazení*. V této části budeme postupovat podle pokynů popsaných v článku [paralelní hromadné importy dat pomocí tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md) k vytvoření nové databáze a načtení dat do dělených tabulek paralelně.

1. Při přihlášení ke svému VIRTUÁLNÍmu počítači spusťte **SQL Server Management Studio**.
2. Připojte se pomocí ověřování systému Windows.
   
    ![SSMS připojit][12]
3. Pokud jste ještě nezměnili režim ověřování SQL Server a vytvořili jste nového uživatele s přihlašovacími údaji SQL, otevřete soubor skriptu s názvem **Change \_ auth. SQL** ve složce **Sample Scripts** . Změňte výchozí uživatelské jméno a heslo. Spusťte skript kliknutím na tlačítko **Spustit** na panelu nástrojů.
   
    ![Spustit skript][13]
4. Ověřte nebo změňte SQL Server výchozí databázi a složky protokolů, abyste zajistili, že nově vytvořené databáze budou uloženy na datovém disku. Image virtuálního počítače s SQL Server optimalizovaná pro načítání datových skladů je předem nakonfigurovaná s daty a disky protokolu. Pokud váš virtuální počítač neobsahoval datový disk a Přidali jste nové virtuální pevné disky během procesu instalace virtuálního počítače, změňte výchozí složky následujícím způsobem:
   
   * Na levém panelu klikněte pravým tlačítkem myši na název SQL Server a klikněte na **vlastnosti**.
     
       ![Vlastnosti SQL Server][14]
   * V seznamu **Vybrat stránku** vlevo vyberte **nastavení databáze** .
   * Ověřte nebo změňte **výchozí umístění databáze** na umístění **datových disků** dle vašeho výběru. V tomto umístění se nacházejí nové databáze, pokud se vytvoří s výchozím nastavením.
     
       ![Výchozí nastavení SQL Database][15]  
5. Pokud chcete vytvořit novou databázi a sadu skupin souborů pro ukládání dělených tabulek, otevřete vzorový skript **vytvořit \_ databázi \_ Default. SQL**. Skript vytvoří ve výchozím umístění dat novou databázi nazvanou **TaxiNYC** a 12 skupin souborů. Každá skupina souborů bude uchovávat data o služební cestě \_ a data o služebním tarifu za jeden měsíc \_ . V případě potřeby upravte název databáze. Kliknutím na **Spustit** spusťte skript.
6. Dále vytvořte dvě tabulky oddílů, jednu pro data na cestách \_ a druhou pro služební \_ tarif. Otevřete vzorový skript **vytvořit \_ dělenou \_ tabulku. SQL**, která bude:
   
   * Vytvořte funkci oddílu pro rozdělení dat po měsících.
   * Vytvořte schéma oddílu pro mapování dat jednotlivých měsíců do jiné skupiny souborů.
   * Vytvoření dvou dělených tabulek mapovaných na schéma oddílu: **nyctaxi \_ Trip** bude obsahovat data o cestách \_ a **nyctaxi \_ tarif** bude obsahovat data o služebním \_ tarifu.
     
     Kliknutím na **Spustit** spusťte skript a vytvořte dělené tabulky.
7. Ve složce **Sample Scripts** jsou k dispozici dva ukázkové skripty PowerShellu, které ukazují paralelní hromadné importy dat do SQL Server tabulek.
   
   * **\_ paralelní \_generic.ps1BCP** je obecný skript k paralelnímu hromadnému importu dat do tabulky. Úpravou tohoto skriptu nastavte vstupní a cílové proměnné, které jsou uvedené v řádcích komentářů ve skriptu.
   * **\_ paralelní \_nyctaxi.ps1BCP** je předem nakonfigurovaná verze obecného skriptu a dá se použít k načtení obou tabulek pro data o cestách NYC taxislužby.  
8. Klikněte pravým tlačítkem myši na název **\_ paralelního \_nyctaxi.ps1skriptu BCP** a kliknutím na **Upravit** ho otevřete v PowerShellu. Zkontrolujte přednastavené proměnné a upravte je podle zvoleného názvu databáze, složky vstupních dat, cílové složky protokolu a cest k souborům formátů vzorku **nyctaxi_trip.xml** a **nyctaxi \_fare.xml** (ve složce **Sample Scripts** ).
   
    ![Hromadné importy dat][16]
   
    Můžete také vybrat režim ověřování, výchozí hodnota je ověřování systému Windows. Kliknutím na zelenou šipku na panelu nástrojů spustíte. Skript spustí 24 hromadných operací hromadného importu paralelně 12 pro každou dělenou tabulku. Průběh importu dat můžete monitorovat tak, že otevřete složku SQL Server výchozí data, jak je uvedeno výše.
9. PowerShellový skript hlásí počáteční a koncové časy. Po dokončení všech hromadných importů se nahlásí koncový čas. Zkontrolujte cílovou složku protokolu a ověřte, že hromadné importy byly úspěšné, tj. v cílové složce protokolu nejsou hlášeny žádné chyby.
10. Vaše databáze je teď připravená na průzkum, strojírenství funkcí a další činnosti podle potřeby. Vzhledem k tomu, že jsou tabulky rozdělené podle pole pro **\_ Datum vyzvednutí** , dotazy, které zahrnují podmínky pro **vyzvednutí hodnoty \_ DateTime** v klauzuli **WHERE** , budou využívat schéma oddílu.
11. V **SQL Server Management Studio** Prozkoumejte uvedené ukázkové dotazy vzorového skriptu **\_ . SQL**. Chcete-li spustit některý z ukázkových dotazů, zvýrazněte řádky dotazu a klikněte na tlačítko **Spustit** na panelu nástrojů.
12. Data cest NYC taxislužby se načítají ve dvou samostatných tabulkách. Pro zlepšení operací spojování se důrazně doporučuje indexovat tabulky. Vzorový skript **vytvořit \_ rozdělený \_ index. SQL** vytvoří dělené indexy na složeném klíči Join **Medallion, hacker \_ a \_ data a času vyzvednutí**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Zkoumání dat a strojírenství funkcí v SQL Server
V této části provedeme zkoumání dat a generování funkcí spuštěním dotazů SQL přímo v **SQL Server Management Studio** pomocí databáze SQL Server vytvořené dříve. Vzorový skript s názvem **ukázkové \_ dotazy. SQL** je k dispozici ve složce **Sample Scripts** . Úpravou skriptu změňte název databáze, pokud se liší od výchozího nastavení: **TaxiNYC**.

V tomto cvičení budeme:

* Připojte se k **SQL Server Management Studio** buď pomocí ověřování systému Windows, nebo pomocí ověřování SQL a přihlašovacího jména SQL a hesla.
* Prozkoumejte distribuci dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat v polích Zeměpisná délka a zeměpisná šířka.
* Vygenerujte binární a mezitřídní popisky klasifikace na **základě \_ velikosti tipu**.
* Generování funkcí a výpočetních/porovnávacích vzdáleností cest.
* Připojte se k oběma tabulkám a extrahujte náhodný vzorek, který se použije k sestavení modelů.

Až budete připraveni přejít k Azure Machine Learning, může to mít jednu z těchto akcí:  

1. Chcete-li extrahovat a vzorkovat data a zkopírovat dotaz přímo do modulu [Import dat][import-data] v Azure Machine Learning, uložte konečný dotaz SQL.
2. Pochovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové databázové tabulce a použijte novou tabulku v modulu [Import dat][import-data] v Azure Machine Learning.

V této části uložíme konečný dotaz pro extrakci a vzorkování dat. Druhá metoda je znázorněna v části pro [zkoumání dat a funkce pro vývoj funkcí v IPython poznámkovém bloku](#ipnb) .

Rychlé ověření počtu řádků a sloupců v tabulkách vydaných dříve pomocí paralelního hromadného importu

- Vykázat počet řádků v tabulce nyctaxi_trip bez prohledávání tabulky: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Sestava počtu sloupců v tabulce nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: distribuce cest pomocí Medallion
Tento příklad identifikuje Medallion (taxislužby čísla) o více než 100 cest během daného časového období. Dotaz by měl těžit z tabulkového přístupu s dělenou výhodou, protože je podmíněné schémam oddílu **pro \_ Datum vyzvednutí**. Dotazování na celou datovou sadu použije také prohledání děleného tabulky nebo indexu.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Průzkum: distribuce cest pomocí Medallion a hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Vyhodnocení kvality dat: ověření záznamů s nesprávnou zeměpisnou délkou nebo zeměpisnou šířkou
Tento příklad zkoumá, pokud některá pole Zeměpisná délka a/nebo zeměpisná délka buď obsahují neplatnou hodnotu (Radian stupně by měly být mezi-90 a 90), nebo musí mít souřadnice (0, 0).

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Prozkoumávání: v nestejné distribuci cest
Tento příklad vyhledá počet cest, které byly v daném časovém období (nebo v plné datové sadě), které jsou v daném časovém období pokryté, nešikmé (nebo v úplném datové sadě). Tato distribuce odráží binární distribuci jmenovky, která se má později použít pro modelování binární klasifikace.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Průzkum: distribuce třídy nebo rozsahu tipu
Tento příklad vypočítá distribuci rozsahů tipů v daném časovém období (nebo v úplné datové sadě, pokud pokrývá celý rok). Tato distribuce tříd popisků se bude používat později pro modelování klasifikace s více třídami.

```sql
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
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Průzkum: výpočty a porovnání vzdálenosti cest
Tento příklad převede vyzvednutí a dropoff zeměpisnou délku a zeměpisnou šířku na geografické body SQL, vypočítá vzdálenost pro cestu pomocí rozdílů geografických bodů SQL a vrátí náhodný vzorek výsledků pro porovnání. Příklad omezuje výsledky na platné souřadnice pouze pomocí dotazu vyhodnocení kvality dat popsaných výše.

```sql
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
```

#### <a name="feature-engineering-in-sql-queries"></a>Inženýrské funkce v dotazech SQL
Dotazy pro zkoumání a vytváření popisků se dají použít ke generování popisků nebo funkcí odebráním části počítání. Další příklady metodologie jazyka SQL najdete v části [zkoumání dat a funkce pro vývoj funkcí v poznámkovém bloku IPython](#ipnb) . Je efektivnější spustit dotazy generace funkcí na celé datové sadě nebo ve velké podmnožině pomocí dotazů jazyka SQL, které se spouštějí přímo na instanci databáze SQL Server. Dotazy mohou být spuštěny v **SQL Server Management Studio**, poznámkovém bloku IPython nebo jakémkoli vývojovém nástroji nebo prostředí, které má přístup k databázi místně nebo vzdáleně.

#### <a name="preparing-data-for-model-building"></a>Příprava dat pro vytváření modelů
Následující dotaz se spojí s tabulkami **nyctaxi \_ TRIPS** a **nyctaxi \_ tarifs** , vygeneruje **binární popisek klasifikace**, který je na ní popsán, **\_ třídu tipů** klasifikační klasifikace s více třídami a extrahuje 1% náhodný vzorek z plné připojené datové sady. Tento dotaz se dá zkopírovat přímo do [Azure Machine Learning Studio](https://studio.azureml.net) [importovat data][import-data] modul pro příjem přímých dat z instance databáze SQL Server v Azure. Dotaz vyloučí záznamy s nesprávnými souřadnicemi (0, 0).

```sql
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
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Zkoumání dat a strojírenství funkcí v IPython poznámkovém bloku
V této části provedeme zkoumání dat a generování funkcí pomocí dotazů Python a SQL pro databázi SQL Server vytvořenou dříve. Ukázkový Poznámkový blok IPython nazvaný **Machine-Learning-data-věda-Process-SQL-Story. ipynb** je k dispozici ve složce **ukázek IPython poznámkových bloků** . Tento Poznámkový blok je také k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Při práci s velkým objemem dat postupujte podle tohoto doporučeného pořadí:

* Přečtěte si malý vzorek dat do datového rámce v paměti.
* Pomocí ukázkových dat provedete některé vizualizace a průzkumy.
* Experimentujte s inženýry funkcí pomocí ukázkových dat.
* V případě většího zkoumání dat, manipulace s daty a inženýrů funkcí použijte Python k vystavení dotazů SQL přímo proti databázi SQL Server ve virtuálním počítači Azure.
* Určete velikost vzorku, která se má použít pro Azure Machine Learning vytváření modelů.

Až budete připraveni pokračovat k Azure Machine Learning, může to mít jednu z těchto akcí:  

1. Chcete-li extrahovat a vzorkovat data a zkopírovat dotaz přímo do modulu [Import dat][import-data] v Azure Machine Learning, uložte konečný dotaz SQL. Tato metoda je znázorněna v tématu [stavební modely v Azure Machine Learning](#mlmodel) .    
2. Zachovejte ukázková a inženýrská data, která plánujete použít pro vytváření modelů v nové databázové tabulce, a pak použijte novou tabulku v modulu [Import dat][import-data] .

Níže jsou uvedeny některé příklady zkoumání dat, vizualizace dat a techniků funkcí. Další příklady najdete v ukázkovém poznámkovém bloku SQL IPython ve složce **ukázek poznámkových bloků IPython** .

#### <a name="initialize-database-credentials"></a>Inicializovat pověření databáze
Nastavení připojení k databázi inicializujte v těchto proměnných:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Vytvoření připojení k databázi

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Vykázat počet řádků a sloupců v tabulce nyctaxi_trip

```sql
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
```

* Celkový počet řádků = 173179759  
* Celkový počet sloupců = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Čtení malého datového vzorku z databáze SQL Server

```sql
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
```

Doba čtení ukázkové tabulky je 6,492000 sekund.  
Počet načtených řádků a sloupců = (84952, 21)

#### <a name="descriptive-statistics"></a>Popisné statistiky
Teď jsou připravené k prozkoumání ukázkových dat. Začneme s prohlížením popisných statistik o **\_ vzdálenosti** (nebo jakémkoli jiném) poli na cestách:

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Vizualizace: příklad vykreslení boxu
V dalším kroku se podíváme na krabicový pohled, který vizualizuje cestu pro quantiles.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![#1 vykreslení][1]

#### <a name="visualization-distribution-plot-example"></a>Vizualizace: příklad vykreslení distribuce

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![#2 vykreslení][2]

#### <a name="visualization-bar-and-line-plots"></a>Vizualizace: pruhové a spojnicové pruhy
V tomto příkladu vytvoříme cestu ke vzdálenosti do pěti přihrádek a vizualizujte výsledky binningu.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Rozdělení přihrádky můžeme vykreslit do pruhového nebo pruhového grafu, jak je uvedeno níže.

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![#3 vykreslení][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![#4 vykreslení][4]

#### <a name="visualization-scatterplot-example"></a>Vizualizace: příklad scatterplot
Pro zjištění, zda existuje korelace, zobrazujeme **\_ v grafu dobu provozu \_ v \_ sekundách** a na **služební \_ dráze** .

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![#6 vykreslení][6]

Podobně můžeme kontrolovat vztah mezi **\_ kódem sazby** a **\_ délkou cesty**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![#8 vykreslení][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling data v SQL
Při přípravě dat pro sestavování modelu v [Azure Machine Learning Studio](https://studio.azureml.net)se můžete rozhodnout, že se má **dotaz SQL použít přímo v modulu import dat** nebo zachovat provedená inženýrská a ukázková data v nové tabulce, kterou můžete použít v modulu [Import dat][import-data] s jednoduchým **příkazem SELECT * z <\_ nového \_ názvu tabulky \_>**.

V této části vytvoříme novou tabulku, která bude obsahovat ukázková a inženýrská data. Příklad přímého dotazu SQL pro sestavování modelu je k dispozici v části [zkoumání dat a strojírenství funkcí v tématu SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Vytvořte ukázkovou tabulku a naplňte ji o 1% spojených tabulek. Přetáhněte tabulku jako první, pokud existuje.
V této části se spojíme s tabulkami **nyctaxi \_ TRIPS** a **nyctaxi \_ jízdné**, extrahujete náhodný vzorek o velikosti 1% a zachovejte ukázková data v novém názvu tabulky **nyctaxi \_ 1 \_**%:

```sql
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
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Zkoumání dat pomocí dotazů SQL v poznámkovém bloku IPython
V této části prozkoumáme distribuci dat pomocí 1% ukázkových dat, která jsou trvalá v nové tabulce, kterou jsme vytvořili výše. Podobné průzkumy je možné provádět pomocí původních tabulek, volitelně můžete pomocí funkce **TABLESAMPLE** omezit ukázku průzkumu nebo omezením výsledků na dané časové období pomocí oddílů data a času **vyzvednutí \_** , jak je znázorněno v části [zkoumání dat a funkce pro vývoj funkcí v SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Průzkum: každodenní distribuce cest

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Průzkum: distribuce cest na Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generování funkcí pomocí dotazů SQL v poznámkovém bloku IPython
V této části vygenerujeme nové popisky a funkce přímo pomocí dotazů SQL, které pracují na vzorové tabulce 1%, kterou jsme vytvořili v předchozí části.

#### <a name="label-generation-generate-class-labels"></a>Generování popisků: generování popisků třídy
V následujícím příkladu generujeme dvě sady popisků, které se mají použít pro modelování:

1. Popisky binárních **tipped** tříd byly označeny (předpověď, jestli se má Tip předávat)
2. **\_ Třída Tip** pro více tříd (předpověď z přihrádky nebo rozsahu Tip)

```sql   
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
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funkce pro vývoj funkcí: počet funkcí pro kategorií sloupce
Tento příklad transformuje pole kategorií do číselného pole nahrazením každé kategorie počtem jejich výskytů v datech.

```sql
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
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Inženýrské funkce: funkce přihrádek pro číselné sloupce
Tento příklad převede souvislé číselné pole na předdefinované rozsahy kategorií, tj. transformuje číselné pole na pole kategorií.

```sql
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
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Strojírenství funkcí: extrakce funkcí umístění z desetinné šířky/délky
V tomto příkladu dojde k rozdělení desítkové reprezentace pole Zeměpisná šířka a/nebo zeměpisná délka do několika polí oblastí různé členitosti, jako je například země/oblast, město, město, blok atd. Nová geografická pole nejsou namapovaná na skutečná umístění. Informace o mapování umístění pro různá místa v kódu najdete v tématu [služby pro službu REST pro mapy Bing](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
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
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Ověření konečného formuláře tabulky natrénuje

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Nyní je připraven pokračovat na sestavení modelu a nasazení modelu v [Azure Machine Learning](https://studio.azureml.net). Data jsou připravená na některý z výše zjištěných problémů předpovědi, konkrétně:

1. Binární klasifikace: pro předpověď, zda byl pro cestu zaplacen Tip.
2. Třída klasifikace s více třídami: pro předpověď rozsahu zaplaceného tipu podle dříve definovaných tříd.
3. Regresní úloha: pro předpověď množství tipu placeného pro cestu.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Vytváření modelů v Azure Machine Learning
Pokud chcete začít modelování, přihlaste se ke svému pracovnímu prostoru Azure Machine Learning. Pokud jste ještě nevytvořili pracovní prostor machine learningu, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](../classic/create-workspace.md).

1. Pokud chcete začít s Azure Machine Learning, přečtěte si téma [co je Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net).
3. Domovská stránka studia poskytuje spoustu informací, videí, kurzů, odkazů na reference k modulům a dalších prostředků. Další informace o Azure Machine Learning najdete v [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Typický zkušební experiment se skládá z následujících kroků:

1. Vytvořte a **+ Nový** experiment.
2. Získat data pro Azure Machine Learning.
3. Proveďte předběžné zpracování, transformaci a manipulaci s daty podle potřeby.
4. Podle potřeby generujte funkce.
5. Rozdělit data na datové sady pro školení, ověřování/testování (nebo mít pro každý z nich samostatné datové sady).
6. Vyberte jeden nebo více algoritmů strojového učení v závislosti na problémech s učením při řešení. Například binární klasifikace, klasifikace s více třídami, regrese.
7. Proveďte výuku jednoho nebo více modelů pomocí školicí datové sady.
8. Vyhodnotí datovou sadu ověřování pomocí vycvičených modelů.
9. Vyhodnoťte modely pro výpočet relevantních metrik pro problém s učením.
10. Optimalizujte model (y) a vyberte nejlepší model, který chcete nasadit.

V tomto cvičení jsme již prozkoumali a provedli analýzu dat v SQL Server a rozhodli jste se v Azure Machine Learning o velikosti vzorků, která se ingestuje. Pro vytvoření jednoho nebo více předpovědí modelů jsme se rozhodli:

1. Získat data pro Azure Machine Learning pomocí modulu [Import dat][import-data] , který je k dispozici v části **vstup a výstup dat** . Další informace najdete na referenční stránce [Import datových][import-data] modulů.
   
    ![Azure Machine Learning import dat][17]
2. Na panelu **vlastnosti** vyberte možnost **Azure SQL Database** jako **zdroj dat** .
3. Do pole **název databázového serveru** zadejte název DNS databáze. Formátovat `tcp:<your_virtual_machine_DNS_name>,1433`
4. Do příslušného pole zadejte **název databáze** .
5. Zadejte **uživatelské jméno SQL** do pole **název uživatelského účtu serveru** a **heslo** v **hesle uživatelského účtu serveru**.
7. V textové oblasti **dotaz do databáze** vložte dotaz, který extrahuje potřebná databázová pole (včetně všech vypočítaných polí, jako jsou popisky), a dolů vyvzorkuje data do požadované velikosti vzorku.

Příkladem binární klasifikace experimentu pro čtení dat přímo z SQL Server databáze je na následujícím obrázku. Podobné experimenty lze vytvořit pro třídy s více třídami a regresní problémy.

![Azure Machine Learning vlak][10]

> [!IMPORTANT]
> V ukázkách dotazů pro extrakci a vzorkování dat modelování, které jsou uvedené v předchozích částech, **jsou v dotazu zahrnuté všechny popisky pro tři cvičení modelování**. Důležitým (vyžadovaným) krokem v každé cvičení modelování je **vyloučení** zbytečných popisků pro ostatní dva problémy a jakékoli jiné **cíle nevracení**. Například při použití binární klasifikace **použijte popisek,** který se zanechal a vyloučí pole **\_ Třída Tip**, **\_ hodnota tipu** a **celkovou \_ částku**. Tato druhá z nich nevrací cíle, protože implikuje Tip.
> 
> Pokud chcete vyloučit nepotřebné sloupce nebo nevrácené cíle, můžete použít modul [Vybrat sloupce v datové sadě][select-columns] nebo [Upravit metadata][edit-metadata]. Další informace najdete v tématu [Výběr sloupců v datové sadě][select-columns] a úpravy odkazů na [metadata][edit-metadata] .
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Nasazení modelů v Azure Machine Learning
Když je model připravený, můžete ho snadno nasadit jako webovou službu přímo z experimentu. Další informace o nasazení Azure Machine Learning webové služby najdete v tématu [nasazení webové služby Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

K nasazení nové webové služby potřebujete:

1. Vytvořte experiment pro bodování.
2. Nasaďte webovou službu.

Pokud chcete vytvořit experiment bodování z **dokončeného** experimentu, klikněte na tlačítko **vytvořit bodování experimentu** v dolním panelu akcí.

![Bodování Azure][18]

Azure Machine Learning se pokusí vytvořit experiment bodování založený na komponentách zkušebního experimentu. Konkrétně to bude:

1. Uložte školicí model a odeberte moduly pro školení modelů.
2. Identifikujte logický **vstupní port** , který bude představovat očekávané schéma vstupních dat.
3. Identifikujte logický **výstupní port** , který bude představovat očekávané výstupní schéma webové služby.

Když se vytvoří experiment bodování, zkontrolujte ho a podle potřeby ho upravte. Typickou úpravou je nahradit vstupní datovou sadu a/nebo dotazem, který vylučuje pole popisků, protože tyto popisky nebudou ve schématu při volání služby k dispozici. Je také vhodné omezit velikost vstupní datové sady nebo dotaz na několik záznamů, které jsou dostatečné pro indikaci vstupního schématu. V případě výstupního portu je běžné vyloučit všechna vstupní pole a zahrnout do výstupu pouze **popisky** s skóre a jejich **pravděpodobnosti** ve výstupu pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

Experimentování s ukázkovým bodováním je na následujícím obrázku. Až budete připraveni k nasazení, klikněte na tlačítko **publikovat webovou službu** na dolním panelu akcí.

![Publikování Azure Machine Learning][11]

V tomto výukovém kurzu jste v tomto návodu vytvořili prostředí Azure Data Sciences, které fungovalo s velkou veřejnou datovou sadou, a to všechno jako při získávání dat pro modelování školení a nasazení webové služby Azure Machine Learning.

### <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a příslušné doprovodné skripty a IPython Poznámkový blok sdílí Microsoft v rámci licence MIT. Další podrobnosti najdete v souboru LICENSE.txt v adresáři ukázkového kódu na GitHubu.

### <a name="references"></a>Reference
•    [Stránka pro stažení Andrés MONROY NYC taxislužby](https://www.andresmh.com/nyctaxitrips/)  
•    [Fólie NYC data taxislužby na cestách pomocí chrisho Whongu](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC taxislužby a Limousine výzkumu a statistiky Komise](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data