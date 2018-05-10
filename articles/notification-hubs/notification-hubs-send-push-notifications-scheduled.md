---
title: Postup odesílání oznámení naplánované | Microsoft Docs
description: Toto téma popisuje naplánované oznámení pomocí Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: nabízená oznámení, nabízených oznámení, plánování nabízená oznámení
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-send-scheduled-notifications"></a>Postupy: Odesílání naplánované oznámení
## <a name="overview"></a>Přehled
Pokud máte scénář, ve kterém chcete poslat oznámení v určitém okamžiku v budoucnu, ale nemají snadný způsob, jak probuzení si kód back-end pro odeslání oznámení. Centra oznámení úrovně standard podporovat funkce, která umožňuje naplánovat oznámení až sedm dní v budoucnu.

Při odesílání oznámení, jednoduše použijte [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) třídy v sadě SDK centra oznámení, jak je znázorněno v následujícím příkladu:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Navíc může zrušit dříve naplánované oznámení pomocí jeho notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Neexistují žádná omezení počtu naplánovaných oznámení, která můžete odeslat.

