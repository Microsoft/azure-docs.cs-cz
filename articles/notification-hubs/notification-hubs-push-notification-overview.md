---
title: Azure Notification Hubs
description: Zjistěte, jak pro přidání možností nabízená oznámení pomocí Azure Notification Hubs.
author: ysxu
manager: erikre
editor: ''
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: ea8d569a8d51afee352860ab60a1fe311496b83c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-notification-hubs"></a>Azure Notification Hubs
## <a name="overview"></a>Přehled
Azure Notification Hubs poskytuje modul snadné použití, více platformami, upraveným push. Pomocí volání API jedné platformě můžete snadno odesílat cílové a přizpůsobené nabízená oznámení na libovolnou mobilní platformu z jakékoli back-end cloudu nebo místně.

Centra oznámení dobře funguje pro scénáře enterprise a příjemce. Zde je několik příkladů, které zákazníci používají Notification Hubs pro:

* Odešlete oznámení o aktuálních zprávách milionům příjemcům s nízkou latencí.
* Odeslat kupónů segmenty chtějí uživatele.
* Související události oznámení odešlete uživatelům nebo skupinám pro média nebo sportu nebo finanční nebo herní aplikace.
* Nabízená propagační obsah aplikace zaujmout a zákazníkům na trh.
* Upozorní uživatele na firemní události, jako nové zprávy a pracovní položky.
* Odešlete kódy pro službu Multi-Factor authentication.

## <a name="what-are-push-notifications"></a>Co jsou nabízená oznámení?
Nabízená oznámení je formulář aplikace uživatele komunikace, kde jsou uživatelé z mobilních aplikací informováni o některé požadované informace, obvykle v místní nabídce nebo v dialogovém okně. Uživatelé mohou obecně k zobrazení nebo zavřete zprávu, a výběr první otevře mobilní aplikaci, která měla oznamovat oznámení.

Nabízená oznámení je důležité pro uživatelských aplikací v zvýšení aktivity a využití a pro podnikové aplikace při komunikaci aktuální obchodní údaje. Protože je energetickou efektivitu pro mobilní zařízení, flexibilní odesílatelům oznámení a k dispozici při odpovídající aplikace nejsou aktivní je komunikace nejlépe aplikace uživatele.

Další informace o nabízená oznámení pro několik oblíbených platformy:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak nabízená oznámení fungují
Nabízená oznámení se doručují prostřednictvím infrastruktur specifických pro platformy označují jako *systémy oznámení platforem* (PNSes). Nabízejí funkce nabízené barebone k doručení zprávy do zařízení s zadané zpracování a nemá žádné společné rozhraní. Verze aplikace, vývojář musí k odesílání oznámení pro všechny zákazníky v iOS, Android a Windows pracovat s APNS (Apple Push Notification Service), FCM (Firebase Cloud Messaging) a WNS (Windows Notification Service), při dávkování odešle.

Na vysoké úrovni zde je, jak funguje nabízené:

1. Klientská aplikace rozhodne chce dostávat oznámení proto kontaktuje systém PNS odpovídající k načtení svého popisovače jedinečný a dočasný push. Typ popisovače závisí na systému (například WNS má identifikátory URI, zatímco APNS obsahuje tokenů).
2. Klientská aplikace si tento popisovač uloží v back-end aplikace nebo zprostředkovatele.
3. Pokud chcete zasílat nabízená oznámení, back-end aplikace kontaktuje systém PNS s použitím popisovače, který cílí na konkrétního klienta aplikace.
4. Systém PNS předá oznámení do zařízení určeného popisovačem.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Obtíže spojené s nabízenými oznámeními
PNSes jsou efektivní, opustí množství práce na vývojáři aplikace kvůli implementaci i běžné scénáře nabízená oznámení, jako je například vysílání nebo odesílání nabízených oznámení segmentovaným uživatelům.

