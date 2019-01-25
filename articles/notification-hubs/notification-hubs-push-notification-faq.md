---
title: 'Azure Notification Hubs: Nejčastější dotazy (FAQ) | Dokumentace Microsoftu'
description: Nejčastější dotazy o návrhu a implementaci řešení v Notification Hubs
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: nabízené oznámení, nabízená oznámení, nabízená oznámení iOS, android nabízená oznámení, ios nabízených oznámení, nabízená oznámení androidu
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2019
ms.author: jowargo
ms.openlocfilehash: 7e31f4f229e722d51f5de6b133e70dfb1c013533
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888154"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Nabízená oznámení pomocí Azure Notification Hubs: Nejčastější dotazy

## <a name="general"></a>Obecné

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Co je struktura prostředků služby Notification hubs?

Azure Notification Hubs má dvě úrovně prostředků: rozbočovače a obory názvů. Centrum je jeden nabízených prostředek, který může obsahovat informace o nabízených oznámení napříč platformami jedné aplikace. Obor názvů je kolekce centra v jedné oblasti.

Doporučené mapování odpovídá jeden obor názvů s jednou aplikací. V rámci oboru názvů může mít rozbočovači produkčního prostředí, který spolupracuje s vašimi aplikacemi v produkčním prostředí, Centrum testování, která funguje s vašimi testování aplikace a tak dále.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Co je cenový model pro Notification Hubs?

Podrobnosti o nejnovější cenách najdete na [ceník Notification Hubs] stránky. Notification Hubs se účtuje na úrovni oboru názvů. (Pro definici oboru názvů, naleznete v tématu "Jak se struktura prostředků služby Notification hubs?") Notification Hubs nabízí tři úrovně:

* **Bezplatné**: Tato úroveň je dobrým výchozím bodem pro zkoumání nabízených možností. To se nedoporučuje pro produkční aplikace. Získejte 500 zařízení a 1 milion nabízených oznámení zahrnutý na obor názvů za měsíc, bez služby se zárukou smlouvy o úrovni (SLA).
* **Základní**: Tato úroveň (nebo na úrovni Standard) se doporučuje pro menší produkční aplikace. Získat 200 000 zařízení a 10 milionů nabízených oznámení na obor názvů za měsíc jako základ zahrnuté.
* **Standard**: Tato úroveň se doporučuje pro středně velké produkční aplikace. Získat zařízení 10 milionů a 10 milionů nabízených oznámení na obor názvů za měsíc jako základ zahrnuté. Zahrnuje bohatá telemetrie (další data o stavu nabízené k dispozici).

Funkce úrovně Standard:

* **Telemetrie s bohatými funkcemi**: Notification Hubs Telemetrii jednotlivých zpráv můžete sledovat jakékoli žádosti o nabízených oznámení a zpětná vazba systému oznámení platformy pro ladění.
* **Víceklientskou architekturu**: Můžete pracovat s přihlašovací údaje systému oznámení platformy na úrovni oboru názvů. Tato možnost umožňuje snadno tenanty rozdělují na rozbočovače v rámci stejného oboru názvů.
* **Plánované nabízené**: Můžete naplánovat oznámení posílat kdykoli.
* **Hromadné operace**: Povoluje funkce exportu/importu registrace, jak je popsáno v [registrace exportu/importu] dokumentu.

### <a name="what-is-the-notification-hubs-sla"></a>Co je smlouva SLA Notification Hubs?

Pro úrovně Basic a Standard centra oznámení správně nakonfigurovaných aplikací můžete odesílat nabízená oznámení nebo provádění operací správy registrace nejméně 99,9 % času. Další informace o smlouvě SLA, přejděte [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) stránky.

> [!NOTE]
> Protože nabízená oznámení závisí na systémy oznámení platforem třetích stran (například Apple APNS a Google FCM), není zaručeno smlouvu SLA pro doručování tyto zprávy. Po Notification Hubs odešle dávek systémy oznámení platforem (zaručené smlouvy SLA), je odpovědností systémů oznámení platforem k doručování nabízených oznámení (žádná smlouva SLA zaručeno, že).

