---
title: Pochopení předdefinovaný koncový bod Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře – popisuje, jak používat integrované, prečíst zařízení cloud zprávy koncový bod kompatibilní s centrem událostí.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: a09cc42763787890a4dabf17b1a1a87e7427ba37
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808532"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Číst zprávy typu zařízení cloud z předdefinovaných koncového bodu

Ve výchozím nastavení, zprávy jsou směrovány na předdefinovaný koncový bod služby směřujících (**zprávy nebo události**) kompatibilní s [Event Hubs][lnk-event-hubs]. Tento koncový bod je aktuálně jenom zveřejněné pomocí [AMQP] [ lnk-amqp] protokolu na portu 5671. IoT hub zpřístupní následující vlastnosti pro vám umožňují řídit předdefinované zasílání zpráv koncový bod kompatibilní s centrem událostí **zprávy nebo události**.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Tuto vlastnost nastavit při vytváření zadat počet [oddíly] [ lnk-event-hub-partitions] pro přijímání událostí zařízení cloud. |
| **Doba uchování**  | Tato vlastnost určuje, jak dlouho ve dnech, po které zprávy jsou uchována službou IoT Hub. Výchozí hodnota je jeden den, ale je možné zvýšit na sedm dní. |

IoT Hub můžete také spravovat skupiny příjemců na integrované zařízení cloud přijímat koncový bod.

Ve výchozím nastavení jsou všechny zprávy, které neodpovídají explicitně pravidel směrování zpráv zapisují na předdefinovaný koncový bod. Pokud zakážete toto záložní směrování, jsou zprávy, které neodpovídají explicitně všechna pravidla pro směrování zpráv vyřadit.

Můžete upravit dobu uchování buď programově pomocí [zprostředkovatele prostředků služby IoT Hub rozhraní REST API][lnk-resource-provider-apis], nebo se [portál Azure] [ lnk-management-portal].

IoT Hub zpřístupní **zprávy nebo události** předdefinovaný koncový bod pro váš back endové služby ke čtení zpráv typu zařízení cloud přijatých rozbočovače. Tento koncový bod je událost kompatibilní s centrem, což vám umožní použít některý z mechanismů služby Event Hubs podporuje pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z předdefinovaných koncového bodu

Při použití [Azure Service Bus SDK pro .NET] [ lnk-servicebus-sdk] nebo [Event Hubs - Event Processor Host][lnk-eventprocessorhost], můžete použít libovolné púřipojovací řetězce IoT Hub se správnými oprávněními. Potom pomocí **zprávy nebo události** jako název centra událostí.

Při použití sady SDK (nebo Integrace produktu), nebudou o IoT Hub, je nutné ji načíst koncový bod kompatibilní s centrem událostí a název kompatibilní s centrem událostí:

1. Přihlaste se k [portál Azure] [ lnk-management-portal] a přejděte do služby IoT hub.
1. Klikněte na **Koncové body**.
1. V **předdefinované koncové body** klikněte na tlačítko **události**. 
1. Otevře se stránka Vlastnosti, která obsahuje následující hodnoty: **koncový bod kompatibilní s centrem událostí**, **název kompatibilní s centrem událostí**, **oddíly**,  **Doba uchování**, a **skupiny příjemců**.

    ![Nastavení zařízení-cloud][img-eventhubcompatible]

Sada SDK centra IoT vyžaduje název koncového bodu služby IoT Hub, což je **zprávy nebo události** jak je uvedeno v části **koncové body**.

Pokud používáte sady SDK vyžaduje **Hostname** nebo **Namespace** hodnotu, odeberte schéma z **koncový bod kompatibilní s centrem událostí**. Například, pokud je váš koncový bod kompatibilní s centrem událostí **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **Hostname** by  **iothub-ns-myiothub-1234.servicebus.windows.net**. **Namespace** by **iothub-ns-myiothub-1234**.

Pak můžete použít všechny zásady sdíleného přístupu, který má **ServiceConnect** oprávnění pro připojení k zadané centra událostí.

Pokud potřebujete vytvořit připojovací řetězec Centru událostí pomocí předchozí informace, použijte následující vzoru:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Sady SDK a integrace, které můžete použít s koncovými body kompatibilní s centrem událostí, které IoT Hub zpřístupní zahrnuje položky v následujícím seznamu:

* [Java Event Hubs klienta](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Můžete zobrazit [spout zdroje](https://github.com/apache/storm/tree/master/external/storm-eventhubs) na Githubu.
* [Apache Spark integrace](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Další postup

Další informace o koncové body centra IoT najdete v tématu [koncové body centra IoT][lnk-endpoints].

[Začínáme] [ lnk-get-started] kurzy vám ukážou, jak odesílat zprávy typu zařízení cloud ze simulovaného zařízení a čtení zpráv z předdefinovaných koncového bodu. Další podrobnosti najdete [zpráv typu zařízení cloud proces IoT Hub pomocí trasy] [ lnk-d2c-tutorial] kurzu.

Pokud chcete směrovat vaše zprávy typu zařízení cloud do vlastní koncové body, přečtěte si téma [použít vlastní koncové body a směrování zpráv pro zprávy typu zařízení cloud][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
