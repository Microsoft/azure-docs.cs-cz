---
title: Omezení rychlosti pro SMS, e-maily, nabízená oznámení aplikací Azure a webhooky
description: Zjistěte, jak Azure omezuje počet možných SMS, e-mailů, nabízených oznámení aplikací Azure nebo webhooku ze skupiny akcí.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 066fcac24571c8e982784a3845a010525ff9088a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665523"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Omezení rychlosti pro hlas, SMS, e-maily, nabízená oznámení o aplikacích Azure a příspěvky webhooku
Omezení rychlosti je pozastavení oznámení, ke kterému dochází, když je na konkrétní telefonní číslo, e-mailovou adresu nebo zařízení odesláno příliš mnoho. Omezení rychlosti zajišťuje, že výstrahy jsou spravovatelné a žalovatelné.

Mezní hodnoty rychlosti jsou:

- **SMS**: Ne více než 1 SMS každých 5 minut.
- **Hlas:** Ne více než 1 hlasový hovor každých 5 minut.
- **E-mail:** Ne více než 100 e-mailů za hodinu.
 
  Ostatní akce nejsou omezeny.

## <a name="rate-limit-rules"></a>Pravidla limitu rychlosti
- Konkrétní telefonní číslo nebo e-mail je omezen, pokud obdrží více zpráv, než umožňuje prahová hodnota.
- Telefonní číslo nebo e-mail může být součástí skupin akcí v mnoha předplatných. Omezení rychlosti platí pro všechna předplatná. Platí, jakmile je dosaženo prahové hodnoty, i v případě, že zprávy jsou odesílány z více předplatných.
- Pokud je e-mailová adresa omezena, je odesláno další oznámení, které sdělí omezení rychlosti. E-mail uvádí, kdy vyprší platnost omezení rychlosti.

## <a name="next-steps"></a>Další kroky ##
* Další informace o [chování upozornění serveru SMS](alerts-sms-behavior.md).
* Získejte [přehled upozornění protokolu aktivit](alerts-overview.md)a zjistěte, jak přijímat výstrahy.  
* Přečtěte si, jak [nakonfigurovat výstrahy při každém zaúčtování oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