### <a name="which-customers-are-using-notification-hubs"></a>Které zákazníci používají Notification Hubs?

Mnozí uživatelé používají Notification Hubs. Tady jsou uvedené některé důležité položky:

* Sochi 2014: Stovky zájmových skupin, 3 + miliony zařízení a 150 + milionů oznámení odeslaných za dva týdny. [Případová studie: Sochi]
* Skanska: [Případová studie: Skanska]
* Seattle časy: [Případová studie: Časy Praha]
* Mural.ly: [Případová studie: Mural.ly]
* 7Digital: [Případová studie: 7Digital]
* Aplikace pro Bing: Desítky milionů zařízení odesílat oznámení 3 miliony za den.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak můžu upgradovat nebo downgradovat Moje centrum nebo oboru názvů do jiné úrovně?

Přejděte  **[Azure Portal]** > **názvové prostory centra oznámení** nebo **Notification Hubs**. Vyberte prostředek, který chcete aktualizovat a přejděte na **cenová úroveň**. Vezměte na vědomí následující požadavky:

* Aktualizované cenové úrovně se vztahuje na *všechny* hubs v oboru názvů, kterou pracujete.
* Pokud počet vašich zařízení překračuje limit úroveň, na kterou se Downgrade na, musíte odstranit zařízení, než spustíte downgrade.

## <a name="design-and-development"></a>Návrh a vývoj

### <a name="which-server-side-platforms-do-you-support"></a>Platformy na straně serveru, na kterých podporujete?

Server sady SDK jsou dostupné pro .NET, Java, Node.js, PHP nebo Python. Rozhraní API pro Notification Hubs jsou založené na rozhraních REST, abyste mohli pracovat přímo s rozhraními REST API, pokud používáte různé platformy nebo nechcete, aby další závislosti. Další informace najdete v části [rozhraní REST API Notification Hubs] stránky.

### <a name="which-client-platforms-do-you-support"></a>Které klientské platformy podporujete?

