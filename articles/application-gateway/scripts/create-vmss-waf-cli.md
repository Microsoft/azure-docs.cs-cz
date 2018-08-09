---
title: Ukázkový skript Azure CLI – omezení webového provozu | Microsoft Docs
description: Ukázkový skript Azure CLI – vytvoření aplikační brány s Firewallem webových aplikací a škálovací sadou virtuálních počítačů. K omezení provozu se používají pravidla OWASP.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 76c45c985f95979caf6a00a7d2e3a36d9ec3ff77
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422922"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Omezení webového provozu s Azure CLI

Tento skript vytvoří aplikační bránu s Firewallem webových aplikací. Pro back-endové servery se použije škálovací sada virtuálních počítačů. Firewall webových aplikací používá k omezení webového provozu pravidla OWASP. Po spuštění skriptu můžete otestovat aplikační bránu použitím její veřejné IP adresy.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud chcete skupinu prostředků, aplikační bránu a všechny související prostředky odebrat, spusťte následující příkaz.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-net) | Vytvoří virtuální síť. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create) | Vytvoří ve virtuální síti podsíť. |
| [az network public-ip create](https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest) | Vytvoří veřejnou IP adresu aplikační brány. |
| [az network application-gateway create](https://docs.microsoft.com/en-us/cli/azure/network/application-gateway?view=azure-cli-latest) | Vytvoří aplikační bránu. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | Vytvoří škálovací sadu virtuálních počítačů. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Vytvoří účet úložiště. |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | Vytvoří účet úložiště. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network_public_ip_show) | Získá veřejnou IP adresu aplikační brány. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Další ukázkové skripty CLI pro aplikační bránu najdete v [dokumentaci ke službě Azure Application Gateway](../cli-samples.md).
