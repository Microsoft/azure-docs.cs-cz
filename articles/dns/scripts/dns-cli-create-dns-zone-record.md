---
title: Vytvoření zóny DNS a záznamu pro název domény – Azure CLI-Azure DNS
description: Tento ukázkový skript Azure CLI předvádí, jak pro název domény vytvořit zónu a záznam DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: 8f5dde0a35f31d2c33ab2857659efc88339c2ead
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210401"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Ukázkový skript Azure CLI: Vytvoření zóny a záznamu DNS

Tento ukázkový skript Azure CLI vytváří zónu a záznam DNS pro název domény. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, zónu DNS a všechny související prostředky.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky: |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Vytvoří zónu Azure DNS. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Přidá do zóny DNS záznam *A*. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Vypíše všechny sady záznamů *A* v zóně DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

