---
title: Výstupní vazba Azure IoT Hub pro Azure Functions
description: Naučte se psát zprávy do datových proudů Azure IoT Hub pomocí Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871775"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Výstupní vazba Azure IoT Hub pro Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Functionsmi výstupními vazbami pro IoT Hub. Podpora IoT Hub je založená na [vazbě Azure Event Hubs](functions-bindings-event-hubs.md).

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-event-iot.md).

> [!IMPORTANT]
> I když následující ukázky kódu používají rozhraní API centra událostí, je daná syntaxe platná pro funkce IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Další kroky

- [Reakce na události odeslané do datového proudu událostí centra událostí (aktivační událost)](./functions-bindings-event-iot-trigger.md)
