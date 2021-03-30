---
title: Architektury řízené událostmi na Edge – Azure Event Grid na IoT Edge
description: Pro přeposílání událostí mezi moduly, hraničními zařízeními a cloudem použijte Azure Event Grid jako modul IoT Edge.
ms.topic: overview
ms.date: 07/08/2020
ms.openlocfilehash: 82a68f6ab32d8ad18c3af506c810b01d12cf794d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171512"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Co je Azure Event Grid v Azure IoT Edge?
Event Grid v IoT Edge přináší výkon a flexibilitu Azure Event Grid na hraničních zařízeních. Vytvářejte témata, publikujte události a přihlaste se k odběru více míst, ať už jsou moduly na stejném zařízení, v jiných hraničních zařízeních nebo službách v cloudu.

Stejně jako v cloudu Event Grid modul IoT Edge zpracovává směrování, filtrování a spolehlivé doručování událostí ve velkém měřítku. Filtrovat události, aby se zajistilo, že se do různých obslužných rutin událostí odesílají pouze relevantní události pomocí pokročilých řetězcových, číselných a logických filtrů. Logika opakování zajišťuje, že událost dosáhne cílového cíle i v případě, že není k dispozici v době publikování. Umožňuje použít Event Grid v IoT Edge jako výkonný mechanismus úložiště a dopředné mechanismu.

Event Grid v IoT Edge podporuje jak CloudEvents v 1.0, tak i vlastní schémata událostí. Zároveň podporuje stejnou sémantiku pro publikování na více systémů, jako Event Grid v cloudu, a umožňuje tak snadnou spolupráci.

Tento článek poskytuje přehled Azure Event Grid v IoT Edge. Podrobné pokyny, jak používat tento modul na hraničních zařízeních, najdete v tématu [publikování a přihlášení k odběru událostí místně](pub-sub-events-webhook-local.md). 

![Event Grid IoT Edge modelu zdrojů a obslužných rutin](../media/edge-overview/functional-model.png)

Tento obrázek ukazuje některé způsoby, jak můžete použít Event Grid na IoT Edge a není vyčerpávající seznam podporovaných funkcí.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Kdy použít Event Grid v IoT Edge

Event Grid v IoT Edge nabízí snadno použitelný a spolehlivý model událostí pro mezi hraničním a cloudem.

Event Grid v IoT Edge je postavená s symetrickou plochou pro modul runtime do cloudové služby Azure, takže můžete použít stejné události a volání rozhraní API kdekoli, co potřebujete. Bez ohledu na to, jestli v cloudu provedete Pub/sub, může být na hranici nebo mezi těmito dvěma Event Grid v IoT Edge teď vaše řešení pro přechod do více stran.

Pomocí Event Grid na IoT Edge můžete aktivovat jednoduché pracovní postupy mezi moduly. Například vytvořte téma a z modulu úložiště publikujte události "objekt BLOB služby Storage" vytvořený v tématu. Nyní můžete k těmto tématům přihlašovat odběr jedné nebo několika funkcí nebo vlastních modulů.

Rozšíříte své funkce mezi hraničními zařízeními. Pokud publikujete události modulu objektů BLOB a chcete používat výpočetní výkon více zařízení poblíž hraničních zařízení, vytvořte odběry mezi zařízeními.

Nakonec se připojte ke cloudu. Pokud mají být události modulu objektů BLOB pravidelně synchronizovány do cloudu, využijte vyšší výpočetní prostředky, které jsou k dispozici v cloudu, nebo zasílejte zpracovaná data, vytvořte další odběry cloudových služeb.

Event Grid v IoT Edge poskytuje flexibilní a spolehlivě oddělené architektury pro události.

## <a name="event-sources"></a>Zdroje událostí

Podobně jako v cloudu Event Grid v IoT Edge umožňuje přímou integraci mezi moduly pro sestavování architektur řízených událostmi. V současné době mohou být události odesílány do Event Grid IoT Edge z:

* Azure Blob Storage ve službě IoT Edge
* CloudEvents zdroje
* Vlastní moduly & kontejnery prostřednictvím HTTP POST

## <a name="event-handlers"></a>Obslužné rutiny událostí

Event Grid na IoT Edge je sestavená tak, aby odesílala události kdekoli, kde chcete. V současné době jsou podporovány následující cíle:

* Další moduly zahrnující IoT Hub, funkce a vlastní moduly
* Jiná hraniční zařízení
* WebHooky
* Cloudová služba Azure Event Grid
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus
* Fronty služby Storage

## <a name="supported-environments"></a>Podporovaná prostředí
V současné době jsou podporována prostředí Windows 64-bit, Linux 64-bit a ARM 32-bit.

## <a name="concepts"></a>Koncepty

V Azure Event Grid existuje pět konceptů, které vám umožní začít:

* **Události** – co se stalo.
* **Zdroje událostí** , kde došlo k události.
* **Témata** – koncový bod, kde vydavatelé odesílají události.
* **Odběry událostí** – koncový bod nebo integrovaný mechanismus pro směrování událostí, někdy do více než jedné obslužné rutiny. Pomocí odběrů taky obslužné rutiny inteligentně filtrují příchozí události.
* **Obslužné rutiny událostí** – aplikace nebo služba, která reaguje na událost.

## <a name="cost"></a>Náklady

Event Grid v IoT Edge je ve verzi Public Preview zdarma.

## <a name="issues"></a>Problémy
Nahlaste všechny problémy pomocí Event Grid v IoT Edge na [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .

## <a name="next-steps"></a>Další kroky

* [Publikování, přihlášení k odběru událostí místně](pub-sub-events-webhook-local.md)
* [Publikování, přihlášení k odběru událostí v cloudu](pub-sub-events-webhook-cloud.md)
* [Předávané události do cloudu Event Grid](forward-events-event-grid-cloud.md)
* [Předávané události do IoTHub](forward-events-iothub.md)
* [Reakce na události služby Blob Storage v místním prostředí](react-blob-storage-events-locally.md)