---
title: Co je Azure Notification Hubs?
description: Přečtěte si, jak přidat funkce nabízených oznámení služby Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: jwargo
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
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f92c9ac6942bfad8df57bb3887d1ba5b7c7190af
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213105"
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

Nabízená oznámení jsou formou komunikace mezi uživateli, kde se uživatelům mobilních aplikací zobrazí oznámení o určitých požadovaných informacích, obvykle v místním nebo dialogovém okně na mobilním zařízení. Uživatelé většinou zvolí zobrazení nebo zavření zprávy; Když zvolíte předchozí, otevře se mobilní aplikace, která oznámení oznámila. Některá oznámení se v tichém režimu doručují na pozadí, které aplikace zpracovávají na pozadí a rozhodují, co dělat.

Nabízená oznámení jsou nedílnou součástí spotřebitelských aplikací, protože zvyšují zapojení a využití aplikace, ale jsou důležitá i v podnikových aplikacích, kde slouží ke zveřejňování aktuálních obchodních informací. Jedná se o nejlepší komunikaci mezi uživateli, protože je energeticky efektivní pro mobilní zařízení, flexibilní pro odesílatele oznámení a k dispozici, pokud nejsou odpovídající aplikace aktivní.

Další informace o nabízených oznámeních pro některé oblíbené platformy najdete v následujících tématech:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak fungují nabízená oznámení

Nabízená oznámení se doručují prostřednictvím infrastruktur odpovídajících určitým platformám. Tyto infrastruktury se označují jako *systémy oznámení platforem* (PNS). Nabízejí jednoduché funkce doručování zpráv do zařízení se zadaným popisovačem. Tyto funkce nemají společné rozhraní. Aby bylo možné odesílat oznámení všem zákazníkům v rámci aplikací pro Android, iOS a Windows, musí vývojář spolupracovat s Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) a Windows Notification Service (WNS) samostatně.

Celkový přehled fungování nabízených oznámení:

