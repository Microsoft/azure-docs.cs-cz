---
title: Kopírování spravovaných disků do předplatného – ukázka CLI
description: Ukázkový skript Azure CLI – Kopírování (přesun) spravovaných disků do stejného nebo jiného předplatného
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
ms.openlocfilehash: 4b1516e492b13426bed6cae8637abd5c713bcd63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010117"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopírování spravovaných disků do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku

Tento skript zkopíruje spravovaný disk do stejného nebo jiného předplatného ve stejné oblasti. Kopie funguje pouze v případě, že odběry jsou součástí stejného tenanta AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření nového spravovaného disku v cílovém předplatném pomocí ID zdrojového spravovaného disku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az disk show](/cli/azure/disk) | Získá všechny vlastnosti spravovaného disku s použitím názvu a vlastností skupiny prostředků spravovaného disku. Vlastnost ID se použije ke zkopírování spravovaného disku do jiného předplatného.  |
| [az disk create](/cli/azure/disk) | Zkopíruje spravovaný disk vytvořením nového spravovaného disku v jiném předplatném s použitím ID a názvu nadřazeného spravovaného disku.  |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a služby Managed disks najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
