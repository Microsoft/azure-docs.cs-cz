---
title: 'Azure Notification Hubs: Nejčastější dotazy (FAQ) | Microsoft Docs'
description: Nejčastější dotazy na návrh nebo implementaci řešení na centra oznámení
services: notification-hubs
documentationcenter: mobile
author: dimazaid
manager: kpiteira
editor: spelluru
keywords: nabízená oznámení, nabízená oznámení, nabízená oznámení iOS, android nabízená oznámení, nabízené ios, android nabízené
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: fbdb52b1b2dee682021fd822d584551f41f723ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Nabízená oznámení pomocí Azure Notification Hubs: Nejčastější dotazy
## <a name="general"></a>Obecné
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Co je strukturu prostředků centra oznámení?

Služba Azure Notification Hubs má dvě úrovně prostředků: rozbočovače a obory názvů. Rozbočovač je jeden nabízené prostředek, k němuž mohou být uloženy různé platformy nabízené informace jednu aplikaci. Obor názvů je kolekce centra v jedné oblasti.

Doporučené mapování odpovídá jeden obor názvů s jednu aplikaci. V oboru názvů může mít produkční rozbočovače, který funguje s produkční aplikace, testování rozbočovače, který funguje s testování aplikace a tak dále.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Co je model ceny pro centra oznámení?
Podrobnosti o nejnovější cenách najdete na [ceny centra oznámení] stránky. Centra oznámení se fakturuje na úrovni oboru názvů. (Pro definici oboru názvů, najdete v části "Co je strukturu prostředků centra oznámení?") Centra oznámení nabízí tři úrovně:

* **Volné**: Tato úroveň je to dobrý výchozí bod pro zkoumání nabízených možností. Nedoporučuje pro produkční aplikace. Získejte 500 zařízení a 1 milion nabízených oznámení zahrnuté na obor názvů za měsíc, s žádná záruka, smlouvu o úrovni (SLA) služby.
* **Základní**: Tato úroveň (nebo na plán úrovně Standard) se doporučuje pro menší produkční aplikace. Získejte 200 000 zařízení a 10 milionů nabízených oznámení na obor názvů měsíčně jako základ zahrnut. Růst možnosti kvót, které jsou zahrnuty.
* **Standardní**: Tato úroveň se doporučuje ve středně velkých provozních aplikací. Získejte 10 milionů zařízení a 10 milionů nabízených oznámení na obor názvů měsíčně jako základ zahrnut. Kvóta zvýšení možnosti a bohaté telemetrie možnosti jsou zahrnuty.

Funkce úrovně Standard:
* **Bohatá telemetrie**: centra oznámení za zpráva Telemetrie můžete sledovat všechny žádosti o nabízenou a zpětnou vazbu systému oznámení platformy pro ladění.
* **Víceklientská**: pověření systému oznámení platformy můžete pracovat na úrovni oboru názvů. Tato možnost umožňuje snadno rozdělují klienty do centra v rámci stejného oboru názvů.
* **Naplánované nabízené**: můžete naplánovat oznámení k odeslání kdykoli.

