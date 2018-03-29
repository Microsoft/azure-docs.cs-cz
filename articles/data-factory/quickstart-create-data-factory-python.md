---
title: Vytvoření datové továrny Azure pomocí Pythonu | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure ke zkopírování dat z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: python
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 83367d85e70a1a83850388c4cb786e93ae194223
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-and-pipeline-using-python"></a>Vytvoření datové továrny a kanálu pomocí Pythonu
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](quickstart-create-data-factory-python.md)

Azure Data Factory je cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály) se schopností ingestovat data z různorodých úložišť dat, zpracovat a transformovat tato data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning, a publikovat výstupní data do úložišť dat, jako je Azure SQL Data Warehouse, aby je mohly využívat aplikace business intelligence (BI).

Tento rychlý start popisuje použití Pythonu k vytvoření datové továrny Azure. Kanál v této datové továrně kopíruje data z jedné složky do jiné složky v úložišti objektů blob Azure.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [úvod do služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako úložiště dat pro **zdroj** a **jímku**. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **V Azure Active Directory vytvořte aplikaci** s využitím [těchto pokynů](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Poznamenejte si následující hodnoty, které použijete v dalších krocích: **ID aplikace**, **ověřovací klíč** a **ID tenanta**. Podle pokynů ve stejném článku přiřaďte aplikaci roli **Přispěvatel**.

### <a name="create-and-upload-an-input-file"></a>Vytvoření a nahrání vstupního souboru

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho na disk jako soubor **input.txt**.

    ```
    John|Doe
    Jane|Doe
    ```
2.  Pomocí nástrojů, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/), vytvořte kontejner **adfv2tutorial** a v něm složku **input**. Potom do složky **input** odešlete soubor **input.txt**.

## <a name="install-the-python-package"></a>Instalace balíčku Pythonu
1. Otevřete terminál nebo příkazový řádek s oprávněními správce. 
2. Nejdřív nainstalujte balíček Pythonu pro prostředky správy Azure:

    ```
    pip install azure-mgmt-resource
    ```
3. Balíček Pythonu pro službu Data Factory nainstalujete spuštěním následujícího příkazu:

    ```
    pip install azure-mgmt-datafactory
    ```

    Sada [Python SDK pro službu Data Factory](https://github.com/Azure/azure-sdk-for-python) podporuje Python 2.7, 3.3, 3.4, 3.5 a 3.6.

## <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

1. Vytvořte soubor s názvem **datafactory.py**. Přidejte následující příkazy pro přidání odkazů na obory názvů.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
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
3. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy DataFactoryManagementClient. Tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Použijete ho také k monitorování podrobných informací o spuštění kanálu. Proměnnou **subscription_id** nastavte na ID vašeho předplatného Azure. Data Factory V2 v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

    ```python   
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}    
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Do metody **Main** přidejte následující kód, který vytvoří **datovou továrnu**. Pokud skupina prostředků už existuje, zakomentujte první příkaz `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
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
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
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
    ds_ls = LinkedServiceReference(ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Vytvoření datové sady pro objekt blob Azure jímky
Do metody Main přidejte následující kód, který vytvoří datovou sadu objektů blob Azure. Informace o vlastnostech datové sady objektů blob Azure najdete v článku [Konektor Azure Blob](connector-azure-blob-storage.md#dataset-properties).

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada objektů blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Do metody **Main** přidejte následující kód, který vytvoří **kanál s aktivitou kopírování**.

```python
    # Create a copy activity
    act_name =  'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(ds_name)
    dsOut_ref = DatasetReference(dsOut_name)
    copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name =  'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```


## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Do metody **Main** přidejte následující kód, který **aktivuje spuštění kanálu**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name,
        {
        }
    )
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu
Pokud chcete monitorovat spuštění kanálu, přidejte do metody **Main** následující kód:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
    print_activity_run_details(activity_runs_paged[0])
```

Potom přidejte následující příkaz, který vyvolá metodu **main** při spuštění programu:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Celý skript
Tady je kompletní kód v Pythonu:

```python
from azure.common.credentials import ServicePrincipalCredentials
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
    print("\n")        

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")

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
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'        

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name =  'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(ds_name)
    dsOut_ref = DatasetReference(dsOut_name)
    copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name =  'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name,
        {
        }
    )

    # Monitor the pipeilne run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
    print_activity_run_details(activity_runs_paged[0])

# Start the main method
main()
```

## <a name="run-the-code"></a>Spuštění kódu
Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojených služeb, datových sad, kanálu a spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Potom použijte nástroj, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), a zkontrolujte, že se objekty blob zkopírovaly z inputBlobPath do outputBlobPath, jak jste zadali v proměnných.

Zde je ukázkový výstup:

```json
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
adf_client.factories.delete(rg_name,df_name)
```

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích.
