---
title: Odeslání oznámení APNS VOIP pomocí Azure Notification Hubs
description: Naučte se odesílat oznámení APNS VOIP prostřednictvím Azure Notification Hubs (není oficiálně podporovaná).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80146885"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Použití služby APN VOIP prostřednictvím Notification Hubs (není oficiálně podporováno)

V Azure Notification Hubs je možné používat oznámení APNS VOIP. pro tento scénář však není oficiální podpora k dispozici.

## <a name="considerations"></a>Požadavky

Pokud se přesto rozhodnete odesílat oznámení služby APN VOIP prostřednictvím Notification Hubs, mějte na paměti následující omezení:

- Odeslání oznámení VOIP vyžaduje, aby se `apns-topic` záhlaví nastavilo na ID aplikačního kompletu + `.voip` přípona. Například pro ukázkovou aplikaci s ID sady `com.microsoft.nhubsample` `apns-topic` by měla být záhlaví nastavena na `com.microsoft.nhubsample.voip.`

   Tato metoda dobře spolupracuje se službou Azure Notification Hubs, protože ID sady prostředků aplikace musí být nakonfigurované jako součást přihlašovacích údajů služby APN pro rozbočovač a hodnotu nelze změnit. Notification Hubs také neumožňuje `apns-topic` přepsání hodnoty hlavičky za běhu.

   Pokud chcete odesílat oznámení VOIP, musíte nakonfigurovat samostatné centrum oznámení s `.voip` ID sady prostředků aplikace.

- Odeslání oznámení VOIP vyžaduje, aby se `apns-push-type` záhlaví nastavilo na hodnotu `voip` .

   Aby zákazníci mohli přejít na iOS 13, Notification Hubs se pokusí odvodit správnou hodnotu `apns-push-type` hlavičky. Logika odvození je záměrně jednoduchá, a přitom je možné vyhnout se neúmyslným standardním oznámením. Tato metoda bohužel způsobuje problémy s oznámeními VOIP, protože Apple zpracovává oznámení VOIP jako zvláštní případ, který nedodržuje stejná pravidla jako standardní oznámení.

   Chcete-li odesílat oznámení VOIP, je nutné zadat explicitní hodnotu `apns-push-type` záhlaví.

- Notification Hubs omezuje datovou část služby APN na 4 KB, jak je popsáno v Apple. V případě oznámení VOIP umožňuje Apple datovou část až do 5 KB. Notification Hubs nerozlišuje mezi standardními oznámeními a oznámeními VOIP; všechna oznámení jsou proto omezená na 4 KB.

   Pokud chcete odesílat oznámení VOIP, nemusíte překročit limit velikosti datové části 4 KB.

## <a name="next-steps"></a>Další kroky

Další informace najdete na následujících odkazech:

- [Dokumentace k `apns-topic` `apns-push-type` hlavičkám a hodnotám, včetně zvláštních případů pro oznámení VoIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentace pro omezení velikosti datové části](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)

- [Notification Hubs aktualizace pro iOS 13](push-notification-updates-ios-13.md#apns-push-type).
