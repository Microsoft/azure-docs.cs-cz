---
title: Rychlý start Azure – trénování hloubkového učení – Azure CLI | Microsoft Docs
description: Rychlý start – naučte se vytrénovat neuronovou síť hloubkového učení TensorFlow na jednom grafickém procesoru (GPU) se službou Batch AI pomocí Azure CLI.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408064"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Rychlý start: Trénování modelu obsáhlý learning s využitím služby Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Tento rychlý start popisuje, jak vytrénovat model hloubkového učení na virtuálním počítači s GPU spravovaném službou Batch AI. Batch AI je spravovaná služba určená pro odborníky na data a výzkumníky v oblasti umělé inteligence. Umožňuje jim trénovat modely AI a strojového učení požadované velikosti v clusterech virtuálních počítačů Azure. 

V tomto příkladu nastavíte službu Batch AI pro trénování ukázkové neuronové sítě [TensorFlow](https://www.tensorflow.org/) na [databázi MNIST](http://yann.lecun.com/exdb/mnist/) ručně psaných číslic pomocí Azure CLI. Po dokončení tohoto rychlého startu budete rozumět klíčovým konceptům použití služby Batch AI k trénování modelu umělé inteligence nebo strojového učení a budete připraveni k trénování různých modelů ve větším měřítku.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

V tomto rychlém startu se předpokládá, že příkazy spouštíte v prostředí Bash, a to buď v prostředí Cloud Shell, nebo na místním počítači. Pokud jste už dokončili rychlý start věnovaný [vytvoření clusteru Batch AI pomocí Azure CLI](quickstart-create-cluster-cli.md), přeskočte první dva kroky, které vytvoří skupinu prostředků a cluster Batch AI.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus2*. Nezapomeňte zvolit umístění Východ USA 2 nebo jiné místo, kde je služba Batch AI dostupná. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Vytvoření clusteru Batch AI

Nejprve příkazem `az batchai workspace create` vytvořte *pracovní prostor* služby Batch AI. Pracovní prostor potřebujete k uspořádání clusterů Batch AI a jiných prostředků.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

K vytvoření clusteru Batch AI použijte příkaz `az batchai cluster create`. Následující příklad vytvoří cluster s jedním uzlem a následujícími vlastnostmi:

* Používá virtuální počítač velikosti NC6, který má jednu GPU NVIDIA Tesla K80. Azure nabízí několik velikostí virtuálních počítačů s různými GPU společnosti NVIDIA.
* Používá výchozí image Ubuntu Server určenou k hostování kontejnerových aplikací. Na této distribuci můžete spouštět většinu trénovacích úloh. 
* Přidá uživatelský účet s názvem *myusername* a vygeneruje klíče SSH, pokud už neexistují ve výchozím umístění klíčů (*~/.ssh*) v místním prostředí.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Výstup tohoto příkazu zobrazuje vlastnosti clusteru. Vytvoření a spuštění uzlu trvá několik minut. Stav clusteru zobrazíte spuštěním příkazu `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

V rané fázi vytváření clusteru se výstup podobná následujícímu, který udává, že cluster je ve stavu `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Pokračujte následujícími kroky a během toho, co se mění stav clusteru, nahrajte trénovací skript a vytvořte trénovací úlohu. Cluster je připravený ke spuštění trénovací úlohy, když jeho stav je `steady` a jeden uzel je `Idle`.

## <a name="upload-training-script"></a>Nahrání trénovacího skriptu

Pomocí příkazu `az storage account create` vytvořte účet úložiště, do kterého se uloží trénovací skript a výstup.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Pomocí příkazu `az storage share create` vytvořte v tomto účtu sdílenou složku Azure s názvem `myshare`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Příkazem `az storage directory create` vytvořte v této sdílené složce Azure adresáře. Vytvořte adresář `scripts` pro trénovací skript a adresář `logs` pro trénovací výstup:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

V prostředí Bash vytvořte místní pracovní adresář a stáhněte si ukázku [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) pro TensorFlow. Tento skript Pythonu trénuje konvoluční neuronovou síť na sadě obrázků MNIST se 60 000 rukou psaných číslic od 0 do 9. Pak model otestuje na sadě testovacích příkladů.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Pomocí příkazu `az storage file upload` nahrajte tento skript do adresáře `scripts` ve sdílené složce.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Odeslání trénovací úlohy

Napřed příkazem `az batchai experiment create` vytvořte v pracovním prostoru *experiment* služby Batch AI. Experiment je logický kontejner pro související úlohy Batch AI.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

V pracovním adresáři vytvořte konfigurační soubor trénovací úlohy `job.json` s následujícím obsahem. Tento konfigurační soubor předáte při odeslání trénovací úlohy.

Soubor `job.json` obsahuje nastavení pro vyhledání souboru skriptu Pythonu a jeho spuštění v kontejneru TensorFlow na uzlu GPU. Určuje také, kam se mají uložit výstupní soubory této úlohy v úložišti Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` udává, že název účtu úložiště bude zadán během odesílání úlohy.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Pomocí příkazu `az batchai job create` odešlete úlohu do uzlu a předejte konfigurační soubor `job.json` a název účtu úložiště:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

Tento příkaz vrátí vlastnosti úlohy a jeho dokončení trvá několik minut. Pokud chcete sledovat průběh této úlohy, použijte příkaz `az batchai job file stream` ke streamování souboru `stdout-wk-0.txt` ze standardního výstupního adresáře na uzlu. Trénovací skript tento soubor vygeneruje po spuštění úlohy.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Příklad výstupu:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

Streamování se zastaví po dokončení úlohy. Ukázkový skript trénuje v 10 *epochách*, neboli průchodech trénovací sadou dat. V tomto příkladu má trénovaný model po 10 epochách chybovost jen 0,8 %.

## <a name="get-job-output"></a>Získání výstupu úlohy

Batch AI vytvoří v účtu úložiště pro každý výstup úlohy jedinečnou strukturu složek. Proměnnou prostředí JOB_OUTPUT_PATH nastavte s touto cestou. Pak pomocí příkazu `az storage file list` vytvořte seznam výstupních souborů v úložišti:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

Výstup se podobá tomuto:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Příkazem `az storage file download` stáhněte jeden nebo několik souborů do místního pracovního adresáře. Příklad:

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v dalších kurzech a ukázkách k Batch AI, použijte pracovní prostor Batch AI, cluster a účet úložiště, které jste vytvořili v tomto rychlém startu. 

Cluster Batch AI se vám účtuje, když běží jeho uzly. Pokud chcete zachovat konfiguraci clusteru, i když nejsou spuštěné žádné úlohy, změňte velikost clusteru na 0 uzlů. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Později můžete velikost změnit na 1 nebo více uzlů, na kterých spustíte své úlohy. Když už cluster nepotřebujete, odstraňte ho pomocí příkazu `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Příkazem `az group delete` můžete odebrat skupinu prostředků pro Batch AI a prostředky úložiště, pokud je už nepotřebujete. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste se dozvěděli, jak použít Batch AI k trénování ukázkového modelu hloubkového učení TensorFlow na virtuálním počítači s jedním grafickým procesorem pomocí Azure CLI. Pokud chcete zjistit další informace o tom, jak trénování modelu distribuovat na cluster s více GPU, pokračujte kurzem služby Batch AI.

> [!div class="nextstepaction"]
> [Kurz distribuovaného trénování](./tutorial-horovod-tensorflow.md)