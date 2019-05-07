---
title: Co je Azure Notification Hubs?
description: Přečtěte si, jak přidat funkce nabízených oznámení služby Azure Notification Hubs.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: 03d4c269f76a89c43dec253367d07f3bf71a06d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141212"
---
# <a name="what-is-azure-notification-hubs"></a>Co je Azure Notification Hubs?

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Notification Hubs funguje jak ve scénářích velkých organizací, tak ve spotřebitelských scénářích. Příklady scénářů:

- Odesílání oznámení o důležitých zprávách milionům uživatelů s minimálním zpožděním.
- Odesílání kupónů závislých na aktuální poloze segmentům zájemců.
- Odesílání oznámení o událostech uživatelům multimediálních, sportovních, finančních nebo herních aplikací nebo jejich skupinám.
- Zasílání propagačního obsahu aplikacím kvůli informovanosti zákazníků a podpoře nabídky.
- Upozorňování uživatelů na podnikové události, jako jsou nové zprávy a pracovní položky.
- Odesílání kódů k vícefaktorovému ověřování.

## <a name="what-are-push-notifications"></a>Co jsou nabízená oznámení?

Nabízená oznámení je forma komunikace uživatelů aplikací uživatelům mobilních aplikací se poslalo oznámení kde některé požadované informace, obvykle v místní nabídce nebo dialogového okna na mobilním zařízení. Uživatelé obecně zvolte k zobrazení nebo Zavřít zprávu; Výběr bývalé otevře mobilních aplikací, které předávají oznámení. Některá oznámení jsou silent - doručit na pozadí pro aplikace ke zpracování na pozadí a rozhodnout, jak postupovat.

Nabízená oznámení jsou nedílnou součástí spotřebitelských aplikací, protože zvyšují zapojení a využití aplikace, ale jsou důležitá i v podnikových aplikacích, kde slouží ke zveřejňování aktuálních obchodních informací. Je uživatel aplikace sdělení nejlépe, protože je energeticky úsporných pro mobilní zařízení pro oznámení odesílatelů, flexibilní a k dispozici, pokud příslušné aplikace nejsou aktivní.

Další informace o nabízených oznámeních pro některé oblíbené platformy najdete v následujících tématech:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak fungují nabízená oznámení

Nabízená oznámení se doručují prostřednictvím infrastruktur odpovídajících určitým platformám. Tyto infrastruktury se označují jako *systémy oznámení platforem* (PNS). Nabízejí jednoduché funkce doručování zpráv do zařízení se zadaným popisovačem. Tyto funkce nemají společné rozhraní. K odesílání oznámení pro všechny zákazníky prostřednictvím na Android, iOS a Windows verze aplikace, vývojář musí samostatně pracovat s Apple Push Notification Service(APNS), Firebase Cloud Messaging(FCM) a Service(WNS) oznámení Windows.

Celkový přehled fungování nabízených oznámení:

