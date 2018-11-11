---
title: Co je Azure Notification Hubs?
description: Přečtěte si, jak přidat funkce nabízených oznámení služby Azure Notification Hubs.
author: dimazaid
manager: kpiteira
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
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: a39eebda5abb41e02ce7254e2084541e86ecee30
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229505"
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
Nabízená oznámení představují způsob komunikace aplikace s uživatelem. Uživatelům mobilních aplikací se oznamují určité požadované informace. Tato oznámení se většinou nabízejí v místní nabídce nebo v dialogovém okně. Uživatelé si většinou můžou vybrat, jestli chtějí zprávu zobrazit nebo zavřít. Pokud zvolí první možnost, otevře se mobilní aplikace, která oznámení zveřejnila.

Nabízená oznámení jsou nedílnou součástí spotřebitelských aplikací, protože zvyšují zapojení a využití aplikace, ale jsou důležitá i v podnikových aplikacích, kde slouží ke zveřejňování aktuálních obchodních informací. Jde o nejlepší způsob komunikace aplikace s uživatelem, protože je energeticky nenáročný na mobilní zařízení, je flexibilní pro odesílatele oznámení a je k dispozici, i když odpovídající aplikace nejsou aktivní.

Další informace o nabízených oznámeních pro některé oblíbené platformy najdete v následujících tématech: 
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak fungují nabízená oznámení
Nabízená oznámení se doručují prostřednictvím infrastruktur odpovídajících určitým platformám. Tyto infrastruktury se označují jako *systémy oznámení platforem* (PNS). Nabízejí jednoduché funkce doručování zpráv do zařízení se zadaným popisovačem. Tyto funkce nemají společné rozhraní. Pokud chcete posílat oznámení všem zákazníkům s verzí aplikace pro iOS, Android a Windows, musí vývojář pracovat se službami Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) a Windows Notification Service (WNS).

Celkový přehled fungování nabízených oznámení:

