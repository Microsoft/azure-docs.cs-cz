---
title: Porovnat Event Grid, směrování pro IoT Hub | Microsoft Docs
description: IoT Hub nabízí svoji vlastní směrovací službu zpráv, ale integruje se s Event Grid pro publikování událostí. Porovnejte tyto dvě funkce.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f0853261e770b2cba9a243ae66b0b0d766fcd92
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024683"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porovnat směrování zpráv a Event Grid IoT Hub

Azure IoT Hub poskytuje možnost streamovat data z připojených zařízení a integrovat tato data do vašich obchodních aplikací. IoT Hub nabízí dvě metody pro integraci událostí IoT do jiných služeb nebo obchodních aplikací Azure. Tento článek pojednává o dvou funkcích, které tuto funkci poskytují, abyste si mohli vybrat, která možnost je pro váš scénář nejvhodnější.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub směrování zpráv](iot-hub-devguide-messages-d2c.md)**: tato funkce IoT Hub umožňuje uživatelům směrovat zprávy typu zařízení-Cloud do koncových bodů služby, jako jsou kontejnery Azure Storage, Event Hubs, Service Bus fronty a Service Bus témata. Směrování také poskytuje možnost dotazování pro filtrování dat před jejich směrováním do koncových bodů. Kromě dat telemetrie zařízení můžete také odesílat [události bez telemetrie](iot-hub-devguide-messages-d2c.md#non-telemetry-events) , které se dají použít ke spuštění akcí. 

**IoT Hub integrace s Event Grid**: Azure Event Grid je plně spravovaná služba Směrování událostí, která používá model publikování a odběru. IoT Hub a Event Grid vzájemně spolupracují a [integrují IoT Hub události do služeb Azure a mimo Azure, a](iot-hub-event-grid.md)to téměř v reálném čase. IoT Hub publikuje [události zařízení](iot-hub-event-grid.md#event-types) i události telemetrie.

## <a name="differences"></a>Rozdíly

Jak směrování zpráv, tak Event Grid povolit konfiguraci výstrah, existují některé klíčové rozdíly mezi těmito dvěma typy. Podrobnosti najdete v následující tabulce:

| Příznak | Směrování zpráv IoT Hub | IoT Hub integrace s Event Grid |
| ------- | --------------- | ---------- |
| **Zprávy a události zařízení** | Ano, směrování zpráv lze použít pro data telemetrie, sestavu nedokončených změn zařízení a události životního cyklu zařízení (např. Když jsou zařízení vytvořená, Odstraněná, připojená a odpojená od IoT Hub), a události změny digitálního vlákna. | Ano, Event Grid lze použít pro události dat telemetrie a životního cyklu zařízení. Ale Event Grid se nedá použít pro události změny zařízení, které jsou v něm události, a události změny digitálního vlákna. |
| **Řazení** | Ano, řazení událostí je zachováno.  | Ne, pořadí událostí není zaručeno. | 
| **Filtrování** | Bohatá filtrování vlastností aplikace zprávy, vlastností systému zpráv, textu zprávy, dvojitých značek zařízení a vlastností, které jsou v zařízení. Filtrování není aplikováno na události změny digitálního vlákna. Příklady najdete v tématu [syntaxe dotazu směrování zpráv](iot-hub-devguide-routing-query-syntax.md). | Filtrování na základě typu události, typu subjektu a atributů v každé události. Příklady najdete v tématu [porozumění událostem filtrování v Předplatných Event Grid](../event-grid/event-filtering.md). Při přihlášení k odběru událostí telemetrie můžete pro data použít další filtry pro filtrování vlastností zpráv, textu zprávy a vlákna zařízení v IoT Hub před publikováním do Event Grid. Viz [jak filtrovat události](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Koncové body** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Fronta služby Service Bus</li> <li>Témata služby Service Bus</li></ul><br>Placené IoT Hub SKU (S1, S2 a S3) jsou omezené na 10 vlastních koncových bodů. na IoT Hub lze vytvořit trasy 100. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Vlastní témata</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Služby třetích stran prostřednictvím webhooků</li></ul><br>jsou podporovány koncové body 500 na IoT Hub. Nejaktuálnější seznam koncových bodů naleznete v tématu [Event Grid obslužných rutin událostí](../event-grid/overview.md#event-handlers). |
| **Náklady** | Za směrování zpráv se neúčtují žádné samostatné poplatky. Účtují se jenom příchozí telemetrie do IoT Hub. Pokud je například zpráva směrována do tří různých koncových bodů, bude se vám účtovat jenom jedna zpráva. | IoT Hub se neúčtují žádné poplatky. Event Grid poskytuje zdarma první 100 000 operací za měsíc a potom $0,60 za milion operací. |

## <a name="similarities"></a>Podobnosti

IoT Hub směrování zpráv a Event Grid mají podobné podobnosti, některé z nich jsou podrobně popsané v následující tabulce:

| Příznak | Směrování zpráv IoT Hub | IoT Hub integrace s Event Grid |
| ------- | --------------- | ---------- |
| **Maximální velikost zprávy** | 256 KB, ze zařízení do cloudu | 256 KB, ze zařízení do cloudu |
| **Spolehlivost** | Vysoká: každý pro každou trasu dodá každé zprávě na koncový bod aspoň jednou. Vyprší platnost všech zpráv, které nejsou doručeny do jedné hodiny. | Vysoká: každý odběr zajišťuje každou zprávu pro Webhook alespoň jednou. Vyprší platnost všech událostí, které nejsou dodány do 24 hodin. | 
| **Škálovatelnost** | Vysoká: optimalizováno pro podporu milionů současně připojených zařízení, která odesílají miliardy zpráv. | Vysoká: podporuje směrování 10 000 000 událostí za sekundu na oblast. |
| **Latence** | Nízká: téměř v reálném čase. | Nízká: téměř v reálném čase. |
| **Odeslat do více koncových bodů** | Ano, odeslat jednu zprávu do více koncových bodů. | Ano, odeslat jednu zprávu do více koncových bodů.  
| **Zabezpečení** | IoT Hub poskytuje identitu jednotlivých zařízení a řízení přístupu odvolatelné. Další informace najdete v tématu [řízení přístupu IoT Hub](iot-hub-devguide-security.md). | Event Grid poskytuje ověřování tři body: odběry událostí, publikování událostí a doručování událostí Webhooku. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak si vybrat

IoT Hub směrování zpráv a integrace IoT Hub s Event Grid provádět různé akce a dosáhnout podobných výsledků. Přebírají informace z vašeho řešení IoT Hub a předají je, aby ostatní služby mohly reagovat. Jak tedy určíte, která z nich se má použít? Vezměte v úvahu následující otázky, které vám pomůžou s vaším rozhodnutím: 

* **Jaký druh dat odesíláte do koncových bodů?**

   Směrování zpráv IoT Hub použijte v případě, že je nutné odesílat data telemetrie jiným službám. Směrování zpráv také umožňuje dotazování aplikace a vlastností systému, textu zprávy, značek a dvojitě vydaných vlastností zařízení.

   IoT Hub Integration with Event Grid funguje s událostmi, ke kterým dochází ve službě IoT Hub. Tyto události IoT Hub zahrnují data telemetrie, zařízení vytvořená, Odstraněná, připojená a odpojená. Při přihlášení k odběru událostí telemetrie můžete pro data použít další filtry pro filtrování vlastností zpráv, textu zprávy a vlákna zařízení v IoT Hub před publikováním do Event Grid. Viz [jak filtrovat události](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Jaké koncové body potřebují přijímat tyto informace?**

   Směrování zpráv IoT Hub podporuje omezený počet jedinečných koncových bodů a typů koncových bodů, ale můžete vytvořit konektory pro přesměrování dat a událostí do dalších koncových bodů. Úplný seznam podporovaných koncových bodů najdete v tabulce v předchozí části. 

   IoT Hub integrace s Event Grid podporuje 500 koncových bodů na IoT Hub a větší množství typů koncových bodů. Nativně se integruje s Azure Functions, Logic Apps, úložištěm a Service Bus frontami a také spolupracuje s Webhooky k rozšiřování odesílání dat mimo ekosystém služby Azure a do obchodních aplikací třetích stran.

* **Záleží na tom, jestli vaše data dorazí do objednávky?**

   Směrování zpráv IoT Hub udržuje pořadí, ve kterém se zprávy odesílají, aby byly doručeny stejným způsobem.

   Event Grid nezaručuje, že koncovým bodům budou přijímat události ve stejném pořadí, ve kterém došlo k. V případech, kdy je absolutní pořadí zpráv významné a/nebo kdy spotřebitel potřebuje důvěryhodný jedinečný identifikátor pro zprávy, doporučujeme použít směrování zpráv. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [směrování zpráv IoT Hub](iot-hub-devguide-messages-d2c.md) a [koncových bodech IoT Hub](iot-hub-devguide-endpoints.md).
* Další informace o službě [Azure Event Grid](../event-grid/overview.md).
* Informace o tom, jak vytvořit směrování zpráv, najdete v kurzu [IoT Hub zpracování zpráv ze zařízení do cloudu pomocí tras](../iot-hub/tutorial-routing.md) .
* Vyzkoušejte Event Grid integraci [odesláním e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
