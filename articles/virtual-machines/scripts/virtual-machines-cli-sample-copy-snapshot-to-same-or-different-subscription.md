---
title: Kopírování snímku spravovaného disku do předplatného – ukázka CLI
description: Ukázkový skript Azure CLI – kopírování (nebo přesun) snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 52efdb91328b5d9349eb3724c4a146348e4a975a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978573"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte pro následující scénáře:

1. Migrujte snímek ve službě Premium Storage (Premium_LRS) do úložiště úrovně Standard (Standard_LRS nebo Standard_ZRS), abyste snížili náklady.
1. Migrujte snímek z místně redundantního úložiště (Premium_LRS, Standard_LRS) do zóny redundantního úložiště (Standard_ZRS), abyste využili vyšší spolehlivosti úložiště ZRS.
1. Přesunutí snímku do jiného předplatného ve stejné oblasti pro delší dobu uchování.

> [!NOTE]
> Oba odběry se musí nacházet ve stejném tenantovi.

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

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
