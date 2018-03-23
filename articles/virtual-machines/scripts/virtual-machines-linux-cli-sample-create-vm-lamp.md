---
title: Ukázkový skript Azure CLI – Nasazení stacku LAMP do škálovací sady virtuálních počítačů s vyrovnáváním zatížení | Microsoft Docs
description: Pomocí rozšíření vlastních skriptů nasadíte stack LAMP do škálovací sady virtuálních počítačů v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 1764fc15a888fbe15cc14b990721240d1baf3c40
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Nasazení stacku LAMP do škálovací sady virtuálních počítačů s vyrovnáváním zatížení

Tento příklad vytvoří škálovací sadu virtuálních počítačů a použije rozšíření, které spustí vlastní skript pro nasazení stacku LAMP do všech virtuálních počítačů v této škálovací sadě.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Připojení

Pomocí tohoto kódu zobrazíte pokyny pro připojení k virtuálním počítačům a škálovací sadě.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a virtuální počítače a všechny související prostředky.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Vytvoří škálovací sadu virtuálních počítačů. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Přidá koncový bod s vyrovnáváním zatížení. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Vytvoří rozšíření, které po nasazení virtuálního počítače spustí vlastní skript. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Spustí vlastní skript na instancích virtuálních počítačů nasazených před použitím rozšíření pro škálovací sadu. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Vertikálně navýší kapacitu škálovací sady přidáním dalších instancí virtuálních počítačů. Na těch se po nasazení spustí vlastní skript. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | Získá IP adresy virtuálních počítačů, které se vytvořily v rámci ukázky. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Získá front-endové a back-endové porty, které používá nástroj pro vyrovnávání zatížení. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
