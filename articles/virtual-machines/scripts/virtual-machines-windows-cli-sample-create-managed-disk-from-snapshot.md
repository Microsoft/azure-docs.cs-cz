---
title: Vytvoření spravovaného disku ze snímku – ukázka objektu CLI
description: Ukázkový skript Azure CLI – Vytvoření spravovaného disku ze snímku
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 00fdfe3669e0ad84702f20be4d7efffcdc871149
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459538"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Vytvoření spravovaného disku ze snímku pomocí rozhraní příkazového řádku

Tento skript vytvoří spravovaný disk ze snímku. Použijte ho k obnovení virtuálního počítače ze snímků disku s operačním systémem a datového disku. Z odpovídajících snímků vytvořte disk s operačním systémem a datový disk a pak připojením spravovaných disků vytvořte nový virtuální počítač. Připojením datových disků vytvořených ze snímků můžete také obnovit datové disky existujícího virtuálního počítače.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření spravovaného disku ze snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Získá všechny vlastnosti snímku s použitím názvu a vlastností skupiny prostředků snímku. Vlastnost ID se použije k vytvoření spravovaného disku.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Vytvoří spravovaný disk s použitím ID spravovaného snímku. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu příkazového příkazu virtuálního počítače a spravovaných disků najdete v [dokumentaci k virtuálnímu počítači Azure Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
