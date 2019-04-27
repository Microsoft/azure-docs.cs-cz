---
title: Ukázkový skript Azure CLI – Připojení disku s operačním systémem | Microsoft Docs
description: Ukázkový skript Azure CLI – Připojení disku s operačním systémem
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f2d4c9a7871e0917b33407605abe1389eb4420e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679319"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Řešení potíží s diskem s operačním systémem virtuálního počítače

Tento skript připojí disk s operačním systémem virtuálního počítače, který selhal, nebo problematického virtuálního počítače jako datový disk ke druhému virtuálnímu počítači. To může být užitečné při řešení potíží s diskem nebo obnovování dat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Vrátí seznam virtuálních počítačů. V tomto případě se možnost dotazu používá k vrácení disku s operačním systémem virtuálního počítače. Tato hodnota se pak přidá do proměnné uri. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Odstraní virtuální počítač. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Vytvoří virtuální počítač.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Připojí disk k virtuálnímu počítači. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Vrátí IP adresy virtuálního počítače. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
