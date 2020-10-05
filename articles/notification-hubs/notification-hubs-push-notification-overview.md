---
title: Co je Azure Notification Hubs?
description: Přečtěte si, jak přidat funkce nabízených oznámení služby Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: b8ac964c62ac4b30e9280e4ae921297ba860d7c2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "86529926"
---
# <a name="what-is-azure-notification-hubs"></a>Co je Azure Notification Hubs?

Azure Notification Hubs poskytují snadno použitelný a Škálovatelný modul nabízených oznámení, který umožňuje odesílat oznámení na libovolnou platformu (iOS, Android, Windows atd.) z libovolného back-endu (cloudu nebo místně). Notification Hubs funguje jak ve scénářích velkých organizací, tak ve spotřebitelských scénářích. Příklady scénářů:

- Odesílání oznámení o důležitých zprávách milionům uživatelů s minimálním zpožděním.
- Odesílání kupónů závislých na aktuální poloze segmentům zájemců.
- Odesílání oznámení o událostech uživatelům multimediálních, sportovních, finančních nebo herních aplikací nebo jejich skupinám.
- Zasílání propagačního obsahu aplikacím kvůli informovanosti zákazníků a podpoře nabídky.
- Informujte uživatele o podnikových událostech, jako jsou nové zprávy a pracovní položky.
- Odesílání kódů k vícefaktorovému ověřování.

## <a name="what-are-push-notifications"></a>Co jsou nabízená oznámení?

Nabízená oznámení jsou formou komunikace mezi uživateli, kde se uživatelům mobilních aplikací zobrazí oznámení o určitých požadovaných informacích, obvykle v místním nebo dialogovém okně na mobilním zařízení. Uživatelé většinou zvolí zobrazení nebo zavření zprávy; Když zvolíte předchozí, otevře se mobilní aplikace, která oznámení oznámila. Některá oznámení se v tichém režimu doručují na pozadí, aby aplikace mohla zpracovat a rozhodnout, co dělat.

Nabízená oznámení jsou nedílnou součástí spotřebitelských aplikací, protože zvyšují zapojení a využití aplikace, ale jsou důležitá i v podnikových aplikacích, kde slouží ke zveřejňování aktuálních obchodních informací. Jedná se o nejlepší komunikaci mezi uživateli, protože je energeticky efektivní pro mobilní zařízení, flexibilní pro odesílatele oznámení a k dispozici, pokud nejsou odpovídající aplikace aktivní.

> [!NOTE]
> Azure Notification Hubs nepodporují nabízená oznámení služby Voice over Internet Protocol (VOIP). [Tento článek ale popisuje, jak můžete používat oznámení služby APN VoIP](voip-apns.md) prostřednictvím Azure Notification Hubs.

Další informace o nabízených oznámeních pro některé oblíbené platformy najdete v následujících tématech:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Jak fungují nabízená oznámení?

Nabízená oznámení se doručují prostřednictvím infrastruktur odpovídajících určitým platformám. Tyto infrastruktury se označují jako *systémy oznámení platforem* (PNS). Nabízejí základní funkce nabízených oznámení pro doručení zprávy do zařízení s poskytovaným popisovačem a nemají žádné společné rozhraní. Aby mohl vývojář odeslat oznámení všem zákazníkům v rámci aplikací pro Android, iOS a Windows, musí spolupracovat samostatně s Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) a Windows Notification Service (WNS).

Celkový přehled fungování nabízených oznámení:

