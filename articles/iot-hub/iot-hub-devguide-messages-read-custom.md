---
title: Principy vlastních koncových bodů služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pomocí směrovacích dotazů směrovat zprávy mezi zařízeními cloud do vlastních koncových bodů.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244340"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Použití tras zpráv a vlastních koncových bodů pro zprávy mezi zařízeními a cloudy

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv [služby](iot-hub-devguide-routing-query-syntax.md) IoT Hub umožňuje uživatelům směrovat zprávy mezi zařízeními do cloudu do koncových bodů orientovaných na služby. Směrování také poskytuje možnost dotazování filtrovat data před směrováním do koncových bodů. Každý nakonfigurovaní směrovací dotaz má následující vlastnosti:

| Vlastnost      | Popis |
| ------------- | ----------- |
| **Název**      | Jedinečný název, který identifikuje dotaz. |
| **Zdroj**    | Původ datového toku, který má být řešen. Například telemetrie zařízení. |
| **Podmínka** | Výraz dotazu pro směrovací dotaz, který je spuštěn proti vlastnostem aplikace zprávy, vlastnostem systému, textu zprávy, značky dvojčete zařízení a vlastnostem dvojčete zařízení, chcete-li zjistit, zda se shoduje s koncovým bodem. Další informace o vytvoření dotazu naleznete v tématu [syntaxe směrovacího dotazu zpráv](iot-hub-devguide-routing-query-syntax.md) |
| **Koncový bod**  | Název koncového bodu, kde IoT Hub odesílá zprávy, které odpovídají dotazu. Doporučujeme zvolit koncový bod ve stejné oblasti jako vaše centrum IoT. |

Jedna zpráva může odpovídat podmínku na více směrovacích dotazů, v takovém případě Služba IoT Hub doručuje zprávu do koncového bodu přidruženého ke každému spárovanému dotazu. IoT Hub také automaticky deduplikuje doručení zpráv, takže pokud zpráva odpovídá více dotazů, které mají stejný cíl, je zapsán pouze jednou do tohoto cíle.

## <a name="endpoints-and-routing"></a>Koncové body a směrování

Centrum IoT má výchozí [předdefinovaný koncový bod](iot-hub-devguide-messages-read-builtin.md). Můžete vytvořit vlastní koncové body pro směrování zpráv pomocí propojení jiných služeb ve vašem předplatném do centra. Služba IoT Hub aktuálně podporuje kontejnery úložiště Azure, centra událostí, fronty service bus a témata služby Service Bus jako vlastní koncové body.

Při použití směrování a vlastní koncové body, zprávy jsou doručovány pouze do předdefinovaného koncového bodu, pokud se neshodují žádný dotaz. Chcete-li doručit zprávy do předdefinovaného koncového bodu i do vlastního koncového bodu, přidejte trasu, která odesílá zprávy do předdefinovaného koncového bodu **událostí.**

> [!NOTE]
> * Služba IoT Hub podporuje jenom zápis dat do kontejnerů Služby Azure Storage jako objekty BLOB.
> * Fronty a témata služby Service Bus s povolenými **relacemi** nebo **zjišťováním duplicit** nejsou podporovány jako vlastní koncové body.

Další informace o vytváření vlastních koncových bodů v centru IoT Hub najdete v tématu [koncové body centra IoT](iot-hub-devguide-endpoints.md).

Další informace o čtení z vlastních koncových bodů najdete v tématu:

* Čtení z [kontejnerů úložiště Azure](../storage/blobs/storage-blobs-introduction.md).

* Čtení z [centra událostí](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Čtení z [front služby Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Čtení z [témat služby Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Další kroky

* Další informace o koncových bodech centra IoT Hub najdete v tématu [koncové body centra IoT](iot-hub-devguide-endpoints.md).

* Další informace o jazyce dotazů, který používáte k definování směrovacích dotazů, naleznete [v tématu Syntaxe dotazu směrovacího dotazu zpráv](iot-hub-devguide-routing-query-syntax.md).

* Proces [IoT Hub zařízení cloud zprávy pomocí trasy](tutorial-routing.md) kurz ukazuje, jak používat směrovací dotazy a vlastní koncové body.