---
title: Směrování a výrazy označení
description: Toto téma vysvětluje, směrování a značku výrazy pro službu Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: jowargo
ms.openlocfilehash: 31a22aabc7b0f1d51a673ef8642037103badcc02
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828158"
---
# <a name="routing-and-tag-expressions"></a>Směrování a značky výrazů

## <a name="overview"></a>Přehled

Výrazy označení vám umožní cílit na konkrétní sady zařízení nebo přesněji řečeno registrace při odesílání nabízených oznámení pomocí Notification Hubs.

## <a name="targeting-specific-registrations"></a>Cílení na konkrétní registrace

Jediný způsob, jak cíl konkrétní upozornění registrace je umožní přiřadit značky s nimi, pak cílit na těchto značek. Jak je popsáno v [Správa registrací](notification-hubs-push-notification-registration-management.md), aby bylo možné přijímat nabízená oznámení aplikace má k registraci zařízení zpracování na centrum oznámení. Po vytvoření registrace v centru oznámení back-end aplikace může odesílat nabízená oznámení k němu. Back-endu aplikací můžete zvolit registrace k cíli s konkrétní oznámení následujícími způsoby:

1. **Vysílání**: všechny registrace v centru oznámení dostávat oznámení.
2. **Značka**: všechny registrace, které obsahují stanovené značky přijímat oznámení.
3. **Označení výrazu**: jehož sady značek odpovídají zadanému výrazu registrace všech oznámení.

## <a name="tags"></a>Značky

Značka může být libovolný řetězec, a to až 120 znaků, který obsahuje alfanumerické a následující jiné než alfanumerické znaky: "_", "@", "#", ".",":", "-". Následující příklad ukazuje aplikace, ze kterého můžete přijímat oznámení informačního nápisu o Hudba konkrétní skupiny. V tomto scénáři je jednoduchý způsob, jak směrovat oznámení o popisek registrace se značkami, které představují různé pruhy, stejně jako v následujícím obrázku:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Na tomto obrázku zprávy označené **Beatles** dosáhne pouze tablety, které registrované se značkou **Beatles**.

Další informace o vytváření registrace pro značky, naleznete v tématu [Správa registrací](notification-hubs-push-notification-registration-management.md).

Oznámení můžete odesílat na značky pomocí metody odeslat oznámení `Microsoft.Azure.NotificationHubs.NotificationHubClient` třídy v [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Můžete také použít Node.js, nebo REST API služby nabízených oznámení.  Tady je příklad použití sady SDK.

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

Značky nemusíte být předem zřízená a mohou odkazovat na více koncepty specifické pro aplikace. Například uživatelé této ukázkové aplikaci můžete komentovat pruhy a nechcete dostávat informační zprávy, nejen pro komentáře na své oblíbené pruhy, ale také pro všechny komentáře od svých přátel, bez ohledu na náramku, na kterém jsou komentáře. Následující obrázek ukazuje příklad tohoto scénáře:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Na tomto obrázku je žádající o aktualizaci pro Beatles Alice a Bob se žádající o aktualizaci pro Wailers. Bob má také zájem o Karel pro komentáře a Karel v zajímá vás Wailers. Pokud jsou oznámení odesílána Karel pro komentáře na Beatles, Alice a Bob ji přijmout.

Přestože můžete kódovat více připomínky můžete vystavit ve značky (například "band_Beatles" nebo "follows_Charlie"), značky jsou jednoduché řetězce a nikoli vlastnosti s hodnotami. Registrace je nalezena shoda, pouze na přítomnosti nebo nepřítomnosti s konkrétní značkou.

Celý podrobný kurz o tom, jak používat značky pro odesílání zájmových skupin, najdete v části [novinkách](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na registraci.

## <a name="using-tags-to-target-users"></a>Pomocí značek pro cílové uživatele

Dalším způsobem, jak používat značky je identifikovat všechna zařízení z určitého uživatele. Registrace může být označené značkou, který obsahuje ID uživatele, jako na následujícím obrázku:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Na tomto obrázku s příznakem uid zpráva: Alice dosáhne všechny registrace označený "uid:Alice"; proto všechny uživatele Jaroslava zařízení.

## <a name="tag-expressions"></a>Výrazy označení

Existují případy, ve kterých má oznámení cílit na sadu registrace, který je identifikován není podle jedné značky, ale podle logického výrazu na značky.

Vezměte v úvahu sportovní aplikaci, která pošle připomenutí pro všechny uživatele v Bostonu o hře mezi Red Sox a Cardinals. Pokud klientská aplikace registruje značky o zájem týmy a umístění, by měl všem uživatelům v Bostonu, kteří chtějí Red Sox nebo Cardinals cílená oznámení. Tuto podmínku můžete vyjádřit pomocí následující logický výraz:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Výrazy označení mohou obsahovat všechny logické operátory, jako například AND (& &), nebo (|) a ne (!). Mohou také obsahovat závorky. Výrazy označení jsou omezené na 20 značky v případě, že obsahují pouze or; v opačném případě jsou omezené na 6 značky.

Tady je příklad odesílání oznámení s výrazy označení pomocí sady SDK.

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
