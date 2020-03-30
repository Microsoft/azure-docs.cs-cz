---
title: Výstupní vazba Azure IoT Hub pro funkce Azure
description: Naučte se psát zprávy do datových proudů Azure IoT Hubs pomocí Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277424"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Výstupní vazba Azure IoT Hub pro funkce Azure

Tento článek vysvětluje, jak pracovat s výstupem Azure Functions vazby pro IoT Hub. Podpora služby IoT Hub je založená na [vazbě Centra událostí Azure](functions-bindings-event-hubs.md).

Informace o nastavení a konfiguraci naleznete v [přehledu](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Zatímco následující ukázky kódu používají rozhraní API centra událostí, uvedená syntaxe je použitelná pro funkce služby IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Další kroky

- [Reakce na události odeslané do datového proudu událostí centra událostí (Aktivační událost)](./functions-bindings-event-iot-trigger.md)
