---
title: 'Kurz: Začínáme s Azure synapse Analytics'
description: Postup podle kroků pro rychlé pochopení základních konceptů v Azure synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 29e5ba149a99f350b8ad9244605470d8b9b61597
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749498"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Začínáme s využitím Azure synapse Analytics

Tento kurz vás provede všemi základními kroky potřebnými k instalaci a používání služby Azure synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Příprava účtu úložiště pro použití v pracovním prostoru synapse

* Otevřete [Azure Portal](https://portal.azure.com)
* Vytvořte nový účet úložiště s následujícími nastaveními:
    * Na kartě **základy**

    |Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    |**Název účtu úložiště**| Můžete mu dát libovolný název.|V tomto dokumentu se na něj budeme odkazovat jako na `contosolake` .
    |**Druh účtu**|Musí být nastavené na`StorageV2`||
    |**Umístění**|Můžete vybrat libovolné umístění.| Doporučujeme, aby váš synapse pracovní prostor Azure Data Lake Storage a účet Gen2 (ADLS) byly ve stejné oblasti.|
    ||||

    * Na kartě **Upřesnit**

    |Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    |**Data Lake Storage Gen2**|`Enabled`| Azure synapse funguje jenom s účty úložiště, kde je toto nastavení povolené.|
    ||||

* Po vytvoření účtu úložiště proveďte Tato přiřazení rolí nebo ověřte, že jsou už přiřazená. V účtu úložiště v levém navigačním panelu vyberte **řízení přístupu (IAM)** .
    * Přiřaďte se k roli **vlastníka** v účtu úložiště.
    * Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** v účtu úložiště.
* V levém navigačním panelu vyberte **kontejnery** a vytvořte kontejner. Můžete mu dát libovolný název. Přijměte výchozí **úroveň veřejného přístupu**. V tomto dokumentu budeme volat kontejner `users` . Vyberte **Vytvořit**. 

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru synapse

* Otevřete [Azure Portal](https://portal.azure.com) a v horní části hledání `Synapse` .
* Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)** .
* Vybrat **+ Přidat**
* Karta **základy** :

    |Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    |**Název pracovního prostoru**|Můžete ji volat cokoli.| V tomto dokumentu použijeme`myworkspace`
    |**Oblast**|Porovnává s oblastí účtu úložiště.||
    |||

* V části **vybrat data Lake Storage Gen 2** vyberte účet a kontejner, který jste vytvořili dříve.

> [!NOTE]
> Vybraný účet úložiště se bude označovat jako "primární" účet úložiště v pracovním prostoru synapse.

* Vyberte **Zkontrolovat a vytvořit**. Vyberte **Vytvořit**. Pracovní prostor bude připraven během několika minut.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Ověření, jestli má MSI synapse v pracovním prostoru přístup k účtu úložiště

To může být pro vás již provedeno. V každém případě byste měli ověřit.

* Otevřete [Azure Portal](https://portal.azure.com) otevřete primární účet úložiště, který jste zvolili pro váš pracovní prostor.
* Ujistěte se, že následující přiřazení existuje, nebo ho vytvořte, pokud ne.
    * Role Přispěvatel dat objektu BLOB služby Storage v účtu úložiště do vašeho pracovního prostoru.
    * Chcete-li přiřadit tuto roli k pracovnímu prostoru, vyberte roli Přispěvatel dat objektu BLOB úložiště, ponechte výchozí **oprávnění přiřadit k** a v poli **Vybrat** zadejte název pracovního prostoru. Vyberte **Uložit**.
    
## <a name="launch-synapse-studio"></a>Spustit synapse Studio

Po vytvoření pracovního prostoru synapse máte dva způsoby, jak otevřít synapse Studio:
* Otevřete pracovní prostor synapse v [Azure Portal](https://portal.azure.com) a v horní části **přehledu** vyberte **Spustit synapse Studio** .
* Přímo přejít na https://web.azuresynapse.net pracovní prostor a přihlásit se k němu.

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

* V synapse studiu na levé straně přejděte ke **správě >ch fondů SQL** .
* Poznámka: všechny pracovní prostory synapse se dodávají s předem vytvořeným fondem s názvem **SQL na vyžádání**.
* Vyberte **+ Nová** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název fondu SQL**| `SQLDB1`|
    |**Úroveň výkonu**|`DW100C`|
* Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
* Váš fond bude připravený během několika minut.

> [!NOTE]
> Synapse fond SQL odpovídá, co se používá pro volání "Azure SQL Data Warehouse".

* Fond SQL spotřebovává Fakturovatelné prostředky, pokud je spuštěný. V takovém případě můžete v případě potřeby pozastavit fond a snížit náklady.
* Když se vytvoří fond SQL, bude se taky přidružit k databázi fondu SQL s názvem **SQLDB1**.

## <a name="create-an-apache-spark-pool-for-azure-synapse-analytics"></a>Vytvoření fondu Apache Spark pro Azure synapse Analytics

* V synapse studiu vyberte na levé straně možnost **spravovat fondy > Apache Spark** .
* Vyberte **+ Nová** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název Apache Spark fondu**|`Spark1`
    |**Velikost uzlu**| `Small`|
    |**Počet uzlů**| Nastavte minimum na 3 a maximum na 3.|
    |||

* Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
* Váš fond Apache Spark bude připravený během několika sekund.

> [!NOTE]
> Bez ohledu na název Apache Spark fond nevypadá jako fond SQL. Je to jenom pro základní metadata, která používáte k informování pracovního prostoru synapse o tom, jak pracovat s Sparkem. 

* Protože se nedají spustit ani zastavit fondy Sparku pro metadata. 
* Když v synapse provedete jakoukoli aktivitu Spark, určíte fond Spark, který se má použít. Fond informuje synapse, kolik prostředků Spark se má použít. Platíte jenom za využití prostředků Thar. Při aktivním zastavení používání fondu se prostředky automaticky vyprší a budou recyklovány.
> [!NOTE]
> Databáze Spark jsou nezávisle vytvořené z fondů Spark. Pracovní prostor má vždycky databázi Spark s názvem **Default** a můžete vytvořit další databáze Spark.

## <a name="sql-on-demand-pools"></a>Fondy na vyžádání SQL

SQL na vyžádání je zvláštní druh fondu SQL, který je vždy k dispozici v pracovním prostoru synapse. Umožňuje pracovat s SQL bez nutnosti vytvářet nebo zabývat se správou synapse fondu SQL.

> [!NOTE]
> Na rozdíl od jiných druhů fondů je fakturace za SQL na vyžádání založená na množství dat naskenovaných pro spuštění dotazu, a ne na počtu prostředků použitých ke spuštění dotazu.

* SQL na vyžádání má také vlastní druh databází SQL na vyžádání, které existují nezávisle na jakémkoli fondu SQL na vyžádání.
* V současné době má pracovní prostor vždy právě jeden fond SQL na vyžádání s názvem **SQL na vyžádání**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Načtení ukázkových dat NYC taxislužby do databáze SQLDB1

* V synapse studiu vyberte v nabídce nejvyšší úrovně modrou možnost **?** .
* Vyberte **začínáme > centrum Začínáme**
* V **ukázkových datech** karty s popiskem dotazu vyberte fond SQL s názvem.`SQLDB1`
* Vyberte **data dotazu**. Zobrazí se oznámení, že se zobrazí zpráva "načítání ukázkových dat", která se zobrazí, a pak zmizí.
* V horní části synapse studia se zobrazí indikátor s světle modrým oznámením, že se data načítají do SQLDB1. Počkejte, než se zeleně odmítne.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Prozkoumejte data NYC taxislužby ve fondu SQL.

* V synapse studiu přejděte do centra **dat** .
* Přejděte do **tabulky SQLDB1 >**. Uvidíte, že se načetly několik tabulek.
* Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL > vybrat prvních 100 řádků**
* Vytvoří se nový skript SQL, který se automaticky spustí.
* Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem SQLDB1.
* Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

* Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
* V okně výsledků skriptu SQL změňte zobrazení na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf **.**

## <a name="create-a-spark-database-and-load-the-nyc-taxi-data-into-it"></a>Vytvořte databázi Spark a načtěte do ní data taxislužby NYC.

K dispozici jsou data v databázi fondu SQL. Nyní ji nahrajeme do databáze Spark.

* V synapse studiu přejděte do části * * vývoj centra.
* Výběr **+** a výběr **poznámkového bloku**
* V horní části poznámkového bloku nastavte hodnotu **připojit na** .`Spark1`
* Vyberte **přidat kód** pro přidání buňky kódu poznámkového bloku a vložte text níže:

    ```scala
    %% spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

 * Přejděte do centra dat, klikněte pravým tlačítkem na databáze a vyberte **aktualizovat** .
 * Nyní byste měli vidět tyto databáze:
     * SQLDB (fond SQL)
     * nyctaxi (Spark)
      
 ## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

 * Vrátit se do poznámkového bloku
 * Vytvořte novou buňku kódu, zadejte následující text a spusťte buňku.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

 * Spusťte tento kód, abyste mohli provádět stejnou analýzu jako dřív s fondem SQL.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
    """) 
    display(df)
    df.write.saveAsTable("nyctaxi.passengercountstats")
    ```

 * Ve výsledcích buňky vyberte možnost **graf** , aby se zobrazila data vizuálů.
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Přizpůsobení dat vizualizace dat pomocí Sparku a poznámkových bloků

Pomocí poznámkových bloků Spark můžete přesně řídit, jak grafy vykreslují. Následující kód ukazuje jednoduchý příklad s využitím oblíbených knihoven matplotlib a Sea-narozené. Vykreslí se stejný graf, který jste viděli při používání dotazů SQL dříve.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Načtení dat z tabulky Spark do tabulky fondu SQL

Dříve jsme zkopírovali data z databáze fondu SQL do služby Spark DB. Pomocí Sparku agregujeme data do nyctaxi. passengercountstats. Nyní spusťte níže uvedenou buňku v poznámkovém bloku a zkopíruje se agregovaná tabulka zpátky do databáze fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analýza dat NYC taxislužby v databázích Sparku pomocí SQL – na vyžádání 

* Tabulky v databázích Spark jsou automaticky viditelné a Queryable v SQL na vyžádání.
* V synapse studiu přejděte do centra pro vývoj a vytvořte nový skript SQL.
* Nastavení **připojení k** **SQL na vyžádání** 
* Do skriptu vložte následující text:

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

* Vyberte **Spustit** .
* Poznámka: při prvním spuštění bude trvat přibližně 10 sekund, než se SQL na vyžádání shromáždí prostředky SQL potřebné ke spuštění vašich dotazů. Následné dotazy tento čas nevyžadují.
  
## <a name="use-pipelines-to-orchestrate-activities"></a>Použití kanálů k orchestraci aktivit

V Azure synapse můžete orchestrovat širokou škálu úloh. V této části uvidíte, jak snadné je.

* V synapse studiu přejděte do centra Orchestration.
* Vyberte možnost **+** **kanál**. Vytvoří se nový kanál.
* Přejděte do centra pro vývoj a najděte všechny poznámkové bloky, které jste vytvořili dříve.
* Přetáhněte tento poznámkový blok do kanálu.
* V kanálu vyberte **Přidat trigger > nový/upravit**.
* V části **zvolit Trigger** vyberte **Nový**a potom v části opakování nastavte spuštění triggeru každé 1 hodinu.
* Vyberte **OK**.
* Vyberte **publikovat vše** a kanál se spustí každou hodinu.
* Pokud chcete, aby kanál běžel nyní bez čekání na další hodinu, vyberte **Přidat trigger > nový/upravit**.

## <a name="working-with-data-in-a-storage-account"></a>Práce s daty v účtu úložiště

Zatím jsme pokryli, že v databázích byly uloženy údaje o těchto scénářích. Nyní ukážeme, jak může Azure synapse analyzovat jednoduché soubory v účtu úložiště. V tomto scénáři použijeme účet úložiště a kontejner, ke kterému jsme pracovní prostor propojili.

Název účtu úložiště: contosolake název kontejneru v účtu úložiště: uživatelé

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Vytváření souborů CSV a Parquet v účtu úložiště

Spusťte následující kód v poznámkovém bloku. V účtu úložiště se vytvoří data CSV a Parquet.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

* V synapse studiu přejděte do centra **dat** .
* Vybrat **propojené**
* Přejděte na **účty úložiště > pracovního prostoru (primární – contosolake)** .
* Vybrat **uživatele (primární) "**
* Měla by se zobrazit složka s názvem "NYCTaxi". Uvnitř by se měly zobrazit dvě složky "PassengerCountStats. csv" a "PassengerCountStats. Parquet".
* Přejděte do složky PassengerCountStats. Parquet.
* Klikněte pravým tlačítkem na soubor Parquet a vyberte Nový Poznámkový blok. vytvoří se Poznámkový blok s buňkou, jako je tato:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

* Spusťte buňku pro analýzu souboru Parquet pomocí Sparku.
* Pravým tlačítkem myši klikněte na soubor Parquet v rámci a vyberte nový **skript SQL > vyberte horní 100 řádků**. vytvoří se Poznámkový blok s buňkou, jako je tato:

    ```py
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
* Skript se připojí k **SQL na vyžádání** a spustí skript. Všimněte si, že odvodí schéma ze souboru Parquet.

## <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI

Vaše data se teď dají snadno analyzovat a vizuálů Power BI. Synapse nabízí jedinečnou integraci, která umožňuje propojit pracovní prostor Power BI se synapse pracovním prostorem. Než začnete, postupujte podle kroků v tomto [rychlém](quickstart-power-bi.md) startu a propojte Power BI pracovní prostor.

### <a name="create-a-power-bi-workspace-and-link-it-to-your-synapse-workspace"></a>Vytvoření pracovního prostoru Power BI a jeho propojení s pracovním prostorem synapse

* Přihlaste se k [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
* Vytvořte nový pracovní prostor Power BI `NYCTaxiWorkspace1` .
* V synapse studiu přejděte do části **správa > propojené služby**.
* Vyberte **+ Nový** a vyberte **připojit k Power BI** a nastavte tato pole:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název**|`NYCTaxiWorkspace1`|
    |**Název pracovního prostoru**|`NYCTaxiWorkspace1`|
    |||
    
* Vyberte **Vytvořit**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Vytvoření datové sady Power BI, která používá data v pracovním prostoru synapse

* V synapse studiu přejděte na **Power BI vývoj >**.
* Přejděte na **NYCTaxiWorkspace1 > Power BI datových sad** a vyberte **Nový Power BI datová sada**.
* Najeďte myší na databázi SQLDB1 a vyberte **Stáhnout soubor. pbids**.
* Otevřete stažený `.pbids` soubor. Spustí se Power BI Desktop a automaticky se připojí k SQLDB1 v pracovním prostoru synapse.
* Pokud se zobrazí dialogové okno s názvem **databáze SQL serveru**:
    * Vyberte **účet Microsoft**. 
    * Vyberte **Přihlásit** se a přihlaste se.
    * Vyberte **Připojit**.
* Otevře se dialogové okno **navigátor** . Když zkontroluje tabulku **PassengerCountStats** a vyberte **načíst**.
* Zobrazí se dialogové okno **nastavení připojení** . Vyberte **DirectQuery** a vyberte **OK** .
* Na levé straně vyberte tlačítko **Sestava** .
* Přidejte **Spojnicový graf** do sestavy.
    * Přetáhněte sloupec **PasssengerCount** do **vizualizací > osu** .
    * Přetáhněte sloupce **SumTripDistance** a **AvgTripDistance** na **vizualizace > hodnoty**.
* Na kartě **Domů** vyberte **publikovat**.
* Zobrazí se dotaz, jestli chcete změny uložit. Vyberte **Uložit**.
* Zobrazí se výzva k výběru názvu souboru. Zvolte `PassengerAnalysis.pbix` a vyberte **Uložit**.
* Zobrazí se výzva k **výběru cílového umístění** `NYCTaxiWorkspace1` a vyberte vybrat. **Select**
* Počkejte na dokončení publikování.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurace ověřování pro datovou sadu

* Otevřete [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) a **Přihlaste se** .
* Vlevo v části **pracovní prostory** vyberte `NYCTaxiWorkspace1` pracovní prostor, do kterého jste publikovali.
* V tomto pracovním prostoru byste měli vidět datovou sadu s názvem `Passenger Analysis` a zprávu s názvem `Passenger Analysis` .
* Najeďte myší na `PassengerAnalysis` datovou sadu a vyberte ikonu se třemi tečkami a vyberte **Nastavení**.
* V části **přihlašovací údaje ke zdroji dat** nastavte metodu ověřování na **OAuth2** a vyberte **Přihlásit**se.

### <a name="edit-a-report-report-in-synapse-studio"></a>Úprava sestavy sestavy v synapse studiu

* Vraťte se zpět do synapse studia a vyberte **Zavřít a aktualizovat** nyní byste měli vidět:
    * V části **Power BI datové sady**se vytvoří nová datová sada s názvem **PassengerAnalysis**.
    * V části **Power BI datové sady**se vytvoří nová sestava s názvem **PassengerAnalysis**.
* Klikněte na sestavu **PassengerAnalysis** . 
    * Nezobrazuje vše, protože stále potřebujete nakonfigurovat ověřování pro datovou sadu.
* V SynapseStudio přejděte k **vývoji > Power BI > název pracovního prostoru > sestavy Power BI**.
* Zavřete všechna okna zobrazující sestavu Power BI.
* Aktualizujte uzel **Power BI sestavy** .
* Vyberte sestavu a teď můžete sestavu upravit přímo v rámci synapse studia.

## <a name="monitor-activities"></a>Monitorování aktivit

* V synapse studiu přejděte do centra monitorování.
* V tomto umístění uvidíte historii všech aktivit prováděných v pracovním prostoru a ty, které jsou teď aktivní.
* Prozkoumejte **spuštění kanálu**, **Apache Spark aplikace**a **požadavky SQL** a uvidíte, co jste už v pracovním prostoru provedli.

## <a name="next-steps"></a>Další kroky

Další informace o [Azure synapse Analytics (Preview)](overview-what-is.md)

