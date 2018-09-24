---
title: Vykreslení scény v cloudu – Azure Batch
description: Kurz – Vykreslení scény Autodesk 3ds Max s využitím Arnoldu, služby Batch Rendering a rozhraní příkazového řádku Azure
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: tutorial
ms.date: 06/18/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f721af16e894ec24e85cdb1ff100d83d58ffadd8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954598"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Kurz: Vykreslení scény pomocí služby Azure Batch 

Azure Batch nabízí možnosti vykreslování v cloudovém měřítku a s platbami za použití. Služba Batch Rendering podporuje vykreslovací aplikace, včetně aplikací Autodesk Maya, 3ds Max, Arnold a V-Ray. V tomto kurzu najdete kroky pro vykreslení malé scény s využitím služby Batch a rozhraní příkazového řádku Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nahrání scény do úložiště Azure
> * Vytvoření fondu služby Batch pro účely vykreslování
> * Vykreslení scény s jedním snímkem
> * Škálování fondu a vykreslení scény s více snímky
> * Stažení vykresleného výstupu

V tomto kurzu vykreslíte scénu 3ds Max s využitím služby Batch a rendereru se sledováním paprsků [Arnold](https://www.autodesk.com/products/arnold/overview). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Ukázková scéna 3ds Max pro tento kurz je na [GitHubu](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene) společně s ukázkovým skriptem Bash a konfiguračními soubory JSON. Scéna 3ds Max je převzatá z [ukázkových souborů pro Autodesk 3ds Max](http://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Ukázkové soubory pro Autodesk 3ds Max jsou dostupné v rámci licence Creative Commons Attribution-NonCommercial-Share Alike. Copyright © Autodesk, Inc.)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI ve verzi 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

Pokud jste to ještě neudělali, vytvořte ve svém předplatném skupinu prostředků, účet Batch a propojený účet úložiště. 

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

Ve vaší skupině prostředků vytvořte účet služby Azure Storage pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create). Pro účely tohoto kurzu použijete účet úložiště k uložení vstupní scény 3ds Max a vykresleného výstupu.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
K vytvoření účtu Batch použijte příkaz [az batch account create](/cli/azure/batch/account#az-batch-account-create). Následující příkaz vytvoří účet Batch s názvem *mybatchaccount* ve skupině prostředků *myResourceGroup* a propojí ho s účtem úložiště, který jste vytvořili.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

K vytváření a správě výpočetních fondů a úloh musíte využít ověřování pomocí služby Batch. Přihlaste se k účtu pomocí příkazu [az batch account login](/cli/azure/batch/account#az-batch-account-login). Po přihlášení budou příkazy `az batch` používat kontext tohoto účtu. Následující příklad používá ověřování pomocí sdíleného klíče na základě názvu a klíče účtu Batch. Pro účely ověřování jednotlivých uživatelů nebo bezobslužných aplikací služba Batch podporuje také ověřování prostřednictvím [Azure Active Directory](batch-aad-auth.md).

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Nahrání scény do úložiště

Pokud chcete do úložiště nahrát vstupní scénu, musíte nejprve přejít do účtu úložiště a vytvořit v něm cílový kontejner pro objekty blob. Přístup k účtu úložiště Azure získáte tak, že exportujete proměnné prostředí `AZURE_STORAGE_KEY` a `AZURE_STORAGE_ACCOUNT`. První příkaz prostředí Bash pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) získá první klíč účtu. Po nastavení těchto proměnných prostředí budou příkazy úložiště používat kontext tohoto účtu.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Teď v účtu úložiště vytvořte kontejner objektů blob pro soubory scény. Následující příklad pomocí příkazu [az storage container create](/cli/azure/storage/container#az-storage-container-create) vytvoří kontejner objektů blob s názvem *scenefiles* a s veřejným oprávněním ke čtení.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Stáhněte scénu `MotionBlur-Dragon-Flying.max` z [GitHubu](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) do místního pracovního adresáře. Příklad:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Nahrajte soubor scény z místního pracovního adresáře do kontejneru objektů blob. Následující příklad používá příkaz [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch), který dokáže nahrát několik souborů:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Vytvoření fondu pro vykreslování

Pomocí příkazu [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) vytvořte fond služby Batch pro účely vykreslování. V tomto příkladu zadáte nastavení fondu v souboru JSON. V rámci aktuálního prostředí vytvořte soubor *mypool.json* a pak do něj zkopírujte a vložte následující obsah. Ujistěte se, že se veškerý text zkopíroval správně. (Tento soubor si můžete stáhnout z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.2.1"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch podporuje vyhrazené uzly a uzly [s nízkou prioritou](batch-low-pri-vms.md) a ve svých fondech můžete použít oba typy. Vyhrazené uzly jsou rezervované pro váš fond. Uzly s nízkou prioritou pocházejí z přebytečné kapacity virtuálních počítačů v Azure a nabízejí se za nižší cenu. Pokud Azure nemá dostatek kapacity, uzly s nízkou prioritou budou nedostupné. 

Zadaný fond obsahuje jediný uzel s nízkou prioritou, na kterém je spuštěná image Windows Serveru se softwarem pro službu Batch Rendering. Tento fond má licenci na vykreslování v aplikaci 3ds Max a Arnoldu. V pozdějším kroku budete fond škálovat na větší počet uzlů.

Vytvořte fond předáním souboru JSON do příkazu `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Zřízení fondu trvá několik minut. K zobrazení stavu fondu můžete použít příkaz [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Následující příkaz zobrazí stav přidělení fondu:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Dokud se stav fondu mění, pokračujte v následujících krocích pro vytváření úloh a úkolů. Fond je zcela zřízený v okamžiku, kdy stav přidělení je `steady` a uzly jsou spuštěné.  

## <a name="create-a-blob-container-for-output"></a>Vytvoření kontejneru objektů blob pro výstup

V příkladech v tomto kurzu každý úkol v úloze vykreslování vytváří výstupní soubor. Před plánováním úloh vytvořte ve svém účtu úložiště kontejner objektů blob jako cíl pro výstupní soubory. Následující příklad pomocí příkazu [az storage container create](/cli/azure/storage/container#az-storage-container-create) vytvoří kontejner *job-myrenderjob* s veřejným oprávněním ke čtení. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

K zápisu výstupních souborů do kontejneru musí služba Batch použít token sdíleného přístupového podpisu (SAS). Vytvořte token pomocí příkazu [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). Tento příklad vytvoří token, který umožňuje zápis do jakéhokoli kontejneru objektů blob v účtu a jehož platnost vyprší 15. listopadu 2018:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2018-11-15
```

Poznamenejte si token vrácený příkazem, který je podobný následujícímu. Tento token použijete později.

```
se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Vykreslení scény s jedním snímkem

### <a name="create-a-job"></a>Vytvoření úlohy

Pomocí příkazu [az batch job create](/cli/azure/batch/job#az-batch-job-create) vytvořte úlohu vykreslování, která se spustí ve fondu. Na začátku úloha neobsahuje žádné úkoly.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Vytvoření úkolu

Pomocí příkazu [az batch task create](/cli/azure/batch/task#az-batch-task-create) vytvořte v úloze úkol vykreslování. V tomto příkladu zadáte nastavení úkolu v souboru JSON. V rámci aktuálního prostředí vytvořte soubor *myrendertask.json* a pak do něj zkopírujte a vložte následující obsah. Ujistěte se, že se veškerý text zkopíroval správně. (Tento soubor si můžete stáhnout z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

Úkol určuje příkaz aplikace 3ds Max, který vykreslí jediný snímek scény *MotionBlur-DragonFlying.max*.

Upravte v souboru JSON elementy `blobSource` a `containerURL` tak, aby obsahovaly název vašeho účtu úložiště a váš token SAS. 

> [!TIP]
> Vaše adresa `containerURL` končí vaším tokenem SAS a vypadá přibližně takto:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "blobSource": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Přidejte úkol do úlohy pomocí následujícího příkazu:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Služba Batch úkol naplánuje a ten se okamžitě spustí, jakmile bude ve fondu dostupný nějaký uzel.


### <a name="view-task-output"></a>Zobrazení výstupu úkolu

Spuštění úkolu trvá několik minut. K zobrazení podrobností o úkolu použijte příkaz [az batch task show](/cli/azure/batch/task#az-batch-task-show).

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

Úkol na výpočetním uzlu vygeneruje soubor *dragon0001.jpg* a nahraje ho do kontejneru *job-myrenderjob* ve vašem účtu úložiště. Pokud chcete zobrazit výstup, stáhněte soubor z úložiště do svého místního počítače pomocí příkazu [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Otevřete na svém počítači soubor *dragon.jpg*. Vykreslený obrázek vypadá přibližně takto:

![Vykreslený snímek s drakem 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Škálování fondu

Teď fond upravíte, abyste ho připravili na větší úlohu vykreslování s několika snímky. Služba Batch nabízí řadu způsobů, jak škálovat výpočetní prostředky, včetně [automatického škálování](batch-automatic-scaling.md), které přidává a odebírá uzly v závislosti na měnících se požadavcích úkolů. Pro účely tohoto základního příkladu zvyšte pomocí příkazu [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) počet uzlů s nízkou prioritou ve fondu na *6*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Změna velikosti fondu trvá několik minut. Zatímco proces probíhá, nastavte další úkoly pro spuštění v rámci stávající úlohy vykreslování.

## <a name="render-a-multiframe-scene"></a>Vykreslení scény s více snímky

Podobně jako v příkladu s jedním snímkem vytvořte pomocí příkazu [az batch task create](/cli/azure/batch/task#az-batch-task-create) úkoly vykreslování v úloze *myrenderjob*. Tady zadejte nastavení úkolů v souboru JSON s názvem *myrendertask_multi.json*. (Tento soubor si můžete stáhnout z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) Každý z šesti úkolů určuje příkazový řádek Arnoldu, kterým se vykreslí jeden snímek scény 3ds Max *MotionBlur-DragonFlying.max*.

V aktuálním prostředí vytvořte soubor *myrendertask_multi.json* a zkopírujte a vložte do něj obsah staženého souboru. Upravte v souboru JSON elementy `blobSource` a `containerURL` tak, aby obsahovaly název vašeho účtu úložiště a váš token SAS. Nezapomeňte změnit nastavení pro každý z šesti úkolů. Uložte soubor a spusťte následující příkaz, který zařadí úkoly do fronty:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Zobrazení výstupu úkolu

Spuštění úkolu trvá několik minut. K zobrazení stavu úkolů použijte příkaz [az batch task list](/cli/azure/batch/task#az-batch-task-list). Příklad:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

K zobrazení podrobností o jednotlivých úkolech použijte příkaz [az batch task show](/cli/azure/batch/task#az-batch-task-show). Příklad:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Úkoly na výpočetních uzlech vygenerují výstupní soubory s názvy *dragon0002.jpg* - *dragon0007.jpg* a nahrají je do kontejneru *job-myrenderjob* ve vašem účtu úložiště. Pokud chcete zobrazit výstup, stáhněte soubory do složky na svém místním počítači pomocí příkazu [az storage blob download-batch](/cli/azure/storage/blob#az-storage-blob-download_batch). Příklad:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Otevřete na svém počítači jeden ze souborů. Vykreslený snímek 6 vypadá přibližně takto:

![Vykreslený snímek s drakem 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, účtu Batch, fondů a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete). Prostředky odstraňte následujícím způsobem:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili tyto postupy:

> [!div class="checklist"]
> * Nahrávání scén do úložiště Azure
> * Vytvoření fondu služby Batch pro účely vykreslování
> * Vykreslení scény s jedním snímkem s využitím Arnoldu
> * Škálování fondu a vykreslení scény s více snímky
> * Stažení vykresleného výstupu

Další informace o vykreslování v cloudovém měřítku najdete v popisu možností služby Batch Rendering. 

> [!div class="nextstepaction"]
> [Služba Batch Rendering](batch-rendering-service.md)
