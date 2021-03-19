---
title: 'Rychlý Start: vytvoření centra událostí pomocí prostředí PowerShell – Azure Event Hubs'
description: Tento rychlý start popisuje, jak pomocí Azure PowerShellu vytvořit centrum událostí a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933949"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rychlý start: Vytvoření centra událostí pomocí Azure PowerShellu

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet][] před tím, než začnete.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte PowerShell místně, k dokončení tohoto rychlého startu je potřeba, abyste měli nejnovější verzi PowerShellu. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure, kterou potřebujete k vytvoření centra událostí. 

Následující příklad vytvoří skupinu prostředků v oblasti USA – východ. Nahraďte `myResourceGroup` názvem skupiny prostředků, kterou chcete použít:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Po vytvoření skupiny prostředků v ní vytvořte obor názvů služby Event Hubs. Obor názvů služby Event Hubs poskytuje jedinečný plně kvalifikovaný název domény, ve které můžete vytvořit centrum událostí. Nahraďte `namespace_name` jedinečným názvem vašeho oboru názvů:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Teď, když máte obor názvů služby Event Hubs, v něm vytvořte centrum událostí:  
Povolené období pro `MessageRetentionInDays` je v rozmezí od 1 do 7 dnů.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gratulujeme! Pomocí Azure PowerShellu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů služby Event Hubs a použili jste ukázkové aplikace k odesílání a přijímání událostí z centra událostí. Podrobné pokyny k odesílání událostí do (nebo) přijímání událostí z centra událostí najdete v tématu kurzy pro **odesílání a příjem** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


[Vytvoření bezplatného účtu]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
