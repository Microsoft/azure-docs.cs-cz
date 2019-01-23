---
title: Dotazování odběrů služby Azure Event Grid
description: Popisuje, jak zobrazit seznam odběrů služby Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: ac43b85858451149ceabf87c77b42d40fbd4eac4
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470970"
---
# <a name="query-event-grid-subscriptions"></a>Dotazování odběrů služby Event Grid 

Tento článek popisuje, jak zobrazit seznam odběrů služby Event Grid ve vašem předplatném Azure. Při dotazování na vašich stávajících předplatných služby Event Grid, je důležité porozumět různým typům předplatných. Můžete poskytovat různé parametry podle typu vašeho předplatného, které chcete získat.

## <a name="resource-groups-and-azure-subscriptions"></a>Skupiny prostředků a předplatných Azure

Skupiny prostředků a předplatných Azure se prostředky Azure. Stejné vlastnosti jako předplatná event gridu k prostředkům Azure, proto nemají předplatná event gridu se skupinami prostředků nebo předplatných Azure. Předplatná Event gridu se skupinami prostředků nebo předplatnými Azure jsou považovány za globální.

Pokud chcete získat předplatná event gridu pro předplatné Azure a skupiny prostředků, nemusíte poskytovat žádné parametry. Ujistěte se, že jste vybrali předplatné Azure, které chcete zadat dotaz. Následující příklady Nezískávat předplatná event gridu pro vlastní témata nebo prostředky Azure.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

Pokud chcete získat předplatná event gridu pro předplatné Azure, zadejte typ tématu **Microsoft.Resources.Subscriptions**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Pokud chcete získat předplatná event gridu pro všechny skupiny prostředků v rámci předplatného Azure, zadejte typ tématu **Microsoft.Resources.ResourceGroups**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Pokud chcete získat předplatná event gridu pro zadané skupině prostředků, zadejte název skupiny prostředků jako parametr.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Vlastní témata a prostředků Azure

Vlastní témata Event grid jsou prostředky Azure. Předplatná event gridu pro vlastní témata a další prostředky, jako je účet Blob storage, proto dotazování stejným způsobem. Pokud chcete získat předplatná event gridu pro vlastní témata, je nutné zadat parametry, které identifikovat prostředek nebo identifikovat umístění prostředku. Není možné široce dotazu předplatná event gridu pro prostředky ve vašem předplatném Azure.

Pokud chcete získat předplatná event gridu pro vlastní témata a dalších prostředků v umístění, zadejte název umístění.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

Získat předplatné na vlastní témata na umístění, zadat umístění a typ tématu **Microsoft.EventGrid.Topics**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Chcete-li získat předplatná do účtů úložiště pro umístění, zadejte umístění a typ tématu **Microsoft.Storage.StorageAccounts**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Pokud chcete získat předplatná event gridu vlastního tématu, zadejte název vlastního tématu a název dané skupiny prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Pokud chcete získat předplatná event gridu pro určitý prostředek, zadejte ID prostředku.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Další postup

* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
