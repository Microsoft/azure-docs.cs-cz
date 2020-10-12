---
title: Postup odeslání naplánovaných oznámení | Microsoft Docs
description: Toto téma popisuje použití naplánovaných oznámení s Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: nabízená oznámení, nabízené oznámení, plánování nabízených oznámení
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998267"
---
# <a name="how-to-send-scheduled-notifications"></a>Postupy: odesílání plánovaných oznámení

Máte-li scénář, ve kterém chcete později odeslat oznámení, ale nemáte snadný způsob, jak probudit back-end kód k odeslání oznámení. Centra oznámení úrovně Standard podporují funkci, která umožňuje naplánovat oznámení až na sedm dní v budoucnu.


## <a name="schedule-your-notifications"></a>Naplánování oznámení
Při odesílání oznámení jednoduše použijte [ `ScheduledNotification` třídu](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) v sadě Notification Hubs SDK, jak je znázorněno v následujícím příkladu:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Zrušit plánovaná oznámení
Můžete také zrušit dříve naplánovaná oznámení pomocí jeho notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Počet naplánovaných oznámení, která můžete odesílat, není nijak omezený.

## <a name="next-steps"></a>Další kroky

Projděte si tyto kurzy:

 - [Nabízená oznámení pro všechna registrovaná zařízení](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Nabízená oznámení odesílaná konkrétním zařízením](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Odesílání lokalizovaných oznámení](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
