---
title: Rychlý start Azure – Trénování CNTK s využitím služby Batch AI – Python | Microsoft Docs
description: Rychle se naučíte spustit pomocí sady Python SDK trénovací úlohu CNTK využívající službu Batch AI.
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Spuštění trénovací úlohy CNTK pomocí sady Azure Python SDK

Tento rychlý start podrobně popisuje použití sady Azure Python SDK ke spuštění trénovací úlohy Microsoft Cognitive Toolkit (CNTK) využívající službu Batch AI. 

V tomto příkladu použijete databázi MNIST ručně zapsaných obrázků k trénování konvoluční neuronové sítě (CNN) na clusteru GPU s jedním uzlem. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

* Azure Python SDK – Viz [pokyny k instalaci](/python/azure/python-sdk-azure-install).

* Účet úložiště Azure – Viz [Vytvoření účtu úložiště Azure](../storage/common/storage-create-storage-account.md).

* Přihlašovací údaje instančního objektu Azure Active Directory – Viz [Vytvoření instančního objektu pomocí rozhraní příkazového řádku](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).

* Pomocí služby Azure Cloud Shell nebo Azure CLI zaregistrujte pro své předplatné poskytovatele prostředků služby Batch AI. Registrace poskytovatele může trvat až 15 minut.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>Konfigurace přihlašovacích údajů
Ve svém skriptu Pythonu vytvořte tyto parametry a použijte vlastní hodnoty:

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

Všechny přihlašovací údaje se doporučuje ukládat do samostatného konfiguračního souboru, což ale tento příklad neukazuje. Informace o implementaci konfiguračního souboru najdete v [návodech](https://github.com/Azure/BatchAI/tree/master/recipes). 

## <a name="authenticate-with-batch-ai"></a>Ověření ve službě Batch AI

Abyste získali přístup ke službě Azure Batch AI, musíte se ověřit pomocí služby Azure Active Directory. Níže je kód pro ověření ve službě (vytvoření objektu `BatchAIManagementClient`) s použitím vašich přihlašovacích údajů instančního objektu a ID předplatného:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Clustery a úlohy služby Batch AI jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Následující fragment kódu vytvoří skupinu prostředků:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Příprava sdílené složky Azure
Pro ilustraci se v tomto rychlém startu k hostování trénovacích dat a skriptů pro úlohu učení používá sdílená složka Azure. 

1. Vytvořte sdílenou složku *batchaiquickstart*.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. Ve sdílené složce vytvořte adresář *mnistcntksample*. 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. Stáhněte [balíček s ukázkou](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) a rozbalte ho. Nahrajte obsah do adresáře, ve kterém budete spouštět skript Pythonu.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>Vytvoření clusteru GPU

Vytvořte cluster Batch AI. V tomto příkladu se cluster skládá z jediného uzlu virtuálního počítače STANDARD_NC6. Tato velikost virtuálního počítače obsahuje jeden grafický procesor NVIDIA K80. Připojte sdílenou složku ke složce *azurefileshare*. Úplná cesta k této složce na výpočetním uzlu GPU je $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>Získání stavu clusteru

Stav clusteru můžete monitorovat pomocí následujícího příkazu: 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

Předchozí kód vypíše základní informace o přidělení clusteru, mezi které patří například:

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

Cluster bude připravený po přidělení uzlů a dokončení jejich přípravy (viz atribut `nodeStateCounts`). Pokud se něco nepovede, atribut `errors` bude obsahovat popis chyby.

## <a name="create-training-job"></a>Vytvoření trénovací úlohy

Jakmile je cluster připravený, nakonfigurujte a odešlete úlohu učení. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>Monitorování úlohy
Stav úlohy si můžete prohlédnout pomocí následujícího příkazu: 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

Výstup je podobný tomuto: `Job state: running`.

`executionState` obsahuje aktuální stav provádění úlohy:
* `queued`: Úloha čeká, až budou dostupné uzly clusteru.
* `running`: Úloha běží.
* `succeeded` (nebo `failed`): Úloha je dokončená a `executionInfo` obsahuje podrobnosti o výsledku.
 
## <a name="list-stdout-and-stderr-output"></a>Výpis výstupu stdout a stderr
Pomocí následujícího příkazu vypište odkazy na soubory protokolů výstupů stdout a stderr:

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>Odstranění prostředků

Pomocí následujícího příkazu odstraňte úlohu:
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

Pomocí následujícího příkazu odstraňte cluster:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak spustit trénovací úlohu CNTK na clusteru Batch AI pomocí sady Azure Python SDK. Další informace o použití služby Batch AI s jinými sadami nástrojů najdete v [návodech k trénování](https://github.com/Azure/BatchAI).
