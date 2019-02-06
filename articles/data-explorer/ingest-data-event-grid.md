---
title: 'Rychlý start: Ingestování objekty BLOB Azure do Průzkumníku dat Azure'
description: V tomto rychlém startu se dozvíte, jak odesílat data účtu úložiště do Průzkumníku dat Azure pomocí odběr služby Event Grid.
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 1/30/2019
ms.openlocfilehash: 343baedb77eb5c8a250e2ab967332dc0905f5112
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743680"
---
# <a name="quickstart-ingest-azure-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Rychlý start: Ingestování objektů BLOB Azure do Průzkumníku dat Azure pomocí přihlášení k odběru oznámení služby Event Grid

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Průzkumník dat Azure nabízí průběžné zpracování (načítání dat) z objektů BLOB zapisovat do kontejnery objektů blob. Toho dosáhnete pomocí nastavení [Azure Event Grid](/azure/event-grid/overview) předplatné pro události vytváření objektů blob a směrování tyto události Kusto prostřednictvím centra událostí. Pro účely tohoto rychlého startu potřebujete účet úložiště se odběr služby Event Grid, která odesílá jeho oznámení do centra událostí. Potom můžete vytvořit datové připojení Event Grid a zobrazit data tok v celém systému.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/)
1. [Cluster a databáze](create-cluster-database-portal.md)
1. [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)
1. [Centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Vytvořit odběr služby Event Grid ve vašem účtu úložiště

1. Na webu Azure Portal přejděte do svého účtu úložiště
1. Klikněte na **události** kartu, pak na **odběr události**

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. V **vytvořit odběr události** okna nejvyšší úrovně v **základní** kartu, zadejte následující hodnoty:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název | *test-grid-connection* | Název služby event grid, kterou chcete vytvořit.|
    | Schéma událostí | *Schéma událostí mřížky* | Schéma, který se má použít pro Event Grid. |
    | Typ tématu | *Účet úložiště* | Typ tématu event gridu. |
    | Prostředek tématu | *gridteststorage* | Název účtu úložiště. |
    | Přihlásit se k odběru všech typů událostí | *Zrušte zaškrtnutí políčka* | Nemáte, dostanete oznámení na všechny události. |
    | Definované typy událostí | *Vytvoření objektu BLOB* | Chcete dostávat oznámení, pro které konkrétní události. |
    | Typ koncového bodu | *Event Hubs* | Typ koncového bodu, do kterého odesíláte události. |
    | Koncový bod | *test-hub* | Centrum událostí, které jste vytvořili |
    | | |

1. Vyberte **další funkce** kartu, pokud chcete sledovat soubory od konkrétního kontejneru. Nastavte filtry pro oznámení následujícím způsobem:
    * **Na základě práv subjektů začíná** pole je *literálu* předponu kontejneru objektů blob (je vzor použitý *startswith*, to může zahrnovat více kontejnerů). Žádné zástupné znaky jsou povoleny.
     To *musí* nastavte následujícím způsobem: *`/blobServices/default/containers/`*[předpony kontejneru]
    * **Na základě práv subjektů skončí s** pole je *literálu* přípona objektu blob. Žádné zástupné znaky jsou povoleny.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Vytvořte tabulku v Průzkumníku dat Azure, do kterého bude Služba Event Hubs odesílat data. Vytvoření tabulky v clusteru a připraví databázi v **požadavky**.

1. Na portálu Azure Portal vyberte v rámci svého clusteru možnost **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-hub/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a vyberte **spustit** k vytvoření tabulky (TestTable), který bude přijímat přijaté data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-grid/run-create-table.png)

1. Zkopírujte následující příkaz do okna a vyberte **spustit** k mapování příchozích dat JSON pro sloupec názvy a datové typy v tabulce (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Vytváření Event Grid datové připojení v Průzkumníku dat Azure

Teď připojíte do služby Event Grid z Průzkumníka dat služby Azure, tak, aby se data přenášejí do kontejneru objektů blob se streamuje do tabulky testu.

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V rámci clusteru, který jste vytvořili, vyberte **Databáze** a pak **TestDatabase**.

    ![Výběr testovací databáze](media/ingest-data-event-hub/select-test-database.png)

1. Vyberte **Ingestace dat** a pak **Přidat datové připojení**.

    ![Přijímání dat](media/ingest-data-event-hub/data-ingestion-create.png)

1. Vyberte typ připojení: **Úložiště objektů blob**.

1. Vyplňte formulář pomocí následující informace a pak klikněte na **vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | Předplatné účtu úložiště | ID vašeho předplatného | ID předplatného, ve které se nachází váš účet úložiště.|
    | Účet úložiště | *gridteststorage* | Název účtu úložiště, který jste vytvořili dříve.|
    | Event Grid | *test-grid-connection* | Název služby Event Grid, který jste vytvořili. |
    | Název centra událostí | *test-hub* | Centrum událostí, které jste vytvořili Toto je automaticky vyplněno při výběru Event Grid. |
    | Skupina uživatelů | *test-group* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVÉM formátu JSON, PSV, SOH, SCSV, TSV a TXT. |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase** a které mapuje příchozí data JSON na názvy sloupců a datové typy tabulky **TestTable**.|
    | | |

## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když jsou připojené Průzkumník dat Azure a účet úložiště, můžete vytvořit ukázková data a nahrát do úložiště objektů blob.

Jsme budete pracovat s skript malé prostředí, která vydává několik základních příkazů rozhraní příkazového řádku Azure k interakci s prostředky služby Azure Storage. Skript nejprve vytvoří nový kontejner v účtu úložiště a pak odešle existujícího souboru (jako objekt blob) do tohoto kontejneru. Pak zobrazí seznam všech objektů BLOB v kontejneru. Můžete použít [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) a spustit skript přímo na portálu.

Uložte následující data do souboru a pomocí níže uvedeného skriptu:

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
> ADX má zásady agregace (dávkování) pro příjem dat určená k optimalizaci procesu ingestování.
Ve výchozím nastavení zásada nastavená na 5 minut.
Budete moct změnit zásady později podle potřeby. V tomto rychlém startu můžete očekávat latenci několik minut.

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

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-hub/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-resource-group*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Dotazování dat v Průzkumníku dat Azure](web-query-data.md)
