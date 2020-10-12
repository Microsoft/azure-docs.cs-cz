---
title: Migrace Azure Notification Hubs a FCM (Google Firebase Cloud Messaging)
description: Popisuje, jak Azure Notification Hubs adresuje migraci Google GCM na FCM.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80127024"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Migrace zpráv v cloudu pro Azure Notification Hubs a Google Firebase

## <a name="current-state"></a>Aktuální stav

Když Google oznámila migraci z Google Cloud Messaging (GCM) na Firebase Cloud Messaging (FCM), služba nabízených oznámení jako náš musela upravit způsob, jakým jsme poslali oznámení na zařízení s Androidem, aby se tato změna vešla.

Aktualizovali jsme naše back-end služby a pak jsme podle potřeby publikovali aktualizace našich rozhraní API a sad SDK. Díky naší implementaci jsme se rozhodli zachovat kompatibilitu s existujícími schématy oznámení GCM, aby se minimalizoval dopad na zákazníky. To znamená, že aktuálně posíláme oznámení na zařízení s Androidem pomocí FCM ve starším režimu FCM. Nakonec chceme přidat skutečnou podporu pro FCM, včetně nových funkcí a formátu datové části. To je dlouhodobá změna a aktuální migrace se zaměřuje na zachování kompatibility se stávajícími aplikacemi a sadami SDK. V aplikaci můžete použít sady SDK GCM nebo FCM (spolu s naší sadou SDK) a ujistěte se, že je oznámení odesíláno správně.

Někteří zákazníci nedávno obdrželi e-mail od společnosti Google Warning o aplikacích pomocí koncového bodu GCM pro oznámení. Toto bylo jenom upozornění a nic se nepřerušilo – oznámení Androidu vaší aplikace se pořád posílají do Google pro zpracování a Google je stále zpracovává. Někteří zákazníci, kteří v konfiguraci služby zadali explicitně zadaný koncový bod GCM, používali i zastaralý koncový bod. Tuto mezeru jsme již identifikovali a při řešení problému na webu Google poslali e-mail.

Nahradili jsme zastaralý koncový bod a tato oprava je nasazená.

## <a name="going-forward"></a>Po přechodu dál

Nejčastější dotazy k FCM Google říkají, že nemusíte nic dělat. V [nejčastějších dotazech k FCM](https://developers.google.com/cloud-messaging/faq)se Google říká, že klientské sady SDK a tokeny GCM budou i nadále fungovat neomezeně. Nebudete ale moct cílit na nejnovější verzi Služby Google Play v aplikaci pro Android, pokud nemigrujete na FCM. "

Pokud vaše aplikace používá knihovnu GCM, pokračujte podle pokynů Google a upgradujte na knihovnu FCM ve vaší aplikaci. Naše sada SDK je kompatibilní s nástrojem, takže nebudete muset aktualizovat cokoli ve vaší aplikaci na naší straně (Pokud jste v naší verzi sady SDK aktuální).

## <a name="questions-and-answers"></a>Otázky a odpovědi

Tady jsou některé odpovědi na běžné otázky, které od zákazníků slyšeli:

**Otázka:** Co je potřeba k tomu, aby byly kompatibilní s datem přerušení (aktuální datum přerušení Google je možné vysílání 29. a může se změnit)?

**A:** Žádným. Budeme udržovat kompatibilitu s existujícím schématem oznámení GCM. Váš GCM klíč bude i nadále fungovat jako normální, stejně jako všechny GCM sady SDK a knihovny, které vaše aplikace používá.

Pokud se rozhodnete upgradovat na sady SDK a knihovny FCM, abyste mohli využívat nové funkce, bude váš GCM klíč pořád fungovat. Pokud chcete, můžete použít FCM klíč, ale při vytváření nového projektu Firebase se ujistěte, že přidáváte Firebase k vašemu stávajícímu projektu GCM. Tím zajistíte zpětnou kompatibilitu se zákazníky, kteří používají starší verze aplikace, která stále používá GCM SDK a knihovny.

Pokud vytváříte nový projekt FCM a nepřipojujete se k existujícímu projektu GCM, po aktualizaci Notification Hubs s novým tajným klíčem FCM ztratíte možnost nabízených oznámení na vaše aktuální instalace aplikací, protože nový klíč FCM neobsahuje odkaz na starý projekt GCM.

**Otázka:** Proč se mi tento e-mail zobrazuje I pro staré koncové body GCM? Co musím udělat?

**A:** Žádným. Migrujeme se na nové koncové body a brzo se dokončí, takže není potřeba žádná změna. Nic neruší, náš jeden zmeškaný koncový bod jednoduše vyvolal varovné zprávy z Google.

**Otázka:** Jak můžu přejít na nové sady SDK a knihovny FCM bez přerušení stávajících uživatelů?

Odpověď: upgrade kdykoli. Společnost Google zatím neoznámila žádné vyřazení stávajících GCM sad SDK a knihoven. Abyste se ujistili, že jste nepřerušili nabízená oznámení vašim stávajícím uživatelům, ujistěte se, že jste vytvořili nový projekt Firebase, který přidružíte ke stávajícímu projektu GCM. Tím zajistíte, že budou nové tajné kódy Firebase fungovat pro uživatele, kteří používají starší verze vaší aplikace pomocí sad SDK a knihoven GCM, stejně jako noví uživatelé vaší aplikace pomocí sad SDK a knihoven FCM.

**Otázka:** Kdy můžu použít nové funkce FCM a schémata pro moje oznámení?

**A:** Po publikování aktualizace našich rozhraní API a sad SDK si můžete zůstat vyladěné – očekáváme, že v nadcházejících měsících budeme mít něco za vás.
