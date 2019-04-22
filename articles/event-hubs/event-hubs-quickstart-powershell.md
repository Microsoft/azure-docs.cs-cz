---
title: Vytvoření centra událostí pomocí prostředí PowerShell – Azure Event Hubs | Dokumentace Microsoftu
description: Tento rychlý start popisuje, jak pomocí Azure PowerShellu vytvořit centrum událostí a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: fba0d3ac5a20bd4f10b815450f6ce08e50f88ea0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681297"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rychlý start: Vytvoření centra událostí pomocí Azure Powershellu

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet][] před tím, než začnete.
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte PowerShell místně, k dokončení tohoto rychlého startu je potřeba, abyste měli nejnovější verzi PowerShellu. Pokud PowerShell potřebujete nainstalovat nebo upgradovat, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps).

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
Povolená délka období pro `MessageRetentionInDays` je mezi 1 až 7 dnů.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Blahopřejeme! Pomocí Azure PowerShellu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili obor názvů služby Event Hubs a použili jste ukázkové aplikace k odesílání a přijímání událostí z centra událostí. Podrobné pokyny týkající se odesílání událostí do (nebo) přijímat události z centra událostí, najdete v článku **odesílat a přijímat události** kurzy: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (pouze pro odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (pouze reecive)](event-hubs-storm-getstarted-receive.md)


[vytvořte si bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
