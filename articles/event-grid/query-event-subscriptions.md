---
title: Dotazování Azure Event Grid předplatných
description: Tento článek popisuje, jak vypsat Event Grid předplatných ve vašem předplatném Azure. Na základě typu předplatného zadáte různé parametry.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86120445"
---
# <a name="query-event-grid-subscriptions"></a>Dotazování Event Grid předplatných 

Tento článek popisuje, jak zobrazit seznam předplatných Event Grid ve vašem předplatném Azure. Při dotazování stávajících předplatných Event Grid je důležité pochopit různé typy předplatných. Můžete zadat různé parametry založené na typu předplatného, které chcete získat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Skupiny prostředků a předplatná Azure

Předplatná Azure a skupiny prostředků nejsou prostředky Azure. Proto odběry služby Event Grid pro skupiny prostředků nebo předplatná Azure nemají stejné vlastnosti jako odběry služby Event Grid pro prostředky Azure. Odběry služby Event Grid do skupin prostředků nebo předplatných Azure se považují za globální.

Pokud chcete získat odběry služby Event Grid pro předplatné Azure a skupiny prostředků, nemusíte zadávat žádné parametry. Ujistěte se, že jste vybrali předplatné Azure, které chcete zadat dotaz. Následující příklady nezískají odběry služby Event Grid pro vlastní témata nebo prostředky Azure.

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

Pokud chcete získat odběry služby Event Grid pro předplatné Azure, zadejte typ tématu **Microsoft. Resources. Subscriptions**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Pokud chcete získat odběry služby Event Grid pro všechny skupiny prostředků v rámci předplatného Azure, zadejte typ tématu **Microsoft. Resources. ResourceGroups**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Pokud chcete získat odběry služby Event Grid pro zadanou skupinu prostředků, zadejte jako parametr název skupiny prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Vlastní témata a prostředky Azure

Vlastní témata služby Event Grid jsou prostředky Azure. Proto se pro vlastní témata a další prostředky, jako je například účet Blob Storage, dotazuje odběry služby Event Grid stejným způsobem. Pokud chcete získat odběry služby Event Grid pro vlastní témata, musíte zadat parametry, které identifikují prostředek, nebo Identifikujte umístění prostředku. Pro prostředky v rámci vašeho předplatného Azure není možné široce dotazovat odběry služby Event Grid.

Chcete-li získat odběry služby Event Grid pro vlastní témata a další prostředky v umístění, zadejte název umístění.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Pokud chcete získat odběry pro vlastní témata pro umístění, zadejte umístění a typ tématu **Microsoft. EventGrid. témat**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Chcete-li získat odběry účtů úložiště pro umístění, zadejte umístění a typ tématu **Microsoft. Storage. StorageAccounts**.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Pokud chcete získat odběry služby Event Grid pro vlastní téma, zadejte název vlastního tématu a název skupiny prostředků.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Pokud chcete získat odběry služby Event Grid pro konkrétní prostředek, zadejte ID prostředku.

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

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
