---
title: Architektury řízené událostmi na hraničních zařízeních – Azure Event Grid na IoT Edge
description: Azure Event Grid můžete použít jako modul na IoT Edge pro události vpřed mezi moduly, hraničními zařízeními a cloudem.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844648"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Co je Azure Event Grid na Azure IoT Edge?
Event Grid na IoT Edge přináší výkon a flexibilitu Azure Event Grid na okraj. Vytvářejte témata, publikujte události a odebíráte si více cílů bez ohledu na to, zda se jedná o moduly na stejném zařízení, jiných hraničních zařízeních nebo službách v cloudu.

Stejně jako v cloudu zpracovává modul Event Grid na IoT Edge směrování, filtrování a spolehlivé doručování událostí ve velkém měřítku. Filtrování událostí, abyste zajistili, že do různých obslužných rutin událostí pomocí rozšířených řetězcových, číselných a logických filtrů budou odesílat pouze relevantní události. Logika opakování zajišťuje, že událost dosáhne cílového cíle i v případě, že není k dispozici v době publikování. Umožňuje používat Event Grid na IoT Edge jako výkonný mechanismus úložiště a dopředu.

Event Grid na IoT Edge podporuje cloudevents v1.0 a vlastní schémata událostí. Podporuje také stejnou pub/sub sémantiku jako Event Grid v cloudu pro snadnou interoperabilitu.

Tento článek obsahuje přehled Azure Event Grid na IoT Edge. Podrobné pokyny k použití tohoto modulu na hraničních zařízeních naleznete v tématu [Publish, subscribe to events locally](pub-sub-events-webhook-local.md). 

![Mřížka událostí na modelu zdrojů a obslužných rutin IoT Edge](../media/edge-overview/functional-model.png)

Tento obrázek znázorňuje některé způsoby použití event gridu na okraji IoT edge a není úplný seznam podporovaných funkcí.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Kdy použít mřížku událostí na ioT edge

Event Grid na IoT Edge poskytuje snadno použitelný a spolehlivý model událostí mezi hranou a cloudem.

Event Grid na IoT Edge je sestavený s symetrickou plochou runtime pro cloudovou službu Azure, takže můžete používat stejné události a volání rozhraní API všude tam, kde potřebujete. Ať už děláte pub/sub v cloudu, na okraji nebo mezi těmito dvěma, Event Grid na IoT Edge teď může být vaše jediné řešení.

Pomocí funkce Event Grid na ioT edge můžete aktivovat jednoduché pracovní postupy mezi moduly. Můžete například vytvořit téma a publikovat události objektu blob úložiště z modulu úložiště do tématu. Nyní se můžete přihlásit k odběru jedné nebo několika funkcí nebo vlastních modulů k tomuto tématu.

Rozšiřte své funkce mezi hraničními zařízeními. Pokud publikujete události modulu blob a chcete použít výpočetní výkon více zařízení v blízkosti hraničních zařízení, vytvořte předplatná mezi zařízeními.

Nakonec se připojte ke cloudu. Pokud se události vašeho modulu blob mají pravidelně synchronizovat do cloudu, použijte větší výpočetní prostředky dostupné v cloudu nebo odesílejte zpracovaná data, vytvořte další předplatná cloudových služeb.

Event Grid na IoT Edge poskytuje flexibilní a spolehlivou oddělenou architekturu událostí.

## <a name="event-sources"></a>Zdroje událostí

Stejně jako v cloudu, Event Grid na IoT Edge umožňuje přímou integraci mezi moduly k vytváření architektur řízených událostmi. V současné době mohou být události odeslány do event gridu na IoT Edge z:

* Azure Blob Storage ve službě IoT Edge
* Zdroje CloudEvents
* Vlastní moduly & kontejnery přes HTTP POST

## <a name="event-handlers"></a>Obslužné rutiny událostí

Event Grid na IoT Edge je vytvořený tak, aby odesílá události kamkolichcete. V současné době jsou podporovány následující cíle:

* Další moduly včetně IoT Hubu, funkcí a vlastních modulů
* Ostatní hraniční zařízení
* Webhooky
* Cloudová služba Azure Event Grid
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus
* Fronty služby Storage

## <a name="supported-environments"></a>Podporovaná prostředí
V současné době jsou podporována 64bitová, 64bitová a ARM 32bitová prostředí systému Windows.

## <a name="concepts"></a>Koncepty

Ve službě Azure Event Grid je pět konceptů, které vám umožní začít:

* **Události** — Co se stalo.
* **Zdroje událostí** — Kde se událost konala.
* **Témata** – koncový bod, kde vydavatelé odesílají události.
* **Odběry událostí** – koncový bod nebo integrovaný mechanismus pro směrování událostí, někdy na více než jednu obslužnou rutinu. Pomocí odběrů taky obslužné rutiny inteligentně filtrují příchozí události.
* **Obslužné rutiny událostí** – aplikace nebo služba, která reaguje na událost.

## <a name="cost"></a>Náklady

Event Grid na IoT Edge je zdarma během public preview.

## <a name="issues"></a>Problémy
Nahlásit všechny problémy s používáním [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)funkce Event Grid na ioT edge na adrese .

## <a name="next-steps"></a>Další kroky

* [Publikovat, přihlásit se k odběru událostí místně](pub-sub-events-webhook-local.md)
* [Publikovat, přihlásit se k odběru událostí v cloudu](pub-sub-events-webhook-cloud.md)
* [Přeposílání událostí do cloudu Event Grid](forward-events-event-grid-cloud.md)
* [Předávání událostí na IoTHub](forward-events-iothub.md)
* [Reakce na události služby Blob Storage v místním prostředí](react-blob-storage-events-locally.md)