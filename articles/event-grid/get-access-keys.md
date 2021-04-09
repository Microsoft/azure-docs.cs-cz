---
title: Získání přístupového klíče pro prostředek Event Grid
description: Tento článek popisuje, jak získat přístupový klíč pro Event Grid téma nebo doménu.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632525"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Získání přístupových klíčů pro Event Grid prostředky (témata nebo domény)
Přístupové klíče slouží k ověřování událostí publikování aplikace pro Azure Event Grid prostředků (témat a domén). Doporučujeme pravidelně znovu vygenerovat klíče a bezpečně je ukládat. Máte k dispozici dva přístupové klíče, abyste mohli při opětovném generování druhé klávesy spravovat připojení pomocí jednoho klíče.

Tento článek popisuje, jak získat přístupové klíče pro prostředek Event Grid (téma nebo doménu) pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku. 

## <a name="azure-portal"></a>portál Azure
V Azure Portal přejděte na kartu **přístupové klíče** **Event Grid tématu** nebo na stránce **Event Grid domény** pro vaše téma nebo doménu.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Stránka přístupových klíčů":::

## <a name="azure-powershell"></a>Azure PowerShell
K získání přístupových klíčů pro témata použijte příkaz [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) . 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Pro získání přístupových klíčů k doménám použijte příkaz [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) . 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
K získání přístupových klíčů pro témata použijte [seznam klíčů AZ eventgrid tématu](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) . 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

K získání přístupových klíčů pro domény použijte [AZ eventgrid Domain Key list](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) . 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [ověřování klientů publikování](security-authenticate-publishing-clients.md). 
