---
title: Vysvětlení vlastní koncové body služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – použití směrování dotazů pro směrování zpráv typu zařízení cloud do vlastní koncové body.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: bbd5058be502839f83db484136d1c97bac4a3d79
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585947"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Použití vlastních koncových bodů a směrování zpráv pro zprávy typu zařízení cloud

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [směrování zpráv](iot-hub-devguide-routing-query-syntax.md) umožňuje uživatelům pro směrování zpráv typu zařízení cloud do koncových bodů služby přístupem. Směrování umožňuje dotazování funkce k filtrování dat před směrování do koncových bodů. Každý směrování dotazů, které nakonfigurujete má následující vlastnosti:

| Vlastnost      | Popis |
| ------------- | ----------- |
| **Název**      | Jedinečný název, který identifikuje dotazu. |
| **Zdroj**    | Původ datového streamu do reagovali na ni. Například telemetrii zařízení. |
| **Podmínka** | Výraz dotazu pro směrování dotaz, který se spustí aplikace vlastnosti zprávy, vlastnosti systému, text zprávy, značky dvojčat zařízení a vlastnosti dvojčete zařízení k určení, zda je shoda pro koncový bod. Další informace o vytváření dotazu, najdete v části Viz [zprávy syntaxi dotazů směrování](iot-hub-devguide-routing-query-syntax.md) |
| **Koncový bod**  | Název koncového bodu, kde služby IoT Hub odesílá zprávy, které odpovídají dotazu. Doporučujeme, abyste zvolili koncový bod ve stejné oblasti jako váš IoT hub. |

Jedna zpráva může odpovídat podmínku pro více směrování dotazů, ve kterých případu služby IoT Hub doručí do koncového bodu spojených s každou odpovídající dotaz. IoT Hub také automaticky deduplicates doručení zpráv, takže pokud zpráva odpovídá více dotazů, které mají stejný cíl, to je zapsat pouze jednou do tohoto cíle.

## <a name="endpoints-and-routing"></a>Koncové body a směrování

Služby IoT hub má výchozí [integrovaný koncový bod](iot-hub-devguide-messages-read-builtin.md). Můžete vytvořit vlastní koncové body pro směrování zpráv propojením dalších služeb v rámci vašeho předplatného k rozbočovači. IoT Hub aktuálně podporuje kontejnery služby Azure Storage, služby Event Hubs, fronty Service Bus a témat Service Bus jako vlastní koncové body.

Při použití směrování a vlastní koncové body zprávy pouze doručovaly do integrovaného koncového bodu pokud neodpovídají žádné dotazu. Doručení zprávy integrovaný koncový bod stejně jako vlastní koncový bod, přidejte trasu, která odesílá zprávy **události** koncového bodu.

> [!NOTE]
> * Zápis dat do služby Azure Storage kontejnery jako objekty BLOB podporuje pouze službu IoT Hub.
> * Fronty služby Service Bus a témat s **relace** nebo **duplicit** povoleny nejsou podporovány jako vlastní koncové body.

Další informace o vytváření vlastních koncových bodů ve službě IoT Hub najdete v tématu [koncové body IoT Hubu](iot-hub-devguide-endpoints.md).

Další informace o čtení z vlastní koncové body naleznete v tématu:

* Čtení z [kontejnery služby Azure Storage](../storage/blobs/storage-blobs-introduction.md).

* Čtení z [služby Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Čtení z [fronty služby Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Čtení z [témat sběrnice Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Další postup

* Další informace o koncových bodech služby IoT Hub najdete v tématu [koncové body IoT Hubu](iot-hub-devguide-endpoints.md).

* Další informace o dotazovací jazyk, která slouží k definování směrování dotazů najdete v tématu [syntaxi dotazů směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

* [Zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](tutorial-routing.md) kurzu se dozvíte, jak pomocí směrování dotazů a vlastní koncové body.