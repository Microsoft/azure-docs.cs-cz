---
title: 'Úvodní příručka: Vytvoření centra událostí pomocí PowerShellu – Centra událostí Azure'
description: Tento rychlý start popisuje, jak pomocí Azure PowerShellu vytvořit centrum událostí a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
services: event-hubs
author: spelluru
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 89ec1957e75fa45eef6c7939a77e5cc1b3cf7806
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162119"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rychlý start: Vytvoření centra událostí pomocí Azure PowerShellu

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,][] než začnete.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte PowerShell místně, k dokončení tohoto rychlého startu je potřeba, abyste měli nejnovější verzi PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci a konfiguraci Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps).

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
Povolená `MessageRetentionInDays` lhůta je mezi 1 a 7 dny.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Blahopřejeme! Pomocí Azure PowerShellu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů služby Event Hubs a použili jste ukázkové aplikace k odesílání a přijímání událostí z centra událostí. Podrobné pokyny k odesílání událostí do centra událostí (nebo) příjmu událostí najdete v tématu **Kurzy odesílání a přijímání událostí:** 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


[vytvořte si bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
