---
title: Ukázkový skript Azure CLI – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku | Microsoft Docs
description: Ukázkový skript Azure CLI – Kopírování (přesun) snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku
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
ms.openlocfilehash: 744990b06b5585763615eb69909c29a27dee5a7f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248698"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopírování snímku spravovaného disku do stejného nebo jiného předplatného pomocí rozhraní příkazového řádku

Tento skript zkopíruje snímek spravovaného disku do stejného nebo jiného předplatného. Tento skript použijte v následujících scénářích:

1. Migrace snímku ve službě storage úrovně Premium (Premium_LRS) do úložiště úrovně Standard (Standard_LRS nebo Standard_ZRS) sníží vaše náklady.
1. Migrace snímek z místně redundantní úložiště (Premium_LRS, Standard_LRS) do zónově redundantní úložiště (Standard_ZRS), abyste využili výhod vyšší spolehlivost úložiště ZRS.
1. Přesunete snímek do jiného předplatného ve stejné oblasti pro delší dobu uchování.

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

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další virtuální počítače a spravované disky ukázkové skripty rozhraní příkazového řádku najdete v [dokumentaci virtuálního počítače s Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
