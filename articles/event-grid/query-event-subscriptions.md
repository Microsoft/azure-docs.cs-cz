---
title: Dotaz na předplatná Azure událostí mřížky
description: Popisuje, jak seznam předplatných Azure událostí mřížky.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce89e1caefbf73512c4ad3c2ce57ebc6f03f2616
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="query-event-grid-subscriptions"></a>Dotaz odběry událostí mřížky 

Tento článek popisuje, jak k zobrazení seznamu odběrů událostí mřížky ve vašem předplatném Azure. Při dotazování existující odběry událostí mřížky, je důležité pochopit různé typy odběry. Můžete zadat různé parametry, na základě typu předplatné, které chcete získat.

## <a name="resource-groups-and-azure-subscriptions"></a>Skupiny prostředků a předplatná Azure

Skupiny prostředků a předplatná Azure nejsou prostředků Azure. Proto odběry mřížky událostí na skupiny prostředků nebo předplatných Azure nemá stejné vlastnosti jako událost mřížky odběrů prostředků Azure. Odběry mřížky událostí na skupiny prostředků nebo předplatných Azure jsou považovány za globální.

Pokud chcete získat odběry mřížky událostí pro předplatné Azure a jeho skupin prostředků, nemusíte poskytovat žádné parametry. Ujistěte se, zda že jste vybrali předplatné Azure, které mají být zobrazeny. Následující příklady Nezískávat odběry mřížky událostí pro vlastní témata nebo prostředků Azure.

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

Pokud chcete získat odběry mřížky událostí pro předplatné Azure, zadejte typ tématu **Microsoft.Resources.Subscriptions**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Pokud chcete získat odběry událostí mřížky pro všechny skupiny zdrojů v rámci předplatného Azure, zadejte typ tématu **Microsoft.Resources.ResourceGroups**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Pokud chcete získat odběry mřížky událostí pro určené skupině prostředků, zadejte název skupiny prostředků jako parametr.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Vlastní témata a prostředky Azure

Událost mřížky vlastní témata jsou prostředky Azure. Proto dotaz odběry mřížky událostí pro vlastní témata a další prostředky, jako je účet úložiště Blob, stejným způsobem. Získat odběry mřížky událostí pro vlastní témata, je nutné zadat parametry, které identifikovat prostředek nebo určení umístění prostředku. Není možné široce dotazu událostí mřížky předplatným pro prostředky v rámci vašeho předplatného Azure.

Pokud chcete získat odběry mřížky událostí pro vlastní témata a dalším prostředkům v umístění, zadejte název umístění.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

Chcete-li získat předplatné na vlastní témata pro umístění, zadat umístění a typ tématu **Microsoft.EventGrid.Topics**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Chcete-li získat předplatné, účty úložiště pro umístění, zadat umístění a typ tématu **Microsoft.Storage.StorageAccounts**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Získat odběry mřížky událostí pro vlastní téma, zadejte název vlastní tématu a název jeho skupin prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Chcete-li získat předplatné mřížky události pro určitý prostředek, zadejte ID prostředku.

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

* Informace o události doručení a opakovaných pokusů [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).
* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
