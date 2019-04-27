---
title: Azure Event Hubs vazby pro službu Azure Functions
description: Vysvětlení použití služby Azure Event Hubs vazby ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 7ca3ab8e3becf884fdba460f1981430e0c66ef99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104448"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs vazby pro službu Azure Functions

Tento článek vysvětluje, jak pracovat s [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) vazby pro službu Azure Functions. Azure Functions podporuje aktivaci a výstupní vazby služby Event hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Pro Azure Functions verzi 1.x, vazby služby Event Hubs jsou součástí [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) balíčku NuGet, verze 2.x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) úložiště GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Pro funkce 2.x, použijte [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) balíčku, verzi 3.x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
