---
title: Kopírování snímku spravovaného disku do předplatného – ukázka CLI
description: Ukázkový skript Azure CLI – kopírování (nebo přesun) snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 19c791cf1f394f5aab6ad2fcd7f98c4b30497286
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458536"
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
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Získá všechny vlastnosti snímku s použitím názvu a vlastností skupiny prostředků snímku. Vlastnost ID se použije ke zkopírování snímku do jiného předplatného.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Zkopíruje snímek vytvořením snímku v jiném předplatném s použitím ID a názvu nadřazeného snímku.  |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače ze snímku](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální počítače a spravované disky najdete v [dokumentaci k virtuálním počítačům Azure s Linuxem](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
