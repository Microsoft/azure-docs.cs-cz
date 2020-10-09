---
title: Model zabezpečení Notification Hubs
description: Přečtěte si o modelu zabezpečení pro Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76263757"
---
# <a name="notification-hubs-security"></a>Notification Hubs zabezpečení

## <a name="overview"></a>Přehled

Toto téma popisuje model zabezpečení Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>Zabezpečení sdíleného přístupového podpisu

Notification Hubs implementuje schéma zabezpečení na úrovni entit označované jako *sdílený přístupový podpis* (SAS). Každé pravidlo obsahuje název, hodnotu klíče (sdílený tajný klíč) a sadu práv, jak je vysvětleno dále v tématu [deklarace zabezpečení](#security-claims). 

Při vytváření rozbočovače se automaticky vytvoří dvě pravidla: jednu s právy na **naslouchání** (kterou používá klientská aplikace) a druhá se **všemi** právy (které používá back-end aplikace):

- **DefaultListenSharedAccessSignature**: uděluje pouze oprávnění k **naslouchání** .
- **DefaultFullSharedAccessSignature**: uděluje **příjem**, **správu**a **odesílání** oprávnění. Tato zásada se použije jenom v back-endu vaší aplikace. Nepoužívejte ho v klientských aplikacích. Použijte zásady jenom s přístupem **naslouchat** . Pokud chcete vytvořit nové zásady vlastního přístupu pomocí nového tokenu SAS, přečtěte si téma [tokeny SAS pro zásady přístupu](#sas-tokens-for-access-policies) dále v tomto článku.

Pokud při provádění správy registrace z klientských aplikací nejsou informace odesílané prostřednictvím oznámení citlivé (například aktualizace počasí), je pro přístup k centru oznámení běžný způsob, jak získat přístup ke službě centra oznámení, zadat klíčovou hodnotu pravidla pro příjem přístupu k klientské aplikaci a poskytnout klíčovou hodnotu pravidla úplný přístup k back-endu aplikace.

Aplikace by neměly vkládat hodnotu klíče v klientských aplikacích pro Windows Store; místo toho si klientské aplikace načtou z back-endu aplikace při spuštění.

Klíč s přístupem k **naslouchání** umožňuje klientské aplikaci zaregistrovat se pro libovolnou značku. Pokud vaše aplikace musí omezit registrace na konkrétní značky pro konkrétní klienty (například když značky reprezentují ID uživatelů), musí back-end aplikace provést registrace. Další informace najdete v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md). Tímto způsobem klientská aplikace nebude mít přímý přístup k Notification Hubs.

## <a name="security-claims"></a>Deklarace identity zabezpečení

Podobně jako u jiných entit jsou operace centra oznámení povoleny pro tři deklarace zabezpečení: **naslouchat**, **odesílat**a **Spravovat**.

| Deklarovat   | Popis                                          | Povolené operace |
| ------- | ---------------------------------------------------- | ------------------ |
| Naslouchat  | Vytváření, aktualizace, čtení a odstraňování jednotlivých registrací | Vytvořit nebo aktualizovat registraci<br><br>Čtení registrace<br><br>Čtení všech registrací pro popisovač<br><br>Odstranit registraci |
| Odeslat    | Odesílání zpráv do centra oznámení                | Odeslat zprávu |
| Spravovat  | CRUD na Notification Hubs (včetně aktualizace přihlašovacích údajů PNS a bezpečnostních klíčů) a čtení registrací na základě značek |Vytváření, aktualizace, čtení a odstraňování Center<br><br>Čtení registrací podle značky |

Notification Hubs akceptuje tokeny SAS vygenerované se sdílenými klíči nakonfigurovanými přímo na rozbočovači.

Není možné odesílat oznámení do více než jednoho oboru názvů. Obory názvů jsou logické kontejnery pro Notification Hubs a nejsou součástí odesílání oznámení.

Použijte zásady přístupu na úrovni oboru názvů (přihlašovací údaje) pro operace na úrovni oboru názvů; Příklad: výpis Center, vytváření a odstraňování Center atd. Oznámení můžou odesílat jenom zásady přístupu na úrovni centra.

### <a name="sas-tokens-for-access-policies"></a>Tokeny SAS pro zásady přístupu

Pokud chcete vytvořit novou deklaraci zabezpečení nebo zobrazit existující klíče SAS, udělejte toto:

1. Přihlaste se k portálu Azure.
2. Vyberte **Všechny prostředky**.
3. Vyberte název centra oznámení, pro který chcete vytvořit deklaraci identity, nebo se podívejte na klíč SAS.
4. V nabídce na levé straně vyberte **zásady přístupu**.
5. Vyberte **nové zásady** a vytvořte novou deklaraci zabezpečení. Zadejte název zásady a vyberte oprávnění, která chcete udělit. Pak vyberte **OK**.
6. V okně zásady přístupu se zobrazí úplný připojovací řetězec (včetně nového klíče SAS). Tento řetězec můžete zkopírovat do schránky pro pozdější použití.

Pokud chcete získat klíč SAS z konkrétní zásady, vyberte tlačítko **Kopírovat** vedle zásady obsahující klíč SAS, který chcete. Vložte tuto hodnotu do dočasného umístění a pak zkopírujte část klíče SAS připojovacího řetězce. V tomto příkladu se používá obor názvů Notification Hubs s názvem **mytestnamespace1**a zásada s názvem **policy2**. Klíč SAS je hodnota poblíž konce řetězce, kterou Určuje **SharedAccessKey**:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Získat klíče SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Další kroky

- [Přehled Notification Hubs](notification-hubs-push-notification-overview.md)
