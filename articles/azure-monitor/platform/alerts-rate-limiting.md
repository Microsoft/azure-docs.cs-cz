---
title: Omezení rychlosti pro SMS, e-maily, nabízená oznámení
description: Seznamte se s tím, jak Azure omezuje počet možných oznámení SMS, e-mailu, Azure App push nebo Webhooku ze skupiny akcí.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114330"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Omezení rychlosti pro hlas, SMS, e-maily, nabízená oznámení aplikací Azure a příspěvky Webhooku
Omezení rychlosti je pozastavení oznámení, ke kterým dochází, když je příliš mnoho zasílání do konkrétního telefonního čísla, e-mailové adresy nebo zařízení. Omezení rychlosti zajišťuje, aby byly výstrahy spravovatelné a napadnutelné.

Prahové hodnoty mezních hodnot:

- **SMS**: nejedná se o více než 1 SMS každých 5 minut.
- **Hlas**: žádné více než jedno volání hlasu každých 5 minut.
- **E-mail**: za hodinu nepřekračuje 100 e-mailů.
 
  Jiné akce nejsou omezeny.

## <a name="rate-limit-rules"></a>Pravidla omezení přenosové rychlosti
- Konkrétní telefonní číslo nebo e-mailové adresy jsou omezené při přijímání více zpráv, než umožňuje prahová hodnota.
- Telefonní číslo nebo e-mail může být součástí skupin akcí v mnoha předplatných. Omezení rychlosti se vztahuje na všechna předplatná. Použije se hned po dosažení prahové hodnoty, a to i v případě, že se zprávy odesílají z více předplatných.
- Když je e-mailová adresa omezená, pošle se další oznámení pro sdělování omezení četnosti. E-mailové stavy, když vyprší platnost omezení rychlosti.

## <a name="next-steps"></a>Další kroky ##
* Přečtěte si další informace o [chování výstrah SMS](alerts-sms-behavior.md).
* Získejte [Přehled výstrah protokolu aktivit](alerts-overview.md)a Naučte se přijímat výstrahy.  
* Naučte se [konfigurovat výstrahy pokaždé, když se publikuje oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

