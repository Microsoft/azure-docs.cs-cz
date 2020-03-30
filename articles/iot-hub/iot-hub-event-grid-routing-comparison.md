---
title: Porovnat mřížku událostí, směrování pro službu IoT Hub | Dokumenty společnosti Microsoft
description: IoT Hub nabízí vlastní službu směrování zpráv, ale také se integruje s Event Grid pro publikování událostí. Porovnejte tyto dvě funkce.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906785"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porovnání směrování zpráv a mřížky událostí pro službu IoT Hub

Azure IoT Hub poskytuje možnost streamovat data z připojených zařízení a integrovat tato data do vašich obchodních aplikací. IoT Hub nabízí dvě metody integrace událostí IoT do jiných služeb Azure nebo obchodních aplikací. Tento článek popisuje dvě funkce, které poskytují tuto možnost, takže si můžete vybrat, která možnost je nejvhodnější pro váš scénář.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md)**: Tato funkce služby IoT Hub umožňuje uživatelům směrovat zprávy mezi zařízeními do cloudu do koncových bodů služby, jako jsou kontejnery služby Azure Storage, centra událostí, fronty service bus a témata služby Service Bus. Směrování také poskytuje možnost dotazování filtrovat data před směrováním do koncových bodů. Kromě telemetrických dat zařízení můžete také odesílat [události bez telemetrie,](iot-hub-devguide-messages-d2c.md#non-telemetry-events) které lze použít ke spuštění akcí. 

**Integrace IoT Hubu s Událostí Grid**: Azure Event Grid je plně spravovaná služba směrování událostí, která používá model publikování a odběru. IoT Hub a Event Grid spolupracují na [integraci událostí IoT Hubu do služeb Azure i mimo Azure](iot-hub-event-grid.md)v téměř reálném čase. IoT Hub publikuje [události zařízení](iot-hub-event-grid.md#event-types) a telemetrické události.

## <a name="differences"></a>Rozdíly

Zatímco směrování zpráv a event grid umožňují konfiguraci výstrah, existují některé klíčové rozdíly mezi těmito dvěma. Podrobnosti naleznete v následující tabulce:

| Funkce | Směrování zpráv služby IoT Hub | Integrace ioT hubu s event gridem |
| ------- | --------------- | ---------- |
| **Zprávy a události zařízení** | Ano, směrování zpráv lze použít pro telemetrická data, změny dvojčete zařízení, události životního cyklu zařízení a události změny digitálních dvojčat (součást [náhledu veřejné verze IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md) | Ano, Event Grid lze použít pro telemetrická data, ale můžete také hlásit, když jsou zařízení vytvořena, odstraněna, připojena a odpojena od služby IoT Hub |
| **Řazení** | Ano, řazení událostí je zachováno.  | Ne, pořadí událostí není zaručeno. | 
| **Filtrování** | Rozšířené filtrování vlastností aplikace zpráv, vlastností systému zpráv, textu zprávy, značek dvojčete zařízení a vlastností dvojčete zařízení. Filtrování se nepoužije na události změny digitálních dvojčat. Příklady naleznete v [tématu Syntaxe směrovacího dotazu zprávy](iot-hub-devguide-routing-query-syntax.md). | Filtrování na základě typu události, typu předmětu a atributů v každé události. Příklady naleznete v [tématu Principy filtrování událostí v odběrech mřížky událostí](../event-grid/event-filtering.md). Při přihlášení k odběru událostí telemetrie, můžete použít další filtry na data filtrovat vlastnosti zprávy, tělo zprávy a dvojče zařízení v centru IoT, před publikováním na Event Grid. Podívejte [se, jak filtrovat události](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Koncové body** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Fronta služby Service Bus</li> <li>Témata služby Service Bus</li></ul><br>Placené sloky centra IoT Hub (S1, S2 a S3) jsou omezeny na 10 vlastních koncových bodů. Pro centrum IoT Hub lze vytvořit 100 tras. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Vlastní témata</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Služby třetích stran prostřednictvím webhooků</li></ul><br>500 koncových bodů na IoT Hub jsou podporované. Nejaktuálnější seznam koncových bodů naleznete v tématu [Obslužné rutiny událostí Event Grid](../event-grid/overview.md#event-handlers). |
| **Náklady** | Pro směrování zpráv se neúčtuje žádný zvláštní poplatek. Je účtován pouze přenos telemetrie do služby IoT Hub. Například pokud máte zprávu směrovnou na tři různé koncové body, bude se vám účtovat pouze jedna zpráva. | Z IoT Hubu se neplatí žádný poplatek. Event Grid nabízí prvních 100 000 operací měsíčně zdarma a poté 0,60 USD za milion operací. |

## <a name="similarities"></a>Podobnosti

Směrování zpráv služby IoT Hub a mřížka událostí mají také podobnosti, z nichž některé jsou podrobně popsány v následující tabulce:

| Funkce | Směrování zpráv služby IoT Hub | Integrace ioT hubu s event gridem |
| ------- | --------------- | ---------- |
| **Maximální velikost zprávy** | 256 KB, zařízení-cloud | 256 KB, zařízení-cloud |
| **Spolehlivost** | Vysoká: Doručuje každou zprávu do koncového bodu alespoň jednou pro každou trasu. Vyprší platnost všech zpráv, které nejsou doručeny do jedné hodiny. | Vysoká: Doručuje každou zprávu do webhooku alespoň jednou pro každé předplatné. Vyprší platnost všech událostí, které nejsou doručeny do 24 hodin. | 
| **Škálovatelnost** | Vysoká: Optimalizováno pro podporu milionů současně připojených zařízení odesílajících miliardy zpráv. | Vysoká: Dokáže směrovat 10 000 000 událostí za sekundu na oblast. |
| **Latence** | Nízká: Téměř v reálném čase. | Nízká: Téměř v reálném čase. |
| **Odeslání více koncovým bodům** | Ano, odeslat jednu zprávu do více koncových bodů. | Ano, odeslat jednu zprávu do více koncových bodů.  
| **Zabezpečení** | Služba Iot Hub poskytuje identitu pro jedno zařízení a řízení odvolatelného přístupu. Další informace naleznete v [řízení přístupu k centru IoT Hub](iot-hub-devguide-security.md). | Event Grid poskytuje ověření na třech bodech: odběry událostí, publikování událostí a doručování událostí webhooku. Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak si vybrat

Směrování zpráv služby IoT Hub a integrace služby IoT Hub s gridem událostí provádějí různé akce, aby bylo dosaženo podobných výsledků. Oba berou informace z vašeho řešení IoT Hub a předávají je dál, aby ostatní služby mohly reagovat. Tak jak se rozhodnete, který z nich použít? Zvažte následující otázky, které vám pomohou řídit vaše rozhodnutí: 

* **Jaký druh dat odesíláte koncovým bodům?**

   Směrování zpráv služby IoT Hub použijte, když máte k odeslání telemetrických dat do jiných služeb. Směrování zpráv také umožňuje dotazování aplikace zprávy a vlastnosti systému, tělo zprávy, značky dvojčete zařízení a vlastnosti dvojčete zařízení.

   Integrace služby IoT Hub s gridem událostí funguje s událostmi, ke kterým dochází ve službě IoT Hub. Tyto události služby IoT Hub zahrnují telemetrická data, vytvořené zařízení, odstraněné, připojené a odpojené. Při přihlášení k odběru událostí telemetrie, můžete použít další filtry na data filtrovat vlastnosti zprávy, tělo zprávy a dvojče zařízení v centru IoT, před publikováním na Event Grid. Podívejte [se, jak filtrovat události](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Jaké koncové body je třeba získat tyto informace?**

   Směrování zpráv služby IoT Hub podporuje omezený počet jedinečných koncových bodů a typů koncových bodů, ale můžete vytvořit konektory pro přesměrování dat a událostí do dalších koncových bodů. Úplný seznam podporovaných koncových bodů najdete v tabulce v předchozí části. 

   Integrace IoT Hub u Sítě událostí podporuje 500 koncových bodů na IoT Hub a větší škálu typů koncových bodů. Nativně se integruje s frontami Azure Functions, Logic Apps, Storage a Service Bus a také spolupracuje s webhooky a rozšiřuje odesílání dat mimo ekosystém služeb Azure a do podnikových aplikací třetích stran.

* **Záleží na tom, jestli vaše data dorazí v pořádku?**

   Směrování zpráv služby IoT Hub udržuje pořadí, ve kterém jsou odesílány zprávy, tak, aby byly doručeny stejným způsobem.

   Event Grid nezaručuje, že koncové body obdrží události ve stejném pořadí, ve které došlo. Pro případy, ve kterých je významné absolutní pořadí zpráv nebo ve kterých spotřebitel potřebuje důvěryhodný jedinečný identifikátor pro zprávy, doporučujeme použít směrování zpráv. 

## <a name="next-steps"></a>Další kroky

* Další informace o [směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md) a [koncových bodech služby IoT Hub](iot-hub-devguide-endpoints.md).
* Další informace o službě [Azure Event Grid](../event-grid/overview.md).
* Informace o tom, jak vytvořit trasy zpráv, najdete [v tématu proces ioT hub zařízení cloud zprávy pomocí trasy](../iot-hub/tutorial-routing.md) kurzu.
* Vyzkoušejte integraci Grid událostí [odesláním e-mailových oznámení o událostech služby Azure IoT Hub pomocí logic apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
