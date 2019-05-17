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
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 2ca3c69178dde830e226812da34917246781c1ee
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762159"
---
# <a name="security-model-of-azure-notification-hubs"></a>Model zabezpečení služby Azure Notification Hubs

## <a name="overview"></a>Přehled

Toto téma popisuje model zabezpečení služby Azure Notification Hubs. Vzhledem k tomu Notification Hubs jsou entity služby Service Bus, implementace modelu zabezpečení jako služby Service Bus. Další informace najdete v tématu [ověřování pomocí služby Service Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) témata.

## <a name="shared-access-signature-security-sas"></a>Zabezpečení sdíleného přístupového podpisu (SAS)

Notification Hubs implementuje schéma zabezpečení na úrovni entity volat SAS (sdíleným přístupovým podpisům). Toto schéma umožňuje deklarovat až 12 autorizační pravidla v popisu, které udělit práva na dané entitě entit pro zasílání zpráv.

Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sada práv, jak je popsáno v části "Deklarací identity zabezpečení." Při vytváření centra oznámení, automaticky vytvoří dvě pravidla: jeden s vlastností listenurimode nastavenou na právy (klientská aplikace využívá) a druhý se všemi právy k (které používá back-endu aplikace).

Při provádění Správa registrací z klientských aplikací, pokud informace odeslány prostřednictvím oznámení není citlivé (například aktualizace počasí), běžný způsob pro přístup k centru oznámení je poskytnout hodnotu klíče pravidlo přístup jen pro naslouchání na klientskou aplikaci a poskytnout hodnotu klíče pravidlo úplný přístup k back-endu aplikace.

Není vhodné vložit hodnotu klíče v klientských aplikacích pro Windows Store. Pro zabránění vkládání hodnota klíče je, aby klientská aplikace načíst z back-endu aplikace při spuštění.

Je důležité pochopit, že klíč s přístupem k naslouchání umožňuje klientskou aplikaci pro registraci všechny značky. Pokud vaše aplikace musí omezení registrace konkrétní značky pro konkrétní klienty (třeba při značky představují ID uživatele), musíte provést back-endu aplikace registrace. Další informace najdete v tématu Správa registrací. Všimněte si, že tímto způsobem klientská aplikace nebudou mít přímý přístup k Notification Hubs.

## <a name="security-claims"></a>Zabezpečení deklarace identity

Podobně jako u jiných entit, operace centra oznámení jsou povoleny pro tři deklarace identity zabezpečení: Naslouchání, odesílat a spravovat.

| Deklarace identity   | Popis                                          | Přípustné operace |
| ------- | ---------------------------------------------------- | ------------------ |
| Naslouchat  | Vytvoření/aktualizaci, čtení a odstranění jednoho registrace | Vytvoří nebo aktualizuje registrace<br><br>Registrace pro čtení<br><br>Číst všechny registrace pro popisovač<br><br>Odstranit registrace |
| Poslat    | Odesílání zpráv do centra oznámení                | Odeslat zprávu |
| Spravovat  | CRUDs v Notification Hubs (včetně, aktualizují se přihlašovací údaje systému oznámení platformy a zabezpečení klíče) a na základě značek čtení registrací |Vytvoření, aktualizaci, čtení nebo odstranění notification hubs<br><br>Čtení registrací podle značky |

Notification Hubs přijímat deklarace identity poskytuje prostřednictvím Microsoft Azure Access Control tokeny a podpis tokeny vygenerovat pomocí sdíleného klíče nakonfigurovaná přímo v centru oznámení.

Není možné odeslat oznámení na více než jeden obor názvů. Obory názvů je logický kontejner pro notification hubs a nejsou zahrnuty s odesíláním oznámení. Zásady přístupu na úrovni oboru názvů (pověření) lze použít pro operace na úrovni oboru názvů, třeba: výpis notification hubs, vytvoření nebo odstranění služby notification hubs, atd. Pouze zásady přístupu na úrovni centra by umožňují odesílat oznámení.
