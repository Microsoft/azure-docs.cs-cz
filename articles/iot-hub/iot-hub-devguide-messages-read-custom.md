---
title: Principy vlastních koncových bodů Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – pomocí směrovacích dotazů můžete směrovat zprávy ze zařízení do cloudu do vlastních koncových bodů.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147643"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Použití směrování zpráv a vlastních koncových bodů pro zprávy ze zařízení do cloudu

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [směrování zpráv](iot-hub-devguide-routing-query-syntax.md) umožňuje uživatelům směrovat zprávy ze zařízení do cloudu do koncových bodů s přístupem ke službám. Směrování také poskytuje možnost dotazování pro filtrování dat před jejich směrováním do koncových bodů. Každý dotaz směrování, který nakonfigurujete, má následující vlastnosti:

| Vlastnost      | Popis |
| ------------- | ----------- |
| **Název**      | Jedinečný název, který identifikuje dotaz. |
| **Zdroj**    | Původ datového proudu, na kterém se má pracovat. Například telemetrie zařízení. |
| **Condition** (Podmínka) | Výraz dotazu pro dotaz směrování, který se spouští proti vlastnostem aplikace zprávy, vlastnostem systému, textu zprávy, značkám dvojitých značek zařízení a nevyhovujícím vlastnostem, aby bylo možné určit, zda se jedná o shodu koncového bodu. Další informace o vytváření dotazů najdete v tématu [syntaxe dotazů směrování zpráv](iot-hub-devguide-routing-query-syntax.md) . |
| **Koncový bod**  | Název koncového bodu, kde IoT Hub odesílá zprávy, které odpovídají dotazu. Doporučujeme, abyste vybrali koncový bod ve stejné oblasti jako centrum IoT. |

Jedna zpráva se může shodovat s podmínkou pro více směrovacích dotazů. v takovém případě IoT Hub doručuje zprávu koncovému bodu přidruženému k jednotlivým odpovídajícím dotazům. IoT Hub také automaticky odstraněné doručování zpráv, takže pokud zpráva odpovídá více dotazům, které mají stejný cíl, je do tohoto cíle zapisována pouze jednou.

## <a name="endpoints-and-routing"></a>Koncové body a směrování

Služba IoT Hub má výchozí [integrovaný koncový bod](iot-hub-devguide-messages-read-builtin.md). Můžete vytvořit vlastní koncové body, na které se budou směrovat zprávy propojením dalších služeb ve vašem předplatném do centra. IoT Hub aktuálně podporuje kontejnery Azure Storage, Event Hubs, Service Bus fronty a Service Bus témata jako vlastní koncové body.

Když použijete směrování a vlastní koncové body, zprávy se doručí pouze do předdefinovaného koncového bodu, pokud se neshodují s žádným dotazem. Chcete-li doručovat zprávy na integrovaný koncový bod i na vlastní koncový bod, přidejte trasu, která odesílá zprávy do koncového bodu integrovaných **událostí** .

> [!NOTE]
> * IoT Hub podporuje jenom zápis dat do kontejnerů Azure Storage jako objekty blob.
> * Service Bus fronty a témata s **relacemi** nebo s povoleným **vyhledáváním duplicit** nejsou podporovány jako vlastní koncové body.

Další informace o vytváření vlastních koncových bodů v IoT Hub najdete v tématu [IoT Hub koncových bodů](iot-hub-devguide-endpoints.md).

Další informace o čtení z vlastních koncových bodů naleznete v tématu:

* Čtení z [Azure Storage kontejnerů](../storage/blobs/storage-blobs-introduction.md).

* Čtení z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Čtení z [Service Busch front](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Čtení z [Service Bus témata](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Další kroky

* Další informace o IoT Hubch koncových bodech najdete v tématu [IoT Hub koncových bodů](iot-hub-devguide-endpoints.md).

* Další informace o dotazovacím jazyku, který používáte k definování dotazů směrování, najdete v tématu [syntaxe dotazu směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

* Postup [IoT Hub zpráv zařízení-Cloud pomocí tras](tutorial-routing.md) vám ukáže, jak používat směrovací dotazy a vlastní koncové body.