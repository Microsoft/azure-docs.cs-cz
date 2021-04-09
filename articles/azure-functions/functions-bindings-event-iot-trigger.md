---
title: Aktivační událost služby Azure IoT Hub pro Azure Functions
description: Naučte se reagovat na události odeslané do datového proudu událostí služby IoT Hub v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612282"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Aktivační událost služby Azure IoT Hub pro Azure Functions

Tento článek vysvětluje, jak pracovat s Azure Functions vazeb pro IoT Hub. Podpora IoT Hub je založená na [vazbě Azure Event Hubs](functions-bindings-event-hubs.md).

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-event-iot.md).

> [!IMPORTANT]
> I když následující ukázky kódu používají rozhraní API centra událostí, je daná syntaxe platná pro funkce IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.jsvlastností

[host.jsv](functions-host-json.md#eventhub) souboru obsahuje nastavení, která řídí chování triggeru centra událostí. Podrobnosti o dostupných nastaveních najdete v části [host.jsv nastavení](functions-bindings-event-iot.md#hostjson-settings) .

## <a name="next-steps"></a>Další kroky

- [Zápis událostí do datového proudu událostí (výstupní vazba)](./functions-bindings-event-iot-output.md)
