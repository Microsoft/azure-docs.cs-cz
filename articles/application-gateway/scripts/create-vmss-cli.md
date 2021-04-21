---
title: Ukázkový skript Azure CLI – Správa webového provozu | Microsoft Docs
description: Ukázkový skript Azure CLI – Správa webového provozu pomocí aplikační brány a škálovací sady virtuálních počítačů
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 17cac91ae62f5faa80fa130364bf67f81ad20a81
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789406"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Správa webového provozu s Azure CLI

Tento skript vytvoří aplikační bránu, která pro back-endové servery používá škálovací sadu virtuálních počítačů. Vytvořená aplikační brána se dá nakonfigurovat tak, aby spravovala webový provoz. Po spuštění skriptu můžete otestovat aplikační bránu použitím její veřejné IP adresy.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud chcete skupinu prostředků, aplikační bránu a všechny související prostředky odebrat, spusťte následující příkaz.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří ve virtuální síti podsíť. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu aplikační brány. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Vytvoří aplikační bránu. |
| [az vmss create](/cli/azure/vmss) | Vytvoří škálovací sadu virtuálních počítačů. |
| [az network public-ip show](/cli/azure/network/public-ip) | Získá veřejnou IP adresu aplikační brány. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure/overview).

Další ukázkové skripty CLI pro aplikační brány najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../cli-samples.md).
