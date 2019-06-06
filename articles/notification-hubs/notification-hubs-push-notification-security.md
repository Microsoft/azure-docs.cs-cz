---
title: Oznámení Centra zabezpečení
description: Toto téma vysvětluje zabezpečení Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431249"
---
# <a name="notification-hubs-security"></a>Oznámení Centra zabezpečení

## <a name="overview"></a>Přehled

Toto téma popisuje model zabezpečení služby Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Zabezpečení sdíleného přístupového podpisu (SAS)

Notification Hubs implementuje schéma zabezpečení na úrovni entity volat SAS (sdíleným přístupovým podpisům). Toto schéma umožňuje deklarovat až 12 autorizační pravidla v popisu, které udělit práva na dané entitě entit pro zasílání zpráv.

Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sada práv, jak je vysvětleno v [deklarací identity zabezpečení](#security-claims). Při vytváření centra oznámení, automaticky vytvoří dvě pravidla: jednu s **naslouchání** práv (klientská aplikace využívá) a jeden u **všechny** práv (back-endu aplikace používá).

Při provádění Správa registrací z klientských aplikací, pokud informace odeslány prostřednictvím oznámení není citlivé (například aktualizace počasí), běžný způsob pro přístup k centru oznámení je poskytnout hodnotu klíče pravidlo přístup jen pro naslouchání na klientskou aplikaci a poskytnout hodnotu klíče pravidlo úplný přístup k back-endu aplikace.

Aplikace by neměl vložit hodnotu klíče v klientských aplikacích pro Windows Store, místo toho mají načíst z back-endu aplikace při spuštění klientské aplikace.

Klíč s **naslouchání** přístup umožňuje klientskou aplikaci pro registraci všechny značky. Pokud vaše aplikace musí omezení registrace konkrétní značky pro konkrétní klienty (třeba při značky představují ID uživatele), musíte provést back-endu aplikace registrace. Další informace najdete v tématu [Správa registrací](notification-hubs-push-notification-registration-management.md). Všimněte si, že tímto způsobem klientská aplikace nebudou mít přímý přístup k Notification Hubs.

## <a name="security-claims"></a>Zabezpečení deklarace identity

Podobně jako u jiných entit, operace centra oznámení jsou povoleny pro tři deklarace identity zabezpečení: **Naslouchání**, **odeslat**, a **spravovat**.

| Deklarovat   | Popis                                          | Přípustné operace |
| ------- | ---------------------------------------------------- | ------------------ |
| Naslouchat  | Vytvoření/aktualizaci, čtení a odstranění jednoho registrace | Vytvoří nebo aktualizuje registrace<br><br>Registrace pro čtení<br><br>Číst všechny registrace pro popisovač<br><br>Odstranit registrace |
| Odeslat    | Odesílání zpráv do centra oznámení                | Odeslání zprávy |
| Spravovat  | CRUDs v Notification Hubs (včetně, aktualizují se přihlašovací údaje systému oznámení platformy a zabezpečení klíče) a na základě značek čtení registrací |Vytvoření, aktualizaci, čtení nebo odstranění notification hubs<br><br>Čtení registrací podle značky |

Notification Hubs přijímá podpis, který generuje tokeny s použitím sdílené klíče nakonfigurovaná přímo v centru oznámení.

Není možné odeslat oznámení na více než jeden obor názvů. Obory názvů je logický kontejner pro notification hubs a nejsou zahrnuty s odesíláním oznámení.
Zásady přístupu na úrovni oboru názvů (pověření) lze použít pro operace na úrovni oboru názvů, třeba: výpis notification hubs, vytvoření nebo odstranění služby notification hubs, atd. Pouze zásady přístupu na úrovni centra by umožňují odesílat oznámení.
