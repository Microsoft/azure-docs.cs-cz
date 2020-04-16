---
title: 'Úvodní příručka: Vytvoření fondu Apache Spark (preview) v Azure Synapse Analytics'
description: Tento rychlý start ukazuje, jak pomocí webových nástrojů vytvořit fond Apache Spark (preview) v Azure Synapse Analytics a spustit dotaz Spark SQL.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424297"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Úvodní příručka: Vytvoření fondu Apache Spark (preview) v Synapse Analytics pomocí webových nástrojů

V tomto rychlém startu se dozvíte, jak vytvořit fond Apache Spark (preview) v Azure Synapse Analytics pomocí webových nástrojů. Pak se naučíte připojit k fondu Apache Spark a spustit sískřivové SQL dotazy proti souborům a tabulkám. Apache Spark umožňuje rychlou analýzu dat a clusterové výpočty s využitím zpracování v paměti. Informace o Sparkna na Synapse Analytics najdete v [tématu Přehled: Apache Spark na Azure Synapse Analytics](apache-spark-overview.md).

> [!IMPORTANT]
> Fakturace pro instance Spark se poměrně účtuje za minutu, ať už je používáte nebo ne. Po dokončení používání instance Spark nezapomeňte vypnout instanci Spark nebo nastavit krátký časový čas. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-an-apache-spark-pool"></a>Vytvoření fondu Apache Spark

V tomto článku se můžete pomocí webových nástrojů vytvořit nový fond Apache Spark.

## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

Poznámkový blok je interaktivní prostředí, které podporuje různé programovací jazyky. Notebook umožňuje interakci s daty, kombinovat kód s markdownem, textem a provádět jednoduché vizualizace.

