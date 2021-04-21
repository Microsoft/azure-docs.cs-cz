---
title: Vytvoření zóny DNS a záznamu pro název domény – Azure CLI-Azure DNS
description: Tento ukázkový skript Azure CLI předvádí, jak pro název domény vytvořit zónu a záznam DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5692c7a81d34ec9005c1c4675c71d63e697c5f47
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783696"
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

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create) | Vytvoří zónu Azure DNS. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Přidá do zóny DNS záznam *A*. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Vypíše všechny sady záznamů *A* v zóně DNS. |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).