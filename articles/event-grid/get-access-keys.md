---
title: Získání přístupového klíče pro prostředek Event Grid
description: Tento článek popisuje, jak získat přístupový klíč pro Event Grid téma nebo doménu.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775218"
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
K získání přístupových klíčů pro témata použijte [seznam klíčů AZ eventgrid tématu](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) . 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

K získání přístupových klíčů pro domény použijte [AZ eventgrid Domain Key list](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) . 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [ověřování klientů publikování](security-authenticate-publishing-clients.md). 
