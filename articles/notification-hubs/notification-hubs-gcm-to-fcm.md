---
title: Azure Notification Hubs a migrace cloudových zpráv Google Firebase (FCM)
description: Popisuje, jak Azure Notification Hubs řeší migraci Google GCM na FCM.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127024"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs a migrace cloudových zpráv Google Firebase

## <a name="current-state"></a>Aktuální stav

Když Společnost Google oznámila přechod z aplikace Google Cloud Messaging (GCM) na Firebase Cloud Messaging (FCM), nabízené služby, jako je ta naše, musely upravit způsob, jakým jsme odeslali oznámení zařízením Android, aby se přizpůsobily změně.

Aktualizovali jsme náš back-end služby a podle potřeby jsme publikovali aktualizace našeho rozhraní API a sad SDK. S naší implementací jsme se rozhodli zachovat kompatibilitu se stávajícími schématy oznámení GCM, abychom minimalizovali dopad na zákazníka. To znamená, že v současné době odesíláme oznámení zařízením Android pomocí FCM v starším režimu FCM. Nakonec chceme přidat skutečnou podporu pro FCM, včetně nových funkcí a formátu datové části. Jedná se o dlouhodobější změnu a současná migrace je zaměřena na zachování kompatibility se stávajícími aplikacemi a sadami SDK. Můžete použít buď GCM nebo FCM SDKs ve vaší aplikaci (spolu s naší Sad SDK) a my se ujistěte, že oznámení je odesláno správně.

Někteří zákazníci nedávno obdrželi od Googlu e-mail s upozorněním na aplikace, které používají koncový bod GCM pro oznámení. To bylo jen varování a nic není rozbité - oznámení androida vaší aplikace jsou stále odesílána společnosti Google ke zpracování a Google je stále zpracovává. Někteří zákazníci, kteří explicitně zadali koncový bod GCM v konfiguraci své služby, stále používali zastaralou koncový bod. Tuto mezeru jsme již identifikovali a pracovali jsme na vyřešení problému, když Google poslal e-mail.

Nahradili jsme tento zastaralá koncový bod a oprava je nasazena.

## <a name="going-forward"></a>Do budoucna

Google FCM FAQ říká, že nemusíte dělat nic. V [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google řekl: "klient SDK a GCM tokeny budou i nadále pracovat neomezeně dlouho. Pokud však nepřenesete migraci do fcm, nebudete moci v aplikaci pro Android cílit na nejnovější verzi služeb Google Play."

Pokud vaše aplikace používá knihovnu GCM, pokračujte a podle pokynů Google upgradujte na knihovnu FCM ve vaší aplikaci. Naše sada SDK je kompatibilní s jedním z nich, takže nebudete muset nic aktualizovat ve své aplikaci na naší straně (pokud jste aktuální s naší verzí SDK).

## <a name="questions-and-answers"></a>Otázky a odpovědi

Zde je několik odpovědí na běžné otázky, které jsme slyšeli od zákazníků:

**Otázka:** Co musím udělat, aby byly kompatibilní do data přerušení (Google aktuální datum přerušení je 29.května a může změnit)?

**A:** Nic. Budeme udržovat kompatibilitu se stávajícím schématem oznámení GCM. Váš klíč GCM bude nadále fungovat stejně normálně jako všechny sady GCM SDK a knihovny používané vaší aplikací.

Pokud se rozhodnete upgradovat na sady FCM SDK a knihovny, abyste využili výhod nových funkcí, bude klíč GCM stále fungovat. Pokud chcete, můžete přepnout na použití klávesy FCM, ale ujistěte se, že přidáváte Firebase do vašeho stávajícího projektu GCM při vytváření nového projektu Firebase. To zaručí zpětnou kompatibilitu se zákazníky, kteří používají starší verze aplikace, které stále používají sady GCM SDK a knihovny.

Pokud vytváříte nový projekt FCM a nepřipojujete se ke stávajícímu projektu GCM, po aktualizaci center oznámení s novým tajným klíčem FCM ztratíte možnost nabízení oznámení na aktuální instalace aplikací, protože nový klíč FCM nemá žádný odkaz na starý GCM Projektu.

**Otázka:** Proč dostávám tento e-mail o starých koncových bodech GCM, které se používají? Co mám dělat?

**A:** Nic. Přešli jsme na nové koncové body a brzy budeme dokončeni, takže není nutná žádná změna. Nic není rozbité, náš jeden zmeškaný koncový bod jednoduše způsobil varovné zprávy od společnosti Google.

**Otázka:** Jak lze přejít na nové sady FCM SDK a knihovny bez porušení stávajících uživatelů?

A: Upgrade kdykoliv. Společnost Google dosud neoznámila žádné vyřazení stávajících sad GCM SDK a knihoven. Abyste nepřerušili nabízená oznámení stávajícím uživatelům, ujistěte se, že při vytváření nového projektu Firebase, který se přizpůsobujete stávajícímu projektu GCM, se přizpůsobujete. Tím zajistíte, že nové tajné kódy Firebase budou fungovat pro uživatele, kteří scházejí starší verze vaší aplikace s sadami GCM SDK a knihovnami, stejně jako pro nové uživatele vaší aplikace pomocí sad FCM SDK a knihoven.

**Otázka:** Kdy mohu pro svá oznámení používat nové funkce a schémata FCM?

**A:** Jakmile zveřejníme aktualizaci našeho rozhraní API a sad SDK, zůstaňte naladěni – očekáváme, že pro vás budeme mít v nadcházejících měsících něco.
