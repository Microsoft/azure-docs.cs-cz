---
title: Zabezpečení pro centra oznámení
description: Toto téma vysvětluje zabezpečení služby Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 9f197a85dfad31ce32d0f9c93127b69d8e33c9ee
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778269"
---
# <a name="security"></a>Zabezpečení
## <a name="overview"></a>Přehled
Toto téma popisuje model zabezpečení Azure Notification hubs. Protože Notification Hubs entity služby sběrnice, implementace modelu zabezpečení jako Service Bus. Další informace najdete v tématu [ověřování sběrnice služby](https://msdn.microsoft.com/library/azure/dn155925.aspx) témata.

## <a name="shared-access-signature-security-sas"></a>Zabezpečení sdílený přístupový podpis (SAS)
Notification Hubs implementuje zabezpečení na úrovni entit schéma volá SAS (sdíleného přístupového podpisu). Toto schéma umožňuje deklarovat až 12 autorizační pravidla v popisu, která udělují oprávnění na dané entity entit pro zasílání zpráv.

Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sada práv, jak je popsáno v části "Deklarací identity zabezpečení." Při vytváření centra oznámení, se automaticky vytvoří dvě pravidla: jednu s naslouchání právy (pomocí klientské aplikace) a druhou se všemi právy k (které používá back-end aplikace).

Při provádění správy registrace z klientské aplikace, pokud odesílané informace prostřednictvím oznámení není citlivé (například počasí aktualizace), běžný způsob přístup Centrum oznámení je umožnit hodnota klíče pravidla přístup jen pro naslouchání na klientskou aplikaci a umožňuje klíčovou hodnotu pravidlo úplný přístup k back-end aplikace.

Není doporučeno vložit hodnotu klíče klienta aplikace pro Windows Store. Způsob, jak se vyhnout vložení hodnota klíče se se klientskou aplikaci načíst z back-end aplikace při spuštění.

Je důležité si uvědomit, že klíč s přístupem k naslouchání umožňuje klientskou aplikaci zaregistrovat pro všechny značky. Pokud vaše aplikace musí registrace omezit na konkrétní značky pro konkrétní klienty (například když značky představují ID uživatele), musíte provést back-end aplikace registrace. Další informace najdete v tématu registrace správy. Všimněte si, že tímto způsobem, klientská aplikace nebudou mít přímý přístup k centru oznámení.

## <a name="security-claims"></a>Deklarací identity zabezpečení
Podobně jako ostatní entity, jsou povolené operace centra oznámení pro tři deklarací identity zabezpečení: naslouchání, odeslání a spravovat.

| Deklarovat | Popis | Povolené operace |
| --- | --- | --- |
| Naslouchání |Vytvořit nebo aktualizovat, čtení a odstranění jednoho registrace |Vytvořit nebo aktualizovat registraci<br><br>Registrace pro čtení<br><br>Číst všechny registrace pro popisovač<br><br>Odstranit registrace |
| Odeslat |Odesílání zpráv do centra oznámení |Odeslat zprávu |
| Spravovat |CRUDs v Notification Hubs (včetně aktualizace systému PNS přihlašovacích údajů a zabezpečení klíče) a čtení registrace podle značky |Vytvoření, aktualizace nebo pro čtení nebo odstranění notification hubs<br><br>Číst registrace podle značky |

Centra oznámení přijímat deklarace identity udělena tokeny řízení přístupu Microsoft Azure a tokeny podpis vygeneroval s sdílených klíčů nakonfigurované přímo v centru oznámení.