### <a name="what-is-the-notification-hubs-sla"></a>Co je SLA centra oznámení?
Správně nakonfigurovaných aplikací pro úrovně Basic a Standard centra oznámení můžete odesílat nabízená oznámení nebo provádět operace správy registrace nejméně 99,9 % času. Další informace o smlouvě SLA, přejděte na [SLA centra oznámení](https://azure.microsoft.com/support/legal/sla/notification-hubs/) stránky.

> [!NOTE]
> Protože nabízená oznámení záviset na jiných výrobců systémy oznámení platforem (například Apple APNS a Google FCM), neexistuje žádná záruka SLA pro doručení těchto zpráv. Po Notification Hubs odesílá na listy systémy oznámení platforem (SLA zaručit), je zodpovědností systémy oznámení platforem pro doručování nabízených oznámení (bez SLA zaručit).

### <a name="which-customers-are-using-notification-hubs"></a>Které zákazníci používají Notification Hubs?
Mnoho zákazníků používat Notification Hubs. Jsou zde uvedeny některé významné ty, které jsou:

* Sochi 2014: Stovky skupinám, 3 + miliony zařízení a 150 + milionů oznámení odeslaných za dva týdny. [Případová studie: Sochi]
* Skanska: [Případová studie: Skanska]
* Časy Praha: [Případová studie: časy Praha]
* Mural.ly: [Případová studie: Mural.ly]
* 7Digital: [Případová studie: 7Digital]
* Aplikace Bing: Desítkami milionů zařízení odesílat oznámení 3 miliony za den.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak upgradovat nebo starší verzi Moje rozbočovače nebo obor názvů k jiné vrstvě?
Přejděte na  **[portál Azure]** > **obory názvů centra oznámení** nebo **Notification Hubs**. Vyberte prostředek, který chcete aktualizovat a přejděte na **cenová úroveň**. Vezměte na vědomí následující požadavky:

* Aktualizované cenové úrovně se vztahuje na *všechny* centra v oboru názvů, kterými pracujete.
* Pokud zařízení počet překračuje limit vrstvy, které jste Downgrade k, budete muset odstranit zařízení, než jste starší verzi.


## <a name="design-and-development"></a>Návrh a vývoj
### <a name="which-server-side-platforms-do-you-support"></a>Serverové platformy, na kterých podporujete?
Server SDK jsou k dispozici pro rozhraní .NET, Java, Node.js, PHP a Python. Rozhraní API centra oznámení jsou založené na rozhraní REST, abyste mohli pracovat přímo s rozhraní REST API, pokud používáte jiné platformy nebo nechcete, aby další závislosti. Další informace najdete na [rozhraní API REST centra oznámení] stránky.

### <a name="which-client-platforms-do-you-support"></a>Které klientské platformy podporujete?
Nabízená oznámení jsou podporovány pro [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [univerzální pro Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Číny (prostřednictvím Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) a [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [aplikace pro Chrome](notification-hubs-chrome-push-notifications-get-started.md), a [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Další informace naleznete [kurzy Notification Hubs Začínáme] stránky.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Podporujete textová zpráva, e-mailu nebo webové oznámení?
Centra oznámení je primárně určený k odesílání oznámení do mobilní aplikace. Neposkytuje e-mailu nebo textu zprávy možnosti. Ale platformy třetí strany, které nabízí tyto možnosti můžete integrovat centra oznámení k odesílání nativní nabízená oznámení pomocí [Mobile Apps].

Centra oznámení také neposkytuje služby v prohlížeči nabízená oznámení doručení mimo pole. Zákazníci můžete implementovat tuto funkci pomocí SignalR na podporovaných platformách straně serveru. Pokud chcete posílat oznámení do aplikací prohlížeče v izolovaném prostoru Chrome, najdete v článku [aplikace pro Chrome kurzu].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Jak jsou mobilní aplikace a související s Azure Notification Hubs a kdy je použít?
Pokud máte stávající mobilní aplikace zpět end a chcete přidat pouze možnost odesílat nabízená oznámení, můžete používat Azure Notification Hubs. Pokud chcete nastavit své mobilní aplikace zpět end od začátku, zvažte použití funkce Mobile Apps služby Azure App Service. Mobilní aplikace automaticky zřídí centra oznámení, takže můžete snadno odesílat nabízená oznámení z back-end mobilní aplikace. Ceny pro Mobile Apps obsahuje základní poplatky za centra oznámení. Platí pouze při překročení zahrnuté nabízených oznámení. Další informace o náklady, přejděte na [App Service – ceny] stránky.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Kolik zařízení může podporovat Pokud odesílat nabízená oznámení prostřednictvím centra oznámení?
Odkazovat [ceny centra oznámení] stránku Podrobnosti o počtu podporovaných zařízení.

Pokud potřebujete podporu pro více než 10 milionů registrovaná zařízení, [kontaktujte nás](https://azure.microsoft.com/overview/contact-us/) přímo jsme vám a škálovat vaše řešení.

### <a name="how-many-push-notifications-can-i-send-out"></a>Kolik nabízená oznámení můžete I poslat?
V závislosti na vybraná úroveň Azure Notification Hubs automatické škálování podle počtu oznámení odesílaných prostřednictvím systému.

> [!NOTE]
> Náklady na celkové využití může zvýšit na základě počtu nabízených oznámení, které jsou obsluhovány. Ujistěte se, že jste vědět vrstvy omezení uvedených na [ceny centra oznámení] stránky.
> 
> 

Naše zákazníky používat Notification Hubs k odesílání milióny nabízených oznámení denně. Nemáte žádnou zvláštní škálování reach nabízených oznámení, dokud používáte Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak dlouho trvá pro odeslat nabízená oznámení k dosažení Moje zařízení?
Ve scénáři použití normální, kde se příchozí zátěží je konzistentní a i, Azure Notification Hubs může zpracovat alespoň *1 milion nabízených oznámení odešle minutu*. Tato míra se můžou lišit v závislosti na počtu značek, povahu příchozí zasílá a jiných vnějších faktorů.

Během doby odhadované doručení služby vypočítá cíle na každou platformu a směrování zpráv do nabízená oznámení služby (PNS) na základě registrované značky nebo značky výrazy. Je zodpovědností systém PNS k odesílání oznámení do zařízení.

Systém PNS nezaručuje všechny smlouvy SLA pro doručování oznámení. Ale většina nabízená oznámení se doručují na cílová zařízení během několika minut (obvykle do 10 minut) od okamžiku, kdy jsou odeslány do centra oznámení. Několik oznámení může trvat delší dobu.

> [!NOTE]
> Centra oznámení Azure má zavedeny zásady vyřaďte všechny nabízená oznámení, které nejsou doručeny pro systém PNS do 30 minut. Toto zpoždění může nastat z mnoha důvodů, ale většina běžně, protože systém PNS je omezování vaší aplikace.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Je k dispozici žádné záruku latence?
Vzhledem k povaze nabízených oznámení (doručení podle externí, specifické pro platformu PNS) neexistuje žádná záruka latence. Obvykle se většina nabízená oznámení se doručují během několika minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co je potřeba vzít v úvahu při navrhování řešení s obory názvů a notification hubs?
#### <a name="mobile-appenvironment"></a>Mobilní aplikace nebo prostředí
* Použijte jeden centra oznámení na mobilní aplikaci na prostředí.
* Ve scénáři víceklientské každého klienta by měl mít samostatné rozbočovače.
* Nikdy sdílet stejnou centra oznámení pro produkční a testovací prostředí. Tento postup může způsobit problémy při odesílání oznámení. (Apple nabízí izolovaného prostoru a produkční Push koncových bodů, každý s samostatné přihlašovací údaje).
* Ve výchozím nastavení můžete poslat zkušební oznámení na vaše zaregistrovaných zařízení prostřednictvím portálu Azure nebo Azure integrované komponenty v sadě Visual Studio. Prahová hodnota je nastavena na 10 zařízení, které jsou vybrány v náhodných z fondu registrace.

> [!NOTE]
> Pokud vaše Centrum byl původně nakonfigurovaný pomocí certifikátu Apple izolovaného prostoru a poté byla nakonfigurována na používání produkční certifikát služby Apple, původní tokenů zařízení jsou neplatné. Neplatný tokeny způsobit nabízených oznámení k selhání. Oddělené produkčním i testovacím prostředí a použití různých centra pro různá prostředí.
> 
> 

#### <a name="pns-credentials"></a>Systém PNS pověření
Pokud mobilní aplikace není zaregistrována portál pro vývojáře na platformě (například Apple nebo Google), budou odeslány tokeny zabezpečení a identifikátor aplikace. Back-end aplikace poskytuje tyto tokeny pro systém PNS platformy, takže nelze odesílat nabízená oznámení do zařízení. Tokeny zabezpečení, může být ve tvaru certifikáty (například Apple iOS nebo Windows Phone) nebo klíče zabezpečení (například Google Android nebo Windows). Musí být nakonfigurované v centra oznámení. Konfigurace se obvykle provádí na úrovni centra oznámení, ale je možné ji provést na úrovni oboru názvů ve víceklientském scénáři.

#### <a name="namespaces"></a>Názvové prostory
Obory názvů lze použít pro nasazení seskupení. Můžete také používají k reprezentaci všech centra oznámení pro všechny klienty ve scénáři víceklientské stejné aplikaci.

#### <a name="geo-distribution"></a>GEO rozdělení.
Geograficky distribuční není vždy důležité ve scénářích nabízená oznámení. Různé PNSes (například služby APN nebo GCM), které doručovat nabízená oznámení do zařízení nejsou rovnoměrně.

Pokud máte aplikaci, která se používá globálně, můžete vytvořit centra v různých oborech názvů pomocí služby Notification Hubs v různých oblastech Azure po celém světě.

> [!NOTE]
> Toto uspořádání nedoporučujeme, protože jeho hodnota se zvyšuje vaše náklady na správu, zejména pro registrace. By mělo být provedeno pouze v případě, že existuje explicitní potřeba.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Je třeba udělat registrace z back-end aplikace nebo přímo prostřednictvím klienta zařízení?
Registrace z back-end aplikace jsou užitečné v případě, že máte k ověřování klientů před vytvořením registrace. Jsou užitečná také když máte značky, které musí být vytvořen nebo upravovat aplikace back-end na základě logiky aplikace. Další informace najdete v tématu [back-end registrace pokyny] a [back-end registrace pokyny 2] stránky.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co je modelu zabezpečení doručování nabízených oznámení?
Používá služba Azure Notification Hubs [sdílený přístupový podpis](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-model zabezpečení založený na. Tokeny podpis sdíleného přístupu můžete použít na kořenové úrovni oboru názvů nebo na úrovni centra podrobné oznámení. Sdílený přístupový podpis tokeny můžete nastavit, třeba postupovat podle různých autorizační pravidla, pro odesílání zpráv oprávnění nebo naslouchat oznámení oprávnění. Další informace najdete v tématu [model zabezpečení Notification Hubs] dokumentu.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak by měly zpracovávat citlivé datové části v nabízená oznámení?
Všechna oznámení jsou doručit na cílová zařízení podle PNS platformu. Při odesílání oznámení do Azure Notification Hubs je zpracovat a předaný příslušných systém PNS.

Všechna připojení, od odesílatele do centra oznámení Azure pro systém PNS, použijte protokol HTTPS.

> [!NOTE]
> Služba Azure Notification Hubs neprotokoluje žádným způsobem datovou část zprávy.
> 
> 

Pokud chcete odeslat citlivé datových částí, doporučujeme pomocí vzoru zabezpečení Push. Odesílatel přináší ping oznámení s identifikátor zprávy do zařízení bez citlivé datové části. Když aplikace na zařízení obdrží datové části, aplikace volá zabezpečení rozhraní API přímo k načtení podrobnostmi o zprávě. Informace o tom, jak implementovat tento vzor, přejděte na [kurzu centra oznámení zabezpečené Push] stránky.

## <a name="operations"></a>Operace
### <a name="what-support-is-provided-for-disaster-recovery"></a>Jaké podpora je k dispozici pro zotavení po havárii?
Poskytujeme pokrytí obnovení po havárii metadata na naší straně (název centra oznámení, připojovací řetězec a další důležité informace). Když se aktivuje na scénář zotavení po havárii je registrační data *pouze segmentovat* infrastruktury centra oznámení, které dojde ke ztrátě. Je nutné implementovat řešení znovu naplnit tato data do nové po obnovení centra:

1. Vytvoření centra oznámení sekundární v jiném datovém centru. Doporučujeme vytvořit od začátku, aby vás chrání před událostí obnovení po havárii, které by mohly ovlivnit možnosti správy. Můžete také vytvořit jeden v době události obnovení po havárii.

2. Naplnění sekundární oznámení rozbočovač se registrace z centra primární oznámení. Nedoporučujeme pokusu Udržovat registrace na obou rozbočovače a jejich synchronizace jako mají registrace. Tento postup nefunguje správně kvůli vyplývajících tendence registrace vyprší na straně systém PNS. Centra oznámení vyčistí je jako obdrží systém PNS názor registrace vypršela platnost, nebo neplatný.  

Máme dvě doporučení pro back-EndY aplikace:

* Použijte back end aplikace, která udržuje danou sadu registrací v jeho koncem. Příkaz bulk insert se pak můžete provádět do centra oznámení sekundární.

* Použijte aplikaci back-end, který získá výpis regulární registrací z centra oznámení primární jako záložní. Příkaz bulk insert se pak můžete provádět do centra oznámení sekundární.

> [!NOTE]
> Funkce exportu/importu registrace je k dispozici ve standardní vrstvě je podrobněji popsaná [registrace exportu/importu] dokumentu.
> 
> 

Pokud nemáte back-end, když se aplikace spustí na cílovém zařízení, fungují v centru oznámení sekundární nové registrace. Nakonec centra sekundární oznámení bude mít aktivních zařízení registrované.

Bude časové období, kdy zařízení s vyberte aplikace nebude přijímat oznámení.

### <a name="is-there-audit-log-capability"></a>Je k dispozici funkce protokolu auditování?
Všechny operace správy Notification Hubs přejděte na protokoly operací, které jsou přístupné [portál Azure].

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží
### <a name="what-troubleshooting-capabilities-are-available"></a>Jaké možnosti pro odstraňování potíží jsou k dispozici?
Služba Azure Notification Hubs poskytuje několik funkcí pro řešení potíží, zejména pro nejčastější scénáře vynechaných oznámení. Podrobnosti najdete v tématu [řešení potíží s Notification Hubs] dokumentu white paper.

### <a name="what-telemetry-features-are-available"></a>Jaké funkce telemetrická data jsou k dispozici?
Zobrazení telemetrická data v Azure Notification Hubs umožňuje [portál Azure]. Podrobnosti o podle metrik, které jsou k dispozici na [metriky centra oznámení] stránky.

> [!NOTE]
> Úspěšné oznámení jednoduše znamená, že externí systém PNS (například APNS pro Apple) nebo GCM pro Google byly dodány nabízená oznámení. Je zodpovědností systém oznámení platformy pro doručení oznámení do cílových zařízení. Systém PNS obvykle nevystavuje doručení metriky třetím stranám.  
> 
> 

Poskytujeme také schopnost exportovat telemetrická data prostřednictvím kódu programu (na vrstvě Standard). Podrobnosti najdete v tématu [metriky centra oznámení ukázka].

[portál Azure]: https://portal.azure.com
[ceny centra oznámení]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Případová studie: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Případová studie: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Případová studie: Časy Praha]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Případová studie: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Případová studie: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[rozhraní API REST centra oznámení]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[kurzy Notification Hubs Začínáme]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[aplikace pro Chrome kurzu]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[back-end registrace pokyny]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[back-end registrace pokyny 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[model zabezpečení Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[kurzu centra oznámení zabezpečené Push]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[řešení potíží s Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[metriky centra oznámení]: https://msdn.microsoft.com/library/dn458822.aspx
[metriky centra oznámení ukázka]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[registrace exportu/importu]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service – ceny]: https://azure.microsoft.com/pricing/details/app-service/