Nabízená oznámení jsou podporovány pro [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (přes Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) a [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [aplikace pro Chrome](notification-hubs-chrome-push-notifications-get-started.md)a [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Další informace najdete v části [Kurzy Začínáme centra oznámení] stránky.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Podporujete textových zpráv, e-mailu nebo web oznámení?

Notification Hubs je primárně určený k odesílání oznámení do mobilní aplikace. Neposkytuje e-mailu nebo text funkcí zasílání a příjmu. Ale platforem třetích stran, které poskytují tyto možnosti je možné integrovat s Notification Hubs k odesílat nativní nabízená oznámení pomocí [Mobile Apps].

Notification Hubs také neposkytuje služby nabízených oznámení v prohlížeči oznámení doručování úprav. Zákazníci můžou implementovat tuto funkci pomocí nástroje SignalR, na které platformy jsou podporované na straně serveru. Pokud chcete odesílat oznámení do aplikací prohlížeče v sandboxu Chrome, najdete v článku [Kurz k aplikacím pro Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Jak se mobilní aplikace a související s Azure Notification Hubs a kdy je použít?

Pokud máte existující back-endu mobilní aplikace a chcete přidat pouze možnost odesílat nabízená oznámení, můžou se používat Azure Notification Hubs. Pokud chcete nastavit back-endu mobilní aplikace od začátku, zvažte použití funkce Mobile Apps služby Azure App Service. Mobilní aplikace automaticky zřídí centra oznámení, takže můžete snadno odesílat nabízená oznámení z back-endu mobilní aplikace. Ceny pro Mobile Apps obsahuje základní poplatky pro Centrum oznámení. Platíte, jenom když překročí zahrnuté nabídky. Podrobné informace o nákladech, přejděte [Ceny služeb App Service] stránky.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Kolik zařízení může podporovat Pokud můžu posílat nabízená oznámení pomocí Notification Hubs?

Odkazovat [ceník Notification Hubs] stránku Podrobnosti o počtu podporovaných zařízení.

Pokud potřebujete podporu pro registrovaná zařízení víc než 10 milionů, [kontaktujte nás](https://azure.microsoft.com/overview/contact-us/) přímo a pomůžeme vám škálování vlastního řešení.

### <a name="how-many-push-notifications-can-i-send-out"></a>Kolik nabízená oznámení můžete mi posílat?

V závislosti na vybrané úrovni Azure Notification Hubs automaticky zvětší na základě počtu oznámení protéká systémem.

> [!NOTE]
> Náklady na celkové využití může zvýšit na základě počtu nabízených oznámení, které jsou obsluhovány. Ujistěte se, že budete vědět, limity příslušné úrovně uvedené na [ceník Notification Hubs] stránky.

Naši zákazníci denně odeslat milióny nabízených oznámení pomocí Notification Hubs. Nemusíte dělat nic zvláštního škálování dosah nabízených oznámení za předpokladu, že používáte Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak dlouho trvá odesílá nabízená oznámení pro moje zařízení?

Ve scénáři normálním používání, ve kterém se příchozí zátěží je konzistentní a i, Azure Notification Hubs můžete zpracovávat alespoň *1 milion nabízených oznámení odešle minutu*. Tato sazba se může lišit v závislosti na počtu značek, povaze odešle příchozí a další externí faktory.

Během doby dodací služby vypočítá cílí na platformy a trasy zpráv k Push Notification Service (systém oznámení platformy) na základě registrované značky nebo výrazy označení. Je odpovědností systému oznámení platformy k odesílání oznámení do zařízení.

Systém oznámení platformy nezaručuje žádnou smlouvu SLA pro doručování oznámení. Nicméně, většina nabízená oznámení se doručují do cílových zařízení během několika minut (obvykle během 10 minut) od okamžiku, kdy se odesílají do Notification Hubs. Několik upozornění, může to trvat déle.

> [!NOTE]
> Azure Notification Hubs zavedl zásadu vyřaďte všechny nabízená oznámení, které nejsou systému oznámení platformy doručit do 30 minut. Toto zpoždění může být z několika důvodů, ale většina obvykle, protože systém oznámení platformy omezuje vaší aplikace.

### <a name="is-there-any-latency-guarantee"></a>Je k dispozici žádné záruky latence?

Vzhledem k povaze nabízená oznámení (doručení podle systému oznámení platformy externí, specifické pro platformu) není zaručeno latence. Obvykle většinou nabízená oznámení se doručují během několika minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co je potřeba vzít v úvahu při navrhování řešení s obory názvů a notification hubs?

#### <a name="mobile-appenvironment"></a>Mobilní aplikace a prostředí

* Pomocí jednoho centra oznámení na mobilní aplikaci, pro každé prostředí.
* Ve scénářích s více tenanty každého klienta by měl mít samostatný rozbočovače.
* Nikdy sdílejí stejné centra oznámení pro produkční a testovací prostředí. Tento postup může způsobit problémy při odesílání oznámení. (Apple nabízí izolovaného prostoru a produkční Push koncových bodů, každý s samostatné přihlašovací údaje).
* Ve výchozím nastavení můžete poslat testovací oznámení registrovaná zařízení prostřednictvím webu Azure portal nebo Azure integrované komponenty v sadě Visual Studio. Prahová hodnota je nastavena na 10 zařízení, které jsou náhodně vybrány z fondu registrace.

> [!NOTE]
> Pokud vaše Centrum byla původně nakonfigurován s certifikátem Apple izolovaného prostoru a potom byl znovu konfigurován pro použití produkčního certifikátu Apple, původní tokenů zařízení jsou neplatné. Neplatné tokeny způsobí nasdílení změn selže. Oddělte produkčním i testovacím prostředí a použití různých rozbočovače pro různá prostředí.

#### <a name="pns-credentials"></a>Přihlašovací údaje systému oznámení platformy

Při registraci mobilních aplikací s portálem pro vývojáře na platformě (například Apple nebo Google), se odesílají tokeny zabezpečení a identifikátor aplikace. Back-endu aplikace poskytuje tyto tokeny platformy systému oznámení platformy tak, aby odesílat nabízená oznámení do zařízení. Tokeny zabezpečení může být ve formě certifikáty (například Apple iOS nebo Windows Phone) nebo klíče zabezpečení (například Google Android nebo Windows). Musí být nakonfigurované ve službě notification hubs. Konfigurace se obvykle provádí na úrovni centra oznámení, ale můžete také provést na úrovni oboru názvů ve scénářích s více tenanty.

#### <a name="namespaces"></a>Obory názvů

Obory názvů lze použít pro nasazení seskupení. Můžete také používají k vyjádření všemi notification hubs pro všechny tenanty stejné aplikace ve scénářích s více tenanty.

#### <a name="geo-distribution"></a>Geo-distribution

Geografická rozptýlenost není vždy důležité ve scénářích nabízených oznámení. Různé PNSes (například APNS a GCM), které doručení nabízených oznámení do zařízení nejsou rovnoměrně.

Pokud máte aplikaci, která se používá globálně, můžete vytvořit centra v různých oborech názvů pomocí služby Notification Hubs v různých oblastech Azure po celém světě.

> [!NOTE]
> Toto uspořádání nedoporučujeme, protože jeho hodnota se zvyšuje správy nákladů, hlavně pro registrace. By mělo být provedeno pouze v případě, že existuje explicitní potřeba.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Mám registrace z back-endu aplikace nebo přímo prostřednictvím klienta zařízení?

Registrace z back-endu aplikace jsou užitečné, když máte k ověřování klientů před vytvořením registrace. Jsou to také užitečné až budete mít značky, které musí být vytvořené nebo změněné aplikačního back-endu na základě aplikace logiky. Další informace najdete v části [pokyny k registraci back-endu] a [pokyny k registraci back-endu 2] stránky.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Jaký je model zabezpečení doručování nabízených oznámení?

Pomocí Azure Notification Hubs [sdílený přístupový podpis](../storage/common/storage-dotnet-shared-access-signature-part-1.md)– model zabezpečení založený na. Na kořenové úrovni oboru názvů nebo na úrovni centra podrobné oznámení, můžete použít tokeny sdíleného přístupového podpisu. Sdílený přístupový podpis, tokeny lze nastavit, postupujte podle různých autorizační pravidla, například, odesílat zprávy oprávnění nebo oznámení oprávnění naslouchat. Další informace najdete v tématu [model zabezpečení Notification Hubs] dokumentu.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak zacházet citlivé datové části v nabízených oznámeních?

Všechna oznámení jsou doručen do cílových zařízení podle platformy systému oznámení platformy. Odeslání oznámení do Azure Notification Hubs je zpracovat a předat příslušné systému oznámení platformy.

Všechna připojení, od odesílatele do Azure Notification Hubs k systému oznámení platformy, pomocí protokolu HTTPS.

> [!NOTE]
> Azure Notification Hubs neprotokoluje žádným způsobem datovou část zprávy.

Posílat citlivé datových částí, doporučujeme pomocí vzoru zabezpečení Push. Odesílatel poskytuje oznámení příkazem ping se identifikátor zprávy do zařízení bez citlivých datové části. Datová část přijetí aplikace na zařízení v aplikaci označuje jako zabezpečení rozhraní API přímo k načtení podrobností zprávy. Informace o tom, jak tento model implementovat, přejděte [Kurzu centra zabezpečení nabízená oznámení] stránky.

## <a name="operations"></a>Operace

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jaká podpora se poskytuje pro zotavení po havárii?

Poskytujeme pokrytí pro zotavení po havárii metadat na naší straně (název centra oznámení, připojovací řetězec a dalších důležitých informací). Když se aktivuje na scénář zotavení po havárii, je registrační data *pouze segmentovat* infrastruktury Notification Hubs, která se ztratí. K implementaci řešení, které naplnit tato data do vaší nové centrum po obnovení budete potřebovat:

1. Vytvoření centra oznámení sekundární v různých datových center. Doporučujeme vytvořit jeden od začátku, aby vás chrání před událostí obnovení po havárii, může mít vliv na možnosti správy. Můžete také vytvořit jeden v době události zotavení po havárii.

2. Naplnění sekundární oznámení centra se registrace z centra oznámení primární. Nedoporučujeme Udržovat registrace na obou rozbočovače a je udržovat synchronizované jak registrace se dělí na. Tento postup nefunguje dobře kvůli přináší větší registrace do vypršení platnosti na straně systému oznámení platformy. Notification Hubs vyčistí je když přijímá zpětná vazba systému oznámení platformy o registraci vypršel nebo není platný.  

Máme dva doporučení pro back-EndY aplikací:

* Pomocí back-endu aplikace, která udržuje danou sadu registrace na jeho konci. To můžete provést hromadné vložení do centra oznámení sekundární.
* Pomocí back-endu aplikace, která získá regulární výpisu registrace v centru oznámení primární jako záložní. To můžete provést hromadné vložení do centra oznámení sekundární.

> [!NOTE]
> Funkce exportu/importu registrace je dostupná na úrovni Standard je popsána v [registrace exportu/importu] dokumentu.

Pokud nemáte k dispozici back-endu, při spuštění aplikace na cílových počítačích, provádějí nové registrace v centru oznámení sekundární. Centrum oznámení sekundární nakonec bude mít všechny aktivní zařízení zaregistrované.

Časové období, kdy zařízení s aplikacemi neotevřených žádná oznámení zobrazovat nebudou.

### <a name="is-there-audit-log-capability"></a>Je k dispozici funkce protokolů auditu?

Ano. Všechny Notification Hubs správy operací aktualizace protokolu aktivit Azure, ke kterému je zpřístupněná [Azure Portal]. Protokol aktivit Azure nabízí přehledy o operace prováděné s prostředky v rámci vašich předplatných. Použití protokolu aktivit, můžete určit, co, kdo, kdy pro všechny operace (PUT, POST, DELETE) provedených pro prostředky ve vašem předplatném zápisu a. Můžete také zjištění stavu operace a další relevantní vlastnosti. Ale. Protokol aktivit nezahrnují operace čtení (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

### <a name="what-troubleshooting-capabilities-are-available"></a>Jaké možnosti pro odstraňování potíží jsou k dispozici?

Azure Notification Hubs poskytuje několik funkcí pro řešení potíží, hlavně pro nejčastější scénáře vynechanými oznámeními. Podrobnosti najdete v tématu [řešení potíží s Notification Hubs] dokument white paper.

### <a name="what-telemetry-features-are-available"></a>Jaké funkce telemetrická data jsou k dispozici?

Zobrazení telemetrických dat v Azure Notification Hubs umožňuje [Azure Portal]. Podrobnosti o metriky jsou k dispozici na [Notification Hubs metriky] stránky.

> [!NOTE]
> Úspěšná oznámení jednoduše znamená, že byly dodány nabízených oznámení do externího systému oznámení platformy (například služby APN pro Apple) nebo pro Google GCM. Je odpovědností systému oznámení platformy doručování oznámení do cílových zařízení. Systém oznámení platformy standardně nevystavuje doručování metrik třetím stranám.  

Nabízíme také možnost exportovat telemetrická data prostřednictvím kódu programu (na úrovni Standard). Podrobnosti najdete v tématu [Ukázky využití Notification Hubs metriky].

[Azure Portal]: https://portal.azure.com
[Ceník Notification Hubs]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Případová studie: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Případová studie: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Případová studie: Časy Praha]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Případová studie: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Případová studie: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Rozhraní REST API Notification Hubs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Kurzy Začínáme centra oznámení]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Kurz k aplikacím pro Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Pokyny k registraci back-endu]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Pokyny k registraci back-endu 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Model zabezpečení Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Kurzu centra zabezpečení nabízená oznámení]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Řešení potíží s Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs metriky]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Ukázky využití Notification Hubs metriky]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Registrace exportu/importu]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Ceny služeb App Service]: https://azure.microsoft.com/pricing/details/app-service/
