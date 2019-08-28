---
title: Vazby Azure Event Hubs pro Azure Functions
description: Naučte se používat vazby Azure Event Hubs v Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: bc75ad08716a001ae0cfd934dbc8d5da668dc1c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086658"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Vazby Azure Event Hubs pro Azure Functions

Tento článek vysvětluje, jak pracovat s Event Hubsmi vazbami [Azure](../event-hubs/event-hubs-what-is-event-hubs.md) pro Azure Functions. Azure Functions podporuje triggery a výstupní vazby pro Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Pro Azure Functions verze 1. x jsou vazby Event Hubs k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , verze 2. x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) úložiště GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Pro Functions 2. x použijte balíček [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , verze 3. x.
Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
