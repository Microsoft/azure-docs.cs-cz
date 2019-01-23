---
title: Odesílání plánovaných oznámení | Dokumentace Microsoftu
description: Toto téma popisuje naplánované oznámení pomocí Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: nabízená oznámení, nabízená oznámení, nabízená oznámení plánování
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471833"
---
# <a name="how-to-send-scheduled-notifications"></a>Jak: Odesílání plánovaných oznámení

Pokud máte scénáře, ve kterém chcete odeslat oznámení v určitém okamžiku v budoucnu, ale nemají snadný způsob, jak funkce do vaší back-end kódu pro odesílání oznámení. Notification hubs úrovně standard podporují funkce, která je možné naplánovat oznámení až na 7 dní v budoucnu.


## <a name="schedule-your-notifications"></a>Naplánovat oznámení
Při odesílání oznámení, jednoduše použít [ `ScheduledNotification` třídy](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) v SDK centra oznámení, jak je znázorněno v následujícím příkladu:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Zrušit plánovaných oznámení
Můžete také zrušit dříve plánovaná oznámení pomocí jeho notificationId:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Neplatí žádné limity počtu plánovaná oznámení můžete odesílat.

## <a name="next-steps"></a>Další postup

Projděte si tyto kurzy:

 - [Nabízená oznámení pro všechna registrovaná zařízení](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Odesílání lokalizovaných oznámení](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Zasílání nabízených oznámení určitým uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
