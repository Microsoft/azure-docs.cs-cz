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
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956953"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Použití vlastních koncových bodů a směrování zpráv pro zprávy typu zařízení cloud

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [směrování zpráv](iot-hub-devguide-routing-query-syntax.md) umožňuje uživatelům pro směrování zpráv typu zařízení cloud do koncových bodů služby přístupem. Směrování umožňuje dotazování funkce k filtrování dat před směrování do koncových bodů. Každý směrování dotazů, které nakonfigurujete má následující vlastnosti:

| Vlastnost      | Popis |
| ------------- | ----------- |
| **Název**      | Jedinečný název, který identifikuje dotazu. |
| **Zdroj**    | Původ datového streamu do reagovali na ni. Například telemetrii zařízení. |
| **Podmínka** | Výraz dotazu pro směrování dotaz, který se spustí aplikace vlastnosti zprávy, systémové vlastnosti, text zprávy, značky dvojčat zařízení a vlastnosti dvojčete zařízení k určení, zda je shoda pro koncový bod. Další informace o vytváření dotazu, najdete v části Viz [zprávy syntaxi dotazů směrování](iot-hub-devguide-routing-query-syntax.md) |
| **Koncový bod**  | Název koncového bodu, kde služby IoT Hub odesílá zprávy, které odpovídají dotazu. Doporučujeme, abyste zvolili koncový bod ve stejné oblasti jako váš IoT hub. |

Jedna zpráva může odpovídat podmínku pro více směrování dotazů, ve kterých případu služby IoT Hub doručí do koncového bodu spojených s každou odpovídající dotaz. IoT Hub také automaticky deduplicates doručení zpráv, takže pokud zpráva odpovídá více dotazů, které mají stejný cíl, to je zapsat pouze jednou do tohoto cíle.

## <a name="endpoints-and-routing"></a>Koncové body a směrování

Služby IoT hub má výchozí [integrovaný koncový bod][lnk-built-in]. Můžete vytvořit vlastní koncové body pro směrování zpráv propojením dalších služeb v rámci vašeho předplatného k rozbočovači. IoT Hub aktuálně podporuje kontejnery služby Azure Storage, služby Event Hubs, fronty Service Bus a témat Service Bus jako vlastní koncové body.

Při použití směrování a vlastní koncové body zprávy pouze doručovaly do integrovaného koncového bodu pokud neodpovídají žádné dotazu. Doručení zprávy integrovaný koncový bod stejně jako vlastní koncový bod, přidejte trasu, která odesílá zprávy **události** koncového bodu.

> [!NOTE]
> Zápis dat do služby Azure Storage kontejnery jako objekty BLOB podporuje pouze službu IoT Hub.

> [!WARNING]
> Fronty služby Service Bus a témat s **relace** nebo **duplicit** povoleny nejsou podporovány jako vlastní koncové body.

Další informace o vytváření vlastních koncových bodů ve službě IoT Hub najdete v tématu [koncové body IoT Hubu][lnk-devguide-endpoints].

Další informace o čtení z vlastní koncové body naleznete v tématu:

* Čtení z [kontejnery služby Azure Storage][lnk-getstarted-storage].
* Čtení z [služby Event Hubs][lnk-getstarted-eh].
* Čtení z [fronty služby Service Bus][lnk-getstarted-queue].
* Čtení z [témat sběrnice Service Bus][lnk-getstarted-topic].

### <a name="next-steps"></a>Další postup

* Další informace o koncových bodech služby IoT Hub najdete v tématu [koncové body IoT Hubu][lnk-devguide-endpoints].
* Další informace o dotazovací jazyk, která slouží k definování směrování dotazů najdete v tématu [syntaxi dotazů směrování zpráv](iot-hub-devguide-routing-query-syntax.md).
* [Zpráv typu zařízení cloud procesu IoT Hubu pomocí tras] [ lnk-d2c-tutorial] kurzu se dozvíte, jak pomocí směrování dotazů a vlastní koncové body.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
