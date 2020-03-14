---
title: Ingestování objektů blob Azure do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak odesílat data účtu úložiště do Azure Průzkumník dat pomocí Event Grid předplatného.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370436"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingestování objektů blob do Azure Průzkumník dat díky přihlášení k odběru oznámení Event Grid

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-grid-resource-manager.md)

Azure Průzkumník dat je rychlá a škálovatelná služba pro zkoumání dat pro data protokolů a telemetrie. Nabízí průběžnou příjem (načítání dat) z objektů BLOB zapsaných do kontejnerů objektů BLOB. 

V tomto článku se naučíte, jak nastavit předplatné [Azure Event Grid](/azure/event-grid/overview) a směrovat události do Azure Průzkumník dat prostřednictvím centra událostí. Chcete-li začít, měli byste mít účet úložiště s odběrem služby Event Grid, který odesílá oznámení do služby Azure Event Hubs. Pak vytvoříte datové připojení Event Grid a uvidíte tok dat v celém systému.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).
* [Cluster a databáze](create-cluster-database-portal.md).
* [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Vytvoření předplatného Event Grid v účtu úložiště

1. V Azure Portal Najděte svůj účet úložiště.
1. Vyberte **události** > **odběr události**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. V okně **vytvořit odběr událostí** na kartě **Basic** zadejte následující hodnoty:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název | *test-Grid – připojení* | Název gridu událostí, který chcete vytvořit.|
    | Schéma událostí | *Event Grid schéma* | Schéma, které má být použito pro Event Grid. |
    | Typ tématu | *Účet úložiště* | Typ tématu Event gridu |
    | Prostředek tématu | *gridteststorage* | Název vašeho účtu úložiště. |
    | Přihlásit se k odběru všech typů událostí | *jejich* | Nedostávat oznámení o všech událostech. |
    | Typy definovaných událostí | *Objekt BLOB se vytvořil.* | Konkrétní události, pro které se má zobrazit oznámení |
    | Typ koncového bodu | *Centrum událostí* | Typ koncového bodu, na který odesíláte události. |
    | Koncový bod | *test-hub* | Centrum událostí, které jste vytvořili |
    | | |

1. Vyberte kartu **filtry** , pokud chcete sledovat soubory z konkrétního kontejneru. Filtry pro oznámení můžete nastavit následujícím způsobem:
    * **Subjekt začínající** v poli je předpona *literálu* kontejneru objektů BLOB. Vzhledem k tomu, že je vzor použit jako *StartsWith*, může zahrnovat více kontejnerů. Nejsou povoleny žádné zástupné znaky.
     *Musí* být nastavená takto: *`/blobServices/default/containers/`* [Předpona kontejneru]
    * **Subjekt končí s** polem je přípona *literálu* objektu BLOB. Nejsou povoleny žádné zástupné znaky.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Vytvoří tabulku v Azure Průzkumník dat, kam Event Hubs odešle data. Vytvořte tabulku v clusteru a databázi připravenou v požadavcích.

1. Na portálu Azure Portal vyberte v rámci svého clusteru možnost **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a výběrem příkazu **Spustit** vytvořte tabulku (test), která přijme ingestovaná data.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-grid/run-create-table.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Vytvoření datového připojení Event Grid v Azure Průzkumník dat

Nyní se můžete připojit k Event Grid z Azure Průzkumník dat, aby se data přenášená do kontejneru objektů BLOB streamal do testovací tabulky. 

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V clusteru, který jste vytvořili, vyberte **databáze** > **TestDatabase**.

    ![Výběr testovací databáze](media/ingest-data-event-grid/select-test-database.png)

1. Vyberte **přijímání dat** > **přidat datové připojení**.

    ![Přijímání dat](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Vyberte typ připojení: **BLOB Storage**.

1. Vyplňte formulář pomocí následujících informací a vyberte **vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Azure Průzkumník dat.|
    | Předplatné účtu úložiště | ID vašeho předplatného | ID předplatného, ve kterém se nachází váš účet úložiště.|
    | Účet úložiště | *gridteststorage* | Název účtu úložiště, který jste vytvořili dříve.|
    | Event Grid | *test-Grid – připojení* | Název, který jste vytvořili pro událost Event Grid. |
    | Název centra událostí | *test-hub* | Centrum událostí, které jste vytvořili. Toto pole se vyplní automaticky při výběru služby Event Grid. |
    | Skupina uživatelů | *test-group* | Skupina uživatelů definovaná v centru událostí, které jste vytvořili. |
    | | |

    Cílová tabulka:

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOH, SCSV, TSV, RAW a TXT. Podporované možnosti komprese: zip a GZip |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase** a které mapuje příchozí data JSON na názvy sloupců a datové typy tabulky **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když je služba Azure Průzkumník dat a účet úložiště připojené, můžete vytvořit ukázková data a nahrát je do úložiště objektů BLOB.

Budeme pracovat se skriptem malého prostředí, který vydává několik základních příkazů rozhraní příkazového řádku Azure pro interakci s Azure Storagemi prostředky. Tento skript vytvoří nový kontejner v účtu úložiště, nahraje do tohoto kontejneru existující soubor (jako objekt BLOB) a pak zobrazí seznam objektů BLOB v kontejneru. Pomocí [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) můžete skript spustit přímo na portálu.

Uložte data do souboru a nahrajte ho pomocí tohoto skriptu:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
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
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Aby se dosáhlo nejlepšího výkonu ingestování, musí být předávána *nekomprimovaná* velikost komprimovaných objektů BLOB odeslaných pro přijímání. Vzhledem k tomu, že Event Grid oznámení obsahují pouze základní údaje, musí být informace o velikosti explicitně sdělovány. Informace o nekomprimované velikosti lze poskytnout nastavením vlastnosti `rawSizeBytes` u metadat objektu BLOB s *nekomprimovanými* velikostmi dat v bajtech.

### <a name="ingestion-properties"></a>Vlastnosti ingestování

[Vlastnosti](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) příjmu objektu blob můžete určit prostřednictvím metadat objektu BLOB.

Tyto vlastnosti lze nastavit:

|**Vlastnost** | **Popis vlastnosti**|
|---|---|
| `rawSizeBytes` | Velikost nezpracovaných (nekomprimovaných) dat Pro Avro/ORC/Parquet se jedná o velikost před použitím komprese specifické pro formát.|
| `kustoTable` |  Název existující cílové tabulky Přepíše `Table` sadu v okně `Data Connection`. |
| `kustoDataFormat` |  Formát dat. Přepíše `Data format` sadu v okně `Data Connection`. |
| `kustoIngestionMappingReference` |  Název existujícího mapování ingestování, které se má použít. Přepíše `Column mapping` sadu v okně `Data Connection`.|
| `kustoIgnoreFirstRecord` | Pokud je nastavená na `true`, Kusto ignoruje první řádek objektu BLOB. Použijte v tabulkovém formátu data (CSV, TSV nebo podobné) k ignorování hlaviček. |
| `kustoExtentTags` | Řetězec představující [značky](/azure/kusto/management/extents-overview#extent-tagging) , které budou připojeny k výslednému rozsahu. |
| `kustoCreationTime` |  Přepíše [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) pro objekt blob, formátovaný jako řetězec ISO 8601. Použijte k naplnění. |

> [!NOTE]
> Azure Průzkumník dat neodstraní objekty blob po ingestování.
> Uložte objekty blob pro thrre do pěti dnů.
> Použijte [životní cyklus úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) ke správě odstranění objektu BLOB. 

## <a name="review-the-data-flow"></a>Kontrola toku dat

> [!NOTE]
> Azure Průzkumník dat má agregační (Batch) zásadu pro ingestování dat navrženou pro optimalizaci procesu ingestování.
Ve výchozím nastavení je zásada nakonfigurovaná na 5 minut.
V případě potřeby budete moct zásady později změnit. V tomto článku můžete očekávat latenci během několika minut.

1. V Azure Portal se v rámci služby Event Grid zobrazuje špička aktivity v době, kdy je aplikace spuštěná.

    ![Graf Event Grid](media/ingest-data-event-grid/event-grid-graph.png)

1. Pokud chcete zkontrolovat, kolik zpráv se zatím dostalo do databáze, spusťte v testovací databázi následující dotaz.

    ```kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz v testovací databázi.

    ```kusto
    TestTable
    ```

    Výsledek by měl vypadat následovně.

    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete znovu použít službu Event Grid, vyčistěte **test-hub-RG**, abyste se vyhnuli nákladům.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-grid/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-grid/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-hub-RG*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
