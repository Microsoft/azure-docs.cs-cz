---
title: Trénování modelu CNTK ve službě Azure Batch AI – Python | Microsoft Docs
description: Trénování neurální sítě Microsoft Cognitive Toolkit (CNTK) v Azure Batch AI pomocí sady Python SDK
services: batch-ai
documentationcenter: na
author: lliimsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 86ce9fd77ccf83ed6f5f1c722b4c8c809ef0eedf
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407911"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Spuštění trénovací úlohy CNTK pomocí sady Azure Python SDK

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

V tomto článku si ukážeme jak používat sadu Azure Python SDK k trénování ukázkového modelu Microsoft Cognitive Toolkit (CNTK) ve službě Batch AI.

V tomto příkladu použijete databázi MNIST ručně zapsaných obrázků k trénování konvoluční neuronové sítě (CNN) na clusteru GPU s jedním uzlem.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Azure Python SDK – Viz [pokyny k instalaci](/python/azure/python-sdk-azure-install). Tento článek vyžaduje alespoň balíček azure-mgmt-batchai verze 2.0.0.

* Účet úložiště Azure – Viz [Vytvoření účtu úložiště Azure](../storage/common/storage-create-storage-account.md).

* Přihlašovací údaje instančního objektu Azure Active Directory – Viz [Vytvoření instančního objektu pomocí rozhraní příkazového řádku](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).

* Pomocí služby Azure Cloud Shell nebo Azure CLI zaregistrujte pro své předplatné poskytovatele prostředků služby Batch AI. Registrace poskytovatele může trvat až 15 minut.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Konfigurace přihlašovacích údajů

Přidejte do souboru skriptu následující kód a nahraďte `FILL-IN-HERE` příslušnými hodnotami:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'

# specify the location in which to create Batch AI resources
mylocation = 'eastus'
```

Mějte na paměti, že vložení přihlašovacích údajů do zdrojového kódu není vhodné a zde je použito, aby byl rychlý start jednodušší.
Zvažte místo toho použití proměnných prostředí nebo samostatného konfiguračního souboru.

## <a name="create-batch-ai-client"></a>Vytvoření klienta Batch AI

Následující kód vytvoří objekt přihlašovacích údajů instančního objektu a klienta Batch AI:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Clustery a úlohy služby Batch AI jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Následující fragment kódu vytvoří skupinu prostředků:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': mylocation})
```


## <a name="prepare-azure-file-share"></a>Příprava sdílené složky Azure

Pro ilustraci se v tomto rychlém startu k hostování trénovacích dat a skriptů pro úlohu učení používá sdílená složka Azure File.

Vytvořte sdílenou složku `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

Ve sdílené složce vytvořte adresář `mnistcntksample`.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False)
```
Stáhněte [ukázkový balíček](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) a rozbalte do aktuálního adresáře. Následujícím kódem nahrajete požadované soubory do sdílené složky Azure:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-batch-ai-workspace"></a>Vytvoření pracovního prostoru Batch AI

Pracovní prostor je kolekce nejvyšší úrovně všech typů prostředků Batch AI. V pracovním prostoru vytvoříte cluster a experimenty Batch AI.

```Python
workspace_name='myworkspace'
batchai_client.workspaces.create(resource_group_name, workspace_name, mylocation)
```

## <a name="create-gpu-cluster"></a>Vytvoření clusteru GPU

Vytvořte cluster Batch AI. V tomto příkladu se cluster skládá z jediného uzlu virtuálního počítače STANDARD_NC6. Tato velikost virtuálního počítače obsahuje jeden grafický procesor NVIDIA K80. Připojte sdílenou složku ke složce `azurefileshare`. Úplná cesta této složky na výpočetním uzlu grafického procesoru je `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # VM size. Use N-series for GPU
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
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, workspace_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Získání stavu clusteru

Stav clusteru můžete monitorovat pomocí následujícího příkazu:

```Python
cluster = batchai_client.clusters.get(resource_group_name, workspace_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Předchozí kód vypíše základní informace o přidělení clusteru, jako v následujícím příkladu:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

Cluster bude připravený po přidělení uzlů a dokončení jejich přípravy (viz atribut `nodeStateCounts`). Pokud se něco nepovede, atribut `errors` bude obsahovat popis chyby.

## <a name="create-experiment-and-training-job"></a>Vytvoření experimentu a trénovací úlohy

Po vytvoření clusteru vytvořte experiment (logický kontejner pro skupinu souvisejících úloh). Potom v experimentu nakonfigurujte a odešlete úlohu učení:

```Python
experiment_name='myexperiment'

batchai_client.experiments.create(resource_group_name, workspace_name, experiment_name)

job_name = 'myjob'

parameters = models.JobCreateParameters(
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Monitorování úlohy

Stav úlohy si můžete prohlédnout pomocí následujícího kódu:

```Python
job = batchai_client.jobs.get(resource_group_name, workspace_name, experiment_name, job_name)

print('Job state: {0} '.format(job.execution_state))
```

Výstup je podobný tomuto: `Job state: running`.

`executionState` obsahuje aktuální stav provádění úlohy:
* `queued`: Úloha čeká, až budou dostupné uzly clusteru.
* `running`: Úloha běží.
* `succeeded` (nebo `failed`): Úloha je dokončená a `executionInfo` obsahuje podrobnosti o výsledku.

## <a name="list-stdout-and-stderr-output"></a>Výpis výstupu stdout a stderr

Pomocí následujícího kódu vytvořte výpis vygenerovaných výstupů stdout a stderr a souborů protokolů:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Výpis vygenerovaných souborů modelů
Pomocí následujícího kódu vytvořte výpis vygenerovaných souborů modelů:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, workspace_name, experiment_name,job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Odstranění prostředků

Pomocí následujícího kódu odstraňte úlohu:
```Python
batchai_client.jobs.delete(resource_group_name, workspace_name, experiment_name, job_name)
```

Pomocí následujícího kódu odstraňte cluster:
```Python
batchai_client.clusters.delete(resource_group_name, workspace_name, cluster_name)
```

Pomocí následujícího kódu odstraňte všechny přidělené prostředky:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Další postup

Další informace o použití služby Batch AI na různých platformách najdete v článku s [návody na trénování](https://github.com/Azure/BatchAI).