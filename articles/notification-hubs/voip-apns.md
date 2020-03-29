---
title: Odesílání oznámení APNS VOIP pomocí center oznámení Azure
description: Zjistěte, jak odesílat oznámení APNS VOIP prostřednictvím center oznámení Azure (není oficiálně podporováno).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146885"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Použití apns VOIP prostřednictvím centra oznámení (není oficiálně podporováno)

Je možné použít oznámení APNS VOIP prostřednictvím Azure Notification Hubs; pro tento scénář však neexistuje žádná oficiální podpora.

## <a name="considerations"></a>Požadavky

Pokud se přesto rozhodnete odesílat oznámení APNS VOIP prostřednictvím center oznámení, mějte na paměti následující omezení:

- Odeslání oznámení VOIP `apns-topic` vyžaduje, aby byla hlavička nastavena `.voip` na ID sady aplikací + příponu. Například u ukázkové aplikace s `com.microsoft.nhubsample`ID `apns-topic` sady by měla být hlavička nastavena na`com.microsoft.nhubsample.voip.`

   Tato metoda nefunguje dobře s Azure Notification Hubs, protože ID sady aplikace musí být nakonfigurované jako součást přihlašovacích údajů centra APNS a hodnotu nelze změnit. Centra oznámení také neumožňuje hodnotu `apns-topic` záhlaví přepsat za běhu.

   Chcete-li odesílat oznámení VOIP, musíte nakonfigurovat samostatné centrum oznámení s ID sady `.voip` prostředků aplikace.

- Odeslání oznámení VOIP `apns-push-type` vyžaduje, aby byla `voip`hlavička nastavena na hodnotu .

   Chcete-li zákazníkům pomoci s přechodem na iOS 13, Notification `apns-push-type` Hubs pokusí odvodit správnou hodnotu pro záhlaví. Logika odvození je záměrně jednoduchá, ve snaze vyhnout se porušení standardníoznámení. Bohužel tato metoda způsobuje problémy s oznámeními VOIP, protože Apple zachází s oznámeními VOIP jako se zvláštním případem, který nedodržuje stejná pravidla jako standardní oznámení.

   Chcete-li odeslat oznámení VOIP, musíte `apns-push-type` zadat explicitní hodnotu záhlaví.

- Centra oznámení omezují datové části APNS na 4 KB, jak dokládá Apple. Pro oznámení VOIP apple umožňuje užitečné zatížení až 5 KB. Centra oznámení nerozlišuje mezi standardnía VOIP oznámení; proto jsou všechna oznámení omezena na 4 KB.

   Chcete-li odeslat oznámení VOIP, nesmí překročit limit velikosti datové části 4 KB.

## <a name="next-steps"></a>Další kroky

Další informace najdete na následujících odkazech:

- [Dokumentace `apns-topic` a `apns-push-type` záhlaví a hodnoty, včetně zvláštních případů pro oznámení VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentace pro omezení velikosti datové části](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aktualizace centra oznámení pro iOS 13](push-notification-updates-ios-13.md#apns-push-type).
