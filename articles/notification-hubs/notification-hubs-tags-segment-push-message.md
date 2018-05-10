---
title: Směrování a značky výrazy
description: Toto téma vysvětluje směrování a značky výrazy pro Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: e08fca0b6b57d654f2b2ff7b935f38d8c517487b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="routing-and-tag-expressions"></a>Výrazy směrování a značka
## <a name="overview"></a>Přehled
Výrazy značky umožňují vytvořit cíl konkrétní sady zařízení, konkrétně registrací, nebo při odesílání nabízených oznámení pomocí centra oznámení.

## <a name="targeting-specific-registrations"></a>Cílení na konkrétní registrace
Jediný způsob, jak cíl konkrétní oznámení registrace je umožní přiřadit značky s nimi, pak cíle těchto značek. Jak je popsáno v [registrace správu](notification-hubs-push-notification-registration-management.md), aby bylo možné přijímat nabízená oznámení, aplikace musí registrovat zařízení zpracování v rozbočovači oznámení. Jakmile registrace je vytvořena v centru oznámení, back-end aplikace může odesílat nabízená oznámení k němu.
Back-end aplikace můžete zvolit registrace k cíli s konkrétní oznámení následujícími způsoby:

1. **Vysílání**: všechny registrace v centru oznámení dostávat oznámení.
2. **Značka**: všechny registrace, které obsahují zadaná značka přijímat oznámení.
3. **Značka výraz**: všechny registrace, jejichž sadu značky odpovídají zadaným výrazem přijímat oznámení.

## <a name="tags"></a>Značky
Značku může být libovolný řetězec, až 120 znaků, který obsahuje alfanumerické znaky a následující jiných než alfanumerických znaků: '_', ' @', '#', '. ',':', '-'. Následující příklad ukazuje aplikace, ze kterého můžete přijímat oznámení informačního nápisu o konkrétní Hudba skupinách. V tomto scénáři je jednoduchý způsob, jak oznámení trasy popisek registrace pomocí značek, které představují různé pruhy, stejně jako na následujícím obrázku:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Na tomto obrázku, označí zprávu **Beatles** dosáhne pouze tablet, který zaregistrovali značky **Beatles**.

Další informace o vytváření registrace pro značky najdete v tématu [registrace správu](notification-hubs-push-notification-registration-management.md).

Oznámení můžete odesílat na základě značek použití metod odeslání oznámení `Microsoft.Azure.NotificationHubs.NotificationHubClient` třídy v [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Můžete také použít Node.js nebo REST API služby nabízených oznámení.  Tady je příklad pomocí sady SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Značky nemusí být předem zřízená a mohou odkazovat na několika koncepty konkrétní aplikaci. Uživatelé této ukázkové aplikaci můžete například komentář k pásma a chcete přijímat informační zprávy, nejen pro komentáře na svých oblíbených pásma, ale také pro všechny komentáře od svých přátel, bez ohledu na vzdálené správy, na kterém jsou komentářů. Následující obrázek znázorňuje příklad tohoto scénáře:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Na tomto obrázku Alice má zájem o aktualizace Beatles a má zájem o aktualizace Wailers Bob. Bob má také zájem o Karel na komentáře a Karel v zajímá Wailers. Při odesílání oznámení pro Karel na komentář na Beatles Alice a Bob ji přijmout.

Když může být kódován více obavy ve značkách (například "band_Beatles" nebo "follows_Charlie"), značky jsou jednoduché řetězce a vlastnosti hodnotami. Registrace je nalezena shoda pouze na přítomnosti nebo absenci s konkrétní značkou tag.

Úplné podrobný kurz o tom, jak používat značky pro odeslání do zájmových skupin, najdete v části [novinkách](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Pomocí značek pro cílové uživatele
Jiný způsob, jak používat značky je identifikace všechna zařízení z určitého uživatele. Registrace může být označené značku, která obsahuje ID uživatele, stejně jako na následujícím obrázku:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Na tomto obrázku, označí zprávu uid: Alice dosáhne všechny registrace s příznakem uid:Alice; proto všechna zařízení od Alice.

## <a name="tag-expressions"></a>Značka výrazy
Existují případy, ve kterých má oznámení cílit sadu registrací identifikovanou není jedinou značku, ale logický výraz na značky.

Vezměte v úvahu aplikace sportu, která odesílá připomenutí pro všechny uživatele v Boston o hru mezi Red Sox a Cardinals. Pokud klientské aplikace zaregistruje značky o zájem o týmy a umístění, by měly pro všechny uživatele v Boston, kteří chtějí Red Sox nebo Cardinals cílené oznámení. Tento stav může být vyjádřený s následující logický výraz:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Výrazy značka může obsahovat všech logických operátorů, například AND (& &), nebo (|) a ne (!). Mohou také obsahovat závorek. Značky výrazy jsou omezeny na 20 značky v případě, že obsahují jenom ORs; jinak jsou omezeny na 6 značky.

Tady je příklad pro odesílání oznámení pomocí výrazů značky pomocí sady SDK.

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
