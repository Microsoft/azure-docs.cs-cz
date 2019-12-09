---
title: Vazby Azure IoT Hub pro Azure Functions
description: Naučte se používat vazby IoT Hub v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 2940f9e2adff82c100ed347431e8c1d27c30202e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924451"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Vazby Azure IoT Hub pro Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Functions vazeb pro IoT Hub. Podpora IoT Hub je založená na [vazbě Azure Event Hubs](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Pro Azure Functions verze 1. x jsou vazby IoT Hub k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , verze 2. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

V případě funkcí 2. x a vyšší použijte balíček [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , verze 3. x. Zdrojový kód pro tento balíček je v [sadu sdk azure webjobs](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> I když následující ukázky kódu používají rozhraní API centra událostí, je daná syntaxe platná pro funkce IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
