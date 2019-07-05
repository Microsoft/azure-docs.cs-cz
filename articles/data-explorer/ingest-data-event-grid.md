---
title: Ingestování objekty BLOB Azure do Průzkumníku dat Azure
description: V tomto článku se dozvíte, jak odesílat data účtu úložiště do Průzkumníku dat Azure pomocí odběr služby Event Grid.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5854a8974a4d2a9dbc1aa690dc2340fd806f4219
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490137"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingestování objekty BLOB do Průzkumníku dat Azure pomocí přihlášení k odběru oznámení služby Event Grid

Průzkumník služby Azure Data je služba pro zkoumání dat rychlé a škálovatelné pro data protokolů a telemetrie. Nabízí průběžné ingestování (načítání dat) z objektů BLOB zapisovat do kontejnery objektů blob. 

V tomto článku se dozvíte, jak nastavit [Azure Event Grid](/azure/event-grid/overview) předplatného a směrování událostí do Průzkumníku dat Azure prostřednictvím centra událostí. Pokud chcete začít, byste měli mít účet úložiště se odběru event gridu, který odesílá oznámení do služby Azure Event Hubs. Pak vytvoříte připojení dat k Event Grid a zobrazit data tok v celém systému.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Vytvoření [bezplatný účet Azure](https://azure.microsoft.com/free/).
* [Cluster a databáze](create-cluster-database-portal.md).
* [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Vytvořit odběr služby Event Grid ve vašem účtu úložiště

1. Na webu Azure Portal vyhledejte svůj účet úložiště.
1. Vyberte **události** > **odběr události**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. V **vytvořit odběr události** okna nejvyšší úrovně v **základní** kartu, zadejte následující hodnoty:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název | *test-grid-connection* | Název služby event grid, který chcete vytvořit.|
    | Schéma událostí | *Schéma událostí mřížky* | Schéma, který se má použít pro event grid. |
    | Typ tématu | *Účet úložiště* | Typ tématu event gridu. |
    | Téma prostředků | *gridteststorage* | Název účtu úložiště. |
    | Předplatit všechny typy událostí | *clear* | Nemáte, dostanete oznámení na všechny události. |
    | Události definované typy | *Vytvoření objektu BLOB* | Chcete dostávat oznámení, pro které konkrétní události. |
    | Typ koncového bodu | *Služba Event hubs* | Typ koncového bodu, do kterého odesíláte události. |
    | Koncový bod | *test-hub* | Centrum událostí, které jste vytvořili |
    | | |

1. Vyberte **další funkce** kartu, pokud chcete sledovat soubory od konkrétního kontejneru. Nastavte filtry pro oznámení následujícím způsobem:
    * **Na základě práv subjektů začíná** pole je *literálu* předponu kontejneru objektů blob. Je vzorek použije *startswith*, to může zahrnovat několik kontejnerů. Žádné zástupné znaky jsou povoleny.
     To *musí* nastavte následujícím způsobem: *`/blobServices/default/containers/`* [předpony kontejneru]
    * **Na základě práv subjektů skončí s** pole je *literálu* přípona objektu blob. Žádné zástupné znaky jsou povoleny.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Vytvořte tabulku v Průzkumníku dat Azure, ve kterém bude Služba Event Hubs odesílat data. Vytvoření tabulky v clusteru a databáze připravená v rámci požadavků.

1. Na portálu Azure Portal vyberte v rámci svého clusteru možnost **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a vyberte **spustit** k vytvoření tabulky (TestTable), která bude dostávat přijatých dat.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-grid/run-create-table.png)

1. Zkopírujte následující příkaz do okna a vyberte **spustit** k mapování příchozích dat JSON pro sloupec názvy a datové typy v tabulce (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Vytváření Event Grid datové připojení v Průzkumníku dat Azure

Teď připojení do služby event grid z Průzkumníka dat služby Azure, tak, aby se data přenášejí do kontejneru objektů blob se streamuje do tabulky testu.

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V rámci clusteru, který vytvořili, vyberte **databází** > **TestDatabase**.

    ![Výběr testovací databáze](media/ingest-data-event-grid/select-test-database.png)

1. Vyberte **ingestování** > **přidat datové připojení**.

    ![Přijímání dat](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Vyberte typ připojení: **Úložiště objektů blob**.

1. Vyplňte formulář s následujícími informacemi a vyberte **vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Průzkumníku dat Azure.|
    | Předplatné účtu úložiště | ID vašeho předplatného | ID předplatného, ve které se nachází váš účet úložiště.|
    | Účet úložiště | *gridteststorage* | Název účtu úložiště, který jste vytvořili dříve.|
    | Event Grid | *test-grid-connection* | Název služby event grid, který jste vytvořili. |
    | Název centra událostí | *test-hub* | Centrum událostí, který jste vytvořili. Toto pole se vyplní automaticky při výběru event grid. |
    | Skupina uživatelů | *test-group* | Skupina uživatelů definované události, kterou jste vytvořili. |
    | | |

    Cílová tabulka:

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVÉM formátu JSON, PSV, SOH, SCSV, TSV a TXT. Podporované možnosti komprese: ZIP a GZip |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase** a které mapuje příchozí data JSON na názvy sloupců a datové typy tabulky **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když jsou připojené Průzkumník dat Azure a účet úložiště, můžete vytvořit ukázková data a nahrajte ho do úložiště objektů blob.

Jsme budete pracovat s skript malé prostředí, která vydává několik základních příkazů rozhraní příkazového řádku Azure k interakci s prostředky služby Azure Storage. Tento skript vytvoří nový kontejner v účtu úložiště, odešle existujícího souboru (jako objekt blob) do tohoto kontejneru a pak vypíše objekty BLOB v kontejneru. Můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) a spustit skript přímo na portálu.

Uložit data do souboru a nahrajte ho pomocí tohoto skriptu:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Kontrola toku dat

> [!NOTE]
> Průzkumník služby Azure Data má zásady agregace (dávkování) pro příjem dat určená k optimalizaci procesu ingestování.
Ve výchozím nastavení zásada nastavená na 5 minut.
Budete moct změnit zásady později podle potřeby. V tomto článku můžou očekávat latenci za několik minut.

1. Na webu Azure Portal v části event gridu, se zobrazí zásobníku v aktivitě při spuštění aplikace.

    ![Event grid grafu](media/ingest-data-event-grid/event-grid-graph.png)

1. Pokud chcete zkontrolovat, kolik zpráv se zatím dostalo do databáze, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Pokud chcete zobrazit obsah zprávy, spusťte následující dotaz v testovací databázi.

    ```Kusto
    TestTable
    ```

    Výsledek by měl vypadat následovně.

    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nemáte v úmyslu znovu pomocí služby event grid, vyčistit **test-hub-rg**, aby se zabránilo bez nákladů.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-grid/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-grid/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků pro odstranění (*test-hub-rg*) a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

* [Dotazování dat v Průzkumníku dat Azure](web-query-data.md)
