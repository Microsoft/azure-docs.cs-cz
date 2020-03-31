---
title: Dotaz na předplatná Azure Event Grid
description: Tento článek popisuje, jak vypsat odběry služby Event Grid ve vašem předplatném Azure. Zadáte různé parametry na základě typu předplatného.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721536"
---
# <a name="query-event-grid-subscriptions"></a>Odběry služby Query Event Grid 

Tento článek popisuje, jak vypsat odběry služby Event Grid ve vašem předplatném Azure. Při dotazování na vaše stávající odběry Event Grid, je důležité pochopit různé typy předplatných. Zadáte různé parametry na základě typu předplatného, které chcete získat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Skupiny prostředků a předplatná Azure

Předplatná Azure a skupiny prostředků nejsou prostředky Azure. Proto odběry mřížky událostí pro skupiny prostředků nebo předplatná Azure nemají stejné vlastnosti jako odběry gridu událostí pro prostředky Azure. Předplatná mřížky událostí pro skupiny prostředků nebo předplatná Azure jsou považovány za globální.

Chcete-li získat předplatná mřížky událostí pro předplatné Azure a jeho skupiny prostředků, nemusíte zadejte žádné parametry. Ujistěte se, že jste vybrali předplatné Azure, na které se chcete dotazovat. V následujících příkladech nezískáte odběry mřížky událostí pro vlastní témata nebo prostředky Azure.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Chcete-li získat předplatná mřížky událostí pro předplatné Azure, zadejte typ tématu **Microsoft.Resources.Subscriptions**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Chcete-li získat odběry mřížky událostí pro všechny skupiny prostředků v rámci předplatného Azure, zadejte typ tématu **Microsoft.Resources.ResourcesGroups**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Chcete-li získat odběry mřížky událostí pro zadanou skupinu prostředků, zadejte jako parametr název skupiny prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Vlastní témata a prostředky Azure

Vlastní témata mřížky událostí jsou prostředky Azure. Proto dotaz předplatných mřížky událostí pro vlastní témata a další prostředky, jako je účet úložiště objektů blob, stejným způsobem. Chcete-li získat odběry mřížky událostí pro vlastní témata, musíte zadat parametry, které identifikují prostředek nebo identifikují umístění prostředku. Není možné široce dotaz ovat předplatná mřížky událostí pro prostředky v rámci předplatného Azure.

Chcete-li získat odběry mřížky událostí pro vlastní témata a další prostředky v umístění, zadejte název umístění.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Chcete-li získat odběry vlastních témat pro umístění, zadejte umístění a typ tématu **Microsoft.EventGrid.Topics**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Chcete-li získat předplatná pro účty úložiště pro umístění, zadejte umístění a typ tématu **Microsoft.Storage.StorageAccounts**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Chcete-li získat odběry mřížky událostí pro vlastní téma, zadejte název vlastního tématu a název jeho skupiny prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Chcete-li získat odběry mřížky událostí pro konkrétní prostředek, zadejte ID prostředku.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Další kroky

* Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