1. Aplikace chce obdržet oznámení, takže kontaktuje PNS pro cílovou platformu, na které je aplikace spuštěná, a požádá o jedinečný a dočasný popisovač push. Typ popisovače závisí na systému (například WNS používá identifikátory URI, zatímco APNS používá tokeny).
2. Klientská aplikace ukládá tento popisovač do back-endu nebo poskytovatele aplikace.
3. Pokud chcete odeslat nabízené oznámení, back-end aplikace kontaktuje PNS pomocí popisovače pro cílení na konkrétní klientskou aplikaci.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![Pracovní postup nabízených oznámení](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními

Systémy PNS jsou velmi výkonné. Přesto ale nechávají většinu práce na vývojáři aplikace, a to i při implementaci běžných scénářů nabízených oznámení, jako je vysílání nabízených oznámení nebo jejich odeslání určitému segmentu uživatelů.

Odesílání nabízených oznámení vyžaduje složitou infrastrukturu, která nesouvisí s hlavní obchodní logikou aplikace. Mezi problémy infrastruktury patří:

- **Závislost na platformě**
  - Back-end vyžaduje pro posílání oznámení do zařízení na různých platformách komplexní a pevně zachovávat platformu závislou na platformách, protože PNSes nejsou sjednocené.
- **Škálování**
  - Podle pokynů pro PNS je potřeba při každém spuštění aplikace aktualizovat tokeny zařízení. Back-end se zabývá velkým objemem provozu a přístup k databázím, aby bylo možné tokeny udržovat v aktuálním stavu. Když počet zařízení roste na stovky, tisíce nebo miliony, náklady na vytvoření a údržbu této infrastruktury jsou obrovské.
  - Většina systémů PNS nepodporuje vysílání na více zařízení. Jednoduché vysílání pro milion zařízení znamená milion volání systémů PNS. Škálovat tento provoz s minimální latencí není úplně jednoduché.
- **Směrování**
  - I když PNSes poskytuje způsob, jak odesílat zprávy do zařízení, většina oznámení aplikací je zaměřená na uživatele nebo zájmové skupiny. Back-end musí udržovat registr pro přidružení zařízení k zájmovým skupinám, uživatelům, vlastnostem atd. Tato režie se přičítá k času uvedení aplikace na trh a náklady na údržbu.

## <a name="why-use-azure-notification-hubs"></a>Proč používat Azure Notification Hubs?

Notification Hubs eliminují všechny složitosti spojené s odesíláním nabízených oznámení, která vlastníte z back-endu vaší aplikace. Platformově univerzální škálovatelná infrastruktura, určená k zasílání nabízených oznámení, zmenšuje potřebu programování oznámení a zjednodušuje back-end. Se službou Notification Hubs zodpovídají zařízení jenom za registraci popisovačů systému PNS v centru, zatímco back-end posílá zprávy uživatelům nebo zájmovým skupinám. Je to vidět na následujícím obrázku:

![Diagram centra oznámení](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs je připravený modul nabízených oznámení pro použití s následujícími výhodami:

- **Jsou pro různé platformy**
  - Podpora pro všechny hlavní nabízené platformy.
  - Nabízejí společné rozhraní pro zasílání oznámení všem platformám ve formátech, které jsou specifické pro danou platformu nebo jsou na ní nezávislé, a to bez práce spojené s určitou platformou.
  - Správa popisovačů zařízení se provádí na jednom místě.
- **Jsou pro různé back-endy**
  - V cloudu nebo v místním prostředí.
  - .NET, Node.js, Java, Python atd.
- **Bohatá sada schémat doručování**
  - Všesměrové vysílání na jednu nebo víc platforem: pomocí jediného volání rozhraní API se můžete okamžitě vysílat na miliony zařízení napříč platformami.
  - Zasílání zařízení: Oznámení mohou být určena i jednotlivým zařízením.
  - Vložení do uživatele: značky a šablony vám pomůžou kontaktovat všechna zařízení pro různé platformy pro uživatele.
  - Vložení do segmentu pomocí dynamických značek: funkce značek pomáhá segmentovat zařízení a nabízet je podle vašich potřeb, ať už posíláte do jednoho segmentu, nebo do výrazu segmentů (například aktivní a žijete v Seattlu není novým uživatelem). Místo omezení pro publikování a přihlášení k odběru můžete značky zařízení aktualizovat kdekoli a kdykoli.
  - Lokalizované vložení: funkce šablon pomáhá dosáhnout lokalizace, aniž by to ovlivnilo back-end kód.
  - Tiché nabízení: Vzorec zasílání a přijetí změn můžete aktivovat tím, že zařízením pošlete tichá oznámení, kterými aktivujete určité změny nebo akce.
  - Naplánovaná nabízená oznámení: můžete naplánovat, aby se oznámení odesílala kdykoli.
  - Přímé nabízení: Registraci zařízení ve službě Notification Hubs můžete přeskočit a oznámení přímo hromadně poslat seznamu popisovačů zařízení.
  - Individuální nabízená oznámení: proměnné nabízení zařízení vám pomůžou poslat přizpůsobená nabízená oznámení specifická pro konkrétní zařízení s přizpůsobenými páry klíč-hodnota.
- **Bohaté telemetrické funkce**
  - Všeobecná telemetrie, zařízení, chyba a provozní telemetrie jsou k dispozici v Azure Portal i programově.
  - Telemetrie na zprávy sleduje každou nabízenou žádost od počátečního volání požadavku do služby Notification Hubs úspěšně odeslala oznámení.
  - Systém oznámení platformy zpětná vazba oznamuje veškerou zpětnou vazbu od PNSes, která pomáhá při ladění.
- **Škálovatelnost**
  - Posílat rychlé zprávy milionům zařízení bez nutnosti opětovného navrhování nebo horizontálního dělení zařízení.
- **Zabezpečení**
  - K dispozici je sdílený tajný přístupový klíč (SAS) nebo federované ověření.

## <a name="next-steps"></a>Další kroky

Začínáme vytvářet a používat centrum oznámení podle [kurzu o zasílání nabízených oznámení mobilním aplikacím](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
