---
title: Ukázkový skript Azure CLI – Kopírování (přesun) spravovaných disků do stejného nebo jiného předplatného
description: Ukázkový skript Azure CLI – Kopírování (přesun) spravovaných disků do stejného nebo jiného předplatného
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
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
ms.openlocfilehash: ea648f8dcc8f5cd8180fa9079cb404d8231e405c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040012"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Kopírování spravovaných disků do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku

Tento skript zkopíruje spravovaný disk do stejného nebo jiného předplatného ve stejné oblasti. Kopie funguje pouze v případě, že odběry jsou součástí stejného tenanta AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření nového spravovaného disku v cílovém předplatném pomocí ID zdrojového spravovaného disku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky: |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Získá všechny vlastnosti spravovaného disku s použitím názvu a vlastností skupiny prostředků spravovaného disku. Vlastnost ID se použije ke zkopírování spravovaného disku do jiného předplatného.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Zkopíruje spravovaný disk vytvořením nového spravovaného disku v jiném předplatném s použitím ID a názvu nadřazeného spravovaného disku.  |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a služby Managed disks najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
