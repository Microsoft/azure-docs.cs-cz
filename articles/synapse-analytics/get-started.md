---
title: 'Kurz: Začínáme s Azure synapse Analytics'
description: V tomto kurzu se naučíte základní kroky pro nastavení a používání Azure synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791856"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Začínáme s Azure synapse Analytics

Tento kurz vás provede základními kroky pro nastavení a použití analýzy Azure synapse.

## <a name="prepare-a-storage-account"></a>Příprava účtu úložiště

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Vytvořte nový účet úložiště s následujícími nastaveními:

    |Karta|Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|---|
    |Základy|**Název účtu úložiště**| Můžete mu dát libovolný název.|V tomto dokumentu na něj odkazujeme jako na **contosolake**.|
    |Základy|**Druh účtu**|Musí být nastavené na **StorageV2**.||
    |Základy|**Umístění**|Vyberte libovolné umístění.| Doporučujeme, aby byl váš pracovní prostor Azure synapse Analytics a účet Azure Data Lake Storage Gen2 ve stejné oblasti.|
    |Upřesnit|**Data Lake Storage Gen2**|**Enabled** (Povoleno)| Azure synapse funguje jenom s účty úložiště, kde je toto nastavení povolené.|
    |||||

1. Po vytvoření účtu úložiště v levém podokně vyberte **řízení přístupu (IAM)** . Pak přiřaďte následující role nebo ověřte, zda jsou již přiřazeny:
    1. Přiřaďte roli **vlastníka** sami sobě.
    1. Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** .
1. V levém podokně vyberte **kontejnery** a vytvořte kontejner.
1. Kontejneru můžete dát libovolný název. V tomto dokumentu budeme pojmenovat **uživatele**kontejneru.
1. Přijměte výchozí nastavení **úroveň veřejného přístupu**a pak vyberte **vytvořit**.

V následujícím kroku nakonfigurujete pracovní prostor Azure synapse tak, aby používal tento účet úložiště jako primární účet úložiště a kontejner pro ukládání dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru synapse

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor pomocí těchto nastavení:

    |Karta|Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|---|
    |Základy|**Název pracovního prostoru**|Můžete ji volat cokoli.| V tomto dokumentu budeme používat **MyWorkspace**.|
    |Základy|**Oblast**|Porovnává s oblastí účtu úložiště.|

1. V části **vybrat data Lake Storage Gen 2**vyberte účet a kontejner, který jste vytvořili dříve.
1. Vyberte **zkontrolovat + vytvořit**  >  **vytvořit**. Váš pracovní prostor je připravený během několika minut.

## <a name="verify-access-to-the-storage-account"></a>Ověřte přístup k účtu úložiště.

Spravované identity pro váš pracovní prostor Azure synapse už můžou mít přístup k účtu úložiště. Pomocí těchto kroků se ujistěte, že:

