---
title: Vysvětlení předdefinovaného koncového bodu Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – popisuje způsob použití integrovaného koncového bodu kompatibilního s centrem událostí ke čtení zpráv ze zařízení do cloudu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147671"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Čtení zpráv ze zařízení do cloudu z integrovaného koncového bodu

Ve výchozím nastavení jsou zprávy směrovány do integrovaného koncového bodu s přístupem ke službě (**zprávy/události**), které jsou kompatibilní s [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Tento koncový bod se v tuto chvíli zveřejňuje jenom pomocí protokolu [AMQP](https://www.amqp.org/) na portu 5671. Služba IoT Hub zpřístupňuje následující vlastnosti, které vám umožní řídit integrované **zprávy a události** koncového bodu zasílání zpráv kompatibilních s centrem událostí.

| Vlastnost            | Popis |
| ------------------- | ----------- |
| **Počet oddílů** | Tuto vlastnost nastavte při vytváření a definujte počet [oddílů](../event-hubs/event-hubs-features.md#partitions) pro ingestování událostí typu zařízení-Cloud. |
| **Doba uchovávání dat**  | Tato vlastnost určuje, jak dlouho jsou zprávy IoT Hub uchovávány ve dnech. Výchozí hodnota je jeden den, ale může se prodloužit na sedm dní. |

IoT Hub umožňuje uchovávání dat v předdefinovaných Event Hubs po dobu maximálně 7 dní. Dobu uchovávání můžete nastavit během vytváření IoT Hub. Doba uchovávání dat v IoT Hub závisí na vaší úrovni služby IoT Hub a typu jednotky. V případě velikosti může předdefinovaná Event Hubs uchovávat zprávy o maximální velikosti zprávy nejméně 24 hodin od kvóty. Například u 1 S1 jednotky IoT Hub poskytuje dostatek úložiště, aby bylo možné uchovávat alespoň 400 tisíc zprávy o velikosti 4k. Pokud vaše zařízení odesílají menší zprávy, můžou se uchovávat po delší dobu (až 7 dní) v závislosti na tom, kolik úložiště se spotřebovává. Garantujeme si, že data pro zadanou dobu uchování budou minimální. Zprávy vyprší a po uplynutí doby uchování nebudou přístupné. 

IoT Hub také umožňuje spravovat skupiny uživatelů na integrovaném koncovém bodu pro příjem zařízení do cloudu. Pro každý IoT Hub můžete mít až 20 skupin uživatelů.

Pokud používáte [směrování zpráv](iot-hub-devguide-messages-d2c.md) a je povolená [záložní trasa](iot-hub-devguide-messages-d2c.md#fallback-route) , všechny zprávy, které se neshodují s dotazem v žádné trase, přecházejí do integrovaného koncového bodu. Pokud tuto záložní trasu zakážete, budou se zprávy, které neodpovídají žádnému dotazu, zahozeny.

Dobu uchovávání můžete upravit buď prostřednictvím kódu programu, pomocí [rozhraní REST api IoT Hub poskytovatele prostředků](/rest/api/iothub/iothubresource), nebo pomocí [Azure Portal](https://portal.azure.com).

IoT Hub zveřejňuje integrovaný koncový bod **zpráv/událostí** pro vaše back-endové služby ke čtení zpráv ze zařízení do cloudu přijatých vaším rozbočovačem. Tento koncový bod je kompatibilní s centrem událostí, což umožňuje použít kterýkoli z mechanismů, které služba Event Hubs podporuje pro čtení zpráv.

## <a name="read-from-the-built-in-endpoint"></a>Čtení z předdefinovaného koncového bodu

Některé integrace produktů a sady Event Hubs SDK si můžou IoT Hub a umožňují použít připojovací řetězec služby IoT Hub pro připojení k předdefinovanému koncovému bodu.

Pokud používáte Event Hubs sady SDK nebo integrace produktů, které nevědí o IoT Hub, potřebujete název koncového bodu kompatibilního s centrem událostí a centra událostí. Tyto hodnoty můžete z portálu načíst následujícím způsobem:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

2. Klikněte na **Předdefinované koncové body**.

3. Část **události** obsahuje následující hodnoty: **oddíly**, **název kompatibilní** s centrem událostí, **koncový bod kompatibilní** s centrem událostí, **dobu uchování** a **skupiny uživatelů**.

    ![Nastavení zařízení-Cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

V portálu obsahuje pole koncový bod kompatibilní s centrem událostí úplný Event Hubs připojovací řetězec, který vypadá nějak takto: **koncový bod = Sb://abcd1234namespace.ServiceBus.Windows.NET/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-ehub-abcd-1234-123456**. Pokud sada SDK, kterou používáte, vyžaduje jiné hodnoty, pak by byla:

| Name | Hodnota |
| ---- | ----- |
| Koncový bod | sb://abcd1234namespace.servicebus.windows.net/ |
| Název hostitele | abcd1234namespace.servicebus.windows.net |
| Obor názvů | abcd1234namespace |

Pak můžete zvolit všechny zásady sdíleného přístupu v rozevíracím seznamu, jak je znázorněno na snímku obrazovky výše. Zobrazuje jenom zásady, které mají oprávnění **ServiceConnect** pro připojení k zadanému centru událostí.

Sady SDK, které můžete použít pro připojení k integrovanému koncovému bodu kompatibilnímu s centrem událostí, který IoT Hub zpřístupňuje zahrnutí:

| Jazyk | Sada SDK | Příklad |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Rychlý start](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Rychlý start](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Rychlý start](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Rychlý start](quickstart-send-telemetry-python.md) |

Integrace produktů, které můžete použít s integrovaným koncovým bodem kompatibilním s centrem událostí, který IoT Hub zpřístupňuje zahrnutí:

* [Azure Functions](../azure-functions/index.yml). Viz [zpracování dat z IoT Hub pomocí Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](../stream-analytics/index.yml). Viz [streamovaná data jako vstup do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Další informace najdete v tématu [Přidání zdroje událostí služby IoT Hub do prostředí Time Series Insights](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). [Zdroj Spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) můžete zobrazit na GitHubu.
* [Apache Spark Integration](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Další kroky

* Další informace o IoT Hubch koncových bodech najdete v tématu [IoT Hub koncových bodů](iot-hub-devguide-endpoints.md).

* V [rychlých startech](quickstart-send-telemetry-node.md) se dozvíte, jak odesílat zprávy ze zařízení do cloudu z simulovaných zařízení a číst zprávy z integrovaného koncového bodu. 

Další podrobnosti najdete v kurzu [proces IoT Hub zprávy ze zařízení do cloudu pomocí tras](tutorial-routing.md) .

* Pokud chcete směrovat zprávy ze zařízení do cloudu na vlastní koncové body, přečtěte si téma [použití směrování zpráv a vlastních koncových bodů pro zprávy ze zařízení do cloudu](iot-hub-devguide-messages-read-custom.md).