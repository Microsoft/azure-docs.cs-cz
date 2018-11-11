---
title: Vysvětlení integrovaný koncový bod Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje, jak použít předdefinované koncový bod kompatibilní s centrem událostí ke čtení zpráv typu zařízení cloud.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 02ea4b94f8d1442360bebb36fdbba13d973f8555
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242400"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu

Ve výchozím nastavení, zprávy jsou směrovány na integrovaný koncový bod služby přístupem (**zpráv/události**), která je kompatibilní s [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/
). Tento koncový bod je aktuálně pouze vystavené pomocí [AMQP](https://www.amqp.org/) protokolu na portu 5671. Služba IoT hub zpřístupní následující vlastnosti pro vám umožňují řídit integrovaný zasílání zpráv koncový bod kompatibilní s centrem událostí **zpráv/události**.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Tuto vlastnost nastavit při vytváření definuje počet [oddíly](../event-hubs/event-hubs-features.md#partitions) pro příjem událostí typu zařízení cloud. |
| **Doba uchování**  | Tato vlastnost určuje dobu v dní, po které zprávy jsou zachována ve službě IoT Hub. Výchozí hodnota je jeden den, ale je možné zvýšit na sedm dní. |

IoT Hub můžete také spravovat skupiny uživatelů na integrovaných zařízení cloud přijímat koncový bod.

Pokud používáte [směrování zpráv](iot-hub-devguide-messages-d2c.md) a [trasy pro použití náhradní lokality](iot-hub-devguide-messages-d2c.md#fallback-route) je povoleno, všechny zprávy, které neodpovídají dotaz na všechny trasy se zapisují do integrovaného koncového bodu. Pokud zakážete tuto trasu pro použití náhradní lokality, se zahodí zprávy, které se neshodují s dotazy.

Můžete upravit dobu uchování buď prostřednictvím kódu programu pomocí [poskytovatele prostředků služby IoT Hub rozhraní REST API](/rest/api/iothub/iothubresource), nebo se [webu Azure portal](https://portal.azure.com).

Služba IoT Hub zpřístupní **zpráv/události** integrovaný koncový bod pro váš back endovým službám umožní číst zprávy typu zařízení cloud přijatých rozbočovače. Tento koncový bod je událost podporuje kompatibilního s centrem, které vám umožní použít některý z mechanismů služby Event Hubs pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z integrovaného koncového bodu

Při použití [Azure Service Bus SDK for .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) nebo [Event Hubs – Event Processor Host](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), můžete použít libovolný připojovací řetězce centra IoT se správnými oprávněními. Pak pomocí **zpráv/události** jako název centra událostí.

Při použití sady SDK (nebo Integrace produktu), které jsou vědět o službě IoT Hub, musíte načíst koncový bod kompatibilní s centrem událostí a název kompatibilní s centrem událostí služby:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.

2. Klikněte na tlačítko **integrovaných koncových bodech**.

3. **Události** oddíl obsahuje následující hodnoty: **koncový bod kompatibilní s centrem událostí**, **název kompatibilní s centrem událostí**, **oddíly**, **Doba uchovávání**, a **skupiny příjemců**.

    ![Nastavení zařízení-cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

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

* Další informace o koncových bodech služby IoT Hub najdete v tématu [koncové body IoT Hubu](iot-hub-devguide-endpoints.md).

* [Rychlých startů](quickstart-send-telemetry-node.md) ukazují, jak odesílat zprávy typu zařízení cloud ze simulovaných zařízení a čtení zpráv z integrovaného koncového bodu. 

Další podrobnosti najdete [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](tutorial-routing.md) kurzu.

* Pokud chcete směrovat zprávy typu zařízení cloud do vlastní koncové body, přečtěte si téma [trasy zpráv a vlastní koncové body používat pro zprávy typu zařízení cloud](iot-hub-devguide-messages-read-custom.md).