1. Aplikace rozhoduje, že chce dostávat oznámení, takže kontaktuje PNS pro cílovou platformu, kde je aplikace spuštěná, a požádá o jedinečný a dočasný popisovač nabízení. Typ popisovače závisí na systému (například WNS používá identifikátory URI, zatímco APNS používá tokeny).
2. Klientská aplikace ukládá tento popisovač do back-endu nebo poskytovatele aplikace.
3. Pokud chcete odeslat nabízené oznámení, back-end aplikace kontaktuje PNS pomocí popisovače pro cílení na konkrétní klientskou aplikaci.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![Pracovní postup nabízených oznámení](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními

Systémy PNS jsou velmi výkonné. Přesto ale nechávají většinu práce na vývojáři aplikace, a to i při implementaci běžných scénářů nabízených oznámení, jako je vysílání nabízených oznámení nebo jejich odeslání určitému segmentu uživatelů.

K zasílání nabízených oznámení je potřeba složitá infrastruktura, která nesouvisí s hlavní obchodní logikou aplikace. Některé problémy týkající se infrastruktury:

- **Závislost na platformě**
  - Back-end vyžaduje pro posílání oznámení do zařízení na různých platformách složitou a pevně danou logiku závislou na platformě, protože PNSes nejsou sjednocené.
- **Škálování**
  - Podle pokynů PNS musí být tokeny zařízení aktualizované při každém spuštění aplikace. Back-end se zabývá velkým objemem provozu a přístup k databázím, aby bylo možné tokeny udržovat v aktuálním stavu. Když počet zařízení roste na stovky, tisíce nebo miliony, náklady na vytvoření a údržbu této infrastruktury jsou obrovské.
  - Většina systémů PNS nepodporuje vysílání na více zařízení. Jednoduché vysílání pro milion zařízení znamená milion volání systémů PNS. Škálovat tento provoz s minimální latencí není úplně jednoduché.
- **Směrování**
  - Systémy PNS sice nabízejí způsob, jak posílat zprávy zařízením, ale většina oznámení posílaných aplikacemi je cílených na uživatele nebo zájmové skupiny. Back-end proto musí udržovat registr, který spojuje zařízení se zájmovými skupinami, uživateli, vlastnostmi apod. Tato dodatečná režie prodlužuje dobu potřebnou k publikování a navyšuje náklady na údržbu aplikace.

## <a name="why-use-azure-notification-hubs"></a>Proč používat Azure Notification Hubs?

Notification Hubs eliminují všechny složitosti spojené s doručováním oznámení z back-endu vaší aplikace. Platformově univerzální škálovatelná infrastruktura, určená k zasílání nabízených oznámení, zmenšuje potřebu programování oznámení a zjednodušuje back-end. Se službou Notification Hubs zodpovídají zařízení jenom za registraci popisovačů systému PNS v centru, zatímco back-end posílá zprávy uživatelům nebo zájmovým skupinám. Je to vidět na následujícím obrázku:

![Diagram centra oznámení](./media/notification-hubs-overview/notification-hub-diagram.png)

Centra oznámení jsou předem připravená jádra pro zasílání oznámení, která mají následující výhody:

- **Jsou pro různé platformy**
  - Podporují všechny hlavní platformy, jako je iOS, Android, Windows, Kindle a Baidu.
  - Nabízejí společné rozhraní pro zasílání oznámení všem platformám ve formátech, které jsou specifické pro danou platformu nebo jsou na ní nezávislé, a to bez práce spojené s určitou platformou.
  - Správa popisovačů zařízení se provádí na jednom místě.
- **Jsou pro různé back-endy**
  - Mohou být cloudové nebo místní.
  - .NET, Node. js, Java, Python atd.
- **Bohatá sada schémat doručování**
  - Všesměrové vysílání na jednu nebo několik platforem: Jedno volání rozhraní API můžete okamžitě vysílat do milionů zařízení napříč platformami.
  - Vložit do zařízení: Můžete směrovat oznámení na jednotlivá zařízení.
  - Odeslat uživateli: Funkce značek a šablon vám pomůžou kontaktovat všechna zařízení uživatele na více platforem.
  - Vložit do segmentu pomocí dynamických značek: Funkce značek pomáhá segmentovat zařízení a nabízet je podle vašich potřeb, ať už posíláte do jednoho segmentu, nebo výrazu segmentů (například aktivní a žijete v Seattlu není New User). Místo omezení publikování a odebírání můžete kdekoli a kdykoli aktualizovat značky zařízení.
  - Lokalizované vložení: Funkce šablon pomáhá dosáhnout lokalizace, aniž by to ovlivnilo back-end kód.
  - Tiché vložení: Vzor nabízení k vyžádání obsahu můžete povolit odesláním tichých oznámení do zařízení a jejich aktivací, aby bylo možné provést určité akce nebo akce.
  - Naplánovaná nabízená oznámení: Můžete naplánovat odesílání oznámení kdykoli.
  - Přímá nabízená oznámení: Můžete přeskočit registraci zařízení pomocí služby Notification Hubs a přímo dávkovou úlohu push do seznamu popisovačů zařízení.
  - Individuální nabízená oznámení: Proměnné nabízených oznámení zařízení pomáhají odesílat přizpůsobená nabízená oznámení specifická pro konkrétní zařízení s přizpůsobenými páry klíč-hodnota.
- **Bohaté telemetrické funkce**
  - Na webu Azure Portal jsou k dispozici obecné telemetrické funkce poskytující informace o nabízených oznámeních, zařízeních, chybách a provozu.
  - Telemetrie zprávy sleduje každé nabízené oznámení od počátečního volání až po úspěšné hromadné odeslání nabízených oznámení službou Notification Hubs.
  - Zpětná vazba systému oznámení platformy oznamuje veškerou zpětnou vazbu systémů PNS, aby bylo možné ji použít k ladění.
- **Škálovatelnost**
  - Rychlé zprávy můžete posílat milionům zařízení bez změny architektury nebo horizontálního dělení zařízení.
- **Zabezpečení**
  - K dispozici je sdílený tajný přístupový klíč (SAS) nebo federované ověření.

## <a name="next-steps"></a>Další kroky

Začněte s vytvářením a používáním centra oznámení pomocí tohoto [kurzu: Nabízená oznámení do mobilních](notification-hubs-android-push-notification-google-fcm-get-started.md)aplikací.

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
