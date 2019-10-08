---
title: Ingestování objektů blob Azure do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak odesílat data účtu úložiště do Azure Průzkumník dat pomocí Event Grid předplatného.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 3c2407472cd15326c295f70c69606fc5ee663f72
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996793"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingestování objektů blob do Azure Průzkumník dat díky přihlášení k odběru oznámení Event Grid

Azure Průzkumník dat je rychlá a škálovatelná služba pro zkoumání dat pro data protokolů a telemetrie. Nabízí průběžnou příjem (načítání dat) z objektů BLOB zapsaných do kontejnerů objektů BLOB. 

V tomto článku se naučíte, jak nastavit předplatné [Azure Event Grid](/azure/event-grid/overview) a směrovat události do Azure Průzkumník dat prostřednictvím centra událostí. Chcete-li začít, měli byste mít účet úložiště s odběrem služby Event Grid, který odesílá oznámení do služby Azure Event Hubs. Pak vytvoříte datové připojení Event Grid a uvidíte tok dat v celém systému.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).
* [Cluster a databáze](create-cluster-database-portal.md).
* [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Vytvoření předplatného Event Grid v účtu úložiště

1. V Azure Portal Najděte svůj účet úložiště.
1. Vyberte **události**@no__t**odběr událostí**1.

    ![Odkaz na dotaz aplikace](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. V okně **vytvořit odběr událostí** na kartě **Basic** zadejte následující hodnoty:

    **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Name | *test-Grid – připojení* | Název gridu událostí, který chcete vytvořit.|
    | Schéma událostí | *Event Grid schéma* | Schéma, které má být použito pro Event Grid. |
    | Typ tématu | *Účet úložiště* | Typ tématu Event gridu |
    | Prostředek tématu | *gridteststorage* | Název vašeho účtu úložiště. |
    | Přihlášení k odběru všech typů událostí | *jejich* | Nedostávat oznámení o všech událostech. |
    | Typy definovaných událostí | *Objekt BLOB se vytvořil.* | Konkrétní události, pro které se má zobrazit oznámení |
    | Typ koncového bodu | *Centra událostí* | Typ koncového bodu, na který odesíláte události. |
    | Koncový bod | *Centrum testování* | Centrum událostí, které jste vytvořili. |
    | | |

1. Vyberte kartu **Další funkce** , pokud chcete sledovat soubory z konkrétního kontejneru. Filtry pro oznámení můžete nastavit následujícím způsobem:
    * **Subjekt začínající** v poli je předpona *literálu* kontejneru objektů BLOB. Vzhledem k tomu, že je vzor použit jako *StartsWith*, může zahrnovat více kontejnerů. Nejsou povoleny žádné zástupné znaky.
     *Musí* být nastavená takto: *`/blobServices/default/containers/`* [Předpona kontejneru]
    * **Subjekt končí s** polem je přípona *literálu* objektu BLOB. Nejsou povoleny žádné zástupné znaky.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Průzkumník dat

Vytvoří tabulku v Azure Průzkumník dat, kam Event Hubs odešle data. Vytvořte tabulku v clusteru a databázi připravenou v požadavcích.

1. V Azure Portal v clusteru vyberte možnost **dotaz**.

    ![Odkaz na dotaz aplikace](media/ingest-data-event-grid/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a výběrem příkazu **Spustit** vytvořte tabulku (test), která přijme ingestovaná data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Spustit dotaz Create](media/ingest-data-event-grid/run-create-table.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Vytvoření datového připojení Event Grid v Azure Průzkumník dat

Nyní se můžete připojit k Event Grid z Azure Průzkumník dat, aby se data přenášená do kontejneru objektů BLOB streamal do testovací tabulky. 

1. Na panelu nástrojů vyberte **oznámení** , abyste ověřili, že nasazení centra událostí bylo úspěšné.

1. V clusteru, který jste vytvořili, vyberte **databáze** > **TestDatabase**.

    ![Vybrat testovací databázi](media/ingest-data-event-grid/select-test-database.png)

1. Vyberte **přijímání dat** > **přidat datové připojení**.

    ![Přijímání dat](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Vyberte typ připojení: **BLOB Storage**.

1. Vyplňte formulář pomocí následujících informací a vyberte **vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Zdroj dat:

    **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-centrum – připojení* | Název připojení, které chcete vytvořit v Azure Průzkumník dat.|
    | Předplatné účtu úložiště | ID vašeho předplatného | ID předplatného, ve kterém se nachází váš účet úložiště.|
    | Účet úložiště | *gridteststorage* | Název účtu úložiště, který jste vytvořili dříve.|
    | Event Grid | *test-Grid – připojení* | Název, který jste vytvořili pro událost Event Grid. |
    | Název centra událostí | *Centrum testování* | Centrum událostí, které jste vytvořili. Toto pole se vyplní automaticky při výběru služby Event Grid. |
    | Skupina uživatelů | *Skupina testů* | Skupina uživatelů definovaná v centru událostí, které jste vytvořili. |
    | | |

    Cílová tabulka:

     **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Tabulka | *Testovací navýšení* | Tabulka, kterou jste vytvořili v **TestDatabase**. |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOH, SCSV, TSV a TXT. Podporované možnosti komprese: zip a GZip |
    | Mapování sloupce | *TestMapping* | Mapování, které jste vytvořili v **TestDatabase**, které mapuje příchozí data JSON na názvy sloupců a datové typy **testovacího**typu.|
    | | |
    
## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když je služba Azure Průzkumník dat a účet úložiště připojené, můžete vytvořit ukázková data a nahrát je do úložiště objektů BLOB.

Budeme pracovat se skriptem malého prostředí, který vydává několik základních příkazů rozhraní příkazového řádku Azure pro interakci s Azure Storagemi prostředky. Tento skript vytvoří nový kontejner v účtu úložiště, nahraje do tohoto kontejneru existující soubor (jako objekt BLOB) a pak zobrazí seznam objektů BLOB v kontejneru. Pomocí [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) můžete skript spustit přímo na portálu.

Uložte data do souboru a nahrajte ho pomocí tohoto skriptu:

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
> Azure Průzkumník dat má agregační (Batch) zásadu pro ingestování dat navrženou pro optimalizaci procesu ingestování.
Ve výchozím nastavení je zásada nakonfigurovaná na 5 minut.
V případě potřeby budete moct zásady později změnit. V tomto článku můžete očekávat latenci během několika minut.

1. V Azure Portal se v rámci služby Event Grid zobrazuje špička aktivity v době, kdy je aplikace spuštěná.

    ![Graf Event Grid](media/ingest-data-event-grid/event-grid-graph.png)

1. Chcete-li zkontrolovat, kolik zpráv bylo v databázi zatím provedeno, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz v testovací databázi.

    ```Kusto
    TestTable
    ```

    Sada výsledků by měla vypadat takto.

    ![Sada výsledků zpráv](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete znovu použít službu Event Grid, vyčistěte **test-hub-RG**, abyste se vyhnuli nákladům.

1. V Azure Portal vyberte **skupiny prostředků** na zcela vlevo a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je levá nabídka sbalená, vyberte ![Tlačítko Rozbalit](media/ingest-data-event-grid/expand.png) a rozbalte ji.

   ![Vyberte skupinu prostředků, kterou chcete odstranit.](media/ingest-data-event-grid/delete-resources-select.png)

1. V části **test-Resource-Group**vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-hub-RG*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
