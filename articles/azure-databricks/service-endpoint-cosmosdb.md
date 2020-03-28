---
title: Kurz – implementace datových cihel Azure s koncovým bodem Cosmos DB
description: Tento kurz popisuje, jak implementovat Azure Databricks ve virtuální síti s koncovým bodem služby povolené pro Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706163"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Kurz: Implementace Datových cihel Azure s koncovým bodem Cosmos DB

Tento kurz popisuje, jak implementovat virtuální sítě vložené Databricks prostředí s koncovým bodem služby povoleno pro Cosmos DB.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks ve virtuální síti
> * Vytvoření koncového bodu služby Cosmos DB
> * Vytvoření účtu Cosmos DB a import dat
> * Vytvoření clusteru Azure Databricks
> * Dotaz Cosmos DB z poznámkového bloku Azure Databricks

## <a name="prerequisites"></a>Požadavky

Než začnete, postupujte takto:

* Vytvořte [pracovní prostor Azure Databricks ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Stáhněte si [konektor Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Stáhněte si ukázková data z [národních center NOAA pro informace o životním prostředí](https://www.ncdc.noaa.gov/stormevents/). Vyberte stav nebo oblast a vyberte **Hledat**. Na další stránce přijměte výchozí hodnoty a vyberte **Hledat**. Pak vyberte **CSV Download** na levé straně stránky ke stažení výsledků.

* Stáhněte si [předkompilovaný binární soubor](https://aka.ms/csdmtool) nástroje pro migraci dat Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Vytvoření koncového bodu služby Cosmos DB

1. Po nasazení pracovního prostoru Azure Databricks do virtuální sítě přejděte do virtuální sítě na [webu Azure Portal](https://portal.azure.com). Všimněte si veřejných a soukromých podsítí, které byly vytvořeny prostřednictvím nasazení Databricks.

   ![Podsítě virtuální sítě](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Vyberte *veřejnou podsíť* a vytvořte koncový bod služby Cosmos DB. Pak **uložit**.
   
   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB

1. Otevřete web Azure Portal. Na levé horní straně obrazovky vyberte **Vytvořit prostředek > databáze > Azure Cosmos DB**.

2. Vyplňte **podrobnosti instance** na kartě **Základy** s následujícími nastaveními:

   |Nastavení|Hodnota|
   |-------|-----|
   |Předplatné|*vaše předplatné*|
   |Skupina prostředků|*vaše skupina prostředků*|
   |Název účtu|db-vnet-service-endpoint|
   |rozhraní API|Core (SQL)|
   |Umístění|USA – západ|
   |Geografická redundance|Zakázat|
   |Zápisy pro více oblastí|Povolení|

   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Vyberte kartu **Síť** a nakonfigurujte virtuální síť. 

   a. Zvolte virtuální síť, kterou jste vytvořili jako podmínku, a pak vyberte *veřejnou podsíť*. Všimněte si, že *privátní podsíť* má poznámku *"Microsoft AzureCosmosDB" koncový bod chybí '*. Důvodem je, že jste povolili pouze koncový bod služby Cosmos DB ve *veřejné podsíti*.

   b. Ujistěte se, že máte povolený **povolený přístup z webu Azure Portal.** Toto nastavení umožňuje přístup k účtu Cosmos DB z portálu Azure. Pokud je tato možnost nastavena na **možnost Odepřít**, zobrazí se při pokusu o přístup k účtu chyby. 

   > [!NOTE]
   > Není nutné pro tento kurz, ale můžete také povolit *povolit přístup z mé IP,* pokud chcete mít přístup k účtu Cosmos DB z místního počítače. Například pokud se připojujete ke svému účtu pomocí Cosmos DB SDK, je třeba povolit toto nastavení. Pokud je zakázán, zobrazí se chyby "Přístup byl odepřen".

   ![Nastavení sítě účtu Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Vyberte **Revize + Vytvořit**a pak **Vytvořit** pro vytvoření účtu Cosmos DB uvnitř virtuální sítě.

5. Po vytvoření účtu Cosmos DB přejděte do části **Klíče** v části **Nastavení**. Zkopírujte primární připojovací řetězec a uložte jej do textového editoru pro pozdější použití.

    ![Stránka klíčů účtu Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Vyberte **Průzkumník dat** a **Nová kolekce,** chcete-li do účtu Cosmos DB přidat novou databázi a kolekci.

    ![Nová kolekce Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Nahrání dat do Cosmos DB

1. Otevřete verzi grafického rozhraní [nástroje pro migraci dat pro cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Nástroj pro migraci dat Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na kartě **Informace o zdroji** vyberte v rozevíracím souboru **Importovat z** položku Soubory **CSV.** Pak vyberte **Přidat soubory** a přidejte csv dat bouře, které jste stáhli jako předpoklad.

    ![Informace o zdroji zdroje nástroje pro migraci dat Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na kartě **Informace o cíli** zadejte připojovací řetězec. Formát připojovacího řetězce je `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint a AccountKey jsou zahrnuty v primárním připojovacím řetězci, který jste uložili v předchozí části. Připojte `Database=<your database name>` jej na konec připojovacího řetězce a vyberte **Ověřit**. Potom přidejte název kolekce a klíč oddílu.

    ![Informace o cíli nástroje pro migraci dat Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Vyberte **Další,** dokud se nedostanete na stránku Souhrn. Potom vyberte **Importovat**.

## <a name="create-a-cluster-and-add-library"></a>Vytvoření clusteru a přidání knihovny

1. Přejděte na azure databricks službu na [webu Azure portal](https://portal.azure.com) a vyberte Spustit pracovní **prostor**.

   ![Spuštění pracovního prostoru Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Vytvořte nový cluster. Zvolte název clusteru a přijměte zbývající výchozí nastavení.

   ![Nové nastavení clusteru](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po vytvoření clusteru přejděte na stránku clusteru a **Install New** vyberte kartu **Knihovny.**

    ![Instalace knihovny konektorů Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Můžete ověřit, zda byla knihovna nainstalována na kartě **Knihovny.**

    ![Karta Knihovny clusteru Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Dotaz Cosmos DB z poznámkového bloku Databricks

1. Přejděte do pracovního prostoru Azure Databricks a vytvořte nový poznámkový blok pythonu.

    ![Vytvoření nového poznámkového bloku Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Spusťte následující kód pythonu a nastavte konfiguraci připojení Cosmos DB. Odpovídajícím způsobem změňte **koncový bod**, **klíč Masterkey**, **databázi**a **kolekci.**

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Pomocí následujícího kódu pythonu načtěte data a vytvořte dočasné zobrazení.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Pomocí následujícího příkazu magie spusťte příkaz SQL, který vrací data.

    ```python
    %sql
    select * from storm
    ```

    Úspěšně jste připojili pracovní prostor Databricks vložených virtuální sítí k prostředku Cosmos DB s povoleným koncovým bodem služby. Další informace o tom, jak se připojit ke službě Cosmos DB, najdete [v tématu Konektor Azure Cosmos DB pro Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstraněníúlohy zabrání zbytečné fakturaci. Pokud plánujete v budoucnu používat pracovní prostor Azure Databricks, můžete cluster zastavit a restartovat později. Pokud nebudete nadále používat tento pracovní prostor Azure Databricks, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu pomocí následujících kroků:

1. V levé nabídce na webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků vyberte **Odstranit**, zadejte název prostředku, který chcete odstranit, do textového pole a pak znovu vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili pracovní prostor Azure Databricks do virtuální sítě a použili konektor Cosmos DB Spark k dotazování dat Cosmos DB z Databricks. Další informace o práci s Azure Databricks ve virtuální síti nadále kurz pro použití SQL Serveru s Azure Databricks.

> [!div class="nextstepaction"]
> [Kurz: Dotaz na kontejner SQL Server Linux Docker ve virtuální síti z poznámkového bloku Azure Databricks](vnet-injection-sql-server.md)
