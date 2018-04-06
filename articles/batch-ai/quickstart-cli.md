---
title: Rychlý start Azure – Trénování CNTK s využitím služby Batch AI – Azure CLI | Microsoft Docs
description: Rychle se naučíte spustit pomocí Azure CLI trénovací úlohu CNTK využívající službu Batch AI.
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Spuštění trénovací úlohy CNTK pomocí Azure CLI

Tento rychlý start podrobně popisuje použití rozhraní příkazového řádku (CLI) Azure ke spuštění trénovací úlohy Microsoft Cognitive Toolkit (CNTK) využívající službu Batch AI. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech.

V tomto příkladu použijete databázi MNIST ručně zapsaných obrázků k trénování konvoluční neuronové sítě (CNN) na clusteru GPU s jedním uzlem spravovaném službou Batch AI. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento rychlý start vyžaduje použití nejnovější verze Azure CLI. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

Také je potřeba pomocí služby Azure Cloud Shell nebo Azure CLI zaregistrovat pro předplatné poskytovatele prostředků služby Batch AI. Registrace poskytovatele může trvat až 15 minut.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Clustery a úlohy služby Batch AI jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create).

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*. Pak pomocí příkazu [az configure](/cli/azure/reference-index#az_configure) nastaví tuto skupinu prostředků a toto umístění jako výchozí.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Nastavení výchozích hodnot pro příkaz `az` je volitelný krok. Můžete se rozhodnout výchozí hodnoty nenastavovat. Pokud se rozhodnete výchozí hodnoty nastavit, po dokončení tohoto kurzu byste měli výchozí nastavení odebrat. Výchozí nastavení odeberete pomocí následujících příkazů:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

V tomto rychlém startu se k hostování dat a skriptů pro trénovací úlohu používá účet úložiště Azure. Účet úložiště vytvoříte příkazem [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Každý účet úložiště musí mít jedinečný název. V předchozím příkazu `az` a v dalších podobných příkazech v tomto kurzu nahraďte hodnotu nastavení `mystorageaccount` názvem svého účtu úložiště.

## <a name="prepare-azure-file-share"></a>Příprava sdílené složky Azure

Pro ilustraci se v tomto rychlém startu k hostování trénovacích dat a skriptů pro úlohu učení používá sdílená složka Azure.

1. Vytvořte sdílenou složku *batchaiquickstart* pomocí příkazu [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Ve sdílené složce vytvořte adresář *mnistcntksample* pomocí příkazu [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Stáhněte [balíček s ukázkou](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) a rozbalte ho. Nahrajte obsah do adresáře pomocí příkazu [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Vytvoření clusteru GPU
Pomocí příkazu [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) vytvořte cluster Batch AI skládající se z jediného uzlu virtuálního počítače s GPU. Virtuální počítač v tomto příkladu používá výchozí image Ubuntu LTS. Pokud místo toho chcete spustit virtuální počítač Microsoftu pro hloubkové učení, který podporuje další architektury trénování, zadejte `image UbuntuDSVM`. Velikost NC6 obsahuje jeden grafický procesor NVIDIA K80. Připojte sdílenou složku ke složce *azurefileshare*. Úplná cesta k této složce na výpočetním uzlu GPU je $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


Po vytvoření clusteru je výstup podobný tomuto:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Získání stavu clusteru

Pokud chcete získat přehled stavu clusteru, spusťte příkaz [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

Výstup je podobný tomuto:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Další podrobnosti získáte spuštěním příkazu [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Zobrazí se všechny vlastnosti clusteru od jeho vytvoření.

Cluster bude připravený po přidělení uzlů a dokončení jejich přípravy (viz atribut `nodeStateCounts`). Pokud se něco nepovede, atribut `errors` bude obsahovat popis chyby.

## <a name="create-training-job"></a>Vytvoření trénovací úlohy

Jakmile je cluster připravený, nakonfigurujte a odešlete úlohu učení.

1. Vytvořte soubor šablony JSON job.json pro vytvoření úlohy:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Pomocí příkazu [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create) vytvořte úlohu *myjob*, která se na clusteru spustí:

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

Výstup je podobný tomuto:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Monitorování úlohy

Pomocí příkazu [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) získejte přehled stavu úlohy:

```azurecli
az batchai job list -o table
```

Výstup je podobný tomuto:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Další podrobnosti získáte spuštěním příkazu [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

`executionState` obsahuje aktuální stav provádění úlohy:

* `queued`: Úloha čeká, až budou dostupné uzly clusteru.
* `running`: Úloha běží.
*   `succeeded` (nebo `failed`): Úloha je dokončená a `executionInfo` obsahuje podrobnosti o výsledku.


## <a name="list-stdout-and-stderr-output"></a>Výpis výstupu stdout a stderr
Pomocí příkazu [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) vypište odkazy na soubory protokolů výstupů stdout a stderr:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

Výstup je podobný tomuto:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Sledování výstupu

Zatímco se úloha provádí, můžete streamovat nebo sledovat výstupní soubory úlohy. Následující příklad pomocí příkazu [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) streamuje protokol stderr.txt:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

Výstup je podobný tomuto. Výstup přerušíte stisknutím Ctrl + C.

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Odstranění prostředků

Pomocí příkazu [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) odstraňte úlohu:

```azurecli
az batchai job delete --name myjob
```
Pomocí příkazu [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) odstraňte cluster:

```azurecli
az batchai cluster delete --name mycluster
```

Pomocí příkazu `az group delete` odstraňte skupinu prostředků, kterou jste pro tento rychlý start vytvořili:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Obnovení výchozího nastavení Azure CLI 2.0

Odeberte výchozí nastavení umístění a skupiny prostředků, která jste vytvořili dříve:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak spustit trénovací úlohu CNTK na clusteru Batch AI pomocí Azure CLI. Další informace o použití služby Batch AI s jinými sadami nástrojů najdete v [návodech k trénování](https://github.com/Azure/BatchAI).

Další informace o použití Azure CLI 2.0 ke správě služby Batch AI najdete v [dokumentaci na GitHubu](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
