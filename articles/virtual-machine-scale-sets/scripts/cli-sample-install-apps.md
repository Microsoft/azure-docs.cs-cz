---
title: Ukázky Azure CLI 2.0 – Instalace aplikací | Microsoft Docs
description: Ukázky Azure CLI 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b1a8510efccf810bd4fbdd647d58d38cd87d019f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Instalace aplikací do škálovací sady virtuálních počítačů pomocí Azure CLI 2.0
Tento skript vytvoří škálovací sadu virtuálních počítačů s Ubuntu a pomocí rozšíření vlastních skriptů nainstaluje základní webovou aplikaci. Po spuštění skriptu můžete k webové aplikaci přistupovat přes webový prohlížeč.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Instalace aplikací do škálovací sady"")]

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
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Nainstaluje rozšíření vlastních skriptů Azure pro spuštění skriptu, který na jednotlivých instancích virtuálních počítačů připraví datové disky. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo nástroje pro vyrovnávání zatížení pro distribuci provozu na portu TCP 80 do instancí virtuálních počítačů ve škálovací sadě. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
| [az group delete](/cli/azure/ad/group#delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
Další informace o Azure CLI 2.0 najdete v [dokumentaci k Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Další ukázkové skripty Azure CLI 2.0 pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../cli-samples.md).