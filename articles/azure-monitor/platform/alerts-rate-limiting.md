---
title: Omezení rychlosti pro SMS, e-maily, nabízená oznámení
description: Zjistěte, jak Azure omezuje počet možných SMS, e-mailů, nabízených oznámení aplikací Azure nebo webhooku ze skupiny akcí.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114330"
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

