---
title: Porovnání událostí mřížky, směrování pro IoT Hub | Microsoft Docs
description: IoT Hub nabízí vlastní službu Směrování zpráv, ale pro publikování událostí se také integruje s událostí mřížky. Porovnejte tyto dvě funkce.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 320320687e441a1296065eb9d0b7b12771036459
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636167"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porovnání směrování zpráv a událostí mřížky pro IoT Hub

Azure IoT Hub poskytuje možnost Streamovat data z připojených zařízení a integraci dat do obchodních aplikací. IoT Hub nabízí dvě metody k integraci IoT události do jiných služeb Azure nebo obchodní aplikace. Tento článek popisuje dvě funkce, které tuto funkci, aby mohli vybrat, která možnost je nejvhodnější pro váš scénář.

* **Směrování zpráv služby IoT Hub**: Toto centrum IoT funkce umožňuje uživatelům [směrování zpráv typu zařízení cloud](iot-hub-devguide-messages-read-custom.md) ke koncovým bodům služby jako kontejnery Azure Storage, Event Hubs, fronty Service Bus a témat sběrnice Service Bus. Pravidla směrování dáte možnost provádět na základě dotazů trasy. Umožňují také [kritické výstrahy](iot-hub-devguide-messages-d2c.md) , spuštění akcí prostřednictvím dotazy a může být založen na hlavičky zpráv a text. 
* **Integrace služby IoT Hub s událostí mřížky**: mřížky událostí Azure je plně spravovaná událostí směrování služba, která používá publikování-přihlášení k odběru modelu. IoT Hub a mřížky událostí společně [integrovat do Azure a služby jiných Azure IoT Hub události](iot-hub-event-grid.md), v skoro v reálném čase. 

## <a name="similarities-and-differences"></a>Podobnosti a rozdílů

Sice směrování zpráv a událostí mřížky povolit konfiguraci výstrahy, existují některé hlavní rozdíly mezi nimi. Naleznete v následující tabulce Podrobnosti:

