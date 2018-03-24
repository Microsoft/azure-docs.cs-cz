---
title: Míra, omezení pro SMS, e-mailů, nabízená oznámení v aplikaci Azure a webhooky | Microsoft Docs
description: Pochopte, jak Azure omezuje počet možných SMS, e-mailu, aplikaci Azure nabízené nebo webhooku oznámení ze skupiny pro akce.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2018
ms.author: dukek
ms.openlocfilehash: 9216a64dbd8201ff09ea5c9283b4db465682a3bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Míra, omezení pro hlasové, SMS, e-mailů, nabízená oznámení v aplikaci Azure a webhooku příspěvcích
Omezení rychlosti je pozastavení oznámení, která nastane po příliš mnoho se odesílají do konkrétní telefonní číslo, e-mailovou adresu nebo zařízení. Omezení rychlosti zajistí výstrahy spravovat a možné použít.

Prahové hodnoty omezení míry jsou:

 - **SMS**: více než 1 SMS každých 5 minut.
 - **Hlasové**: více než 1 hlasový hovor každých 5 minut.
 - **E-mailu**: více než 100 e-mailů za hodinu.
 
 Další akce nejsou míra omezené.

## <a name="rate-limit-rules"></a>Míra limit pravidla
- Konkrétní telefonní číslo nebo e-mailu je míra při přijetí více zpráv, než prahová hodnota umožňuje omezené.
- Telefonní číslo nebo e-mailu může být součástí akce skupin napříč mnoho odběrů. Omezení rychlosti platí ve všech předplatných. Jakmile je dosaženo prahové hodnoty, platí i v případě, že jsou zpráv odesílány z více předplatných.
- Míra omezené po e-mailovou adresu další oznámení se odesílá komunikovat omezení rychlosti. Stavy e-mailu, když vyprší platnost omezení rychlosti.

## <a name="next-steps"></a>Další postup ##
* Další informace o [SMS výstrahy chování](monitoring-sms-alert-behavior.md).
* Získat [přehled výstrah aktivity protokolu](monitoring-overview-alerts.md)a zjistěte, jak dostávat výstrahy.  
* Zjistěte, jak [Konfigurace upozornění pokaždé, když je odeslána oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).
