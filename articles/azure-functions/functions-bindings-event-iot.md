---
title: Vazby Azure IoT Hub pro funkce Azure
description: Naučte se používat aktivační událost a vazbu služby IoT Hub ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586121"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Vazby Azure IoT Hub pro funkce Azure

Tato sada článků vysvětluje, jak pracovat s vazby Funkce Azure pro Službu IoT Hub. Podpora služby IoT Hub je založená na [vazbě Centra událostí Azure](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Zatímco následující ukázky kódu používají rozhraní API centra událostí, uvedená syntaxe je použitelná pro funkce služby IoT Hub.

| Akce | Typ |
|--------|------|
| Reagujte na události odeslané do datového proudu událostí centra IoT. | [Trigger](./functions-bindings-event-iot-trigger.md) |
| Zápis událostí do datového proudu událostí IoT | [Výstupní vazba](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Další kroky

- [Reakce na události odeslané do datového proudu událostí centra událostí (Aktivační událost)](./functions-bindings-event-iot-trigger.md)
- [Zápis událostí do datového proudu událostí (výstupní vazba)](./functions-bindings-event-iot-output.md)
