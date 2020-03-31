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
ms.openlocfilehash: f5b59d1669d89c73b93199d1f833da149003d399
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159332"
---
# <a name="what-is-azure-notification-hubs"></a>Co je Azure Notification Hubs?

Azure Notification Hubs poskytují snadno použitelný a škálovatelné push engine, který umožňuje odesílat oznámení na libovolnou platformu (iOS, Android, Windows atd.) z libovolného back-endu (cloud nebo místní). Notification Hubs funguje jak ve scénářích velkých organizací, tak ve spotřebitelských scénářích. Příklady scénářů:

- Odesílání oznámení o důležitých zprávách milionům uživatelů s minimálním zpožděním.
- Odesílání kupónů závislých na aktuální poloze segmentům zájemců.
- Odesílání oznámení o událostech uživatelům multimediálních, sportovních, finančních nebo herních aplikací nebo jejich skupinám.
- Zasílání propagačního obsahu aplikacím kvůli informovanosti zákazníků a podpoře nabídky.
- Upozorněte uživatele na události organizace, jako jsou nové zprávy a pracovní položky.
- Odesílání kódů k vícefaktorovému ověřování.

## <a name="what-are-push-notifications"></a>Co jsou nabízená oznámení?

Nabízená oznámení jsou formou komunikace mezi aplikacemi a uživateli, kdy jsou uživatelé mobilních aplikací upozorněni na určité požadované informace, obvykle v automaticky otevíraném okně nebo dialogovém okně na mobilním zařízení. Uživatelé se obvykle rozhodnou zprávu zobrazit nebo zavřít. výběrem první otevře mobilní aplikace, která sdělila oznámení. Některá oznámení jsou tichá – doručená na pozadí, aby se aplikace zpracovala a rozhodla, co má dělat.

Nabízená oznámení jsou nedílnou součástí spotřebitelských aplikací, protože zvyšují zapojení a využití aplikace, ale jsou důležitá i v podnikových aplikacích, kde slouží ke zveřejňování aktuálních obchodních informací. Je to nejlepší komunikace mezi aplikacemi, protože je energeticky efektivní pro mobilní zařízení, flexibilní pro odesílatele oznámení a je dostupná, když odpovídající aplikace nejsou aktivní.

> [!NOTE]
> Azure Notification Hubs oficiálně nepodporuje nabízená oznámení voice over internetprotocol (VOIP). vtomto článku však [popisuje, jak můžete použít oznámení APNS VOIP](voip-apns.md) prostřednictvím centra oznámení Azure.

Další informace o nabízených oznámeních pro některé oblíbené platformy najdete v následujících tématech:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Jak fungují nabízená oznámení?

Nabízená oznámení se doručují prostřednictvím infrastruktur odpovídajících určitým platformám. Tyto infrastruktury se označují jako *systémy oznámení platforem* (PNS). Nabízejí základní funkce nabízení k doručení zprávy do zařízení s poskytnutým popisovačem a nemají žádné společné rozhraní. Aby vývojář mohl odeslat oznámení všem zákazníkům ve verzích aplikace pro Android, iOS a Windows, musí pracovat samostatně se službou Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) a službou Windows Notification Service (WNS).

Celkový přehled fungování nabízených oznámení:

