---
title: Seznamte se s integrovaným koncovým bodem služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popisuje, jak používat předdefinovaný koncový bod kompatibilní s centrem událostí ke čtení zpráv mezi zařízeními a cloudy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729989"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu

Ve výchozím nastavení jsou zprávy směrovány do integrovaného koncového bodu směřujícího ke službě **(zprávy/události),** který je kompatibilní s [event huby](https://azure.microsoft.com/documentation/services/event-hubs/). Tento koncový bod je aktuálně vystaven pouze pomocí protokolu [AMQP](https://www.amqp.org/) na portu 5671. Centrum IoT zpřístupňuje následující vlastnosti, které umožňují řídit integrované **zprávy nebo události**koncového bodu zasílání zpráv kompatibilní s centrem událostí.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Nastavte tuto vlastnost při vytváření definovat počet [oddílů](../event-hubs/event-hubs-features.md#partitions) pro zařízení-cloud události ingestování. |
| **Retenční čas**  | Tato vlastnost určuje, jak dlouho ve dnech zprávy jsou zachovány pomocí ioT Hub. Výchozí hodnota je jeden den, ale může být zvýšena na sedm dní. |

IoT Hub umožňuje uchovávání dat ve vestavěných event hubů po dobu maximálně 7 dnů. Můžete nastavit čas uchování při vytváření centra IoT Hub. Doba uchovávání dat v centru IoT Hub závisí na vaší úrovni centra IoT a typu jednotky. Pokud jde o velikost, integrované centra událostí můžete zachovat zprávy maximální velikost zprávy až do alespoň 24 hodin kvóty. Například pro 1 Jednotky S1 IoT Hub poskytuje dostatek úložiště zachovat alespoň 400 kB zprávy velikosti 4 kB každý. Pokud vaše zařízení posílají menší zprávy, mohou být uchovávány déle (až 7 dní) v závislosti na tom, kolik úložiště je spotřebováno. Garantujeme, že údaje budou uchovávány minimálně po stanovenou dobu uchovávání. Platnost zpráv vyprší a po uplynutí doby uchovávání nebude přístupná. 

Služba IoT Hub také umožňuje spravovat skupiny spotřebitelů na integrovaném koncovém bodu příjmu zařízení do cloudu. Pro každý IoT Hub můžete mít až 20 skupin spotřebitelů.

Pokud používáte [směrování zpráv](iot-hub-devguide-messages-d2c.md) a [záložní trasa](iot-hub-devguide-messages-d2c.md#fallback-route) je povolena, všechny zprávy, které neodpovídají dotazu na libovolné trase, přejdou do předdefinovaného koncového bodu. Pokud zakážete tuto záložní trasu, zprávy, které neodpovídají žádný dotaz jsou vynechány.

Čas uchovávání informací můžete upravit buď programově pomocí [rest API poskytovatele prostředků služby IoT Hub](/rest/api/iothub/iothubresource), nebo pomocí [portálu Azure](https://portal.azure.com).

Služba IoT Hub zveřejňuje předdefinované koncové funkce **zpráv a událostí** pro back-endové služby ke čtení zpráv mezi zařízeními a cloudy přijatých vaším centrem. Tento koncový bod je kompatibilní s centrem událostí, který umožňuje používat některý z mechanismů, které služba Event Hubs podporuje pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z předdefinovaného koncového bodu

Některé integrace produktů a sady SDK centra událostí si služby IoT Hub uvědomují a umožňují vám připojit se k integrovanému koncovému bodu pomocí připojovacího řetězce služby IoT hub.

Při použití sady SDK centra událostí nebo integrace produktů, které nejsou vědomi IoT Hub, budete potřebovat koncový bod kompatibilní s centrem událostí a název kompatibilní s centrem událostí. Tyto hodnoty můžete načíst z portálu následujícím způsobem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.

2. Klepněte **na položku Předdefinované koncové body**.

3. Část **Události** obsahuje následující hodnoty: **oddíly**, **název kompatibilní s centrem událostí**, koncový bod kompatibilní s centrem **událostí**, **čas uchování**a **skupiny příjemce**.

    ![Nastavení mezi zařízeními a cloudy](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Na portálu obsahuje pole koncového bodu kompatibilního s centrem událostí úplný připojovací řetězec Event Hubs, který vypadá takto: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Pokud sada SDK, kterou používáte, vyžaduje jiné hodnoty, budou:

| Název | Hodnota |
| ---- | ----- |
| Koncový bod | sb://abcd1234namespace.servicebus.windows.net/ |
| Název hostitele | abcd1234namespace.servicebus.windows.net |
| Obor názvů | abcd1234oboru |

Potom můžete použít všechny zásady sdíleného přístupu, které mají **serviceconnect** oprávnění pro připojení k zadanému centru událostí.

Sady SDK, které můžete použít k připojení k integrovanému koncovému bodu kompatibilnímu s centrem událostí, který služby IoT Hub zveřejňuje, zahrnují:

| Jazyk | Sada SDK | Příklad | Poznámky |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Rychlý start](quickstart-send-telemetry-dotnet.md) | Používá informace kompatibilní s event huby. |
 Java | https://github.com/Azure/azure-event-hubs-java | [Rychlý start](quickstart-send-telemetry-java.md) | Používá informace kompatibilní s event huby. |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Rychlý start](quickstart-send-telemetry-node.md) | Používá připojovací řetězec centra IoT Hub. |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Používá připojovací řetězec centra IoT Hub. |

Integrace produktů, které můžete použít s integrovaným koncovým bodem kompatibilním s centrem událostí, který služby IoT Hub zveřejňuje, zahrnují:

* [Funkce Azure](https://docs.microsoft.com/azure/azure-functions/). Viz [Zpracování dat z IoT Hubu pomocí Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Viz [Stream data as input into Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Přehledy časových řad](https://docs.microsoft.com/azure/time-series-insights/). Viz [Přidání zdroje událostí centra IoT hub do prostředí Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm výtok](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). [Zdroj výtoku](https://github.com/apache/storm/tree/master/external/storm-eventhubs) můžete zobrazit na GitHubu.
* [Integrace Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Datové cihly Azure](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Další kroky

* Další informace o koncových bodech centra IoT Hub najdete v tématu [koncové body centra IoT](iot-hub-devguide-endpoints.md).

* [Rychlé starty](quickstart-send-telemetry-node.md) vám ukážou, jak odesílat zprávy mezi zařízeními a cloudy ze simulovaných zařízení a číst zprávy z předdefinovaného koncového bodu. 

Další podrobnosti najdete [v tématu proces zpráv zařízení-cloud služby IoT Hub pomocí trasy](tutorial-routing.md) kurzu.

* Pokud chcete směrovat zprávy mezi zařízeními a cloudem do vlastních koncových bodů, přečtěte si část [Použití tras zpráv a vlastních koncových bodů pro zprávy mezi zařízeními](iot-hub-devguide-messages-read-custom.md)a cloudy .
