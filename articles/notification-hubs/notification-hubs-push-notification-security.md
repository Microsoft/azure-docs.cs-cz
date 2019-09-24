---
title: Notification Hubs zabezpečení
description: Toto téma vysvětluje zabezpečení pro centra oznámení Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 753493100bbdb34255574656a47217560e2d321a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213055"
---
# <a name="notification-hubs-security"></a>Notification Hubs zabezpečení

## <a name="overview"></a>Přehled

Toto téma popisuje model zabezpečení Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Zabezpečení sdíleného přístupového podpisu (SAS)

Notification Hubs implementuje schéma zabezpečení na úrovni entity nazývané SAS (sdílený přístupový podpis). Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sadu práv, jak je vysvětleno v tématu [deklarace identity zabezpečení](#security-claims). Při vytváření centra oznámení se automaticky vytvoří dvě pravidla: jednu s právy na **naslouchání** (kterou používá klientská aplikace) a druhá se **všemi** právy (které aplikace back-end používá).

Pokud při provádění správy registrace z klientských aplikací nejsou informace odesílané prostřednictvím oznámení citlivé (například aktualizace o počasí), je pro přístup k centru oznámení běžný způsob, jak získat přístup ke službě centrum oznámení, aby byla hodnota klíče pravidla pro příjem pouze pro přístup k klientské aplikaci, a pokud chcete, aby klíčová hodnota pravidla měla plný přístup k back-endu aplikace.

Aplikace by neměly vkládat hodnotu klíče do klientských aplikací pro Windows Store, místo toho si klientské aplikace načtou z back-endu aplikace při spuštění.

Klíč s přístupem k **naslouchání** umožňuje klientské aplikaci zaregistrovat se pro libovolnou značku. Pokud vaše aplikace musí omezit registrace na konkrétní značky pro konkrétní klienty (například když značky reprezentují ID uživatelů), musí back-end aplikace provést registrace. Další informace najdete v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md). Tímto způsobem klientská aplikace nebude mít přímý přístup k Notification Hubs.

## <a name="security-claims"></a>Deklarace identity zabezpečení

Podobně jako u jiných entit jsou operace centra oznámení povoleny pro tři deklarace zabezpečení: **Naslouchat**, **odesílat**a **Spravovat**.

| Deklarace identity   | Popis                                          | Povolené operace |
| ------- | ---------------------------------------------------- | ------------------ |
| Naslouchání  | Vytváření, aktualizace, čtení a odstraňování jednotlivých registrací | Vytvořit nebo aktualizovat registraci<br><br>Čtení registrace<br><br>Čtení všech registrací pro popisovač<br><br>Odstranit registraci |
| Poslat    | Odesílání zpráv do centra oznámení                | Odeslat zprávu |
| Spravovat  | CRUD na Notification Hubs (včetně aktualizace přihlašovacích údajů PNS a bezpečnostních klíčů) a čtení registrací na základě značek |Vytvořit, aktualizovat, číst a odstranit centra oznámení<br><br>Čtení registrací podle značky |

Notification Hubs přijímá podpisové tokeny vytvořené se sdílenými klíči nakonfigurovanými přímo v centru oznámení.

Není možné odesílat oznámení do více než jednoho oboru názvů. Obory názvů představují logický kontejner pro centra oznámení a nejsou zapojená do odesílání oznámení.
Zásady přístupu na úrovni oboru názvů (přihlašovací údaje) se dají použít pro operace na úrovni oboru názvů, například: výpis Center oznámení, vytváření nebo odstraňování Center oznámení atd. Jenom zásady přístupu na úrovni centra by vám umožnily odeslat oznámení.