1. V zobrazení portálu Azure pro pracovní prostor Synapse Analytics, který chcete použít, vyberte **Spustit Synapse Studio**.
2. Po spuštění studia Synapse Analytics Vyberte **možnost Rozvíjet**. Potom najeďte na položku **Poznámkové bloky.** Vyberte tři tečky (**...**).
3. Odtud vyberte **Nový poznámkový blok**. Vytvoří se a otevře se nový poznámkový blok s automaticky generovaným názvem.
  ![Nový poznámkový blok](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Nový poznámkový blok")

4. V okně **Vlastnosti** zadejte název poznámkového bloku.
5. Na panelu nástrojů klepněte na **tlačítko Publikovat**.
6. Pokud je ve vašem pracovním prostoru pouze jeden fond Apache Spark, je ve výchozím nastavení vybrán. Pomocí rozevíracího souboru vyberte správný fond Apache Spark, pokud není vybrán žádný.
7. Klepněte na **tlačítko Přidat kód**. Výchozí jazyk `Pyspark`je . Budete používat kombinaci Pyspark a Spark SQL, takže výchozí volba je v pořádku.
8. Dále vytvoříte jednoduchý objekt Spark DataFrame, se který bude pracovat. V tomto případě jej vytvoříte z kódu. K dispozici jsou tři řádky a tři sloupce:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Nyní spusťte buňku pomocí jedné z následujících metod:

   - Stiskněte **klávesu SHIFT + ENTER**.
   - Vyberte modrou ikonu přehrávání vlevo od buňky.
   - Na panelu nástrojů vyberte tlačítko **Spustit vše.**

   ![Vytvoření objektu datového rámce](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Výstup z úlohy Spark")

10. Pokud instance fondu Apache Spark ještě není spuštěná, je automaticky spuštěna. Stav instance fondu Apache Spark můžete zobrazit pod buňkou, kterou používáte, a také na stavovém panelu v dolní části poznámkového bloku. V závislosti na velikosti bazénu by mělo začít trvat 2-5 minut. Po dokončení spuštění kódu se zobrazí informace pod buňkou, které ukazují, jak dlouho trvalo spuštění a jeho spuštění. Ve výstupní buňce se zobrazí výstup.

    ![Výstup z provádění buňky](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Výstup z úlohy Spark")

11. Data nyní existuje v Datovém rámečku odtud můžete použít data mnoha různými způsoby. Budete potřebovat v různých formátech pro zbytek tohoto rychlého startu.
12. Zadejte níže uvedený kód do jiné buňky a spusťte jej, vytvoří se tabulka Spark, CSV a parketový soubor s kopiemi dat:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Pokud používáte průzkumník úložiště, je možné zobrazit dopad dvou různých způsobů psaní souboru použitého výše. Pokud není zadán žádný systém souborů, použije `default>user>trusted-service-user>demo_df`se výchozí nastavení, v tomto případě . Data jsou uložena do umístění zadaného systému souborů.

    Všimněte si, že ve formátu "csv" a "parkety", psát operace adresář je vytvořen s mnoha rozdělených souborů.

    ![Zobrazení průzkumníka úložiště výstupu](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "Zobrazení průzkumníka úložiště výstupu")

    ![Zobrazení průzkumníka úložiště výstupu](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "Zobrazení průzkumníka úložiště výstupu")

## <a name="run-spark-sql-statements"></a>Spouštění příkazů Spark SQL

Jazyk SQL (Structured Query Language) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Vložte následující kód do prázdné buňky a spusťte jej. Příkaz uvádí tabulky ve fondu.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Když používáte notebook s fondem Synapse Analytics Apache `sqlContext` Spark, získáte přednastavení, které můžete použít ke spouštění dotazů pomocí Spark SQL. `%%sql`informuje poznámkový blok, `sqlContext` aby k spuštění dotazu použil přednastavení. Dotaz načte prvních 10 řádků ze systémové tabulky, která je ve výchozím nastavení dodávána se všemi fondy Synapse Analytics Apache Spark.

2. Spuštěním dalšího dotazu zobrazíte data v tabulce `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Kód vytvoří dvě výstupní buňky, jednu, která obsahuje výsledky dat, druhou, která zobrazuje zobrazení úlohy.

    Ve výchozím nastavení zobrazení výsledků zobrazuje mřížku, ale pod mřížkou je přepínač zobrazení, který umožňuje přepínat mezi zobrazením mřížky a grafu.

    ![Výstup dotazu v synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Výstup dotazu v synapse Analytics Spark")

3. V přepínači **Zobrazení** vyberte **Graf**
4. Z pravé strany vyberte ikonu **Zobrazit volby.**
5. V poli **Typ grafu** vyberte pruhový graf.
6. V poli sloupce osy X vyberte "stav".
7. V poli sloupce osy Y vyberte "plat".
8. V poli **Agregace** vyberte možnost "AVG".
9. Vyberte **Použít**.

   ![Výstup grafu v Synapse Analytics Spark](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Výstup grafu v Synapse Analytics Spark")

10. Je možné získat stejné zkušenosti se systémem SQL, ale bez nutnosti přepínat jazyky. Můžete to provést nahrazením výše uvedené buňky SQL touto buňkou PySpark, výstupní prostředí je stejné, protože se používá příkaz **zobrazení:**

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Každá z dříve provedených buněk měla možnost přejít na **history server** a **monitorování**. Kliknutím na odkazy přejdete do různých částí uživatelského prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Synapse Analytics ukládá vaše data do Azure Data Lake Storage. Můžete bezpečně nechat Spark instance vypnout, když není v provozu. Za fond Synapse Analytics Apache Spark se vám účtuje, pokud je spuštěný, i když se nepoužívá. Vzhledem k tomu, že poplatky za fond jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl nechat instance Spark vypnout, když nejsou používány.

Chcete-li zajistit vypnutí instance Spark, ukončte všechny připojené relace (poznámkové bloky). Fond se vypne, když je dosaženo **doby nečinnosti** zadané ve fondu Apache Spark. Můžete také vybrat **konec relace** ze stavového řádku v dolní části poznámkového bloku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit fond Synapse Analytics Apache Spark a spustit základní dotaz Spark SQL.

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Oficiální dokumentace Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Některé oficiální dokumenty Apache Spark závisí na použití konzole Spark, která není k dispozici na Azure Synapse Spark. Místo toho použijte [poznámkový blok](../spark/apache-spark-notebook-create-spark-use-sql.md) nebo prostředí [IntelliJ.](../spark/intellij-tool-synapse.md)
