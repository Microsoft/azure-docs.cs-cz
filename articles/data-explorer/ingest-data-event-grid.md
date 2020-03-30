---
title: Ingestování objektů BLOB Azure do Průzkumníka dat Azure
description: V tomto článku se dozvíte, jak odesílat data účtu úložiště do Průzkumníka dat Azure pomocí předplatného Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370436"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingestování objektů BLOB do Průzkumníka dat Azure přihlášením k odběru oznámení gridu událostí

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-grid-resource-manager.md)

Azure Data Explorer je rychlá a škálovatelná služba pro zkoumání dat pro data protokolu a telemetrie. Nabízí nepřetržité ingestování (načítání dat) z objektů BLOB zapsané do kontejnerů objektů blob. 

V tomto článku se dozvíte, jak nastavit předplatné [Azure Event Grid](/azure/event-grid/overview) a směrovat události do Azure Data Exploreru prostřednictvím centra událostí. Chcete-li začít, měli byste mít účet úložiště s odběrem mřížky událostí, který odesílá oznámení do Centra událostí Azure. Pak vytvoříte datové připojení Event Grid a uvidíte tok dat v celém systému.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/).
* [Cluster a databáze](create-cluster-database-portal.md).
* [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Vytvoření předplatného event gridu v účtu úložiště

1. Na webu Azure Portal najděte svůj účet úložiště.
1. Vyberte**odběr událostí**událostí . **Events** > 

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. V okně **Vytvořit odběr události** na kartě **Základní** zadejte následující hodnoty:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Name (Název) | *připojení testovací mřížky* | Název mřížky událostí, kterou chcete vytvořit.|
    | Schéma událostí | *Schéma služby Event Grid* | Schéma, které by měly být použity pro mřížku událostí. |
    | Typ tématu | *Účet úložiště* | Typ tématu mřížky událostí. |
    | Zdroj tématu | *gridteststorage* | Název vašeho účtu úložiště. |
    | Přihlásit se ke všem typům událostí | *Jasné* | Nenechte se upozorňují na všechny události. |
    | Definované typy událostí | *Vytvoření objektu blob* | Které konkrétní události, na které chcete být upozorněni. |
    | Typ koncového bodu | *Centrum událostí* | Typ koncového bodu, do kterého odesíláte události. |
    | Koncový bod | *test-hub* | Centrum událostí, které jste vytvořili |
    | | |

1. Pokud chcete sledovat soubory z určitého kontejneru, vyberte kartu **Filtry.** Filtry pro oznámení nastavte takto:
    * **Předmět začíná** pole je *literál* předpona kontejneru objektů blob. Jako vzor použít je *začínás*, může span více kontejnerů. Nejsou povoleny žádné zástupné znaky.
     *Musí* být nastavena takto: *`/blobServices/default/containers/`*[předpona kontejneru]
    * **Pole Zakončení objektu** je *literálová* přípona objektu blob. Nejsou povoleny žádné zástupné znaky.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Vytvořte tabulku v Průzkumníku dat Azure, kam budou centra událostí odesílat data. Vytvořte tabulku v clusteru a databázi připravené v požadavcích.

1. Na portálu Azure Portal vyberte v rámci svého clusteru možnost **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-grid/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit,** chcete-li vytvořit tabulku (TestTable), která obdrží příjem dat.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-grid/run-create-table.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit,** chcete-li namapovat příchozí data JSON na názvy sloupců a datové typy tabulky (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Vytvoření datového připojení Grid událostí v Průzkumníku dat Azure

Teď se připojte k Event Grid z Azure Data Explorer, takže data toku do kontejneru objektů blob se streamuje do testovací tabulky. 

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V rámci vytvořeného clusteru vyberte **možnost Databáze** > **testdatabáze**.

    ![Výběr testovací databáze](media/ingest-data-event-grid/select-test-database.png)

1. Vyberte **položku** > Přidání**datového připojení**k datům .

    ![Přijímání dat](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Vyberte typ připojení: **Úložiště objektů blob**.

1. Vyplňte formulář následujícími informacemi a vyberte **Vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Průzkumníku dat Azure.|
    | Předplatné účtu úložiště | ID předplatného | ID předplatného, ve kterém se nachází váš účet úložiště.|
    | Účet úložiště | *gridteststorage* | Název účtu úložiště, který jste vytvořili dříve.|
    | Event Grid | *připojení testovací mřížky* | Název mřížky událostí, kterou jste vytvořili. |
    | Název centra událostí | *test-hub* | Centrum událostí, které jste vytvořili. Toto pole se automaticky vyplní, když vyberete mřížku událostí. |
    | Skupina uživatelů | *test-group* | Skupina spotřebitelů definovaná v centru událostí, které jste vytvořili. |
    | | |

    Cílová tabulka:

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW a TXT. Podporované možnosti komprese: Zip a GZip |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase** a které mapuje příchozí data JSON na názvy sloupců a datové typy tabulky **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když jsou Azure Data Explorer a účet úložiště připojené, můžete vytvořit ukázková data a nahrát je do úložiště objektů blob.

Budeme pracovat s malým shellovým skriptem, který vydává několik základních příkazů Azure CLI pro interakci s prostředky Azure Storage. Tento skript vytvoří nový kontejner ve vašem účtu úložiště, nahraje existující soubor (jako objekt blob) do tohoto kontejneru a pak zobrazí seznam objektů BLOB v kontejneru. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) můžete použít ke spuštění skriptu přímo na portálu.

Uložte data do souboru a nahrajte je pomocí tohoto skriptu:

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
> Chcete-li dosáhnout nejlepšího výkonu při požití, musí být *sdělena nekomprimovaná* velikost komprimovaných objektů BLOB odeslaných k požití. Vzhledem k tomu, že oznámení mřížky událostí obsahují pouze základní podrobnosti, musí být informace o velikosti explicitně sděleny. Informace o nekomprimované velikosti lze `rawSizeBytes` poskytnout nastavením vlastnosti metadat objektu blob s *nekomprimovanou* velikostí dat v bajtech.

### <a name="ingestion-properties"></a>Vlastnosti požití

Můžete určit [vlastnosti ingestování](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) objektu blob ingestování prostřednictvím metadat objektu blob.

Tyto vlastnosti lze nastavit:

|**Vlastnost** | **Popis vlastnosti**|
|---|---|
| `rawSizeBytes` | Velikost nezpracovaných (nekomprimovaných) dat. Pro Avro/ORC/Parquet se jedná o velikost před použitím komprese specifické pro formát.|
| `kustoTable` |  Název existující cílové tabulky. Přepíše `Table` sadu na `Data Connection` noži. |
| `kustoDataFormat` |  Formát dat. Přepíše `Data format` sadu na `Data Connection` noži. |
| `kustoIngestionMappingReference` |  Název existujícímapování ingestování, které má být použito. Přepíše `Column mapping` sadu na `Data Connection` noži.|
| `kustoIgnoreFirstRecord` | Pokud je `true`nastavena na , Kusto ignoruje první řádek objektu blob. Pomocí dat v tabulkovém formátu (CSV, TSV nebo podobných) můžete záhlaví ignorovat. |
| `kustoExtentTags` | Řetězec představující [značky,](/azure/kusto/management/extents-overview#extent-tagging) které budou připojeny do výsledného rozsahu. |
| `kustoCreationTime` |  Přepíše [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) pro objekt blob formátovaný jako řetězec ISO 8601. Používá se pro zasypávání. |

> [!NOTE]
> Azure Data Explorer neodstraní objekty BLOB po požití.
> Ponechte kuličky po dobu pěti dnů.
> Ke správě odstranění objektů blob použijte [životní cyklus úložiště objektů Blob Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 

## <a name="review-the-data-flow"></a>Kontrola toku dat

> [!NOTE]
> Azure Data Explorer má zásady agregace (dávkování) pro ingestování dat určené k optimalizaci procesu ingestování.
Ve výchozím nastavení je zásada nakonfigurována na 5 minut.
V případě potřeby budete moci zásady později změnit. V tomto článku můžete očekávat latenci několik minut.

1. Na webu Azure Portal se v mřížce událostí zobrazí špička aktivity, když je aplikace spuštěná.

    ![Graf mřížky událostí](media/ingest-data-event-grid/event-grid-graph.png)

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

Pokud neplánujete znovu použít mřížku událostí, vyčistěte **test-hub-rg**, abyste se vyhnuli nákladům.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-grid/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-grid/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit *(test-hub-rg),* a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data v Průzkumníku dat Azure](web-query-data.md)
