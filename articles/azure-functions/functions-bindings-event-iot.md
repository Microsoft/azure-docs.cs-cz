---
title: Vazby Azure IoT Hub pro Azure Functions
description: Naučte se používat Trigger IoT Hub a vazbu v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77586121"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Vazby Azure IoT Hub pro Azure Functions

Tato sada článků vysvětluje, jak pracovat s Azure Functions vazeb pro IoT Hub. Podpora IoT Hub je založená na [vazbě Azure Event Hubs](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> I když následující ukázky kódu používají rozhraní API centra událostí, je daná syntaxe platná pro funkce IoT Hub.

| Akce | Typ |
|--------|------|
| Reagovat na události odeslané do datového proudu událostí služby IoT Hub. | [Trigger](./functions-bindings-event-iot-trigger.md) |
| Zápis událostí do datového proudu událostí IoT | [Výstupní vazba](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Další kroky

- [Reakce na události odeslané do datového proudu událostí centra událostí (aktivační událost)](./functions-bindings-event-iot-trigger.md)
- [Zápis událostí do datového proudu událostí (výstupní vazba)](./functions-bindings-event-iot-output.md)
