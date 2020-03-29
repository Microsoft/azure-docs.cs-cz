---
title: Směrování a tag výrazy v Centru oznámení Azure
description: Přečtěte si, jak směrovat a označovat výrazy pro Centra oznámení Azure.
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
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062314"
---
# <a name="routing-and-tag-expressions"></a>Směrování a tag výrazy

## <a name="overview"></a>Přehled

Výrazy značek umožňují cílit na konkrétní sady zařízení nebo konkrétněji na registrace při odesílání nabízených oznámení prostřednictvím center oznámení.

## <a name="targeting-specific-registrations"></a>Cílení na konkrétní registrace

Jediným způsobem, jak cílit na konkrétní registrace oznámení, je přidružit k nim značky a pak tyto značky. Jak je popsáno v [správa registrace](notification-hubs-push-notification-registration-management.md), pro příjem nabízených oznámení, aplikace musí zaregistrovat popisovač zařízení v centru oznámení. Jakmile aplikace vytvoří registraci v centru oznámení, back-end aplikace může odesílat nabízená oznámení. Back-end aplikace můžete zvolit registrace cílit s konkrétní oznámení následujícími způsoby:

1. **Vysílání**: oznámení obdrží všechna registrace v centru oznámení.
2. **Značka**: všechny registrace, které obsahují zadanou značku, obdrží oznámení.
3. **Výraz značky**: oznámení obdrží všechny registrace, jejichž sada značek odpovídá zadanému výrazu.

## <a name="tags"></a>Značky

Značkou může být libovolný řetězec, až 120 znaků, obsahující alfanumerické a`_`následující nealfanumerické`#`znaky:`.`"`:`, "`-`, "`@`, ' , ' , ' , ' , ' ' ' " . Následující příklad ukazuje aplikaci, ze které můžete přijímat informační zprávy o konkrétních hudebních skupinách. V tomto scénáři jednoduchý způsob, jak směrovat oznámení je označit registrace s tagy, které představují různá pásma, jako na následujícím obrázku:

![Přehled značek](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na obrázku, zpráva označená **Beatles** dosáhne pouze tablet, který se zaregistroval s tag **Beatles**.

Další informace o vytváření registrací značek naleznete v [tématu Správa registrace](notification-hubs-push-notification-registration-management.md).

Můžete odesílat oznámení na značky pomocí metody `Microsoft.Azure.NotificationHubs.NotificationHubClient` odesílání oznámení třídy v [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Můžete také použít Node.js nebo nabízená oznámení REST API.  Tady je příklad pomocí sady SDK.

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

Značky nesmí být předem zřízené a mohou odkazovat na více konceptů specifických pro aplikaci. Například uživatelé této ukázkové aplikace mohou komentovat kapely a chtějí dostávat toasty, a to nejen pro komentáře k jejich oblíbeným kapelám, ale také pro všechny komentáře od svých přátel, bez ohledu na kapelu, na které komentují. Následující obrázek upozorňuje na příklad tohoto scénáře:

![Značky přátel](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

V tomto příkladu, Alice má zájem o aktualizace pro Beatles, a Bob má zájem o aktualizace pro Wailers. Bob se také zajímá o Komentáře Charlie, a Charlie se zajímá o Wailers. Když je odesláno oznámení o Charlieho komentář k Beatles, Notification Hubs odešle to alice a Bob.

Zatímco můžete kódovat více problémů ve `band_Beatles` značkách (například nebo), `follows_Charlie`značky jsou jednoduché řetězce a ne vlastnosti s hodnotami. Registrace odpovídá pouze na přítomnost nebo nepřítomnost konkrétní značky.

Úplný podrobný návod, jak používat značky pro odesílání zájmovým skupinám, naleznete v [tématu Nejnovější zprávy](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na registraci.

## <a name="using-tags-to-target-users"></a>Použití značek k cílení na uživatele

Dalším způsobem, jak používat značky, je identifikovat všechna zařízení přidružená k určitému uživateli. Registraci můžete označit značkou, která obsahuje ID uživatele, jako na následujícím obrázku:

![Označit uživatele](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na obrázku se označená `user_Alice` zpráva dostane do `user_Alice`všech zařízení označených písmenem .

## <a name="tag-expressions"></a>Tag výrazy

Existují případy, kdy oznámení musí cílit na sadu registrací identifikovaných nikoli jedinou značkou, ale logickým výrazem používajícím značky.

Vezměme si sportovní aplikaci, která pošle připomínku všem v Bostonu o hře mezi Red Sox a Kardinálové. Pokud klientská aplikace registruje značky o zájmu o týmy a umístění, pak oznámení by měla být zaměřena na všechny v Bostonu, kteří mají zájem buď Red Sox nebo Kardinálové. Tuto podmínku lze vyjádřit následujícím logickým výrazem:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Tag výrazy](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

Výrazy značek podporují běžné logické `AND` `&&`operátory, například ( `OR` ), (`||`). `NOT` `!` mohou také obsahovat závorky. Tag výrazy `OR` pomocí pouze operátory mohou odkazovat 20 tagů; výraz `AND` s operátory, ale žádné `OR` operátory nemohou odkazovat na 10 značek; jinak jsou výrazy značek omezeny na 6 značek.

Tady je příklad pro odesílání oznámení s výrazy značek pomocí sady SDK:

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
