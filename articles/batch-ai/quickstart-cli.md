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
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294069"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Spuštění trénovací úlohy CNTK pomocí Azure CLI

Azure CLI 2.0 umožňuje vytvořit a spravovat prostředky Batch AI – vytvořit/odstranit souborové servery a clustery Batch AI a odeslat/ukončit/odstranit/monitorovat trénovací úlohy.

Tento rychlý start ukazuje, jak pomocí sady Microsoft Cognitive Toolkit (CNTK) vytvořit cluster GPU a spustit trénovací úlohu.

Na stránce úložiště GitHub služby Batch AI je dostupný trénovací skript [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py). Tento skript slouží k trénování konvoluční neuronové sítě v databázi MNIST ručně psaných číslic.

Oficiální příklad CNTK je upravený tak, aby prostřednictvím argumentů příkazového řádku připouštěl zadání umístění trénovací sady dat a umístění výstupního adresáře.

## <a name="quickstart-overview"></a>Přehled rychlého startu

* Vytvoříte cluster GPU s jedním uzlem (s velikostí virtuálního počítače `Standard_NC6`) a názvem `nc6`.
* Vytvoříte účet úložiště pro uložení vstupu a výstupu úlohy.
* Vytvoříte sdílenou složku Azure se dvěma složkami `logs` a `scripts`, do kterých se budou ukládat výstup úlohy a trénovací skripty.
* Vytvoříte kontejner objektů blob Azure `data`, který bude sloužit k ukládání dat pro trénink.
* Do vytvořené sdílené složky a kontejneru nasadíte trénovací skript a data pro trénink.
* Nakonfigurujete úlohu tak, aby připojila sdílenou složku Azure a kontejner objektů blob Azure k uzlu clusteru a zpřístupnila je jako běžný souborový systém ve složkách `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` a `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` je proměnná prostředí nastavená pro úlohu službou Batch AI.
* Budete monitorovat provádění úlohy prostřednictvím streamování jejího standardního výstupu.
* Po dokončení úlohy zkontrolujte její výstup a vygenerované modely.
* Nakonec odstraníte všechny přidělené prostředky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Přístup k Azure CLI 2.0 s modulem batchai verze 0.3 nebo novější. Můžete použít buď rozhraní Azure CLI 2.0 dostupné ve službě [Azure Cloud Shell](../cloud-shell/overview.md), nebo ho pomocí [těchto pokynů](/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalovat do místního prostředí.

  Pokud používáte Cloud Shell, změňte pracovní adresář na `/usr/$USER/clouddrive`, protože v domovském adresáři není žádné volné místo:

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příkaz vytvoří novou skupinu prostředků `batchai.quickstart` v umístění USA – východ:

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>Vytvoření pracovního prostoru Batch AI

Následující příkaz ve skupině prostředků vytvoří pracovní prostor Batch AI. Pracovní prostor Batch AI je kolekce nejvyšší úrovně všech typů prostředků Batch AI.

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>Vytvoření clusteru GPU

Následující příkaz v pracovním prostoru vytvoří cluster GPU s jedním uzlem (velikost virtuálního počítače je Standard_NC6), přičemž jako image operačního systému použije Ubuntu Data Science Virtual Machine:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM umožňuje spouštět veškeré trénovací úlohy v kontejnerech Dockeru a spouštět nejoblíbenější rozhraní hloubkového učení přímo na virtuálním počítači.

Možnost `--generate-ssh-keys` sděluje rozhraní Azure CLI, že má vygenerovat privátní a veřejný klíč SSH, pokud je ještě nemáte. K uzlům cluster můžete získat přístup zadáním aktuálního uživatelského jména a vygenerovaného klíče SSH.

Pokud používáte Cloud Shell, doporučuje se zálohovat složku ~/.ssh do trvalého úložiště.

Příklad výstupu:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Následující příkaz vytvoří účet úložiště ve stejné skupině prostředků použité k vytvoření clusteru Batch AI. Účet úložiště použijete k uložení vstupu a výstupu úlohy. Aktualizujte příkaz zadáním jedinečného názvu účtu úložiště.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>Nasazení dat

### <a name="download-the-training-script-and-training-data"></a>Stažení trénovacího skriptu a dat pro trénink

* Stáhněte a extrahujte předběžně zpracovanou databázi MNIST z [tohoto umístění](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) do aktuální složky.

Pro GNU/Linux nebo Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Upozorňujeme, že možná budete muset nainstalovat nástroj `unzip`, pokud ho vaše distribuce GNU/Linuxu nemá.

* Stáhněte si do aktuální složky ukázkový skript [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py):

Pro GNU/Linux nebo Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>Vytvoření sdílené složky Azure a nasazení trénovacího skriptu

Následující příkazy vytvoří sdílené složky Azure `scripts` a `logs` a zkopírují trénovací skript do složky `cntk` ve sdílené složce `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>Vytvoření kontejneru objektů blob a nasazení dat pro trénink

Následující příkazy vytvoří kontejner objektů blob Azure `data` a zkopírují data pro trénink do složky `mnist_cntk`:

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>Odeslání trénovací úlohy

### <a name="create-a-batch-ai-experiment"></a>Vytvoření experimentu Batch AI

Experiment je logický kontejner pro související úlohy Batch AI. Pomocí následujícího příkazu vytvořte experiment ve svém pracovním prostoru:

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>Příprava konfiguračního souboru úlohy

Vytvořte konfigurační soubor trénovací úlohy `job.json` s následujícím obsahem. Použijte název vašeho účtu úložiště.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Tento konfigurační soubor určuje:

* `nodeCount` – počet uzlů vyžadovaných úlohou (v případě tohoto rychlého startu 1).
* `cntkSettings` – určuje cestu k trénovacímu skriptu a argumenty příkazového řádku. Argumenty příkazového řádku zahrnují cestu k datům pro trénink a cílovou cestu pro ukládání vygenerovaných modelů. `AZ_BATCHAI_OUTPUT_MODEL` je proměnná prostředí nastavená službou Batch AI na základě konfigurace výstupního adresáře (viz níže).
* `stdOutErrPathPrefix` – cesta k umístění, kde Batch AI vytvoří adresáře obsahující výstup úlohy a protokoly.
* `outputDirectories` – kolekce výstupních adresářů, které vytvoří Batch AI. Pro každý adresář vytvoří Batch AI proměnnou prostředí s názvem `AZ_BATCHAI_OUTPUT_<id>`, kde `<id>` je identifikátor adresáře.
* `mountVolumes` – seznam systémů souborů, které se mají připojit při provádění úlohy. Systémy souborů se připojují pod `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` je proměnná prostředí nastavená službou Batch AI.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` – název účtu úložiště, který se určí při odesílání úlohy prostřednictvím proměnné prostředí `--storage-account-name parameter` nebo `AZURE_BATCHAI_STORAGE_ACCOUNT` na vašem počítači.

### <a name="submit-the-job"></a>Odeslání úlohy

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

Příklad výstupu:
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>Monitorování provádění úlohy

Trénovací skript hlásí průběh tréninku v souboru `stderr.txt` ve standardním výstupním adresáři. Průběh můžete monitorovat pomocí následujícího příkazu:

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

Příklad výstupu:
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

Po dokončení úlohy (úspěšném nebo neúspěšném) se streamování zastaví.

## <a name="inspect-generated-model-files"></a>Prohlížení vygenerovaných souborů modelů

Úloha ukládá vygenerované soubory modelů do výstupního adresáře s atributem `id`, který se rovná `MODEL`. Pomocí následujícího příkazu můžete vypsat soubory modelů a získat adresy URL pro stažení:

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

Příklad výstupu:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Vygenerované soubory si můžete prohlédnout i na webu Portal nebo v průzkumníku služby Azure Storage. K rozlišení výstupu z různých úloh vytvoří Batch AI pro každý z nich jedinečnou strukturu složek. Cestu ke složce obsahující požadovaný výstup najdete pomocí atributu `jobOutputDirectoryPathSegment` odeslané úlohy:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

Příklad výstupu:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, pomocí následujícího příkazu odstraňte skupinu prostředků a všechny přidělené prostředky:

```azurecli
az group delete -n batchai.quickstart -y
```
