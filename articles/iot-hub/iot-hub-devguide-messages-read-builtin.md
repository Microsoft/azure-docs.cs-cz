---
title: Vysvětlení integrovaný koncový bod Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje, jak používat integrované prečíst typu zařízení cloud zprávy koncový bod kompatibilní s centrem událostí.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 767c91e4926e553b63b8331ac99edcd7823d2c13
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055011"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu

Ve výchozím nastavení, zprávy jsou směrovány na integrovaný koncový bod služby přístupem (**zpráv/události**), která je kompatibilní s [Event Hubs][lnk-event-hubs]. Tento koncový bod je aktuálně pouze vystavené pomocí [AMQP] [ lnk-amqp] protokolu na portu 5671. Služba IoT hub zpřístupní následující vlastnosti pro vám umožňují řídit integrovaný zasílání zpráv koncový bod kompatibilní s centrem událostí **zpráv/události**.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Tuto vlastnost nastavit při vytváření definuje počet [oddíly] [ lnk-event-hub-partitions] pro příjem událostí typu zařízení cloud. |
| **Doba uchování**  | Tato vlastnost určuje dobu v dní, po které zprávy jsou zachována ve službě IoT Hub. Výchozí hodnota je jeden den, ale je možné zvýšit na sedm dní. |

IoT Hub můžete také spravovat skupiny uživatelů na integrovaných zařízení cloud přijímat koncový bod.

Ve výchozím nastavení jsou všechny zprávy, které se neshodují s explicitně pravidel směrování zpráv zapisují na integrovaný koncový bod. Pokud zakážete tuto trasu pro použití náhradní lokality, se zahodí zprávy, které explicitně neodpovídají žádná pravidla směrování zpráv.

Můžete upravit dobu uchování buď prostřednictvím kódu programu pomocí [poskytovatele prostředků služby IoT Hub rozhraní REST API][lnk-resource-provider-apis], nebo se [webu Azure portal] [ lnk-management-portal].

Služba IoT Hub zpřístupní **zpráv/události** integrovaný koncový bod pro váš back endovým službám umožní číst zprávy typu zařízení cloud přijatých rozbočovače. Tento koncový bod je událost podporuje kompatibilního s centrem, které vám umožní použít některý z mechanismů služby Event Hubs pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z integrovaného koncového bodu

Při použití [Azure Service Bus SDK for .NET] [ lnk-servicebus-sdk] nebo [Event Hubs – Event Processor Host][lnk-eventprocessorhost], můžete použít jakékoli připojení, IoT Hub řetězce se správnými oprávněními. Pak pomocí **zpráv/události** jako název centra událostí.

Při použití sady SDK (nebo Integrace produktu), které jsou vědět o službě IoT Hub, musíte načíst koncový bod kompatibilní s centrem událostí a název kompatibilní s centrem událostí služby:

1. Přihlaste se k [webu Azure portal] [ lnk-management-portal] a přejděte do služby IoT hub.
1. Klikněte na **Koncové body**.
1. V **integrovaných koncových bodech** klikněte na tlačítko **události**. 
1. Otevře se stránka Vlastnosti, která obsahuje následující hodnoty: **koncový bod kompatibilní s centrem událostí**, **název kompatibilní s centrem událostí**, **oddíly**,  **Doba uchovávání**, a **skupiny příjemců**.

    ![Nastavení zařízení-cloud][img-eventhubcompatible]

IoT Hub SDK vyžaduje název koncového bodu služby IoT Hub, který je **zpráv/události** zobrazen pod **koncové body**.

Pokud používáte sadu SDK vyžaduje **Hostname** nebo **Namespace** hodnoty, odeberte schéma z **koncový bod kompatibilní s centrem událostí**. Například, pokud je váš koncový bod kompatibilní s centrem událostí **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **Hostname** by  **IOT hub ns myiothub 1234.servicebus.windows.net**. **Namespace** by **iothub-ns-myiothub-1234**.

Pak můžete použít libovolné zásady sdíleného přístupu, který má **ServiceConnect** oprávnění pro připojení k zadané centra událostí.

Pokud je potřeba vytvořit připojovací řetězec centra událostí pomocí pokrývají informace uvedené výše, používají následující vzor:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Sady SDK a integrace, které můžete použít s koncové body kompatibilní s centrem událostí, které služba IoT Hub zpřístupní obsahuje položky v následujícím seznamu:

* [Java Event Hubs klienta](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Můžete zobrazit [zdroje spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) na Githubu.
* [Integrace Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Další postup

Další informace o koncových bodech služby IoT Hub najdete v tématu [koncové body IoT Hubu][lnk-endpoints].

[Rychlých startů] [ lnk-get-started] ukazují, jak odesílat zprávy typu zařízení cloud ze simulovaných zařízení a čtení zpráv z integrovaného koncového bodu. Další podrobnosti najdete [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras] [ lnk-d2c-tutorial] kurzu.

Pokud chcete směrovat zprávy typu zařízení cloud do vlastní koncové body, přečtěte si téma [trasy zpráv a vlastní koncové body používat pro zprávy typu zařízení cloud][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph
[lnk-amqp]: https://www.amqp.org/
