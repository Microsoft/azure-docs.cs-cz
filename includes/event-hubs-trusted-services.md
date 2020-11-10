---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427189"
---
## <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu
Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** , udělí se těmto službám přístup k vašim prostředkům Event Hubs.

| Důvěryhodná služba | Podporované scénáře použití | 
| --------------- | ------------------------- | 
| Azure Event Grid | Povoluje Azure Event Grid odesílat události do Center událostí ve vašem oboru názvů Event Hubs. Je také nutné provést následující kroky: <ul><li>Povolit identitu přiřazenou systémem pro téma nebo doménu</li><li>Přidejte identitu do role odesilatele dat Azure Event Hubs v oboru názvů Event Hubs</li><li>Pak nakonfigurujte odběr událostí, který používá centrum událostí jako koncový bod pro použití identity přiřazené systémem.</li></ul> <p>Další informace najdete v tématu [doručování událostí se spravovanou identitou](../articles/event-grid/managed-service-identity.md) .</p>|
| Azure Monitor (nastavení diagnostiky) | Umožňuje Azure Monitor odesílat diagnostické informace do Center událostí ve vašem oboru názvů Event Hubs. |
| Azure Stream Analytics | Umožňuje Azure Stream Analytics úlohy číst data z ([vstup](../articles/stream-analytics/stream-analytics-add-inputs.md)) nebo zapisovat data do ([výstupních](../articles/stream-analytics/event-hubs-output.md)) centra událostí ve vašem oboru názvů Event Hubs. |
| Azure IoT Hub | Povoluje IoT Hub odesílat zprávy do Center událostí v oboru názvů centra událostí. Je také nutné provést následující kroky: <ul><li>Povolení identity přiřazené systémem pro Centrum IoT</li><li>Přidejte identitu do role odesilatele dat Azure Event Hubs v oboru názvů Event Hubs.</li><li>Pak nakonfigurujte IoT Hub, který používá centrum událostí jako vlastní koncový bod pro použití ověřování založeného na identitách.</li></ul>
