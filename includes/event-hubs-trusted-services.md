---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654445"
---
## <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu
Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu obejít toto nastavení brány firewall** , udělí se těmto službám přístup k vašim prostředkům Event Hubs.

| Důvěryhodná služba | Podporované scénáře použití | 
| --------------- | ------------------------- | 
| Azure Event Grid | Povoluje Azure Event Grid odesílat události do Center událostí ve vašem oboru názvů Event Hubs. Je také nutné provést následující kroky: <ul><li>Povolit identitu přiřazenou systémem pro téma nebo doménu</li><li>Přidejte identitu do role odesilatele dat Azure Event Hubs v oboru názvů Event Hubs</li><li>Pak nakonfigurujte odběr událostí, který používá centrum událostí jako koncový bod pro použití identity přiřazené systémem.</li></ul> <p>Další informace najdete v tématu [doručování událostí se spravovanou identitou](../articles/event-grid/managed-service-identity.md) .</p>|
| Azure Monitor (nastavení diagnostiky) | Umožňuje Azure Monitor odesílat diagnostické informace do Center událostí ve vašem oboru názvů Event Hubs. |