1. Klientská aplikace se rozhodne, že chce dostávat oznámení. Kontaktuje příslušný systém oznámení platformy (PNS) a načte jedinečný dočasný popisovač nabízených oznámení. Typ popisovače závisí na systému (například WNS používá identifikátory URI a APNS používá tokeny).
2. Klientská aplikace uloží popisovač v back-endové aplikaci neboli u poskytovatele.
3. Pokud chce aplikace odeslat nabízené oznámení, back-endová aplikace kontaktuje PNS prostřednictvím popisovače určeného pro konkrétní klientskou aplikaci.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![Pracovní postup nabízených oznámení](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními
Systémy PNS jsou velmi výkonné. Přesto ale nechávají většinu práce na vývojáři aplikace, a to i při implementaci běžných scénářů nabízených oznámení, jako je vysílání nabízených oznámení nebo jejich odeslání určitému segmentu uživatelů.

K zasílání nabízených oznámení je potřeba složitá infrastruktura, která nesouvisí s hlavní obchodní logikou aplikace. Některé problémy týkající se infrastruktury:

- **Závislost na platformě**
    - Back-end musí mít složitou a na údržbu náročnou logiku, která závisí na určité platformě a slouží k posílání oznámení zařízením s různými platformami, protože systémy PNS nejsou jednotné.
- **Škálování**
    - Podle pokynů PNS musí být tokeny zařízení aktualizované při každém spuštění aplikace. Back-end zpracovává velký počet přenosů a přístupů k databázi, jenom aby udržel aktuální tokeny. Pokud počet zařízení vzroste do stovek milionů a pak do miliard, jsou náklady na vytvoření a údržbu této infrastruktury velmi vysoké.
    - Většina systémů PNS nepodporuje vysílání na více zařízení. Jednoduché vysílání pro milion zařízení znamená milion volání systémů PNS. Škálovat tento provoz s minimální latencí není úplně jednoduché.
- **Směrování** 
    - Systémy PNS sice nabízejí způsob, jak posílat zprávy zařízením, ale většina oznámení posílaných aplikacemi je cílených na uživatele nebo zájmové skupiny. Back-end proto musí udržovat registr, který spojuje zařízení se zájmovými skupinami, uživateli, vlastnostmi apod. Tato dodatečná režie prodlužuje dobu potřebnou k publikování a navyšuje náklady na údržbu aplikace.

## <a name="why-use-azure-notification-hubs"></a>Proč používat Azure Notification Hubs?
Služba Notification Hubs eliminuje všechny komplikace, se kterými se můžete setkat, kdybyste nabízená oznámení z back-endové aplikace zasílali sami. Platformově univerzální škálovatelná infrastruktura, určená k zasílání nabízených oznámení, zmenšuje potřebu programování oznámení a zjednodušuje back-end. Se službou Notification Hubs zodpovídají zařízení jenom za registraci popisovačů systému PNS v centru, zatímco back-end posílá zprávy uživatelům nebo zájmovým skupinám. Je to vidět na následujícím obrázku:

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
    - Můžete vysílat pro jednu nebo více platforem. Jedním voláním rozhraní API můžete hned vysílat do milionů zařízení s různými platformami.
    - Zasílání zařízení: Oznámení mohou být určena i jednotlivým zařízením.
    - Zasílání uživateli: Funkce značek a šablon umožňují poslat oznámení všem zařízením uživatele bez ohledu na jejich platformu.
    - Zasílání segmentu zařízení s využitím dynamických značek: Funkce značek pomáhá segmentovat zařízení a zasílat jim oznámení tak, jak potřebujete. Přitom nezáleží, jestli posíláte oznámení jednomu segmentu nebo výrazu ze segmentů (například aktivní uživatelé AND žijící v určitém městě NOT noví uživatelé). Místo omezení publikování a odebírání můžete kdekoli a kdykoli aktualizovat značky zařízení.
    - Lokalizovaná oznámení: Funkce šablon umožňuje používat lokalizaci beze změny back-endového kódu.
    - Tiché nabízení: Vzorec zasílání a přijetí změn můžete aktivovat tím, že zařízením pošlete tichá oznámení, kterými aktivujete určité změny nebo akce.
    - Plánované nabízení: Posílání oznámení můžete naplánovat na libovolnou dobu.
    - Přímé nabízení: Registraci zařízení ve službě Notification Hubs můžete přeskočit a oznámení přímo hromadně poslat seznamu popisovačů zařízení.
    - Přizpůsobené nabízení: Proměnné pro zasílání nabízených oznámení zařízením umožňují posílat přizpůsobená nabízená oznámení určitým zařízením s vlastními dvojicemi klíč-hodnota.
- **Bohaté telemetrické funkce**
    - Na webu Azure Portal jsou k dispozici obecné telemetrické funkce poskytující informace o nabízených oznámeních, zařízeních, chybách a provozu.
    - Telemetrie zprávy sleduje každé nabízené oznámení od počátečního volání až po úspěšné hromadné odeslání nabízených oznámení službou Notification Hubs.
    - Zpětná vazba systému oznámení platformy oznamuje veškerou zpětnou vazbu systémů PNS, aby bylo možné ji použít k ladění.
- **Škálovatelnost** 
    - Rychlé zprávy můžete posílat milionům zařízení bez změny architektury nebo horizontálního dělení zařízení.
- **Zabezpečení**
    - K dispozici je sdílený tajný přístupový klíč (SAS) nebo federované ověření.

## <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps
Pro zajištění plynulého a sjednocujícího prostředí napříč službami Azure nabízí [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) integrovanou podporu pro nabízená oznámení prostřednictvím služby Notification Hubs. Služba [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací určenou pro vývojáře a integrátory systémů ve velkých firmách. Přináší bohatou sadu funkcí pro vývojáře pro mobilní zařízení.

Vývojáři v Mobile Apps mohou službu Notification Hubs využívat v rámci následujícího pracovního postupu:

1. Načtení popisovače systému PNS zařízení
2. Registrace zařízení ve službě Notification Hubs se provádí pohodlně v registračním rozhraní API sady Mobile Apps Client SDK.

    > [!NOTE]
    > Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.
1. Odesílání oznámení z back-endu aplikace pomocí Notification Hubs

Zde jsou některé výhody, které vývojáři získají díky této integraci:

- **Sady Mobile Apps Client SDK:** Tyto sady jsou univerzální pro všechny platformy. Nabízejí jednoduchá rozhraní API pro registraci a automatickou komunikaci s centrem oznámení automaticky propojeným s mobilní aplikací. Vývojáři se nemusí zabývat přihlašovacími údaji pro Notification Hubs a pracovat s další službou.
    - *Zasílání nabízených oznámení uživatelům:* Sady SDK automaticky označí dané zařízení ověřeným ID funkce Mobile Apps, aby byl možný scénář nabízených oznámení pro uživatele.
    - *Zasílání nabízených oznámení zařízením:* Sady SDK k registraci ve službě Notification Hubs automaticky používají instalační ID funkce Mobile Apps jako identifikátor GUID, aby vývojáři nemuseli spravovat identifikátory GUID různých služeb.
- **Instalační model:** Funkce Mobile Apps pracuje s nejnovějším modelem nabízených oznámení služby Notification Hubs. Zastupuje všechny vlastnosti nabízených oznámení v instalaci JSON spojené se zařízením, aby vyhovovaly Službě nabízených oznámení a daly se snadno použít.
- **Flexibilita:** Vývojáři se vždy mohou rozhodnout, že budou pracovat přímo se službou Notification Hubs, i když je tato služba integrovaná.
- **Integrované prostředí [Azure Portal](https://portal.azure.com)**: Nabízená oznámení jsou v Mobile Apps vizuálně znázorněná jako funkce. Vývojáři mohou prostřednictvím Mobile Apps snadno pracovat s přidruženým centrem oznámení.

## <a name="next-steps"></a>Další kroky

Začínáme vytvářet a používat centrum oznámení podle [kurzu o zasílání nabízených oznámení mobilním aplikacím](notification-hubs-android-push-notification-google-fcm-get-started.md). 

[0]: ./media/notification-hubs-overview/registration-diagram.png

[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: http://azure.microsoft.com/services/notification-hubs

[Notification Hubs tutorials and guides]: http://azure.microsoft.com/documentation/services/notification-hubs

[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started

[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started

[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started

[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started

[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started

[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started

[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started

[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx

[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx

[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/

[templates]: notification-hubs-templates-cross-platform-push-messages.md

[Azure portal]: https://portal.azure.com

[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