| Funkce | Směrování zpráv služby IoT Hub | Integrace služby IoT Hub s událostí mřížky |
| ------- | --------------- | ---------- |
| **Zprávy zařízení** | Ano, směrování zpráv slouží pro telemetrická data. | Ne, události mřížky pouze slouží pro události služby IoT Hub-telemetrická data. |
| **Typ události** | Ano, směrování zpráv může hlásit twin změny a události životního cyklu zařízení. | Ano, mřížky událostí může hlásit, když se zařízení zaregistrují do služby IoT Hub a v případě zařízení se odstraní. |
| **Řazení** | Ano, je udržovat řazení událostí.  | Ne, pořadí událostí není zaručena. | 
| **Maximální velikost zprávy** | 256 KB, zařízení cloud | 64 kB |
| **Filtrování** | Bohaté možnosti filtrování pomocí jazyka SQL podporuje filtrování záhlaví zprávy a obsah. Příklady najdete v tématu [IoT Hub dotazovací jazyk](iot-hub-devguide-query-language.md). | Filtrování podle přípony/předpona ID zařízení, který funguje dobře pro hierarchické služby, jako je úložiště. |
| **Koncové body** | <ul><li>Centrum událostí</li> <li>Objekt blob úložiště</li> <li>Fronta služby Service Bus</li> <li>Service Bus Topics</li></ul><br>Placené SKU centra IoT (S1, S2 a S3) jsou omezeny na 10 vlastní koncové body. 100 trasy se dají vytvořit na IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Centrum událostí</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Služby třetích stran, prostřednictvím Webhooky</li></ul><br>Nejnovější seznam koncových bodů najdete v tématu [obslužné rutiny událostí událostí mřížky](../event-grid/overview.md#event-handlers). |
| **Náklady** | Je bezplatná samostatné pro směrování zpráv. Pouze příchozí telemetrická data do služby IoT Hub je účtován. Například pokud máte zprávu směruje na tři různými koncovými body, se vám účtuje pouze jednu zprávu. | Není nijak zpoplatněn ze služby IoT Hub. Událost mřížky nabízí nejprve 100 000 operací za měsíc zdarma a pak 0,60 $ za mil. operace, po který. |

Směrování zpráv služby IoT Hub a událostí mřížky mít podobnosti příliš, z nichž některé jsou podrobně popsané v následující tabulce:

| Funkce | Směrování zpráv služby IoT Hub | Integrace služby IoT Hub s událostí mřížky |
| ------- | --------------- | ---------- |
| **Spolehlivost** | Vysoká: Přináší každou zprávu koncový bod alespoň jednou pro každý postup. Platnost vyprší všechny zprávy, které nejsou doručeny do jedné hodiny. | Vysoká: Přináší každou zprávu webhooku alespoň jednou pro každé předplatné. Platnost vyprší všechny události, které nejsou doručeny do 24 hodin. | 
| **Škálovatelnost** | Vysoká: Optimalizované pro podporu miliony současně připojených zařízení odesílání až miliardy zprávy. | Vysoká: Umožňující směrování 10 000 000 událostí za sekundu na oblast. |
| **Latence** | Nízká: Skoro v reálném čase. | Nízká: Skoro v reálném čase. |
| **Poslat několik koncových bodů** | Ano, odesílat do jedné zprávy do víc koncových bodů. | Ano, odesílat do jedné zprávy do víc koncových bodů.  | 
| **Zabezpečení** | Služba IOT Hub zajišťuje podle zařízení identity a řízení přístupu odvolatelné. Další informace najdete v tématu [řízení přístupu služby IoT Hub](iot-hub-devguide-security.md). | Událost mřížky poskytuje ověření na tři body: událost odběry, publikování událostí a webhooku události doručení. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak zvolit

Směrování zpráv služby IoT Hub a integraci služby IoT Hub s událostí mřížky provádět různé akce k dosažení podobné výsledky. Budou trvat informace z vašeho řešení IoT Hub i předat na, aby mohly další služby reagovat. Jak můžete rozhodnout, které z nich používat? Kromě data z předchozí části použijte následující otázky seznamů vaše rozhodnutí: 

* **Jaký typ dat jsou odesílání do koncových bodů?**

   Pomocí služby IoT Hub směrování zpráv při Neodesílat telemetrická data do jiných služeb. Zpráva směrování také umožňuje dotazování záhlaví zprávy a obsah zprávy. 

   Integrace služby IoT Hub s událostí mřížky funguje s událostmi, ke kterým dochází ve službě IoT Hub. Tyto události služby IoT Hub zahrnují vytváření zařízení a odstranění. 

* **Jaké koncových bodů je nutné přijmout tyto informace?**

   Směrování zpráv služby IoT Hub podporuje omezenou koncových bodů, ale můžete vytvořit konektory přesměrovat data a události na další koncové body. Úplný seznam podporovaných koncových bodů najdete v tabulce v předchozí části. 

   Integrace služby IoT Hub s událostí mřížky podporuje další koncové body. Spolupracuje také s webhooky rozšíření směrování mimo ekosystém služby Azure a do jiných obchodních aplikací. 

* **Je důležité, pokud vaše data dorazí v pořadí?**

   Směrování zpráv služby IoT Hub uchovává pořadí, ve kterém jsou zprávy odesílány, tak, aby dorazí stejným způsobem.

   Událost mřížky nezaručuje, že koncové body se zobrazí události ve stejném pořadí, aby k nim došlo. Schéma události však nezahrnuje časové razítko, který slouží k identifikaci pořadí po události, které přicházejí na koncový bod. 

## <a name="next-steps"></a>Další postup

* Další informace o [směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md) a [koncové body centra IoT](iot-hub-devguide-endpoints.md).

* Další informace o [Azure událostí mřížky](../event-grid/overview.md)

* Vyzkoušet integrace mřížky událostí podle [odesílající e-mailová oznámení o událostech Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md)