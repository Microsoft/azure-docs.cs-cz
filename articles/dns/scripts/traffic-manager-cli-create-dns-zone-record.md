---
title: Příklad CLI – Vytvoření zóny a záznamu DNS pro název domény – Azure | Microsoft Docs
description: Tento ukázkový skript Azure CLI předvádí, jak pro název domény vytvořit zónu a záznam DNS.
services: load-balancer
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: kumud
ms.openlocfilehash: 3f9f272f6ae79ad39ba9d9ad09775f3082774a0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32773106"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Ukázkový skript Azure CLI: Vytvoření zóny a záznamu DNS

Tento ukázkový skript Azure CLI vytváří zónu a záznam DNS pro název domény. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive

# Create a resource group.
az group create \
  -n myResourceGroup \
  -l eastus

# Create a DNS zone. Substitute zone name "contoso.com" with the values for your own.

az network dns zone create \
  -g MyResourceGroup \
  -n contoso.com

# Create a DNS record. Substitute zone name "contoso.com" and IP address "1.2.3.4* with the values for your own.

az network dns record-set a add-record \
  -g MyResourceGroup \
  -z contoso.com \
  -n www \
  -a 1.2.3.4

# Get a list the DNS records in your zone
az network dns record-set list \
  -g MyResourceGroup \ 
  -z contoso.com
```

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
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Vytvoří zónu Azure DNS. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set#az-network-dns-record-set-a-add-record) | Přidá do zóny DNS záznam *A*. |
| [az network dns record-set list](/cli/azure/network/dns/record-set#az-network-dns-record-set-a-list) | Vypíše všechny sady záznamů *A* v zóně DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

