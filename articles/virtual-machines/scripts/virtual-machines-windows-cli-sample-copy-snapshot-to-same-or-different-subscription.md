---
title: Kopírování snímku spravovaného disku do předplatného – ukázka CLI
description: Ukázkový skript Azure CLI – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku
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
ms.openlocfilehash: 54029e2ccbf592dfcde484728fa9cb969a668edd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010086"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte pro následující scénáře:

1. Migrujte snímek ve službě Premium Storage (Premium_LRS) do úložiště úrovně Standard (Standard_LRS nebo Standard_ZRS), abyste snížili náklady.
1. Migrujte snímek z místně redundantního úložiště (Premium_LRS, Standard_LRS) do zóny redundantního úložiště (Standard_ZRS), abyste využili vyšší spolehlivosti úložiště ZRS.
1. Přesunutí snímku do jiného předplatného ve stejné oblasti pro delší dobu uchování.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření snímku v cílovém předplatném pomocí ID zdrojového snímku používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Získá všechny vlastnosti snímku s použitím názvu a vlastností skupiny prostředků snímku. Vlastnost ID se použije ke zkopírování snímku do jiného předplatného.  |
| [az snapshot create](/cli/azure/snapshot) | Zkopíruje snímek vytvořením snímku v jiném předplatném s použitím ID a názvu nadřazeného snímku.  |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a služby Managed disks najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
