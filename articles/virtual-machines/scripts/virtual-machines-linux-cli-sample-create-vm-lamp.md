---
title: Ukázkový skript Azure CLI – nasazení zásobníku LAMP ve Load-Balanced sadě škálování virtuálního počítače
description: Pomocí rozšíření vlastních skriptů nasadíte stack LAMP do škálovací sady virtuálních počítačů v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501861"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Nasazení stacku LAMP do škálovací sady virtuálních počítačů s vyrovnáváním zatížení

Tento příklad vytvoří škálovací sadu virtuálních počítačů a použije rozšíření, které spustí vlastní skript pro nasazení stacku LAMP do všech virtuálních počítačů v této škálovací sadě.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Připojit

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
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vmss create](/cli/azure/vmss) | Vytvoří škálovací sadu virtuálních počítačů. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Přidá koncový bod s vyrovnáváním zatížení. |
| [az vmss extension set](/cli/azure/vmss/extension) | Vytvoří rozšíření, které po nasazení virtuálního počítače spustí vlastní skript. |
| [az vmss update-instances](/cli/azure/vmss) | Spustí vlastní skript na instancích virtuálních počítačů nasazených před použitím rozšíření pro škálovací sadu. |
| [az vmss scale](/cli/azure/vmss) | Vertikálně navýší kapacitu škálovací sady přidáním dalších instancí virtuálních počítačů. Na těch se po nasazení spustí vlastní skript. |
| [az network public-ip list](/cli/azure/network/public-ip) | Získá IP adresy virtuálních počítačů, které se vytvořily v rámci ukázky. |
| [az network lb show](/cli/azure/network/lb) | Získá front-endové a back-endové porty, které používá nástroj pro vyrovnávání zatížení. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
