---
title: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Linuxem a serverem NGINX | Microsoft Docs
description: Ukázkový skript Azure CLI – Vytvoření virtuálního počítače s Linuxem a serverem NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5aa682af11744389739a539e900924ed93a3bbd6
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691146"
---
# <a name="create-a-vm-with-nginx"></a>Vytvoření virtuálního počítače se serverem NGINX

Tento skript vytvoří virtuální počítač Azure a pomocí rozšíření vlastních skriptů virtuálního počítače Azure nainstaluje server NGINX. Po spuštění skriptu můžete k ukázkovému webu přistupovat na veřejné IP adrese virtuálního počítače.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

Rozšíření vlastních skriptů zkopíruje na virtuální počítač tento skript. Skript se pak spustí a nainstaluje a nakonfiguruje webový server NGINX.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Vytvoří virtuální počítač. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Vytvoří pravidlo skupiny zabezpečení sítě, které povolí příchozí provoz. V této ukázce se otevře port 80 pro přenosy HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension) | Přidá do virtuálního počítače rozšíření virtuálního počítače a spustí ho. V této ukázce se rozšíření vlastních skriptů používá k instalaci serveru NGINX.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