Nabízená je jednou z nejžádanějších funkcí v mobilních cloudové služby, protože jeho pracovní vyžaduje komplexní infrastruktury, které se nevztahují na hlavní obchodní logice aplikace. Některé z problémů infrastruktury jsou:

* **Závislost na platformě**: 

  * Back-end musí mít komplexní a pevné udržovat závislé na platformě logiku k odesílání oznámení do zařízení na různých platformách, jako nejsou unified PNSes.
* **Škálování**:

  * Podle pokynů pro systém PNS zařízení tokeny musí aktualizovat při každé spuštění aplikace. To znamená, že back-end se zabývají velký objem přenosů a databáze přístup jenom k zachování aktualizovaného stavu tokenů. Když počet zařízení naroste a stovky tisíc milionů, je masivní náklady na vytvoření a údržbu této infrastruktury.
  * Většina PNSes nepodporuje vysílání na více zařízení. To znamená jednoduchého vysílání miliony zařízení výsledkem volání milion PNSes. Toto množství provozu škálování s minimální latence není triviální.
* **Směrování**:
  
  * I když PNSes poskytnout způsob, jak posílat zprávy do zařízení, většina oznámení aplikace se budou zaměřovat na uživatelům nebo skupinám. To znamená, že back-end musí udržovat registr pro přidružení zařízení zájmových skupin, vlastností, uživatelů atd. Tato dodatečná režie prodlužuje čas potřebný k náklady na trh a údržbu aplikace.

## <a name="why-use-notification-hubs"></a>Proč používat Notification Hubs?
Notification Hubs eliminuje složité všechny kroky přidružené k povolení oznámení vlastní. Jeho infrastrukturu pro více platformami a horizontálně škálovanou nabízená oznámení snižuje související nabízené kódy a zjednodušuje váš back-end. Zařízení s Notification Hubs, jsou jenom za registraci jejich popisovačů systému PNS s rozbočovačem, zatímco back-end odešle zprávy do uživatelům nebo skupinám, jak je znázorněno na následujícím obrázku:

![][1]

Centra oznámení je připravené k použití nabízené modul má následující výhody:

* **Různé platformy**

  * Podpora pro všechny hlavní nabízené platformy včetně iOS, Android, Windows a Kindle a Baidu.
  * Společné rozhraní k replikaci na všech platformách ve formátech specifické pro platformu a nezávislé na platformě žádná práce specifické pro platformu.
  * Zařízení zpracovávat správu na jednom místě.
* **Mezi back-EndY**
  
  * Cloudu nebo místně
  * Rozhraní .NET, Node.js, Java, atd.
* **Bohatá sada schémat doručování**:

  * *Všesměrové vysílání pro jednu nebo více platforem*: můžete můžete okamžitě všesměrového vysílání na miliony zařízení napříč platformami pomocí jednoho volání rozhraní API.
  * *Vložit zařízení*: oznámení pro jednotlivá zařízení, můžete vybrat.
  * *Oznámení uživateli*: funkce značky a šablony můžete dosáhnout všechna zařízení a platformy uživatele.
  * *Nabízená instalace segment s dynamické značky*: funkce značky vám pomůže segment zařízení a nabízené podle svých potřeb, zda jsou odesílání do jednoho segmentu nebo výraz segmentů (například active život o AND v Praze není nového uživatele). Místo je omezena na protokol pub-sub, můžete aktualizovat zařízení značky kdekoliv a kdykoliv.
  * *Lokalizované nabízené*: funkce šablony vám pomůže dosáhnout lokalizace bez ovlivnění back-end kód.
  * *Tichou nabízené*: vzoru nabízené pro vyžádání obsahu můžete umožňuje odesílání tichou oznámení do zařízení a jejich dokončení určité si nebo akce, která aktivuje.
  * *Naplánované nabízené*: můžete naplánovat kdykoli odeslat oznámení.
  * *Přímé nabízené*: můžete přeskočit registraci zařízení v naší službě a přímo dávky nabízené seznam popisovačů zařízení.
  * *Přizpůsobené nabízené*: zařízení nabízené proměnné pomáhá odeslat konkrétní zařízení přizpůsobené nabízená oznámení pomocí vlastní páry klíč hodnota.
