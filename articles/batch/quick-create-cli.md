---
title: Rychlý start Azure – spuštění úlohy služby Batch – rozhraní příkazového řádku
description: Můžete se rychle naučit, jak pomocí Azure CLI spustit úlohu služby Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 75703b4b10bbf3864076952990df2e6c2a9ab28d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969644"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Rychlý start: Spuštění první úlohy služby Batch pomocí rozhraní příkazového řádku Azure

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí Azure CLI vytvořit účet Batch, *fond* výpočetních uzlů (virtuálních počítačů) a *úlohu*, která bude ve fondu spouštět *úkoly*. Každý ukázkový úkol spustí základní příkaz na jednom z uzlů fondu. Po dokončení tohoto rychlého startu budete rozumět klíčovým konceptům služby Batch a budete moct službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet Batch můžete propojit s účtem Azure Storage. I když to k tomuto rychlému startu není nutné, účet úložiště je užitečný při nasazování aplikací a ukládání vstupních a výstupních dat ve většině reálných způsobů využití. Ve vaší skupině prostředků vytvořte účet úložiště pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create).

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

K vytvoření účtu Batch použijte příkaz [az batch account create](/cli/azure/batch/account#az-batch-account-create). Účet potřebujete pro vytvoření výpočetních prostředků (fondy výpočetních uzlů) a úloh služby Batch.

Následující příkaz vytvoří účet služby Batch s názvem *mybatchaccount* v *myResourceGroup* a propojí účet úložiště, který jste vytvořili.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

K vytváření a správě výpočetních fondů a úloh musíte využít ověřování pomocí služby Batch. Přihlaste se k účtu pomocí příkazu [az batch account login](/cli/azure/batch/account#az-batch-account-login). Po přihlášení budou příkazy `az batch` používat kontext tohoto účtu.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Vytvoření fondu výpočetních uzlů

Teď, když máte účet služby Batch, vytvořte ukázkový fond linuxových výpočetních uzlů pomocí příkazu [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). Následující příkaz vytvoří fond nazvaný *mypool* se 2 uzly velikosti *Standard_A1_v2*, na kterých běží Ubuntu 16.04 LTS. Navržená velikost uzlu nabízí pro tento rychlý příklad dobrou rovnováhu mezi výkonem a náklady.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

Služba Batch vytvoří fond okamžitě, ale přidělení a spuštění uzlů úložiště bude několik minut trvat. Během této doby je fond ve stavu `resizing`. K zobrazení stavu fondu můžete použít příkaz [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Tento příkaz zobrazí všechny vlastnosti fondu a můžete zadat dotaz na specifické vlastnosti. Následující příkaz zobrazí stav přidělení fondu:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Dokud se stav fondu mění, pokračujte v následujících krocích pro vytváření úloh a úkolů. Fond je připravený ke spuštění úkolů v okamžiku, kdy stav přidělení je `steady` a všechny uzly jsou spuštěné. 

## <a name="create-a-job"></a>Vytvoření úlohy

Teď máte vytvořený fond a můžete vytvořit úlohu, která se v něm bude spouštět.  Úloha služby Batch je logická skupina jednoho nebo víc úkolů. Úloha zahrnuje nastavení společná všem úkolům, jako je priorita a fond, ve kterém se mají úkoly spouštět. Vytvořte úlohu služby Batch pomocí příkazu [az batch job create](/cli/azure/batch/job#az-batch-job-create). Následující příkaz vytvoří úlohu *myjob* ve fondu *mypool*. Na začátku úloha neobsahuje žádné úkoly.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Vytvoření úkolů

Teď použijte příkaz [az batch task create](/cli/azure/batch/task#az-batch-task-create) pro vytvoření úkolů ke spuštění v této úloze. V tomto příkladu vytvoříte čtyři stejné úkoly. Každý úkol spustí `command-line`, kde se zobrazí proměnné prostředí služby Batch ve výpočetním uzlu, a potom 90 sekund čeká. Při použití služby Batch se aplikace nebo skript zadávají právě na příkazovém řádku. Služba Batch poskytuje několik způsobů, jak nasazovat aplikace a skripty do výpočetních uzlů.

Následující skript Bash vytvoří 4 paralelní úkoly (*mytask1* až *mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

Výstup příkazu ukazuje nastavení pro jednotlivé úkoly. Služba Batch distribuuje tyto úkoly na výpočetní uzly.

## <a name="view-task-status"></a>Zobrazení stavu úkolů

Po vytvoření služba Batch zařadí úkol do fronty pro spuštění ve fondu. Jakmile bude dostupný uzel, který ho bude moct spustit, úkol se spustí.

K zobrazení stavu úkolů služby Batch použijte příkaz [az batch task show](/cli/azure/batch/task#az-batch-task-show). Následující příklad ukazuje údaje o úkolu *mytask1* spuštěném v jednom z uzlů fondu.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

Výstup tohoto příkazu obsahuje řadu podrobností, ale poznamenejte si `exitCode` příkazového řádku úkolu a `nodeId`. Hodnota `exitCode` rovná 0 indikuje, že se příkazový řádek úkolu úspěšně dokončil. Hodnota `nodeId` udává ID uzlu fondu, ve kterém se úkol spustil.

## <a name="view-task-output"></a>Zobrazení výstupu úkolu

K zobrazení seznamu souborů, které úkol vytvořil ve výpočetním uzlu, použijte příkaz [az batch task file list](/cli/azure/batch/task#az-batch-task-file-list). Následující příkaz zobrazí seznam souborů, které vytvořil úkol *mytask1*: 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

Výstup je podobný tomuto:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Pokud chcete některý z výstupních souborů stáhnout do místního adresáře, použijte příkaz [az batch task file download](/cli/azure/batch/task#az-batch-task-file-download). V tomto příkladu je výstup úkolu v souboru `stdout.txt`. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Obsah souboru `stdout.txt` můžete zobrazit v textovém editoru. Obsah zahrnuje proměnné prostředí služby Azure Batch nastavené v uzlu. Při vytváření vlastních úloh služby Batch můžete na tyto proměnné prostředí odkazovat na příkazových řádcích úkolů a v aplikacích a skriptech spouštěných těmito příkazovými řádky. Příklad:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v prohlížení kurzů a ukázek služby Batch, použijte účet Batch a propojený účet úložiště, které jste vytvořili v tomto rychlém startu. Za samotný účet Batch se neúčtují žádné poplatky.

Poplatky se účtují za fondy, ve kterých jsou spuštěné uzly, i když nejsou naplánované žádné úlohy. Pokud nějaký fond už nepotřebujete, odstraňte ho pomocí příkazu [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. 

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, účtu Batch, fondů a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete). Prostředky odstraňte následujícím způsobem:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Batch, fond služby Batch a úlohu služby Batch. Úloha spustila ukázkové úkoly a prohlédli jste si výstup vytvořený v jednom z uzlů. Teď chápete klíčové koncepty služby Batch a můžete službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě. Další informace o službě Azure Batch najdete v dalších kurzech o službě Azure Batch. 


> [!div class="nextstepaction"]
> [Kurzy o službě Azure Batch](./tutorial-parallel-dotnet.md)
