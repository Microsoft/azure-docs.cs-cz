---
title: Rychlý start Azure – vytvoření clusteru Batch AI – Azure CLI | Microsoft Docs
description: Rychlý start – vytvoření clusteru Batch AI pro trénování modelů strojového učení a umělé inteligence – Azure CLI
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
ms.openlocfilehash: 1ea12c9a544704ea91b85ae944e611e6769b5592
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407129"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Rychlý start: Vytvoření clusteru pro úlohy trénování služby Batch AI pomocí Azure CLI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit cluster Batch AI, který můžete použít k trénování modelů umělé inteligence a strojového učení. Batch AI je spravovaná služba určená pro odborníky na data a výzkumníky v oblasti umělé inteligence. Umožňuje jim trénovat modely AI a strojového učení požadované velikosti na clusterech virtuálních počítačů Azure.

Tento cluster má zpočátku jeden uzel GPU. Na konci tohoto rychlého startu budete mít cluster, jehož kapacitu můžete vertikálně navýšit a používat k trénování modelů. K odeslání trénovacích úloh do clusteru můžete použít Batch AI, nástroje [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) nebo [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

V tomto rychlém startu se předpokládá, že příkazy spouštíte v prostředí Bash, a to buď v prostředí Cloud Shell, nebo na místním počítači.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pomocí příkazu `az group create` vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus2*. Zvolte umístění, například Východ USA 2, ve kterém je služba Batch AI dostupná.

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

K vytvoření clusteru Batch AI použijte příkaz `az batchai cluster create`. Následující příklad vytvoří cluster s následujícími vlastnostmi:

* Obsahuje jeden uzel s virtuálním počítačem velikosti NC6, který má jednu GPU NVIDIA Tesla K80. 
* Používá výchozí image Ubuntu Serveru určenou k hostování kontejnerových aplikací, které můžete použít pro většinu trénovacích úloh. 
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

Výstup tohoto příkazu zobrazuje vlastnosti clusteru. Vytvoření a spuštění uzlu trvá několik minut. Stav clusteru můžete zobrazit spuštěním příkazu `az batchai cluster show`. 

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
Cluster je připravený k použití, když má stav `steady` a jeden uzel je `Idle`.

## <a name="list-cluster-nodes"></a>Seznam uzlů clusteru 

Pokud se potřebujete připojit k uzlům clusteru (v našem případě je uzel jeden) kvůli instalaci aplikací nebo provádění údržby, získáte informace o připojení spuštěním příkazu `az batchai cluster node list`:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

Výstup JSON je podobný tomuto:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Tyto informace použijte k vytvoření připojení SSH k uzlu. V následujícím příkazu například nahraďte IP adresu správnou hodnotou svého uzlu:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Pokračujte ukončením relace SSH.

## <a name="resize-the-cluster"></a>Změna velikosti clusteru

Když cluster používáte ke spouštění trénovacích úloh, budete zřejmě potřebovat více výpočetních prostředků. Pokud například chcete zvětšit velikost na 2 uzly pro distribuované trénovací úlohy, spusťte příkaz [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Změna velikosti clusteru bude trvat několik minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v dalších kurzech a ukázkách Batch AI, použijte pracovní prostor Batch AI vytvořený v tomto rychlém startu. 

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
    --resource-group myResourceGroup \
```

Příkazem `az group delete` můžete odebrat skupinu prostředků Batch AI, pokud je už nepotřebujete. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit cluster Batch AI pomocí Azure CLI. Pokud se chcete dozvědět, jak používat cluster Batch AI k trénování modelu, pokračujte rychlým startem určeným k trénování modelu hloubkového učení.

> [!div class="nextstepaction"]
> [Trénování modelu hloubkového učení](./quickstart-tensorflow-training-cli.md)
