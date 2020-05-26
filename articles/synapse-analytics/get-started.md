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
ms.openlocfilehash: dcad90713227e55437523c91997175242078e9e4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836477"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Začínáme s využitím Azure synapse Analytics

Tento kurz vás provede všemi základními kroky potřebnými k instalaci a používání služby Azure synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Příprava účtu úložiště pro použití v pracovním prostoru synapse

1. Otevřete [Azure Portal](https://portal.azure.com)
1. Vytvořte nový účet úložiště s následujícími nastaveními:
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

1. Po vytvoření účtu úložiště v levém navigačním panelu vyberte **řízení přístupu (IAM)** . Pak přiřaďte následující role nebo ověřte, zda jsou již přiřazeny. 
    a. * Přiřaďte sami sobě roli **vlastníka** v účtu úložiště b. * Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** v účtu úložiště.
1. V levém navigačním panelu vyberte **kontejnery** a vytvořte kontejner. Můžete mu dát libovolný název. Přijměte výchozí **úroveň veřejného přístupu**. V tomto dokumentu budeme volat kontejner `users` . Vyberte **Vytvořit**. 

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru synapse

1. Otevřete [Azure Portal](https://portal.azure.com) a v horní části hledání `Synapse` .
1. Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)** .
1. Vybrat **+ Přidat**
1. Karta **základy** :

    |Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    |**Název pracovního prostoru**|Můžete ji volat cokoli.| V tomto dokumentu použijeme`myworkspace`
    |**Věřitel**|Porovnává s oblastí účtu úložiště.||
    |||

1. V části **vybrat data Lake Storage Gen 2**vyberte účet a kontejner, který jste vytvořili dříve.
    > [!NOTE]
    > V pracovním prostoru synapse odkazujeme na účet úložiště, který jste zvolili jako primární účet úložiště. Tento účet se používá k ukládání dat v tabulkách Apache Spark a pro protokoly vytvořené při vytváření fondů Spark nebo spuštění aplikací Spark.

1. Vyberte **Zkontrolovat a vytvořit**. Vyberte **Vytvořit**. Pracovní prostor bude připraven během několika minut.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Ověření, jestli má MSI synapse v pracovním prostoru přístup k účtu úložiště

To může být pro vás již provedeno. V každém případě byste měli ověřit.

1. Otevřete [Azure Portal](https://portal.azure.com) a otevřete primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém navigačním panelu vyberte **řízení přístupu (IAM)** . Pak přiřaďte následující role nebo ověřte, zda jsou již přiřazeny. 
    a. Přiřaďte identitu pracovního prostoru k roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště. Identita pracovního prostoru má stejný název jako pracovní prostor. V tomto dokumentu je název pracovního prostoru `myworkspace` tak, aby identita pracovního prostoru byla`myworkspaced`
1. Vyberte **Uložit**.
    
## <a name="launch-synapse-studio"></a>Spustit synapse Studio

Po vytvoření pracovního prostoru synapse máte dva způsoby, jak otevřít synapse Studio:
* Otevřete pracovní prostor synapse v [Azure Portal](https://portal.azure.com) a v horní části **přehledu** vyberte **Spustit synapse Studio** .
* Přímo přejít na https://web.azuresynapse.net pracovní prostor a přihlásit se k němu.

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

1. V synapse studiu na levé straně navigace vyberte **spravovat > fondů SQL** .

    > [!NOTE] 
    > Všechny pracovní prostory synapse se dodávají s předem vytvořeným fondem s názvem **SQL na vyžádání**.

1. Vyberte **+ Nová** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název fondu SQL**| `SQLDB1`|
    |**Úroveň výkonu**|`DW100C`|
    |||

1. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
1. Váš fond SQL bude připravený během několika minut.

    > [!NOTE]
    > Synapse fond SQL odpovídá, co se používá pro volání "Azure SQL Data Warehouse".

Fond SQL spotřebovává Fakturovatelné prostředky, pokud je spuštěný. V takovém případě můžete v případě potřeby pozastavit fond a snížit náklady.

Když se vytvoří fond SQL, bude se taky přidružit k databázi fondu SQL s názvem **SQLDB1**.

## <a name="create-an-apache-spark-pool"></a>Vytvoření fondu Apache Spark

1. V synapse studiu vyberte na levé straně možnost **spravovat fondy > Apache Spark** .
1. Vyberte **+ Nová** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název Apache Spark fondu**|`Spark1`
    |**Velikost uzlu**| `Small`|
    |**Počet uzlů**| Nastavte minimum na 3 a maximum na 3.|
    |||

1. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
1. Váš fond Apache Spark bude připravený během několika sekund.

> [!NOTE]
> Bez ohledu na název Apache Spark fond nevypadá jako fond SQL. Je to jenom pro základní metadata, která používáte k informování pracovního prostoru synapse o tom, jak pracovat s Sparkem. 

Vzhledem k tomu, že jsou metadata, nelze spustit ani zastavit fondy Spark. 

Když v synapse provedete jakoukoli aktivitu Spark, určíte fond Spark, který se má použít. Fond informuje synapse, kolik prostředků Spark se má použít. Platíte jenom za využití prostředků Thar. Při aktivním používání fondu se prostředky automaticky vyprší a budou recyklovány.

> [!NOTE]
> Databáze Spark jsou nezávisle vytvořené z fondů Spark. Pracovní prostor má vždycky databázi Spark s názvem **Default** a můžete vytvořit další databáze Spark.

## <a name="the-sql-on-demand-pool"></a>Fond na vyžádání SQL

Každý pracovní prostor obsahuje předem sestavený nebo neodstranitelné fondy s názvem **SQL na vyžádání**. Fond na vyžádání SQL umožňuje pracovat s SQL bez nutnosti vytvářet nebo považovat za správu synapse fondu SQL. Na rozdíl od jiných druhů fondů je fakturace za SQL na vyžádání založená na množství dat naskenovaných pro spuštění dotazu, a ne na počtu prostředků použitých ke spuštění dotazu.

* SQL na vyžádání má také vlastní databáze SQL na vyžádání, které existují nezávisle na jakémkoli fondu SQL na vyžádání.
* V současné době má pracovní prostor vždy právě jeden fond SQL na vyžádání s názvem **SQL na vyžádání**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Načtení ukázkových dat NYC taxislužby do databáze SQLDB1

1. V synapse studiu vyberte v nabídce nejvyšší úrovně modrou možnost **?** .
1. Vyberte **začínáme > centrum Začínáme**
1. Na kartě **ukázková data dotazu**s popiskem vyberte fond SQL s názvem.`SQLDB1`
1. Vyberte **data dotazu**. Zobrazí se oznámení, že se zobrazí zpráva "načítání ukázkových dat", která se zobrazí, a pak zmizí.
1. V horní části synapse studia se zobrazí indikátor s světle modrým oznámením, že se data načítají do SQLDB1. Počkejte, než se zeleně odmítne.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Prozkoumejte data NYC taxislužby ve fondu SQL.

1. V synapse studiu přejděte do centra **dat** .
1. Přejděte do **tabulky SQLDB1 >**. Uvidíte, že se načetly několik tabulek.
1. Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL > vybrat prvních 100 řádků**
1. Vytvoří se nový skript SQL, který se automaticky spustí.
1. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem SQLDB1.
1. Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Načtení ukázkových dat NYC taxislužby do databáze Spark nyctaxi

K dispozici jsou data v tabulce v `SQLDB1` . Nyní ji nahrajeme do databáze Spark s názvem "nyctaxi".

1. V synapse studiu přejděte do centra pro **vývoj** .
1. Výběr **+** a výběr **poznámkového bloku**
1. V horní části poznámkového bloku nastavte hodnotu **připojit na** .`Spark1`
1. Vyberte **přidat kód** pro přidání buňky kódu poznámkového bloku a vložte text níže:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Přejděte do centra **dat** , klikněte pravým tlačítkem na **databáze** a vyberte **aktualizovat**.
1. Nyní byste měli vidět tyto databáze:
    - SQLDB (fond SQL)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vrátit se do poznámkového bloku
1. Vytvořte novou buňku kódu, zadejte následující text a spusťte buňku, abyste mohli například NYC data taxislužby, která jsme načetli do sady `nyctaxi` Spark DB.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spusťte následující kód, který provede stejnou analýzu jako dříve s fondem SQL `SQLDB1` . Tento kód také uloží výsledky analýzy do tabulky s názvem `nyctaxi.passengercountstats` a vizualizuje výsledky.

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

1. Ve výsledcích buňky vyberte možnost **graf** , aby se zobrazila data vizuálů.
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Přizpůsobení dat vizualizace dat pomocí Sparku a poznámkových bloků

Pomocí poznámkových bloků můžete ovládat vykreslování grafů. Následující kód ukazuje jednoduchý příklad pomocí oblíbených knihoven `matplotlib` a `seaborn` . Vykreslí se stejný druh spojnicového grafu, který jste viděli při spuštění dotazů SQL dříve.

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

Dříve jsme zkopírovali data z tabulky fondů SQL `SQLDB1.dbo.Trip` do tabulky Spark `nyctaxi.trip` . Pomocí Sparku jsme data agreguje do tabulky Spark `nyctaxi.passengercountstats` . Nyní zkopírujeme data z `nyctaxi.passengercountstats` do tabulky fondu SQL s názvem `SQLDB1.dbo.PassengerCountStats` . 

Ve svém poznámkovém bloku spusťte níže uvedenou buňku. Nakopíruje agregovanou tabulku Spark zpátky do tabulky fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analýza dat NYC taxislužby v databázích Sparku pomocí SQL na vyžádání 

Tabulky v databázích Spark jsou automaticky viditelné a Queryable na vyžádání SQL.

1. V synapse studiu přejděte do centra pro **vývoj** a vytvořte nový skript SQL.
1. Nastavení **připojení k** **SQL na vyžádání** 
1. Vložte do skriptu následující text a spusťte skript.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Při prvním spuštění dotazu, který používá SQL na vyžádání, bude trvat přibližně 10 sekund, než se SQL na vyžádání shromáždí prostředky SQL potřebné ke spuštění vašich dotazů. Následné dotazy tento čas nevyžadují a budou mnohem rychlejší.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrace aktivit s kanály

V Azure synapse můžete orchestrovat širokou škálu úloh. V této části uvidíte, jak snadné je.

1. V synapse studiu přejděte do centra **Orchestration** .
1. Vyberte možnost **+** **kanál**. Vytvoří se nový kanál.
1. Přejděte do centra pro vývoj a najděte Poznámkový blok, který jste vytvořili dříve.
1. Přetáhněte tento poznámkový blok do kanálu.
1. V kanálu vyberte **Přidat trigger > nový/upravit**.
1. V části **zvolit Trigger** vyberte **Nový**a potom v části opakování nastavte spuštění triggeru každé 1 hodinu.
1. Vyberte **OK**.
1. Vyberte **publikovat vše** a kanál se spustí každou hodinu.
1. Pokud chcete, aby kanál běžel hned, aniž byste čekali na další hodinu, vyberte **Přidat trigger > nový/upravit**.

## <a name="working-with-data-in-a-storage-account"></a>Práce s daty v účtu úložiště

Zatím jsme pokryli, že v databázích v pracovním prostoru byly uloženy údaje o těchto scénářích. Nyní ukážeme, jak pracovat se soubory v účtech úložiště. V tomto scénáři použijeme primární účet úložiště v pracovním prostoru a kontejneru, který jsme určili při vytváření pracovního prostoru.

* Název účtu úložiště:`contosolake`
* Název kontejneru v účtu úložiště:`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Vytváření souborů CSV a Parquet v účtu úložiště

Spusťte následující kód v poznámkovém bloku. Vytvoří soubor CSV a soubor Parquet v účtu úložiště.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

1. V synapse studiu přejděte do centra **dat** .
1. Vybrat **propojené**
1. Přejděte na **účty úložiště > MyWorkspace (Primary-contosolake)** .
1. Vybrat **uživatele (primární) "**
1. Měla by se zobrazit složka s názvem "NYCTaxi". Uvnitř by se měly zobrazit dvě složky "PassengerCountStats. csv" a "PassengerCountStats. Parquet".
1. Přejděte do složky PassengerCountStats. Parquet.
1. Klikněte pravým tlačítkem na soubor Parquet a vyberte **Nový Poznámkový blok**. vytvoří se Poznámkový blok s buňkou, jako je tato:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Spusťte buňku.
1. Pravým tlačítkem myši klikněte na soubor Parquet v rámci a vyberte **Nový skript sql > vyberte horní 100 řádků**. vytvoří se skript SQL podobný tomuto:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. Ve skriptu se pole **připojit k** nastaví na **vyžádání SQL**.
1. Spusťte skript.

## <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI

Z dat NYX taxislužby jsme vytvořili agregované datové sady ve dvou tabulkách:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Pracovní prostor Power BI můžete propojit s pracovním prostorem synapse. Díky tomu můžete snadno získat data do svého pracovního prostoru Power BI a upravovat sestavy Power BI přímo v pracovním prostoru synapse.

### <a name="create-a-power-bi-workspace"></a>Vytvoření pracovního prostoru Power BI

1. Přihlaste se k [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Vytvořte nový pracovní prostor Power BI `NYCTaxiWorkspace1` .

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Propojit pracovní prostor synapse s novým pracovním prostorem Power BI

1. V synapse studiu přejděte do části **správa > propojené služby**.
1. Vyberte **+ Nový** a vyberte **připojit k Power BI** a nastavte tato pole:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název**|`NYCTaxiWorkspace1`|
    |**Název pracovního prostoru**|`NYCTaxiWorkspace1`|
    |||
    
1. Vyberte **Vytvořit**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Vytvoření datové sady Power BI, která používá data v pracovním prostoru synapse

1. V synapse studiu přejděte na **Power BI vývoj >**.
1. Přejděte na **NYCTaxiWorkspace1 > Power BI datových sad** a vyberte **Nový Power BI datová sada**.
1. Najeďte myší na `SQLDB1` databázi a vyberte **Stáhnout soubor. pbids**.
1. Otevřete stažený `.pbids` soubor. 
1. Spustí se Power BI Desktop a automaticky se připojí k `SQLDB1` vašemu pracovnímu prostoru synapse.
1. Pokud se zobrazí dialogové okno s názvem **databáze SQL serveru**: a. Vyberte **účet Microsoft**. 
    b. Vyberte **Přihlásit** se a přihlaste se.
    c. Vyberte **Připojit**.
1. Otevře se dialogové okno **navigátor** . V takovém případě Ověřte tabulku **PassengerCountStats** a vyberte **načíst**.
1. Zobrazí se dialogové okno **nastavení připojení** . Vyberte **DirectQuery** a vyberte **OK** .
1. Na levé straně vyberte tlačítko **Sestava** .
1. Přidejte **Spojnicový graf** do sestavy.
    a. Přetáhněte sloupec **PasssengerCount** na **vizualizace > osy** b. Přetáhněte sloupce **SumTripDistance** a **AvgTripDistance** na **vizualizace > hodnoty**.
1. Na kartě **Domů** vyberte **publikovat**.
1. Zobrazí se dotaz, jestli chcete změny uložit. Vyberte **Uložit**.
1. Zobrazí se výzva k výběru názvu souboru. Zvolte `PassengerAnalysis.pbix` a vyberte **Uložit**.
1. Zobrazí se výzva k **výběru cílového umístění** `NYCTaxiWorkspace1` a vyberte vybrat. **Select**
1. Počkejte na dokončení publikování.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurace ověřování pro datovou sadu

1. Otevřete [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) a **Přihlaste se** .
1. Vlevo v části **pracovní prostory** vyberte `NYCTaxiWorkspace1` pracovní prostor.
1. V tomto pracovním prostoru byste měli vidět datovou sadu s názvem `Passenger Analysis` a zprávu s názvem `Passenger Analysis` .
1. Najeďte myší na `PassengerAnalysis` datovou sadu a vyberte ikonu se třemi tečkami a vyberte **Nastavení**.
1. V části **přihlašovací údaje ke zdroji dat**nastavte **metodu ověřování** na **OAuth2** a vyberte **Přihlásit**se.

### <a name="edit-a-report-in-synapse-studio"></a>Úprava sestavy v synapse studiu

1. Vraťte se na synapse Studio a vyberte **Zavřít a aktualizovat** . 
1. Přejít do centra pro **vývoj** 
1. Najeďte myší na **Power BI** a klikněte na uzel aktualizovat **Power BI sestavy** .
1. Teď pod **Power BI** byste měli vidět: a. * V části **NYCTaxiWorkspace1 > Power BI datové sady**se vytvoří nová datová sada s názvem **PassengerAnalysis**.
    b. * V části **NYCTaxiWorkspace1 > Power BI sestav**se zobrazí nová sestava s názvem **PassengerAnalysis**.
1. Vyberte sestavu **PassengerAnalysis** . 
1. Sestava se otevře a teď můžete sestavu upravit přímo v synapse studiu.

## <a name="monitor-activities"></a>Monitorování aktivit

1. V synapse studiu přejděte do centra monitorování.
1. V tomto umístění uvidíte historii všech aktivit prováděných v pracovním prostoru a ty, které jsou teď aktivní.
1. Prozkoumejte **spuštění kanálu**, **Apache Spark aplikace**a **požadavky SQL** a uvidíte, co jste už v pracovním prostoru provedli.

## <a name="next-steps"></a>Další kroky

Další informace o [Azure synapse Analytics (Preview)](overview-what-is.md)