1. Otevřete [Azure Portal](https://portal.azure.com) a primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém podokně vyberte **řízení přístupu (IAM)** .
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny. Pro identitu pracovního prostoru používáme stejný název a název pracovního prostoru.
    1. Pro roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště přiřaďte **MyWorkspace** jako identitu pracovního prostoru.
    1. Přiřaďte **MyWorkspace** jako název pracovního prostoru.

1. Vyberte **Uložit**.

## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://portal.azure.com). V horní části **přehledu** vyberte **Spustit synapse Studio**.
* Přejdete do https://web.azuresynapse.net svého pracovního prostoru a přihlásíte se k němu.

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název fondu SQL**| **SQLDB1**|
    |**Úroveň výkonu**|**DW100C**|
    |||

1. Vyberte **zkontrolovat + vytvořit**  >  **vytvořit**. Váš fond SQL bude připravený během několika minut. Váš fond SQL je přidružen k databázi fondu SQL, která se také označuje jako **SQLDB1**.

Fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

## <a name="create-an-apache-spark-pool"></a>Vytvoření fondu Apache Spark

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název Apache Spark fondu**|**Spark1**
    |**Velikost uzlu**| **Malá**|
    |**Počet uzlů**| Nastavte minimum na 3 a maximum na 3.|

1. Vyberte **zkontrolovat + vytvořit**  >  **vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

> [!NOTE]
> Bez ohledu na název Apache Spark fond nevypadá jako fond SQL. Jedná se o jenom některá základní metadata, která používáte k tomu, abyste synapse pracovnímu prostoru Azure, jak pracovat s Sparkem.

Vzhledem k tomu, že se jedná o metadata, nelze spustit ani zastavit fondy Spark.

Při provádění aktivity Sparku v Azure synapse určíte fond Spark, který se má použít. Tento fond oznamuje službě Azure synapse, kolik prostředků Spark se má použít. Platíte jenom za prostředky, které používáte. Při aktivním zastavení fondu se prostředky automaticky vyprší a recykluje se.

> [!NOTE]
> Databáze Spark jsou nezávisle vytvořené z fondů Spark. Pracovní prostor má vždy databázi Spark s názvem **Default**. Můžete vytvořit další databáze Spark.

## <a name="the-sql-on-demand-pool"></a>Fond na vyžádání SQL

Každý pracovní prostor obsahuje předem sestavený fond s názvem **SQL na vyžádání**. Tento fond nejde odstranit. Fond na vyžádání SQL umožňuje pracovat s SQL bez nutnosti vytvářet nebo považovat za správu fondu SQL ve službě Azure synapse.

Na rozdíl od jiných druhů fondů je fakturace za SQL na vyžádání založená na množství dat naskenovaných pro spuštění dotazu, nikoli na počtu prostředků použitých ke spuštění dotazu.

* SQL na vyžádání má vlastní databáze SQL na vyžádání, které existují nezávisle na jakémkoli fondu SQL na vyžádání.
* Pracovní prostor má vždy přesně jeden fond SQL na vyžádání s názvem **SQL na vyžádání**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Načtení ukázkových dat NYC taxislužby do databáze SQLDB1

1. V synapse studiu v nabídce nejvyšší úrovně modrá vyberte **?** .
1. Vyberte **Začínáme**s  >  **úvodním startem**.
1. Na kartě s popisem **dotazu ukázková data**vyberte fond SQL s názvem **SQLDB1**.
1. Vyberte **data dotazu**. Krátce se zobrazí oznámení "načtení ukázkových dat". Světle modrý stavový řádek v horní části synapse studia indikuje, že se data načítají do SQLDB1.
1. Jakmile se stavový řádek změní na zelený, zavřete ho.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Prozkoumejte data NYC taxislužby ve fondu SQL.

1. V synapse studiu přejdete do centra **dat** .
1. Přejít na **SQLDB1**  >  **tabulky**SQLDB1. Zobrazí se několik načtených tabulek.
1. Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL**  >  **Vyberte horní 100 řádků**.
1. Počkejte, než se vytvoří a spustí nový skript SQL.
1. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem **SQLDB1**.
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

    Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Načtení dat taxislužby NYC do databáze Spark nyctaxi

K dispozici jsou data v tabulce v **SQLDB1**. Načtěte ho do databáze Spark s názvem **nyctaxi**.

1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vyberte **+**  >  **Poznámkový blok**.
1. V horní části poznámkového bloku nastavte hodnotu **připojit k** **Spark1**.
1. Vyberte **přidat kód** pro přidání buňky kódu poznámkového bloku a vložte následující text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Přejděte do centra **dat** , klikněte pravým tlačítkem na **databáze**a pak vyberte **aktualizovat**. Tyto databáze by se měly zobrazit:

    - **SQLDB1** (fond SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vraťte se do poznámkového bloku.
1. Vytvořte novou buňku kódu a zadejte následující text. Pak spuštěním buňky zobrazte data NYC taxislužby, která jsme načetli do databáze **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spusťte následující kód, který provede stejnou analýzu, jakou jsme provedli dříve s fondem SQL **SQLDB1**. Tento kód uloží výsledky analýzy do tabulky s názvem **nyctaxi. passengercountstats** a vizualizuje výsledky.

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

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Přizpůsobení vizualizace dat pomocí Sparku a poznámkových bloků

Můžete řídit, jak se grafy vykreslují pomocí poznámkových bloků. Následující kód ukazuje jednoduchý příklad. Používá oblíbené knihovny **matplotlib** a **Seaborn**. Kód vykresluje stejný druh spojnicového grafu jako příkazy jazyka SQL, které byly dříve spuštěny.

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

Dříve jsme zkopírovali data z tabulky fondu SQL **SQLDB1. dbo. Trip** do tabulky Spark **nyctaxi. Trip**. Pomocí Sparku jsme data agreguje do tabulky Spark **nyctaxi. passengercountstats**. Nyní zkopírujeme data z **nyctaxi. passengercountstats** do tabulky fondu SQL s názvem **SQLDB1. dbo. passengercountstats**.

Spusťte následující buňku v poznámkovém bloku. Nakopíruje agregovanou tabulku Spark zpátky do tabulky fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analýza dat NYC taxislužby v databázích Sparku pomocí SQL na vyžádání

Tabulky v databázích Spark jsou automaticky viditelné a mohou být dotazovány na vyžádání SQL.

1. V synapse studiu přejdete do centra pro **vývoj** a vytvoříte nový skript SQL.
1. Nastavte **připojení k** **SQL na vyžádání**.
1. Vložte do skriptu následující text a spusťte skript.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Při prvním spuštění dotazu, který používá SQL na vyžádání, trvá SQL na vyžádání přibližně 10 sekund pro shromáždění prostředků SQL potřebných ke spuštění dotazů. Následné dotazy budou mnohem rychlejší.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrace aktivit s kanály

V Azure synapse můžete orchestrovat širokou škálu úloh.

1. V synapse studiu přejdete do centra **Orchestration** .
1. Vyberte **+**  >  **kanál** a vytvořte nový kanál.
1. Přejít do centra pro **vývoj** a vyhledat Poznámkový blok, který jste vytvořili dříve.
1. Přetáhněte tento poznámkový blok do kanálu.
1. V kanálu vyberte **Přidat Trigger**  >  **Nový/upravit**.
1. V části **zvolit aktivační událost**vyberte **Nový**a potom v části **opakování** nastavte spuštění triggeru každé 1 hodinu.
1. Vyberte **OK**.
1. Vyberte **Publikovat vše**. Kanál se spouští každou hodinu.
1. Pokud chcete kanál spustit nyní, aniž byste čekali na další hodinu, vyberte **Přidat Trigger**  >  **Nový/upravit**.

## <a name="work-with-data-in-a-storage-account"></a>Práce s daty v účtu úložiště

Zatím jsme pokryli scénáře, kdy se data nacházejí v databázích v pracovním prostoru. Teď vám ukážeme, jak pracovat se soubory v účtech úložiště. V tomto scénáři použijeme primární účet úložiště pracovního prostoru a kontejneru, který jsme určili při vytváření pracovního prostoru.

* Název účtu úložiště: **contosolake**
* Název kontejneru v účtu úložiště: **Uživatelé**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Vytváření souborů CSV a Parquet v účtu úložiště

Spusťte následující kód v poznámkovém bloku. Vytvoří soubor CSV a soubor Parquet v účtu úložiště.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

1. V synapse studiu otevřete centrum **dat** a pak vyberte **propojit**.
1. Přejít na **účty úložiště**  >  **MyWorkspace (Primary-contosolake)**.
1. Vybrat **uživatele (primární) "**. Měla by se zobrazit složka **NYCTaxi** . Uvnitř byste měli vidět dvě složky s názvem **PassengerCountStats.csv** a **PassengerCountStats. Parquet**.
1. Otevřete složku **PassengerCountStats. Parquet** . V rámci uvidíte soubor Parquet s názvem, jako je *součást-00000-2638e00c-0790-496b-a523-578da9a15019-C000. přichycení. Parquet*.
1. Klikněte pravým tlačítkem na **. Parquet**a pak vyberte **Nový Poznámkový blok**. Vytvoří Poznámkový blok, který má podobné buňky:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Spusťte buňku.
1. Klikněte pravým tlačítkem na soubor Parquet uvnitř a pak vyberte **Nový skript SQL**  >  **Vybrat horní 100 řádků**. Vytvoří skript SQL podobný tomuto:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     Ve skriptu je pole **připojit k** **na vyžádání**nastaveno na hodnotu SQL.

1. Spusťte skript.

## <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI

Z dat NYC taxislužby jsme vytvořili agregované datové sady ve dvou tabulkách:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Pracovní prostor Power BI můžete propojit s pracovním prostorem Azure synapse. Díky tomu můžete snadno získat data do svého pracovního prostoru Power BI. Sestavy Power BI můžete upravovat přímo v pracovním prostoru Azure synapse.

### <a name="create-a-power-bi-workspace"></a>Vytvoření pracovního prostoru Power BI

1. Přihlaste se k [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Vytvořte nový pracovní prostor Power BI nazvaný **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Propojení pracovního prostoru Azure synapse s novým pracovním prostorem Power BI

1. V synapse studiu si přečtěte, jak **Spravovat**  >  **propojené služby**.
1. Vyberte **Nový**  >  **připojit k Power BI**a pak nastavte tato pole:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|
    |**Název**|**NYCTaxiWorkspace1**|
    |**Název pracovního prostoru**|**NYCTaxiWorkspace1**|

1. Vyberte **Vytvořit**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Vytvoření datové sady Power BI, která používá data v pracovním prostoru Azure synapse

1. V synapse studiu si Projděte **vývoj**  >  **Power BI**.
1. Přejít na **NYCTaxiWorkspace1**  >  **Power BI datových sad** a vyberte **Nový Power BI datová sada**.
1. Najeďte myší na databázi **SQLDB1** a vyberte **Stáhnout soubor. pbids**.
1. Otevřete stažený soubor **. pbids** . Power BI Desktop se otevře a automaticky se připojí k **SQLDB1** v pracovním prostoru Azure synapse.
1. Pokud se zobrazí dialogové okno s názvem **databáze SQL serveru**:
    1. Vyberte **účet Microsoft**.
    1. Vyberte **Přihlásit** se a přihlaste se ke svému účtu.
    1. Vyberte **Připojit**.
1. Po otevření dialogového okna **navigátor** ověřte tabulku **PassengerCountStats** a vyberte **načíst**.
1. Po zobrazení dialogového okna **nastavení připojení** vyberte **DirectQuery**  >  **OK**.
1. Na levé straně vyberte tlačítko **Sestava** .
1. Přidejte **Spojnicový graf** do sestavy.
    1. Přetáhněte sloupec **PassengerCount** na osu **vizualizace**  >  **Axis**.
    1. Přetáhněte sloupce **SumTripDistance** a **AvgTripDistance** na hodnoty **vizualizace**  >  **Values**.
1. Na kartě **Domů** vyberte **publikovat**.
1. Vyberte **Uložit** a uložte tak provedené změny.
1. Zvolte název souboru **PassengerAnalysis. pbix**a pak vyberte Save ( **Uložit**).
1. V **možnosti vybrat cíl**zvolte možnost **NYCTaxiWorkspace1**a pak klikněte na tlačítko **Vybrat**.
1. Počkejte na dokončení publikování.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurace ověřování pro datovou sadu

1. Otevřete [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) a **Přihlaste se**.
1. Na levé straně v části **pracovní prostory**vyberte pracovní prostor **NYCTaxiWorkspace1** .
1. V tomto pracovním prostoru Najděte datovou sadu nazvanou **Analýza osobního** prostředí a sestavu s názvem **Analýza osobních**dat.
1. Najeďte myší na **PassengerAnalysis** datovou sadu, vyberte tlačítko se třemi tečkami (...) a pak vyberte **Nastavení**.
1. V části **přihlašovací údaje ke zdroji dat**nastavte **metodu ověřování** na **OAuth2**a pak vyberte **Přihlásit**se.

### <a name="edit-a-report-in-synapse-studio"></a>Úprava sestavy v synapse studiu

1. Vraťte se na synapse Studio a vyberte **Zavřít a aktualizovat**.
1. Přejít do centra pro **vývoj** .
1. Najeďte myší na **Power BI** a vyberte uzel aktualizovat **Power BI sestavy** .
1. V části **Power BI** byste měli vidět:
    1. V části **NYCTaxiWorkspace1**  >  **Power BI datové sady**se vytvoří nová datová sada s názvem **PassengerAnalysis**.
    1. V části **NYCTaxiWorkspace1**  >  **Power BI sestavy**se zobrazí nová sestava s názvem **PassengerAnalysis**.
1. Vyberte sestavu **PassengerAnalysis** . Sestava se otevře a můžete ji přímo v synapse studiu upravovat.

## <a name="monitor-activities"></a>Monitorování aktivit

1. V synapse studiu přejdete do centra **monitorování** .
1. V tomto umístění uvidíte historii všech aktivit prováděných v pracovním prostoru a ty, které jsou teď aktivní.
1. Prozkoumejte **spuštění kanálu**, **Apache Spark aplikace**a **požadavky SQL** , abyste viděli, co jste už v pracovním prostoru provedli.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure synapse Analytics (verze Preview pracovních prostorů)](overview-what-is.md).