1. Aplikace chce přijímat oznámení, takže kontaktuje PNS pro cílovou platformu, na které je aplikace spuštěna, a požaduje jedinečný a dočasný popisovač push. Typ popisovače závisí na systému (například WNS používá identifikátory URI, zatímco APNS používá tokeny).
2. Klientská aplikace ukládá tento popisovač v back-endu aplikace nebo zprostředkovatele.
3. Chcete-li odeslat nabízené oznámení, back-end aplikace kontaktuje PNS pomocí popisovače k cílení na konkrétní klientskou aplikaci.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![Pracovní postup nabízených oznámení](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními

Systémy PNS jsou velmi výkonné. Přesto ale nechávají většinu práce na vývojáři aplikace, a to i při implementaci běžných scénářů nabízených oznámení, jako je vysílání nabízených oznámení nebo jejich odeslání určitému segmentu uživatelů.

Odesílání nabízených oznámení vyžaduje komplexní infrastrukturu, která nesouvisí s hlavní obchodní logikou aplikace. Některé z problémů v oblasti infrastruktury jsou:

- **Závislost na platformě**
  - Back-end vyžaduje komplexní a těžko udržovatelné logiky závislé na platformě odesílat oznámení do zařízení na různých platformách, jako PNSes nejsou jednotné.
- **Měřítko**
  - Podle pokynů PNS musí být tokeny zařízení aktualizovány při každém spuštění aplikace. Back-end se zabývá velkým množstvím provozu a přístupu k databázi jen proto, aby tokeny byly aktuální. Když se počet zařízení zvyšuje na stovky, tisíce nebo miliony, náklady na vytvoření a údržbu této infrastruktury jsou obrovské.
  - Většina systémů PNS nepodporuje vysílání na více zařízení. Jednoduché vysílání pro milion zařízení znamená milion volání systémů PNS. Škálovat tento provoz s minimální latencí není úplně jednoduché.
- **Směrování**
  - Přestože PNSes poskytují způsob, jak odesílat zprávy do zařízení, většina oznámení aplikací jsou zaměřeny na uživatele nebo zájmových skupin. Back-end musí udržovat registr pro přidružení zařízení k zájmovým skupinám, uživatelům, vlastnostem atd. Tato režie zvyšuje čas na trhu a náklady na údržbu aplikace.

## <a name="why-use-azure-notification-hubs"></a>Proč používat Azure Notification Hubs?

Centra oznámení eliminují všechny složitosti spojené s odesíláním nabízených oznámení z back-endu aplikace. Platformově univerzální škálovatelná infrastruktura, určená k zasílání nabízených oznámení, zmenšuje potřebu programování oznámení a zjednodušuje back-end. Se službou Notification Hubs zodpovídají zařízení jenom za registraci popisovačů systému PNS v centru, zatímco back-end posílá zprávy uživatelům nebo zájmovým skupinám. Je to vidět na následujícím obrázku:

![Diagram centra oznámení](./media/notification-hubs-overview/notification-hub-diagram.png)

Notification Hubs je váš připravený tlačný modul s následujícími výhodami:

- **Jsou pro různé platformy**
  - Podpora pro všechny hlavní push platformy.
  - Nabízejí společné rozhraní pro zasílání oznámení všem platformám ve formátech, které jsou specifické pro danou platformu nebo jsou na ní nezávislé, a to bez práce spojené s určitou platformou.
  - Správa popisovačů zařízení se provádí na jednom místě.
- **Jsou pro různé back-endy**
  - Cloud nebo místní.
  - .NET, Node.js, Java, Python atd.
- **Bohatá sada schémat doručování**
  - Vysílání na jednu nebo více platforem: Můžete okamžitě vysílat do milionů zařízení napříč platformami s jedním voláním rozhraní API.
  - Zasílání zařízení: Oznámení mohou být určena i jednotlivým zařízením.
  - Nabízeno pro uživatele: Značky a šablony vám pomohou oslovit všechna zařízení pro různé platformy pro uživatele.
  - Nabízený segment s dynamickými značkami: Funkce značky vám pomůže segmentovat zařízení a tlačit na ně podle vašich potřeb, ať už odesíláte do jednoho segmentu nebo výraz segmentů (například aktivní a žije v Seattlu není nový uživatel). Místo toho, abyste byli omezeni na publikování a přihlášení k odběru, můžete aktualizovat značky zařízení kdekoli a kdykoli.
  - Lokalizované nabízená: Funkce šablony pomáhá dosáhnout lokalizace bez ovlivnění back-endového kódu.
  - Tiché nabízení: Vzorec zasílání a přijetí změn můžete aktivovat tím, že zařízením pošlete tichá oznámení, kterými aktivujete určité změny nebo akce.
  - Plánované nabízení: Oznámení můžete naplánovat tak, aby byla odeslána kdykoli.
  - Přímé nabízení: Registraci zařízení ve službě Notification Hubs můžete přeskočit a oznámení přímo hromadně poslat seznamu popisovačů zařízení.
  - Personalizované nabízení: Proměnné nabízení zařízení vám pomohou odesílat přizpůsobené nabízená oznámení specifická pro zařízení s přizpůsobenými páry klíč-hodnota.
- **Bohaté telemetrické funkce**
  - Obecné nabízení, zařízení, chyba a telemetrie operace jsou k dispozici na webu Azure Portal i programově.
  - Telemetrie pro jednotlivé zprávy sleduje každý nabízený text z vašeho počátečního volání požadavku na službu Notification Hubs, která úspěšně odesílá nabízená oznámení.
  - Zpětná vazba systému oznámení platformy sděluje veškerou zpětnou vazbu od pnses pomoci při ladění.
- **Škálovatelnost**
  - Posílejte rychlé zprávy milionům zařízení bez nutnosti re-architecting nebo zařízení střepů.
- **Zabezpečení**
  - K dispozici je sdílený tajný přístupový klíč (SAS) nebo federované ověření.

## <a name="next-steps"></a>Další kroky

Začínáme vytvářet a používat centrum oznámení podle [kurzu o zasílání nabízených oznámení mobilním aplikacím](notification-hubs-android-push-notification-google-fcm-get-started.md).

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
