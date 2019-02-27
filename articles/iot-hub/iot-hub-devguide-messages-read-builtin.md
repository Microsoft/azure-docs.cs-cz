---
title: Vysvětlení integrovaný koncový bod Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje, jak použít předdefinované koncový bod kompatibilní s centrem událostí ke čtení zpráv typu zařízení cloud.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: dobett
ms.openlocfilehash: 81cdd53769cc33daaed70ba824a0a3bbf68f8134
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877227"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu

Ve výchozím nastavení, zprávy jsou směrovány na integrovaný koncový bod služby přístupem (**zpráv/události**), která je kompatibilní s [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Tento koncový bod je aktuálně pouze vystavené pomocí [AMQP](https://www.amqp.org/) protokolu na portu 5671. Služba IoT hub zpřístupní následující vlastnosti pro vám umožňují řídit integrovaný zasílání zpráv koncový bod kompatibilní s centrem událostí **zpráv/události**.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Tuto vlastnost nastavit při vytváření definuje počet [oddíly](../event-hubs/event-hubs-features.md#partitions) pro příjem událostí typu zařízení cloud. |
| **Doba uchování**  | Tato vlastnost určuje dobu v dní, po které zprávy jsou zachována ve službě IoT Hub. Výchozí hodnota je jeden den, ale je možné zvýšit na sedm dní. |

IoT Hub můžete také spravovat skupiny uživatelů na integrovaných zařízení cloud přijímat koncový bod.

Pokud používáte [směrování zpráv](iot-hub-devguide-messages-d2c.md) a [trasy pro použití náhradní lokality](iot-hub-devguide-messages-d2c.md#fallback-route) je povoleno, všechny zprávy, které neodpovídají dotaz na žádné trase přejít na integrovaný koncový bod. Pokud zakážete tuto trasu pro použití náhradní lokality, se zahodí zprávy, které neodpovídají žádné dotazu.

Můžete upravit dobu uchování buď prostřednictvím kódu programu pomocí [poskytovatele prostředků služby IoT Hub rozhraní REST API](/rest/api/iothub/iothubresource), nebo se [webu Azure portal](https://portal.azure.com).

Služba IoT Hub zpřístupní **zpráv/události** integrovaný koncový bod pro váš back endovým službám umožní číst zprávy typu zařízení cloud přijatých rozbočovače. Tento koncový bod je událost podporuje kompatibilního s centrem, které vám umožní použít některý z mechanismů služby Event Hubs pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z integrovaného koncového bodu

Některé integrace produktů a Event Hubs sady SDK služby IoT Hub víme a umožňují používat IoT hub služba připojovací řetězec pro připojení k integrovaný koncový bod.

Pokud používáte Event Hubs sad SDK nebo integrace produktů, které nejste vědomi služby IoT Hub, potřebujete koncový bod kompatibilní s centrem událostí a název kompatibilní s centrem událostí. Tyto hodnoty můžete načíst z portálu následujícím způsobem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.

2. Klikněte na tlačítko **integrovaných koncových bodech**.

3. **Události** oddíl obsahuje následující hodnoty: **Oddíly**, **název kompatibilní s centrem událostí**, **koncový bod kompatibilní s centrem událostí**, **doba uchovávání**, a **skupiny příjemců**.

    ![Nastavení zařízení-cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Na portálu pole pro koncový bod kompatibilní s centrem událostí obsahuje úplný připojovací řetězec služby Event Hubs, bude vypadat takto: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Pokud používáte sadu SDK vyžaduje jiné hodnoty, pak by:

| Název | Hodnota |
| ---- | ----- |
| Koncový bod | sb://abcd1234namespace.servicebus.windows.net/ |
| Název hostitele | abcd1234namespace.servicebus.windows.net |
| Obor názvů | abcd1234namespace |

Pak můžete použít libovolné zásady sdíleného přístupu, který má **ServiceConnect** oprávnění pro připojení k zadané centra událostí.

Sady SDK, které můžete použít pro připojení k integrovaný koncový bod kompatibilní s centrem událostí, které služba IoT Hub zpřístupní zahrnují:

| Jazyk | Sada SDK | Příklad: | Poznámky |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Rychlý start](quickstart-send-telemetry-dotnet.md) | Pomocí informací kompatibilní se službou Event Hubs |
 Java | https://github.com/Azure/azure-event-hubs-java | [Rychlý start](quickstart-send-telemetry-java.md) | Pomocí informací kompatibilní se službou Event Hubs |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Rychlý start](quickstart-send-telemetry-node.md) | Používá připojovací řetězec služby IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Používá připojovací řetězec služby IoT Hub |

Integrace produktů, které můžete použít integrovaný koncový bod kompatibilní s centrem událostí, které služba IoT Hub zpřístupní patří:

* [Služba Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Zobrazit [zpracování dat ze služby IoT Hub s využitím Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Zobrazit [Stream data jako vstup do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Zobrazit [přidání zdroje událostí IoT hub do prostředí Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Můžete zobrazit [zdroje spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) na Githubu.
* [Integrace Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Další postup

* Další informace o koncových bodech služby IoT Hub najdete v tématu [koncové body IoT Hubu](iot-hub-devguide-endpoints.md).

* [Rychlých startů](quickstart-send-telemetry-node.md) ukazují, jak odesílat zprávy typu zařízení cloud ze simulovaných zařízení a čtení zpráv z integrovaného koncového bodu. 

Další podrobnosti najdete [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](tutorial-routing.md) kurzu.

* Pokud chcete směrovat zprávy typu zařízení cloud do vlastní koncové body, přečtěte si téma [trasy zpráv a vlastní koncové body používat pro zprávy typu zařízení cloud](iot-hub-devguide-messages-read-custom.md).