1. Aplikace rozhodne, že to chce obdržet oznámení, tak pro cílovou platformu, kde je spuštěná a požadavků nabízené jedinečný a dočasný popisovač aplikace kontaktuje systém oznámení platformy. Typ popisovače závisí na systému (například služby nabízených oznámení Windows používá identifikátory URI APNS používá tokeny).
2. Klientská aplikace si tento popisovač uloží v back-endu aplikace nebo zprostředkovatele.
3. K odesílání nabízených oznámení, back-end aplikace kontaktuje systém oznámení platformy pomocí popisovač pro cílové konkrétní klientské aplikace.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![Pracovní postup nabízených oznámení](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními

Systémy PNS jsou velmi výkonné. Přesto ale nechávají většinu práce na vývojáři aplikace, a to i při implementaci běžných scénářů nabízených oznámení, jako je vysílání nabízených oznámení nebo jejich odeslání určitému segmentu uživatelů.

K zasílání nabízených oznámení je potřeba složitá infrastruktura, která nesouvisí s hlavní obchodní logikou aplikace. Některé problémy týkající se infrastruktury:

- **Závislost na platformě**
  - Back-end vyžaduje komplexní a obtížné udržovat závislého na platformě logiky k odesílání oznámení do zařízení na různých platformách jako PNSes nejsou unified.
- **Škálování**
  - Podle pokynů PNS musí být tokeny zařízení aktualizované při každém spuštění aplikace. Back-endu se zabývá velký objem přenosů a databázi přístup jenom k lepšímu tokeny aktuální. Počet zařízení naroste stovky, tisíce nebo miliony, náklady na vytvoření a údržbu této infrastruktury při velkém.
  - Většina systémů PNS nepodporuje vysílání na více zařízení. Jednoduché vysílání pro milion zařízení znamená milion volání systémů PNS. Škálovat tento provoz s minimální latencí není úplně jednoduché.
- **Směrování**
  - Systémy PNS sice nabízejí způsob, jak posílat zprávy zařízením, ale většina oznámení posílaných aplikacemi je cílených na uživatele nebo zájmové skupiny. Back-end proto musí udržovat registr, který spojuje zařízení se zájmovými skupinami, uživateli, vlastnostmi apod. Tato dodatečná režie prodlužuje dobu potřebnou k publikování a navyšuje náklady na údržbu aplikace.

## <a name="why-use-azure-notification-hubs"></a>Proč používat Azure Notification Hubs?

Notification Hubs eliminuje všem složitosti přidružené k odesílání nabízených oznámení na vlastní z vaší aplikace back-endu. Platformově univerzální škálovatelná infrastruktura, určená k zasílání nabízených oznámení, zmenšuje potřebu programování oznámení a zjednodušuje back-end. Se službou Notification Hubs zodpovídají zařízení jenom za registraci popisovačů systému PNS v centru, zatímco back-end posílá zprávy uživatelům nebo zájmovým skupinám. Je to vidět na následujícím obrázku:

![Diagram centra oznámení](./media/notification-hubs-overview/notification-hub-diagram.png)

Centra oznámení jsou předem připravená jádra pro zasílání oznámení, která mají následující výhody:

- **Jsou pro různé platformy**
  - Podporují všechny hlavní platformy, jako je iOS, Android, Windows, Kindle a Baidu.
  - Nabízejí společné rozhraní pro zasílání oznámení všem platformám ve formátech, které jsou specifické pro danou platformu nebo jsou na ní nezávislé, a to bez práce spojené s určitou platformou.
  - Správa popisovačů zařízení se provádí na jednom místě.
- **Jsou pro různé back-endy**
  - Mohou být cloudové nebo místní.
  - .NET, Node.js, Java atd.
- **Bohatá sada schémat doručování**
  - Vysílání na jeden nebo více platforem: Můžete se okamžitě vysílání na miliony zařízení napříč platformami pomocí jediného volání rozhraní API.
  - Odeslat do zařízení: Můžete cílit oznámení pro jednotlivá zařízení.
  - Push pro uživatele: Značky a šablony funkce vám pomůže oslovit všechny různé platformy zařízení uživatele.
  - Vložit do segmentu dynamické značky: Funkce značky vám pomůže segmentu zařízení a metodou push do nich podle svých potřeb, jestli posíláte na jeden segment nebo výraz segmentů (například aktivní a odpovídající Seattle není nového uživatele). Místo omezení publikování a odebírání můžete kdekoli a kdykoli aktualizovat značky zařízení.
  - Lokalizovaná nabízená: Funkce šablony pomáhá dosahovat lokalizace bez ovlivnění kódu back-endu.
  - Tiché nabízených oznámení: Model push pro vyžádání obsahu můžete povolit posílání tiché oznámení do zařízení a aktivaci jejich dokončení některých si nebo akce.
  - Plánované nabízené oznámení: Můžete naplánovat, kdykoli odeslat oznámení.
  - Přímé nabízených oznámení: Můžete přeskočit registraci zařízení se službou Notification Hubs a přímo batch metodou push do seznamu popisovače zařízení.
  - Přizpůsobená nabízená: Proměnné nabízených oznámení zařízení, které individuální pomáhá zasílané specifická pro zařízení nabízená oznámení pomocí přizpůsobených páry klíč hodnota.
- **Bohaté telemetrické funkce**
  - Na webu Azure Portal jsou k dispozici obecné telemetrické funkce poskytující informace o nabízených oznámeních, zařízeních, chybách a provozu.
  - Telemetrie zprávy sleduje každé nabízené oznámení od počátečního volání až po úspěšné hromadné odeslání nabízených oznámení službou Notification Hubs.
  - Zpětná vazba systému oznámení platformy oznamuje veškerou zpětnou vazbu systémů PNS, aby bylo možné ji použít k ladění.
- **Škálovatelnost**
  - Rychlé zprávy můžete posílat milionům zařízení bez změny architektury nebo horizontálního dělení zařízení.
- **Zabezpečení**
  - K dispozici je sdílený tajný přístupový klíč (SAS) nebo federované ověření.

## <a name="next-steps"></a>Další postup

Začínáme s vytváření a používání centra oznámení pomocí následujících [kurzu: Nabízená oznámení do mobilní aplikace](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
