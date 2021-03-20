---
title: Směrování a označení výrazů v Azure Notification Hubs
description: Naučte se směrovat a označovat výrazy pro Azure Notification Hubs.
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
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998298"
---
# <a name="routing-and-tag-expressions"></a>Výrazy směrování a značek

## <a name="overview"></a>Přehled

Výrazy značek umožňují při posílání nabízených oznámení prostřednictvím Notification Hubs cílit na konkrétní sady zařízení nebo na další konkrétní registrace.

## <a name="targeting-specific-registrations"></a>Cílení na konkrétní registrace

Jediným způsobem, jak zaměřit na konkrétní registrace oznámení, je přidružit k nim značky a pak tyto značky cílit. Jak je popsáno v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md), pokud chcete dostávat nabízená oznámení, musí aplikace registrovat popisovač zařízení v centru oznámení. Jakmile aplikace vytvoří registraci v centru oznámení, back-end aplikace může do ní odesílat nabízená oznámení. Back-end aplikace může zvolit registrace pro cílení na konkrétní oznámení následujícími způsoby:

1. **Všesměrové vysílání**: všechny registrace v centru oznámení obdrží oznámení.
2. **Tag**: všechny registrace, které obsahují určenou značku, obdrží oznámení.
3. **Výraz značky**: všechny registrace, jejichž sada značek odpovídá zadanému výrazu, obdrží oznámení.

## <a name="tags"></a>Značky

Tag může být libovolný řetězec, maximálně 120 znaků, obsahující alfanumerické a následující nealfanumerické znaky: ', ', ' ', ' ', ' ', ' ', ' ' `_` `@` `#` `.` `:` `-` . Následující příklad ukazuje aplikaci, ze které můžete přijímat informační zprávy týkající se konkrétních hudebních skupin. V tomto scénáři je jediným způsobem, jak směrovat oznámení, je označovat registrace pomocí značek, které reprezentují různá pásma, jak je znázorněno na následujícím obrázku:

![Přehled značek](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na obrázku se zpráva označená pomocí **Beatles** dostane jenom tablet, který je zaregistrovaný pomocí značky **Beatles**.

Další informace o vytváření registrací pro značky najdete v tématu [Správa registrace](notification-hubs-push-notification-registration-management.md).

Oznámení můžete odesílat do značek pomocí metod odeslání oznámení `Microsoft.Azure.NotificationHubs.NotificationHubClient` třídy v sadě [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Můžete také použít Node.js nebo rozhraní REST API nabízených oznámení.  Tady je příklad použití sady SDK.

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

Značky nesmí být předem zřízené a můžou odkazovat na několik konceptů specifických pro danou aplikaci. Například uživatelé této ukázkové aplikace mohou komentovat pruhy a chtít dostávat informační zprávy, nejen komentáře k oblíbeným pásem, ale také pro všechny komentáře od jejich přátel bez ohledu na pásmo, na kterém jsou komentáře. Následující obrázek zvýrazňuje příklad tohoto scénáře:

![Značky – přátelé](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

V tomto příkladu Alice zajímá aktualizace pro Beatles a Bob má zájem o aktualizace Wailers. Bob je také zajímat o komentáře Charlie a Charlie se zajímá o Wailers. Když se pošle oznámení na Charlie komentář k Beatles, Notification Hubs ho pošle Alici i Bobovi.

I když můžete zakódovat více otázek ve značkách (například `band_Beatles` nebo `follows_Charlie` ), značky jsou jednoduché řetězce a nikoli vlastnosti s hodnotami. Registrace odpovídá jenom na přítomnosti nebo absenci konkrétní značky.

Úplný podrobný návod, jak používat značky pro posílání do zájmových skupin, najdete v tématu popisujícím [novinky](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na registraci.

## <a name="using-tags-to-target-users"></a>Použití značek pro cílení na uživatele

Další možností použití značek je identifikovat všechna zařízení přidružená ke konkrétnímu uživateli. Můžete označit registraci značkou, která obsahuje ID uživatele, jak je znázorněno na následujícím obrázku:

![Označit uživatele](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na obrázku se zobrazí zpráva s příznakem pro `user_Alice` všechna zařízení s označením `user_Alice` .

## <a name="tag-expressions"></a>Výrazy značek

Existují případy, kdy musí oznámení cílit na sadu registrací identifikovaných pomocí jedné značky, ale pomocí logického výrazu pomocí značek.

Uvažujte o sportovní aplikaci, která pošle připomenutí všem v Boston o hře mezi červeným Sox a mohutnými. Pokud klientská aplikace zaregistruje značky týkající se zájmu v týmech a umístění, je nutné oznámení směrovat všem uživatelům, kteří mají zájem o červenou Sox nebo mohutnosti. Tento stav lze vyjádřit pomocí následujícího logického výrazu:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Výrazy značek](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Výrazy značky podporují běžné logické operátory, jako například `AND` ( `&&` ), `OR` ( `||` ) a `NOT` ( `!` ). mohou také obsahovat závorky. Výrazy značek používající pouze `OR` operátory mohou odkazovat 20 značek; výraz s `AND` operátory, ale žádné `OR` operátory nemohou odkazovat na 10 značek; jinak jsou výrazy značek omezeny na 6 značek.

Tady je příklad pro posílání oznámení pomocí výrazů značek pomocí sady SDK:

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
