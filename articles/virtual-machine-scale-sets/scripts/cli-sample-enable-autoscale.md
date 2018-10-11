---
title: Ukázky Azure CLI – Povolení automatického škálování na základě hostitele | Microsoft Docs
description: Ukázky Azure CLI
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f146f673127d4495c2a2a392e26c1f51cd82a188
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951266"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Automatické škálování škálovací sady virtuálních počítačů pomocí Azure CLI
Tento skript vytvoří škálovací sadu virtuálních počítačů s Ubuntu a použije metriky hostitele k automatickému škálování s ohledem na měnící se zatížení procesoru.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript k vytvoření skupiny prostředků, škálovací sady virtuálních počítačů a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Vytvoří škálovací sadu virtuálních počítačů a připojí ji k virtuální síti, podsíti a skupině zabezpečení sítě. Za účelem distribuce provozu do několika instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) | Vytvoří a použije pravidla automatického škálování na škálovací sadu virtuálních počítačů. |
| [az group delete](/cli/azure/ad/group#delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Další ukázkové skripty Azure CLI pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../cli-samples.md).