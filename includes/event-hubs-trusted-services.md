---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 743d7d46474b4ba55df50398f29cbdb964b5ff08
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978863"
---
## <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu
Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** , udělí se těmto službám přístup k vašim prostředkům Event Hubs.

| Důvěryhodná služba | Podporované scénáře použití | 
| --------------- | ------------------------- | 
| Azure Event Grid | Povoluje Azure Event Grid odesílat události do Center událostí ve vašem oboru názvů Event Hubs. Je také nutné provést následující kroky: <ul><li>Povolit identitu přiřazenou systémem pro téma nebo doménu</li><li>Přidejte identitu do role odesilatele dat Azure Event Hubs v oboru názvů Event Hubs</li><li>Pak nakonfigurujte odběr událostí, který používá centrum událostí jako koncový bod pro použití identity přiřazené systémem.</li></ul> <p>Další informace najdete v tématu [doručování událostí se spravovanou identitou](../articles/event-grid/managed-service-identity.md) .</p>|
| Azure Monitor (nastavení diagnostiky) | Umožňuje Azure Monitor odesílat diagnostické informace do Center událostí ve vašem oboru názvů Event Hubs. Azure Monitor může číst z centra událostí a také zapisovat data do centra událostí. |
| Azure Stream Analytics | Umožňuje Azure Stream Analytics úlohy číst data z ([vstup](../articles/stream-analytics/stream-analytics-add-inputs.md)) nebo zapisovat data do ([výstupních](../articles/stream-analytics/event-hubs-output.md)) centra událostí ve vašem oboru názvů Event Hubs. <p>**Důležité**: Stream Analytics úloha by měla být nakonfigurovaná tak, aby pro přístup k centru událostí používala **spravovanou identitu** . Další informace najdete v tématu [použití spravovaných identit pro přístup k centru událostí z Azure Stream Analytics úlohy (Preview)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Povoluje IoT Hub odesílat zprávy do Center událostí v oboru názvů centra událostí. Je také nutné provést následující kroky: <ul><li>Povolení identity přiřazené systémem pro Centrum IoT</li><li>Přidejte identitu do role odesilatele dat Azure Event Hubs v oboru názvů Event Hubs.</li><li>Pak nakonfigurujte IoT Hub, který používá centrum událostí jako vlastní koncový bod pro použití ověřování založeného na identitách.</li></ul>