* **Bohatá telemetrie**
  
  * Obecné nabízená oznámení, zařízení, chyby a operaci telemetrie je k dispozici na webu Azure portal a programově.
  * Za zpráva Telemetrie sleduje každý nabízené z úvodního požadavku volání naši službu nabízených oznámení se úspěšně dávkování.
  * Zpětná vazba systému oznámení platformy komunikuje všechny zpětnou vazbu ze systémů oznámení čip jako pomůcku při ladění.
* **Škálovatelnost** 
  
  * Odeslání rychlé zprávy na miliony zařízení bez horizontálního dělení předělávání architektury nebo zařízení.
* **Zabezpečení**

  * Sdílený tajný klíč přístupového (SAS) nebo federovaného ověřování.

## <a name="integration-with-app-service-mobile-apps"></a>Integrace s App Service Mobile Apps
Pro zajištění plynulého a sjednocujícího prostředí napříč službami Azure nabízí [App Service Mobile Apps] integrovanou podporu pro nabízená oznámení prostřednictvím služby Notification Hubs. Služba [App Service Mobile Apps] nabízí vysoce škálovatelnou a globálně dostupnou platformu pro vývoj mobilních aplikací určenou pro vývojáře a integrátory systémů ve velkých firmách. Přináší bohatou sadu funkcí pro vývojáře pro mobilní zařízení.

Vývojáři v Mobile Apps mohou službu Notification Hubs využívat v rámci následujícího pracovního postupu:

1. Načtení popisovače systému PNS zařízení
2. Registrace zařízení s Notification Hubs prostřednictvím vhodné API registraci Mobile Apps Client SDK
   * Mějte na paměti, že služba Mobile Apps odstraní z bezpečnostních důvodů při registraci všechny značky. Služba Notification Hubs vám umožní přiřadit značky k zařízením přímo z back-endu.
3. Odesílání oznámení z back-endu aplikace pomocí Notification Hubs

Zde jsou některé výhody, které vývojáři získají díky této integraci:

* **Mobilní aplikace klientské sady SDK**: tyto multiplatformní sady SDK poskytují jednoduchá rozhraní API pro registraci a komunikují s centrem oznámení propojeným s mobilní aplikace automaticky. Vývojáři se nemusí zabývat přihlašovacími údaji pro Notification Hubs a pracovat s další službou.

  * *Oznámení uživateli*: SDK automaticky označí dané zařízení s Mobile Apps ověřené ID uživatele pro povolení nabízené uživatelský scénář.
  * *Vložit zařízení*: SDK automaticky používají instalační ID Mobile Apps jako identifikátor GUID pro registraci v Notification Hubs, což vývojáře potíže Správa více identifikátorů GUID služby.
* **Instalační model**: Mobile Apps pracuje s nejnovějším modelem nabízených oznámení centra oznámení představují všechny vlastnosti nabízených oznámení související se zařízením v instalaci JSON, který zarovnaná s služeb nabízených oznámení a je snadno použitelný.
* **Flexibilita**: vývojáři se vždy mohou rozhodnout pracovat přímo s Notification Hubs i s integrací na místě.
* **Integrované prostředí na [portál Azure]**: oznámení v Mobile Apps vizuálně reprezentována funkce a vývojáři mohou snadno pracovat s přidruženým centrem oznámení přes Mobile Apps.

## <a name="next-steps"></a>Další kroky
Další informace o Notification Hubs naleznete v těchto tématech:

* **[Jak zákazníci používají Notification Hubs]**
* **[Kurzy a příručky k Notification Hubs]**
* **Kurzy Začínáme centra oznámení**: [iOS], [Android], [univerzální pro Windows], [Windows Phone], [ Kindle], [Xamarin.iOS], [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Jak zákazníci používají Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
[Kurzy a příručky k Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[univerzální pro Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[ Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[portál Azure]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
