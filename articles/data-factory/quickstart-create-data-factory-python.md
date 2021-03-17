---
title: 'Rychlý Start: vytvoření Azure Data Factory pomocí Pythonu'
description: Pomocí datové továrny zkopírujte data z jednoho umístění v úložišti objektů BLOB v Azure do jiného umístění.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 01/15/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: f92a09e78d65f3723b9dfa83574f603dc113ebeb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372361"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Rychlé zprovoznění: Vytvoření datové továrny a kanálu pomocí Pythonu

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto rychlém startu vytvoříte datovou továrnu pomocí Pythonu. Kanál v této datové továrně kopíruje data z jedné složky do jiné složky v úložišti objektů BLOB v Azure.

Azure Data Factory je cloudová služba pro integraci dat, která umožňuje vytvářet pracovní postupy založené na datech pro orchestraci a automatizaci přesunu dat a transformace dat. Pomocí Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty, které se nazývají kanály.

Kanály mohou ingestovat data z různorodých úložišť dat. Kanály zpracovávají nebo transformují data pomocí výpočetních služeb, jako jsou Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning. Kanály publikují výstupní data do úložišť dat, jako jsou Azure synapse Analytics pro aplikace business intelligence (BI).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Účet Azure Storage](../storage/common/storage-account-create.md).

* [Průzkumník služby Azure Storage](https://storageexplorer.com/) (volitelné).

* [Aplikace v Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Poznamenejte si následující hodnoty, které chcete použít v pozdějších krocích: **ID aplikace**, **ověřovací klíč** a **ID tenanta**. Podle pokynů ve stejném článku přiřaďte aplikaci roli **Přispěvatel** .

## <a name="create-and-upload-an-input-file"></a>Vytvoření a nahrání vstupního souboru

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho na disk jako soubor **input.txt**.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Pomocí nástrojů, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/), vytvořte kontejner **adfv2tutorial** a v něm složku **input**. Potom do složky **input** odešlete soubor **input.txt**.

## <a name="install-the-python-package"></a>Instalace balíčku Pythonu

1. Otevřete terminál nebo příkazový řádek s oprávněními správce. 
2. Nejdřív nainstalujte balíček Pythonu pro prostředky správy Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Balíček Pythonu pro službu Data Factory nainstalujete spuštěním následujícího příkazu:

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Sada Python SDK pro Data Factory](https://github.com/Azure/azure-sdk-for-python) podporuje Python 2,7, 3,3, 3,4, 3,5, 3,6 a 3,7.

4. Pokud chcete nainstalovat balíček Python pro ověřování identity Azure, spusťte následující příkaz:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > V některých běžných závislostech může být balíček Azure-identity v konfliktu s "Azure-CLI". Pokud splňujete jakýkoliv problém s ověřováním, odeberte Azure-CLI a jeho závislosti nebo použijte čistý počítač bez instalace balíčku Azure-CLI, aby fungoval.
    
## <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

1. Vytvořte soubor s názvem **datafactory.py**. Přidejte následující příkazy pro přidání odkazů na obory názvů.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Přidejte následující funkce, které tisknou informace.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy DataFactoryManagementClient. Tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Použijete ho také k monitorování podrobných informací o spuštění kanálu. Proměnnou **subscription_id** nastavte na ID vašeho předplatného Azure. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Do metody **Main** přidejte následující kód, který vytvoří **datovou továrnu**. Pokud skupina prostředků už existuje, zakomentujte první příkaz `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Vytvoření propojené služby

Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure Storage**.

V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto rychlém startu stačí jako zdroj kopírování i úložiště jímky vytvořit jednu propojenou službu Azure Storage s názvem AzureStorageLinkedService. Hodnoty `<storageaccountname>` a `<storageaccountkey>` nahraďte názvem a klíčem účtu služby Azure Storage.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady, jednu pro zdroj a druhou pro jímku.

### <a name="create-a-dataset-for-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Do metody Main přidejte následující kód, který vytvoří datovou sadu objektů blob Azure. Informace o vlastnostech datové sady objektů blob Azure najdete v článku [Konektor Azure Blob](connector-azure-blob-storage.md#dataset-properties).

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada objektů blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Vytvoření datové sady pro Azure Blob jímky

Do metody Main přidejte následující kód, který vytvoří datovou sadu objektů blob Azure. Informace o vlastnostech datové sady objektů blob Azure najdete v článku [Konektor Azure Blob](connector-azure-blob-storage.md#dataset-properties).

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada objektů blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Do metody **Main** přidejte následující kód, který vytvoří **kanál s aktivitou kopírování**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Do metody **Main** přidejte následující kód, který **aktivuje spuštění kanálu**.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

Pokud chcete monitorovat spuštění kanálu, přidejte do metody **Main** následující kód:

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Potom přidejte následující příkaz, který vyvolá metodu **main** při spuštění programu:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Celý skript

Tady je kompletní kód v Pythonu:

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojených služeb, datových sad, kanálu a spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Pak pomocí nástrojů, jako je například [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) , zkontrolujte, zda jsou objekty blob zkopírovány do "outputBlobPath" z "inputBlobPath", jak jste určili v proměnných.

Zde je ukázkový výstup:

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit datovou továrnu, přidejte do programu následující kód:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Další kroky

Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích.
