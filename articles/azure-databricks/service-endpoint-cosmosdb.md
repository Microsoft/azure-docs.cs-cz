---
title: Kurz – implementace Azure Databricks s koncovým bodem Cosmos DB
description: V tomto kurzu se dozvíte, jak implementovat Azure Databricks ve virtuální síti s povoleným koncovým bodem služby pro Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779601"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Kurz: implementace Azure Databricks s koncovým bodem Cosmos DB

V tomto kurzu se dozvíte, jak implementovat virtuální síť s vloženým prostředím datacihly s koncovým bodem služby, který je povolený pro Cosmos DB.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks ve virtuální síti
> * Vytvoření koncového bodu služby Cosmos DB
> * Vytvoření účtu Cosmos DB a import dat
> * Vytvoření clusteru Azure Databricks
> * Dotazování Cosmos DB z poznámkového bloku Azure Databricks

## <a name="prerequisites"></a>Požadavky

Než začnete, udělejte toto:

* Vytvořte [Azure Databricks pracovní prostor ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Stáhněte si [konektor Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Stáhněte si ukázková data z [národních Center NOAA pro informace o prostředí](https://www.ncdc.noaa.gov/stormevents/). Vyberte stav nebo oblast a vyberte **Hledat**. Na další stránce přijměte výchozí hodnoty a vyberte **Hledat**. Pak na levé straně stránky vyberte **Stáhnout soubor CSV** a stáhněte výsledky.

* Stáhněte [předem kompilovaný binární soubor](https://aka.ms/csdmtool) nástroje pro migraci dat Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Vytvoření koncového bodu služby Cosmos DB

1. Po nasazení pracovního prostoru Azure Databricks do virtuální sítě přejděte do [Azure Portal](https://portal.azure.com)na virtuální síť. Všimněte si veřejných a privátních podsítí, které byly vytvořeny pomocí nasazení datacihly.

   ![Podsítě virtuální sítě](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Vyberte *veřejnou podsíť* a vytvořte koncový bod služby Cosmos DB. Pak **uložte**.
   
   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB

1. Otevřete web Azure Portal. V levé horní části obrazovky vyberte **vytvořit prostředek > databáze > Azure Cosmos DB**.

2. Vyplňte **Podrobnosti instance** na kartě **základy** s následujícím nastavením:

   |Nastavení|Hodnota|
   |-------|-----|
   |Předplatné|*Vaše předplatné*|
   |Resource Group|*vaše skupina prostředků*|
   |Account Name|DB-VNet-Service-Endpoint|
   |Rozhraní API|Core (SQL)|
   |Umístění|USA – západ|
   |Geografická redundance|Zakázat|
   |Zápisy pro více oblastí|Povolit|

   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Vyberte kartu **síť** a nakonfigurujte svou virtuální síť. 

   a. Zvolte virtuální síť, kterou jste vytvořili jako požadavek, a pak vyberte *Veřejná podsíť*. Všimněte si, že v *privátní podsíti* *chybí koncový bod Microsoft AzureCosmosDB*. Důvodem je to, že jste povolili koncový bod služby Cosmos DB ve *veřejné podsíti*.

   b. Ujistěte se, že máte povolený **přístup z Azure Portal** . Toto nastavení umožňuje přístup k účtu Cosmos DB z Azure Portal. Pokud je tato možnost nastavená na **Odepřít**, při pokusu o přístup k vašemu účtu se zobrazí chyby. 

   > [!NOTE]
   > V tomto kurzu to není nutné, ale pokud chcete mít přístup k vašemu Cosmos DB účtu z místního počítače, můžete povolit *přístup i z této IP adresy* . Pokud se například připojujete k účtu pomocí Cosmos DB SDK, musíte povolit toto nastavení. Pokud je zakázaný, zobrazí se chyby "přístup byl odepřen".

   ![Nastavení sítě Cosmos DB účtu](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Vyberte **zkontrolovat + vytvořit**a pak **vytvořit** a vytvořte účet Cosmos DB v rámci virtuální sítě.

5. Po vytvoření účtu Cosmos DB přejděte na **klíče** v části **Nastavení**. Zkopírujte primární připojovací řetězec a uložte ho v textovém editoru pro pozdější použití.

    ![Stránka klíčů účtu Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Vyberte **Průzkumník dat** a **novou kolekci** a přidejte do svého účtu Cosmos DB novou databázi a kolekci.

    ![Cosmos DB novou kolekci](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Nahrání dat do Cosmos DB

1. Otevřete verzi grafického rozhraní [Nástroje pro migraci dat pro Cosmos DB](https://aka.ms/csdmtool) **Dtui. exe**.

    ![Nástroj pro migraci dat Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na kartě **zdrojové informace** vyberte v rozevíracím seznamu **Import z** možnost **soubory CSV** . Pak vyberte **Přidat soubory** a přidejte soubor CSV s daty, který jste stáhli jako požadavek.

    ![Cosmos DB informace o zdroji nástroje pro migraci dat](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na kartě **cílové informace** zadejte připojovací řetězec. Formát připojovacího řetězce je `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>` . AccountEndpoint a AccountKey jsou součástí primárního připojovacího řetězce, který jste uložili v předchozí části. Přidejte `Database=<your database name>` na konec připojovacího řetězce a vyberte **ověřit**. Pak přidejte název kolekce a klíč oddílu.

    ![Cosmos DB informace o cíli nástroje pro migraci dat](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Vyberte **Další** , dokud se nedostanete na stránku Souhrn. Pak vyberte **importovat**.

## <a name="create-a-cluster-and-add-library"></a>Vytvoření clusteru a přidání knihovny

1. Přejděte ke službě Azure Databricks v [Azure Portal](https://portal.azure.com) a vyberte **Spustit pracovní prostor**.

2. Vytvořte nový cluster. Vyberte název clusteru a potvrďte zbývající výchozí nastavení.

   ![Nové nastavení clusteru](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po vytvoření clusteru přejděte na stránku clusteru a vyberte kartu **knihovny** . Vyberte **nainstalovat novou** a nahrajte soubor JAR konektoru Sparku, abyste mohli knihovnu nainstalovat.

    ![Nainstalovat knihovnu Spark Connector](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Můžete ověřit, že se knihovna nainstalovala na kartu **knihovny** .

    ![Karta knihovny clusteru datacihly](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Dotazování Cosmos DB z poznámkového bloku datacihly

1. Přejděte do pracovního prostoru Azure Databricks a vytvořte nový Poznámkový blok Python.

    ![Vytvoření nového poznámkového bloku datacihly](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Spusťte následující kód Pythonu, abyste nastavili konfiguraci Cosmos DBho připojení. Odpovídajícím způsobem změňte **koncový bod**, **masterkey**, **databázi**a **kolekci** .

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

3. K načtení dat a vytvoření dočasného zobrazení použijte následující kód Pythonu.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. K provedení příkazu SQL, který vrací data, použijte následující příkaz Magic.

    ```python
    %sql
    select * from storm
    ```

    Úspěšně jste propojili pracovní prostor datacihlů vložený do virtuální sítě do prostředku Cosmos DBho koncového bodu služby. Další informace o tom, jak se připojit k Cosmos DB, najdete v tématu [konektor Azure Cosmos DB pro Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstranění úlohy se vyhne zbytečnému fakturaci. Pokud plánujete použít pracovní prostor Azure Databricks v budoucnu, můžete cluster zastavit a restartovat ho později. Pokud nebudete nadále používat tento Azure Databricks pracovní prostor, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu, pomocí následujících kroků:

1. V nabídce na levé straně Azure Portal klikněte na **skupiny prostředků** a pak klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte název prostředku, který chcete odstranit, a pak vyberte **Odstranit** znovu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili pracovní prostor Azure Databricks do virtuální sítě a použili Cosmos DB konektor Spark k dotazování Cosmos DB dat z datových cihl. Další informace o práci s Azure Databricks ve virtuální síti najdete v kurzu použití SQL Server s Azure Databricks.

> [!div class="nextstepaction"]
> [Kurz: dotazování kontejneru Docker SQL Server Linux ve virtuální síti z poznámkového bloku Azure Databricks](vnet-injection-sql-server.md)
