---
title: Azure Notification Hubs a migrace Google Firebase Cloud Messaging (FCM)
description: Popisuje, jak Azure Notification Hubs adresy Google GCM FCM migrace.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458293"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs a migrace Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Aktuální stav

Když Google oznámili jeho migrace z Google Cloud Messaging (GCM) pro službu Firebase Cloud Messaging (FCM), služby nabízených oznámení jako naše museli upravit, jak jsme poslali oznámení na zařízení s Androidem, aby odpovídala změnám.

Budeme aktualizovat naše back-end službu a pak zaktualizovali naše rozhraní API a sad SDK, podle potřeby. S naší implementace jsme se rozhodli zachovat kompatibilitu s existující schémata oznámení GCM minimalizovat dopad pro zákazníka. To znamená, že jsme právě odesílat oznámení do zařízení s Androidem pomocí FCM v režimu starší verze FCM. Nakonec chceme přidat true podporu FCM, včetně nových funkcí a Formát datové části. Aktuální migrace se zaměřuje na zachování kompatibility se stávajícími aplikacemi a sady SDK, která je dlouhodobější změnit. Můžete použít buď GCM nebo sad SDK FCM ve vaší aplikaci (spolu s naší sady SDK) a jsme měli jistotu, že je správně odesláno oznámení.

Zákazníci, kteří nedávno přijatých e-mailu z Google varování týkající se aplikací s využitím koncového bodu služby GCM pro oznámení. To bylo pouhým upozorněním a nic není přerušeno – oznámení systému Android vaší aplikace jsou stále odesílaná Googlu pro zpracování a Google je stále zpracovává. Někteří zákazníci, kteří se explicitně zadat koncový bod služby GCM v jejich konfiguraci služby stále používali nepoužívané koncový bod. Bylo již identifikovali překonání tohoto rozdílu jsme pracovali na řešení problému, při odesílání e-mailu Google.

Jsme nahradit tohoto nepoužívané koncového bodu a nasadit opravu.

## <a name="going-forward"></a>Do budoucna

Nejčastější dotazy týkající se společnosti Google FCM říká, že nemusíte dělat nic. V [nejčastější dotazy k FCM](https://developers.google.com/cloud-messaging/faq), Google řekl: "klientské sady SDK a služby GCM tokeny budou nadále fungovat po neomezenou dobu. Ale nebudete mít k cílení nejnovější verzi vaší aplikace pro Android služby Google Play, pokud migrujete do FCM."

Pokud vaše aplikace používá knihovnu GCM, pokračujte a postupujte podle pokynů od Googlu a upgradovat na knihovně FCM ve vaší aplikaci. Naše sada SDK je kompatibilní s buď, takže nebudete muset nic ve vaší aplikaci na naší straně aktualizovat (za předpokladu, jste aktuální verzi naší sady SDK).

## <a name="questions-and-answers"></a>Otázky a odpovědi

Tady je několik odpovědi na běžné dotazy, které jsme od zákazníků Slyšeli jsme:

**Otázka:** Co musím udělat, abyste slučitelná než datum přerušení (Google je aktuální datum přerušení je 29. května a může se změnit)?

**Odpověď:** Nothing. Budeme udržovat kompatibilitu s existujícím schématu oznámení GCM. Klíč GCM budou nadále fungovat jako obvykle, stejně jako všechny služby GCM sady SDK a knihoven, které používá vaše aplikace.

Pokud se rozhodnete upgradovat na FCM sady SDK a knihoven, abyste mohli využívat nové funkce, klíč GCM budou i nadále fungovat. Možná přejdete na použití klíče FCM, pokud chcete, ale ujistěte se, že chcete při vytváření nového projektu Firebase přidat Firebase do existujícího projektu služby GCM. Tím se zaručí zpětné kompatibility se zákazníky, na kterých běží starší verze aplikace, které dál používat GCM sad SDK a knihoven.

Pokud vytváříte nový projekt FCM a není připojení k existujícímu projektu služby GCM, jakmile upgradujete Notification Hubs se nový tajný kód FCM ztratíte schopnost k odesílání nabízených oznámení na vaše aktuální instalace aplikace, vzhledem k tomu, že nový klíč FCM nemá žádný odkaz na původní GCM projekt.

**Otázka:** Proč se zobrazuje tento e-mail o staré koncové body služby GCM používá? Co mám dělat?

**Odpověď:** Nothing. Můžeme se migrace na nové koncové body a budou brzy hotovi, takže žádná změna je nezbytné. Nic není přerušeno, naše jeden nestihli jste koncový bod jednoduše způsobila zprávy upozornění z Googlu.

**Otázka:** Jak se můžete přejít na nové FCM sady SDK a knihoven bez narušení stávajících uživatelů?

Odpověď: Kdykoli upgradovat. Google ještě neoznámila jakékoli vyřazení existující GCM sady SDK a knihoven. K zajištění neovlivní nabízená oznámení na vaše stávající uživatele, ujistěte se, že když vytvoříte nový projekt Firebase, kterou přiřazujete pomocí existující projektu služby GCM. Tím se zajistí nové Firebase tajné kódy fungují pro uživatele, kteří používají starší verze vaší aplikace pomocí služby GCM sad SDK a knihoven, jakož i nových uživatelů vaší aplikace pomocí FCM sad SDK a knihoven.

**Otázka:** Kdy mohu použít nové funkce FCM a schémata pro moje oznámení?

**Odpověď:** Jakmile jsme naše rozhraní API a sad SDK, sledujte – publikovat aktualizaci Očekáváme, že mají něco za vás v nadcházejících měsících.
