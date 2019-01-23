---
title: Ohodnotit omezení pro SMS, e-mailů, webhooky a nabízená oznámení v aplikaci Azure
description: Zjistěte, jak Azure omezuje počet možných SMS, e-mailu, aplikace Azure nabízených oznámení nebo webhooková oznámení ze skupiny akcí.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: cb70dd30d2c3218ac0ad6ef4bd7b71108845f8cb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476440"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Ohodnotit omezení pro hlasové, SMS, e-mailů, nabízená oznámení v aplikaci Azure a webhook příspěvky
Omezení četnosti se pozastavení oznámení, která nastane, pokud se příliš mnoho se odesílají do konkrétního telefonního čísla, e-mailovou adresu nebo zařízení. Omezení rychlosti zajišťuje výstrahy spravovatelné a užitečné.

Limit prahové hodnoty míry jsou:

 - **SMS**: Víc než 1 SMS každých 5 minut.
 - **Hlasové**: Víc než 1 hlasový hovor každých 5 minut.
 - **e-mailu**: Více než 100 e-mailů do jedné hodiny.
 
 Další akce nejsou rychlost je omezená.

## <a name="rate-limit-rules"></a>Frekvence omezení pravidel
- Konkrétní telefonní číslo nebo e-mailu je rychlostně omezený když přijímá více zpráv, než umožňuje prahovou hodnotu.
- Telefonní číslo nebo e-mailu můžou být součástí skupiny akcí napříč mnoha předplatnými. Omezení četnosti se vztahuje na všechna předplatná. Jakmile je dosaženo prahové hodnoty, platí i v případě, že jsou zprávy odesílány z více předplatných.
- Když e-mailová adresa je omezena rychlost, další oznámení se posílá komunikovat, omezení rychlosti. Stavy e-mailu, když vyprší platnost omezení četnosti.

## <a name="next-steps"></a>Další postup ##
* Další informace o [chování výstrah SMS](alerts-sms-behavior.md).
* Získat [přehled upozornění protokolu aktivit](alerts-overview.md)a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

