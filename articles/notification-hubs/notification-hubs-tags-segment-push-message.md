---
title: Výrazy směrování a značek
description: Toto téma vysvětluje směrování a označení výrazů pro centra oznámení Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 236e222da9e9a64d4b93002d28c94fa6fe469c08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971973"
---
# <a name="routing-and-tag-expressions"></a>Výrazy směrování a značek

## <a name="overview"></a>Přehled

Výrazy značek umožňují při posílání nabízených oznámení prostřednictvím Notification Hubs cílit na konkrétní sady zařízení nebo na další konkrétní registrace.

## <a name="targeting-specific-registrations"></a>Cílení na konkrétní registrace

Jediným způsobem, jak zaměřit na konkrétní registrace oznámení, je přidružit k nim značky a pak tyto značky cílit. Jak je popsáno v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md), aby bylo možné přijímat nabízená oznámení, musí aplikace registrovat popisovač zařízení v centru oznámení. Po vytvoření registrace v centru oznámení může back-end aplikace do ní odesílat nabízená oznámení. Back-end aplikace může zvolit registrace pro cílení na konkrétní oznámení následujícími způsoby:

1. **Všesměrové vysílání**: všechny registrace v centru oznámení obdrží oznámení.
2. **Tag**: všechny registrace, které obsahují určenou značku, obdrží oznámení.
3. **Výraz značky**: všechny registrace, jejichž sada značek odpovídá zadanému výrazu, obdrží oznámení.

## <a name="tags"></a>Značky

Značkou může být libovolný řetězec, maximálně 120 znaků, obsahující alfanumerické znaky a následující jiné než alfanumerické znaky: _, @, #,,:,. Následující příklad ukazuje aplikaci, ze které můžete přijímat informační zprávy týkající se konkrétních hudebních skupin. V tomto scénáři je jediným způsobem, jak směrovat oznámení, je označovat registrace pomocí značek, které reprezentují různá pásma, jak je znázorněno na následujícím obrázku:

![Přehled značek](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

V tomto obrázku bude označená zpráva **Beatles** dostane jenom tablet, který je zaregistrovaný pomocí značky **Beatles**.

Další informace o vytváření registrací pro značky najdete v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md).

Oznámení můžete odesílat do značek pomocí metod odeslání oznámení třídy `Microsoft.Azure.NotificationHubs.NotificationHubClient` v sadě [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Můžete také použít Node. js nebo rozhraní REST API nabízených oznámení.  Tady je příklad použití sady SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

Značky není nutné předem zřizovat a můžou odkazovat na více konceptů specifických pro aplikaci. Například uživatelé této ukázkové aplikace mohou komentovat pruhy a chtít dostávat informační zprávy, nejen komentáře k oblíbeným pásem, ale také pro všechny komentáře od jejich přátel bez ohledu na pásmo, na kterém jsou komentáře. Příklad tohoto scénáře vidíte na následujícím obrázku:

![Značky – přátelé](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

V tomto obrázku Alice zajímá aktualizace pro Beatles a Bob má zájem o aktualizace Wailers. Bob je také zajímat o komentáře Charlie a Charlie se zajímá o Wailers. Když se pošle oznámení na Charlie komentář k Beatles, Alice i Bob obdrží.

I když můžete zakódovat více otázek ve značkách (například "band_Beatles" nebo "follows_Charlie"), značky jsou jednoduché řetězce a nikoli vlastnosti s hodnotami. Registrace se shoduje jenom na přítomnosti nebo absenci konkrétní značky.

Úplný podrobný návod, jak používat značky pro posílání do zájmových skupin, najdete v tématu popisujícím [novinky](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na registraci.

## <a name="using-tags-to-target-users"></a>Použití značek pro cílení na uživatele

Další možností použití značek je identifikovat všechna zařízení určitého uživatele. Registrace mohou být označeny značkou, která obsahuje ID uživatele, jak je znázorněno na následujícím obrázku:

![Označit uživatele](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

V tomto obrázku se jedná o UID označené zprávy: Alice dosáhne všech registrací označených jako UID: Alice; proto všechna zařízení Alice.

## <a name="tag-expressions"></a>Výrazy značek

V některých případech je nutné, aby oznámení bylo cíleno na sadu registrací identifikovaných pomocí jedné značky, ale pomocí logického výrazu u značek.

Uvažujte o sportovní aplikaci, která pošle připomenutí všem v Boston o hře mezi červeným Sox a mohutnými. Pokud klientská aplikace zaregistruje značky týkající se zájmu v týmech a umístění, je nutné oznámení směrovat všem uživatelům, kteří mají zájem o červenou Sox nebo mohutnosti. Tento stav lze vyjádřit pomocí následujícího logického výrazu:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Výrazy značek](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Výrazy značky mohou obsahovat všechny logické operátory, například a (& &), nebo (| |) a nikoli (!). Mohou obsahovat také závorky. Výrazy značek jsou omezeny na 20 značek, pokud obsahují pouze ORs; v opačném případě jsou omezeny na 6 značek.

Tady je příklad pro posílání oznámení pomocí výrazů značek pomocí sady SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
