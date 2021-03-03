---
title: 'Rychlý Start: Vytvoření fondu Apache Spark bez serveru pomocí webových nástrojů'
description: V tomto rychlém startu se dozvíte, jak pomocí webových nástrojů vytvořit fond Apache Spark bez serveru ve službě Azure synapse Analytics a jak spustit dotaz Spark SQL.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 060c78621b82f4698d4596383cd155d85d483d8e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669493"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Rychlý Start: Vytvoření fondu Apache Spark bez serveru ve službě Azure synapse Analytics pomocí nástrojů pro web

V tomto rychlém startu se dozvíte, jak pomocí webových nástrojů vytvořit fond Apache Spark bez serveru ve službě Azure synapse. Pak se naučíte připojit se ke fondu Apache Spark a spouštět dotazy Spark SQL proti souborům a tabulkám. Apache Spark umožňuje rychlou analýzu dat a clusterové výpočty s využitím zpracování v paměti. Informace o Sparku ve službě Azure synapse najdete v tématu [Přehled: Apache Spark v Azure synapse](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> Faktura za instance Spark se účtuje poměrnou rychlostí za minutu, ať už je používáte, nebo ne. Ujistěte se, že jste instanci Spark po dokončení používání vypnuli, nebo nastavte krátký časový limit. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor analýzy synapse](quickstart-create-workspace.md)
- [Fond Apache Spark bez serveru](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

Poznámkový blok je interaktivní prostředí, které podporuje různé programovací jazyky. Poznámkový blok vám umožní pracovat s daty, kombinovat kód s Markdownu, textem a provádět jednoduché vizualizace.

1. V zobrazení Azure Portal pracovního prostoru Azure synapse, který chcete použít, vyberte **Spustit synapse Studio**.
2. Po spuštění synapse studia vyberte **vývoj**. Pak vyberte **+** ikonu pro přidání nového prostředku.
3. Odtud vyberte **Poznámkový blok**. Vytvoří se nový Poznámkový blok, který se otevře s automaticky generovaným názvem.
 
     ![Nový Poznámkový blok](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Nový Poznámkový blok")

4. V okně **vlastnosti** zadejte název poznámkového bloku.
5. Na panelu nástrojů klikněte na **publikovat**.
6. Pokud je ve vašem pracovním prostoru jenom jeden Apache Spark fond, pak je vybraný ve výchozím nastavení. Pomocí rozevíracího seznamu vyberte správný fond Apache Spark, pokud není vybraný žádný.
7. Klikněte na **přidat kód**. Výchozí jazyk je `Pyspark` . Budete používat kombinaci Pyspark a Spark SQL, takže je výchozí volba velmi jemná. Další podporované jazyky jsou Scala a .NET pro Spark.
8. Dále vytvoříte jednoduchý objekt Spark dataframe pro manipulaci. V tomto případě jej vytvoříte z kódu. Existují tři řádky a tři sloupce:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Nyní spusťte buňku pomocí jedné z následujících metod:

   - Stiskněte **SHIFT + ENTER**.
   - Vyberte modrou ikonu přehrávání vlevo od buňky.
   - Na panelu nástrojů vyberte tlačítko **Spustit vše** .

       ![Vytvořit objekt datového rámce](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Pokud instance fondu Apache Spark ještě není spuštěná, spustí se automaticky. Stav instance Apache Spark fondu můžete zobrazit pod buňkou, kterou používáte, a také na panelu stavu v dolní části poznámkového bloku. V závislosti na velikosti fondu by se měla začít trvat 2-5 minut. Po skončení běhu kódu se zobrazí informace pod buňkou, která ukazuje, jak dlouho trvalo spuštění a jeho spuštění. V buňce Output (výstup) uvidíte výstup.

    ![Výstup z provádění buňky](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. Data nyní existují v datovém snímku. data můžete použít mnoha různými způsoby. Budete ho potřebovat v různých formátech pro zbytek tohoto rychlého startu.
12. Níže zadejte kód v jiné buňce a spusťte jej. tím se vytvoří tabulka Spark, sdílený svazek clusteru a soubor Parquet s kopiemi dat:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Pokud používáte Průzkumníka služby Storage, je možné zobrazit dopad dvou různých způsobů psaní souboru použitého výše. Pokud není zadaný žádný systém souborů, použije se v tomto případě výchozí hodnota `default>user>trusted-service-user>demo_df` . Data jsou uložena do umístění zadaného systému souborů.

    Všimněte si, že ve formátech "CSV" i "Parquet" se operace zápisu vytvoří adresář s mnoha soubory dělenými oddíly.

    ![Zobrazení výstupu v Průzkumníkovi služby Storage](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "Zobrazení výstupu v Průzkumníkovi služby Storage")

    ![Snímek obrazovky, který zvýrazní výchozí > demodata > demo_df cestu.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Zobrazení výstupu v Průzkumníkovi služby Storage")

## <a name="run-spark-sql-statements"></a>Spouštění příkazů Spark SQL

Jazyk SQL (Structured Query Language) (SQL) je nejběžnějším a široce používaným jazykem pro dotazování a definování dat. Spark SQL funguje jako rozšíření Apache Spark pro zpracování strukturovaných dat a používá známou syntaxi jazyka SQL.

1. Vložte následující kód do prázdné buňky a spusťte kód. Příkaz vypíše tabulky ve fondu.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Pokud používáte Poznámkový blok s Apache Spark fondem Azure synapse, získáte předvolbu `sqlContext` , kterou můžete použít ke spouštění dotazů pomocí Spark SQL. `%%sql` instruuje Poznámkový blok, aby použil předvolby `sqlContext` ke spuštění dotazu. Dotaz načte prvních 10 řádků ze systémové tabulky, které jsou ve výchozím nastavení součástí všech fondů služby Azure synapse Apache Spark.

2. Spuštěním dalšího dotazu zobrazíte data v tabulce `demo_df`.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    Kód vytvoří dvě výstupní buňky, jednu, která obsahuje výsledky dat druhé, což zobrazuje zobrazení úlohy.

    Ve výchozím nastavení zobrazuje zobrazení výsledků mřížku. Ale pod mřížkou je přepínač zobrazení, který umožňuje přepínání mezi zobrazeními mřížky a grafu.

    ![Dotaz na výstup v Azure synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Dotaz na výstup v Azure synapse Spark")

3. V přepínači **zobrazení** vyberte možnost **graf**.
4. Vyberte ikonu **Možnosti zobrazení** ze zcela pravé strany.
5. V poli **typ grafu** vyberte "pruhový graf".
6. V poli sloupce osy X vyberte "State" (stav).
7. V poli sloupce osy Y vyberte "mzda".
8. V poli **agregace** vyberte "AVG".
9. Vyberte **Použít**.

   ![Výstup grafu ve službě Azure synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Výstup grafu ve službě Azure synapse Spark")

10. Je možné získat stejné možnosti, jak spustit SQL, ale bez nutnosti přepínat jazyky. To můžete provést tak, že nahradíte buňku SQL výše touto buňkou PySpark, takže výstupní prostředí je stejné, protože se používá příkaz **Display** :

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Každá z dříve spuštěných buněk měla možnost přejít na **Server historie** a **monitorovat**. Kliknutím na odkazy přejdete do různých částí uživatelského prostředí.

> [!NOTE]
> Některá z [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/latest/) se spoléhá na použití konzoly Spark, která není v synapse Spark dostupná. Místo toho používejte [Poznámkový blok](quickstart-apache-spark-notebook.md) nebo [IntelliJ](./spark/intellij-tool-synapse.md) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure synapse ukládá vaše data v Azure Data Lake Storage. Pokud se instance Sparku nepoužívá, můžete ji bezpečně nechat vypnout. Účtuje se vám Apache Spark fond bez serveru, pokud je spuštěný, i když se nepoužívá. 

Vzhledem k tomu, že se poplatky za fond mnohokrát účtují více než poplatky za úložiště, má ekonomický smysl, aby instance Sparku byly vypnuté, když se nepoužívají.

Pokud chcete zajistit, aby se instance Spark vypnula, ukončete všechny připojené relace (poznámkové bloky). Fond se ukončí, když je dosaženo **času nečinnosti** zadaného ve fondu Apache Spark. Můžete také vybrat možnost **ukončit relaci** ze stavového řádku v dolní části poznámkového bloku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit fond Apache Spark bez serveru a spustit základní dotaz Spark SQL.

- [Azure Synapse Analytics](overview-what-is.md)
- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark)



