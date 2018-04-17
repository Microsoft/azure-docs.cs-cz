---
title: Pochopení vlastní koncové body Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - pravidel směrování pro směrování zpráv typu zařízení cloud do vlastní koncové body.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: 3d54da43141dc2bdf34c9f71adc41dc7cf24ff10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Použít vlastní koncové body a směrování zpráv pro zprávy typu zařízení cloud

IoT Hub umožňuje směrovat [zpráv typu zařízení cloud] [ lnk-device-to-cloud] na koncové body služby směřujících centra IoT podle vlastnosti zprávy. Pravidla směrování poskytují flexibilitu pro odesílání zpráv, kde budou muset přejít bez nutnosti dalších služeb nebo vlastní kód. Každé pravidlo směrování, která můžete konfigurovat má následující vlastnosti:

| Vlastnost      | Popis |
| ------------- | ----------- |
| **Název**      | Jedinečný název, který identifikuje pravidlo. |
| **Zdroj**    | Původ datový proud do být reagovali na ni. Například zařízení telemetrie. |
| **Podmínka** | Výraz dotazu pro směrování pravidlo, které je spouštění hlavičky a text zprávy a určuje, zda je shoda pro koncový bod. Další informace o vytváření podmínku trasy, najdete v článku [odkaz - dotazovacího jazyka pro úlohy a dvojčata zařízení][lnk-devguide-query-language]. |
| **koncový bod**  | Název koncového bodu, kde IoT Hub odešle zprávy, které splňují podmínku. Koncové body musí být ve stejné oblasti jako službu IoT hub, jinak vám může být účtovat mezi oblastmi zápisy. |

Do jedné zprávy může odpovídat na více pravidel směrování, ve kterých případ IoT Hub doručení zprávy do koncového bodu spojené s každou odpovídající pravidlo podmínku. Centrum IoT také automaticky deduplicates doručení zpráv, takže pokud zpráva odpovídá více pravidel, které mají stejný cíl, ho je zapsat pouze jednou do tohoto cílového místa.

## <a name="endpoints-and-routing"></a>Koncové body a směrování

Centrum IoT má výchozí [vestavěným koncovým bodem][lnk-built-in]. Můžete vytvořit vlastní koncové body pro směrování zpráv do pomocí jiných služeb v rámci vašeho předplatného propojení k rozbočovači. IoT Hub aktuálně podporuje kontejnery Azure Storage, Event Hubs, fronty Service Bus a témat sběrnice Service Bus jako vlastní koncové body.

Při použití směrování a vlastní koncové body pouze doručování zpráv do vestavěným koncovým bodem pokud neodpovídají všechna pravidla. Pro doručení zprávy do předdefinovaný koncový bod také tak, aby vlastní koncový bod, přidejte trasu, která odesílá zprávy a pokuste se **události** koncový bod.

> [!NOTE]
> IoT Hub podporuje pouze zápis dat do Azure Storage kontejnerů jako objekty BLOB.

> [!WARNING]
> Fronty sběrnice a témata s **relací** nebo **duplicitní detekce** povoleno nejsou podporovány jako vlastní koncové body.

Další informace o vytváření vlastní koncové body v centru IoT najdete v tématu [koncové body centra IoT][lnk-devguide-endpoints].

Další informace o čtení ze vlastní koncové body najdete v tématu:

* Čtení z [kontejnery Azure Storage][lnk-getstarted-storage].
* Čtení z [služby Event Hubs][lnk-getstarted-eh].
* Čtení z [fronty Service Bus][lnk-getstarted-queue].
* Čtení z [témat sběrnice Service Bus][lnk-getstarted-topic].

## <a name="latency"></a>Latence

Při směrování zařízení cloud telemetrické zprávy pomocí předdefinovaných koncových bodů, je po vytvoření první trasy k mírnému zvýšení latence začátku do konce.

Ve většině případů průměrném zvyšování latence je menší než jedna sekunda. Můžete monitorovat pomocí latence **d2c.endpoints.latency.builtIn.events** [IoT Hub metrika](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). Vytvoření nebo odstranění všech trasy po první nemá negativní vliv na latenci začátku do konce.

### <a name="next-steps"></a>Další postup

Další informace o koncové body centra IoT najdete v tématu [koncové body centra IoT][lnk-devguide-endpoints].

Další informace o dotazovací jazyk používáte k definování pravidel směrování, najdete v části [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query-language].

[Zpráv typu zařízení cloud proces IoT Hub pomocí trasy] [ lnk-d2c-tutorial] kurzu se dozvíte, jak používat pravidla směrování a vlastní koncové body.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
