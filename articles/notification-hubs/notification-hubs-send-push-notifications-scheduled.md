---
title: Jak odeslat naplánovaná oznámení | Dokumenty společnosti Microsoft
description: Toto téma popisuje použití naplánované oznámení s Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: nabízená oznámení,nabízená oznámení,plánování nabízených oznámení
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212998"
---
# <a name="how-to-send-scheduled-notifications"></a>Postup: Odeslání naplánovaných oznámení

Pokud máte scénář, ve kterém chcete odeslat oznámení v určitém okamžiku v budoucnu, ale nemají snadný způsob, jak probudit back-end kód odeslat oznámení. Centra oznámení standardní úrovně podporují funkci, která umožňuje plánovat oznámení až sedm dní v budoucnu.


## <a name="schedule-your-notifications"></a>Plánování oznámení
Při odesílání oznámení jednoduše použijte [ `ScheduledNotification` třídu](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) v sadě SDK centra oznámení, jak je znázorněno v následujícím příkladu:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Zrušení naplánovaných oznámení
Také můžete zrušit dříve naplánované oznámení pomocí jeho notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Počet naplánovaných oznámení, která můžete odeslat, není nijak limitován.

## <a name="next-steps"></a>Další kroky

Projděte si tyto kurzy:

 - [Nabízená oznámení na všechna registrovaná zařízení](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Odesílání lokalizovaných oznámení](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
