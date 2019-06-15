---
title: Implementace Azure Databricks s koncovým bodem služby Cosmos DB
description: Tento kurz popisuje, jak implementovat Azure Databricks ve virtuální síti s koncový bod služby povolený pro službu Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: d1268ea2cfc22e6350edb32230588a497be8bc79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054452"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Kurz: Implementace Azure Databricks s koncovým bodem služby Cosmos DB

Tento kurz popisuje, jak implementovat virtuální sítě povolené vloženého prostředí Databricks s koncovým bodem služby pro službu Cosmos DB.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks ve virtuální síti
> * Vytvoření koncového bodu služby Cosmos DB
> * Vytvořit účet služby Cosmos DB a importovat data
> * Vytvoření clusteru služby Azure Databricks
> * Dotaz Cosmos DB ze Azure Databricks Poznámkový blok

## <a name="prerequisites"></a>Požadavky

Než začnete, postupujte takto:

* Vytvoření [pracovního prostoru Azure Databricks ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Stáhněte si [konektor Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Stáhněte si ukázková data z [NOAA National Center pro informace o okolnostech](https://www.ncdc.noaa.gov/stormevents/). Vyberte zemi/region nebo oblasti a pak vyberte **hledání**. Na další stránce, přijměte výchozí hodnoty a vyberte **hledání**. Potom vyberte **stáhnout CSV** na levé straně stránky pro stažení výsledků.

* Stáhněte si [předkompilovaný binární soubor](https://aka.ms/csdmtool) z nástroje pro migraci dat Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Vytvoření koncového bodu služby Cosmos DB

1. Po nasazení pracovnímu prostoru Azure Databricks do virtuální sítě přejděte do virtuální sítě [webu Azure portal](https://portal.azure.com). Všimněte si, že veřejný a privátní podsítě, které byly vytvořeny až po nasazení Databricks.

   ![Podsítě virtuální sítě](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Vyberte *veřejné podsítě* a vytvoření koncového bodu služby Cosmos DB. Potom **Uložit**.
   
   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB

1. Otevřete web Azure Portal. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek > databáze > Azure Cosmos DB**.

2. Vyplňte **podrobnosti Instance** na **Základy** kartu s následujícím nastavením:

   |Nastavení|Hodnota|
   |-------|-----|
   |Předplatné|*Vaše předplatné*|
   |Skupina prostředků|*Vaše skupina prostředků*|
   |Název účtu|db-vnet-service-endpoint|
   |Rozhraní API|Jádro (SQL)|
   |Location|Západní USA|
   |Geo-Redundancy|Zakázat|
   |Zápis ve více oblastech|Povolení|

   ![Přidání koncového bodu služby Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Vyberte **sítě** kartu a konfigurace virtuální sítě. 

   a. Zvolte virtuální síť, kterou jste vytvořili jako předpoklad a pak vyberte *veřejné podsítě*. Všimněte si, že *privátní podsítě* má poznámku *koncového bodu "Microsoft AzureCosmosDB" chybí "* . Důvodem je, že jste pouze povolili koncový bod služby Cosmos DB *veřejné podsítě*.

   b. Zkontrolujte, že máte **povolit přístup z portálu Azure portal** povolena. Toto nastavení umožňuje přístup k účtu služby Cosmos DB na webu Azure Portal. Pokud je tato možnost nastavená na **Odepřít**, zobrazí se chyby při pokusu o přístup ke svému účtu. 

   > [!NOTE]
   > Není nutné pro účely tohoto kurzu, ale můžete také povolit *povolit přístup z Moje IP adresa* Pokud chcete mít možnost přístup ke svému účtu služby Cosmos DB z místního počítače. Například pokud se připojujete k vašemu účtu pomocí sady SDK Cosmos DB, je potřeba povolit toto nastavení. Pokud je zakázaná, zobrazí se chyby "Přístup byl odepřen".

   ![Nastavení sítě účtu databáze cosmos](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Vyberte **revize + vytvořit**a potom **vytvořit** k vytvoření účtu služby Cosmos DB ve virtuální síti.

5. Po vytvoření účtu služby Cosmos DB, přejděte na **klíče** pod **nastavení**. Zkopírujte primární připojovací řetězec a uložte ho v textovém editoru pro pozdější použití.

    ![Stránka klíče účtu cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Vyberte **Průzkumník dat** a **novou kolekci** přidáte nové databáze a kolekce ke svému účtu služby Cosmos DB.

    ![Nová kolekce cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Nahrání dat do služby Cosmos DB

1. Otevřete verzi grafické rozhraní [nástroj pro migraci dat pro službu Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Nástroj pro migraci dat cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na **informace o zdroji** kartu, vyberte možnost **soubory CSV** v **importovat z** rozevíracího seznamu. Potom vyberte **přidat soubory** a přidat data storm sdíleného svazku clusteru, které jste stáhli jako předpoklad.

    ![Informace o zdroji cosmos DB nástroj pro migraci dat](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na **informace o cílovém** kartu, zadejte připojovací řetězec. Formát připojovacího řetězce je `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint a AccountKey, jsou součástí primární připojovací řetězec, který jste uložili v předchozí části. Připojit `Database=<your database name>` konec připojovací řetězec a vyberte **ověřte**. Pak přidejte klíč název a oddílu kolekce.

    ![Informace o cílovém cosmos DB nástroj pro migraci dat](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Vyberte **Další** dokud se nedostanete na stránku Souhrn v. Vyberte **Import**.

## <a name="create-a-cluster-and-add-library"></a>Vytvoření clusteru a přidat knihovnu

1. Přejděte k vaší službě Azure Databricks v [webu Azure portal](https://portal.azure.com) a vyberte **spustit pracovní prostor**.

   ![Spustit pracovní prostor Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Vytvoření nového clusteru. Vyberte prosím název clusteru a potvrďte zbývající výchozí nastavení.

   ![Nové nastavení clusteru](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po vytvoření clusteru, přejděte na stránku pro cluster a vyberte **knihovny** kartu. Vyberte **nainstalovat nový** a nahrajte soubor jar konektoru Spark k instalaci knihovny.

    ![Nainstalujte knihovnu konektoru Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Můžete ověřit, že byla nainstalována knihovny **knihovny** kartu.

    ![Karta knihovny clusteru Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Dotaz Cosmos DB z poznámkového bloku Databricks

1. Přejděte do pracovního prostoru Azure Databricks a vytvořte nový poznámkový blok python.

    ![Vytvoření nového poznámkového bloku Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Spusťte následující python kódu pro nastavení konfigurace připojení Cosmos DB. Změnit **koncový bod**, **Masterkey**, **databáze**, a **kolekce** odpovídajícím způsobem.

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

3. Pomocí následujícího kódu python k načtení dat a vytvoření dočasné zobrazení.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Použijte následující magický příkaz k provedení příkazu SQL, která vrací data.

    ```python
    %sql
    select * from storm
    ```

    Pracovního prostoru virtuální sítě – vloží Databricks jste úspěšně propojili prostředek Cosmos DB povolené pro koncový bod služby. Přečíst další informace o tom, jak se připojit ke službě Cosmos DB, najdete v článku [konektor služby Azure Cosmos DB pro Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstraněním úlohy se vyhnete zbytečným fakturace. Pokud plánujete používat pracovní prostor Azure Databricks v budoucnu, můžete zastavit clusteru a počítač restartovat později. Pokud nebudete nadále používat tento pracovní prostor Azure Databricks, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu pomocí následujících kroků:

1. V nabídce vlevo na webu Azure Portal klikněte na **skupiny prostředků** a pak klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků, vyberte **odstranit**, zadejte název prostředku, který chcete odstranit v textovém poli a pak vyberte **odstranit** znovu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili pracovnímu prostoru Azure Databricks k virtuální síti a použití konektoru Cosmos DB Spark k dotazování dat Cosmos DB v Databricks. Další informace o práci s Azure Databricks ve virtuální síti, pokračujte ke kurzu pro Azure Databricks pomocí systému SQL Server.

> [!div class="nextstepaction"]
> [Kurz: Dotaz kontejneru Dockeru Linux SQL serveru ve virtuální síti ze Azure Databricks Poznámkový blok](vnet-injection-sql-server.md)
