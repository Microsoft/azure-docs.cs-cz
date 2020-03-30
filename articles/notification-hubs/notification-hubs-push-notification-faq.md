---
title: Azure Notification Hubs Nejčastější dotazy (časté dotazy) | Dokumenty společnosti Microsoft
description: Časté otázky týkající se navrhování a implementace řešení v Centru oznámení Azure.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: push oznámení, push oznámení, iOS push oznámení, android push oznámení, ios push, android push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263825"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Nabízená oznámení s Azure Notification Hubs: Nejčastější dotazy

## <a name="general"></a>Obecné

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Jaká je struktura prostředků centra oznámení?

Azure Notification Hubs má dvě úrovně prostředků: centra a obory názvů. Rozbočovač je jeden nabízený prostředek, který může obsahovat informace o nabízení mezi platformami jedné aplikace. Obor názvů je kolekce rozbočovačů v jedné oblasti. Doporučené mapování odpovídá jednomu oboru názvů s jednou aplikací. V rámci oboru názvů můžete mít produkční centrum, které pracuje s produkční aplikací, testovací centrum, které pracuje s testovací aplikací a tak dále.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Jaký je cenový model pro centra oznámení?

Nejnovější podrobnosti o cenách najdete na stránce [Ceny centra oznámení.] Centra oznámení se účtují na úrovni oboru názvů. (Definice oboru názvů naleznete v tématu "Jaká je struktura prostředků center oznámení?") Centra oznámení nabízejí tři úrovně:

* **Volný:** Tato úroveň je dobrým výchozím bodem pro zkoumání nabízených možností. Nedoporučuje se pro produkční aplikace. Získáte 500 zařízení a 1 milion nabízených oznámení zahrnuty na obor názvů za měsíc, bez záruky smlouvy o úrovni služeb (SLA).
* **Základní:** Tato úroveň (nebo úroveň Standard) se doporučuje pro menší produkční aplikace. Získáte 200 000 zařízení a 10 milionů nabízených oznámení zahrnutých na obor názvů za měsíc jako směrný plán.
* **Standardní**: Tato úroveň se doporučuje pro středně velké až velké produkční aplikace. Získáte 10 milionů zařízení a 10 milionů nabízených oznámení zahrnuty na obor názvů za měsíc jako směrný plán. Zahrnuje bohatou telemetrii (další data o stavu nabízených hlášení).

Standardní funkce úrovně:

* **Bohatá telemetrie : Telemetrie**oznamovacích polí na zprávu můžete sledovat všechny požadavky na nabízenou hlášení a zpětnou vazbu systému oznámení platformy pro ladění.
* **Víceklientské :** Můžete pracovat s pověřeními systému oznámení platformy na úrovni oboru názvů. Tato možnost umožňuje snadno rozdělit klienty do rozbočovačů ve stejném oboru názvů.
* **Plánované nabízení**: Oznámení můžete naplánovat tak, aby byla odeslána kdykoli.
* **Hromadné operace**: Povolí registrace funkce exportu a importu, jak je popsáno v dokladu [Export/Import registrací.]

### <a name="what-is-the-notification-hubs-sla"></a>Co je sla centra oznámení?

U úrovní Basic a Standard Notification Hubs mohou správně nakonfigurované aplikace odesílat nabízená oznámení nebo provádět operace správy registrace alespoň v 99,9 % času. Další informace o sla najdete na stránce [SLA centra oznámení.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Vzhledem k tomu, že nabízená oznámení závisí na systémech oznamování platformy třetích stran, jako je služba nabízených oznámení (APNs) společnosti Google a cloudová zpráva Společnosti Google Firebase (FCM), neexistuje žádná záruka SLA pro doručení těchto zpráv. Poté, co centra oznámení odešle dávky do systémů oznámení platformy (SLA zaručena), je odpovědností systémů oznámení platformy k doručení nabízených oznámení (žádná záruka SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak můžu upgradovat nebo downgrade mého rozbočovače nebo oboru názvů na jinou úroveň?

Přejděte na**obory názvů Centra oznámení** na **[portálu]** > Azure nebo v **centrech oznámení**. Vyberte prostředek, který chcete aktualizovat, a přejděte na **Cenovou úroveň**. Všimněte si následujících požadavků:

* Aktualizovaná cenová úroveň platí pro *všechna* centra v oboru názvů, se kterým pracujete.
* Pokud počet zařízení překročí limit úrovně, na kterou se sníží, musíte před přechodem na nižší verzi zařízení odstranit.

## <a name="design-and-development"></a>Návrh a vývoj

### <a name="which-server-side-platforms-do-you-support"></a>Které platformy na straně serveru podporujete?

Sady SDK serveru jsou k dispozici pro .NET, Java, Node.js, PHP a Python. Rozhraní API centra oznámení jsou založená na rozhraních REST, takže můžete pracovat přímo s rozhraními REST API, pokud používáte různé platformy nebo nechcete další závislost. Další informace najdete na stránce [REST API centra oznámení.]

### <a name="which-client-platforms-do-you-support"></a>Které klientské platformy podporujete?

Nabízená oznámení jsou podporována pro [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), Android [China (přes Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) a Android a [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Další informace najdete na stránce [kurzy Začínáme centra oznámení.]

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Podporujete textová oznámení, e-mailová nebo webová oznámení?

Centra oznámení odesílají oznámení zařízením s mobilními aplikacemi. Neposkytuje možnosti e-mailu nebo textové zprávy. Centra oznámení také neposkytuje službu doručování nabízených oznámení v prohlížeči po vybalení. Zákazníci mohou implementovat tuto funkci pomocí SignalR nad podporované platformy na straně serveru. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Kolik zařízení mohu podporovat, když posílám nabízená oznámení prostřednictvím center oznámení?

Podrobnosti o počtu podporovaných zařízení najdete na stránce [Ceny centra oznámení.]

Pokud potřebujete podporu pro více než 10 milionů registrovaných zařízení, musíte rozdělit zařízení mezi více rozbočovačů.

### <a name="how-many-push-notifications-can-i-send-out"></a>Kolik nabízených oznámení mohu odeslat?

V závislosti na vybrané úrovni se centra Oznámení Azure automaticky zvětšují na základě počtu oznámení protékajících systémem.

> [!NOTE]
> Celkové náklady na využití se mohou zvýšit na základě počtu odeslaných nabízených oznámení. Ujistěte se, že jste si vědomi limitů úrovní uvedených na stránce [Ceny centra oznámení.]

Naši zákazníci používají centra oznámení k odesílání milionů nabízených oznámení denně. Pokud používáte Centra oznámení Azure, nemusíte dělat nic zvláštního, abyste mohli škálovat dosah nabízených oznámení.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak dlouho trvá, než se odeslaná nabízená oznámení dostanou do mého zařízení?

V případě normálního použití, kde příchozí zatížení je konzistentní a sudé, Centra oznámení Azure můžete zpracovat alespoň *1 milion nabízených oznámení odešle minutu*. Tato rychlost se může lišit v závislosti na počtu značek, povaze příchozích odešle a dalších externích faktorech.

Během odhadované doby doručení služba vypočítá cíle na platformu a směruje zprávy do služby nabízených oznámení (PNS) na základě registrovaných značek nebo výrazů značek. Je odpovědností PNS odesílat oznámení do zařízení.

PNS nezaručuje žádné SLA pro doručování oznámení. Většina nabízených oznámení se však docílových zařízení doručuje během několika minut (obvykle do 10 minut) od jejich odeslání do center oznámení. Několik oznámení může trvat déle.

> [!NOTE]
> Azure Notification Hubs má zásady na místě k přetažení všechna nabízená oznámení, které nejsou doručeny do PNS do 30 minut. K tomuto zpoždění může dojít z mnoha důvodů, ale nejčastěji proto, že PNS je omezení aplikace.

### <a name="is-there-any-latency-guarantee"></a>Existuje nějaká záruka latence?

Vzhledem k povaze nabízených oznámení (jsou dodávány externí, specifické pro platformu PNS), neexistuje žádná záruka latence. Většina nabízených oznámení se obvykle doručuje během několika minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co je třeba vzít v úvahu při navrhování řešení s obory názvů a centry oznámení?

#### <a name="mobile-appenvironment"></a>Mobilní aplikace/prostředí

* Použijte jedno centrum oznámení pro mobilní aplikaci a prostředí.
* Ve scénáři s více tenanty by měl mít každý klient samostatné centrum.
* Nikdy nesdílejte stejné centrum oznámení pro produkční a testovací prostředí. Tento postup může způsobit problémy při odesílání oznámení. (Apple nabízí sandbox a produkční push koncové body, každý se samostatnými přihlašovacími údaji.)
* Ve výchozím nastavení můžete odesílat testovací oznámení registrovaným zařízením prostřednictvím portálu Azure nebo integrované součásti Azure ve Visual Studiu. Prahová hodnota je nastavena na 10 zařízení, která jsou vybrána náhodně z fondu registrace.

> [!NOTE]
> Pokud byl váš rozbočovač původně nakonfigurován s certifikátem izolovaného prostoru Apple a pak byl překonfigurován tak, aby používal produkční certifikát Apple, jsou původní tokeny zařízení neplatné. Neplatné tokeny způsobit selhání pushů. Oddělte produkční a testovací prostředí a používejte různá rozbočovače pro různá prostředí.

#### <a name="pns-credentials"></a>Pověření pns

Když je mobilní aplikace zaregistrována na portálu pro vývojáře platformy (například Apple nebo Google), odešlou se identifikátor aplikace a bezpečnostní tokeny. Back-end aplikace poskytuje tyto tokeny do pns platformy tak, aby nabízená oznámení mohou být odesílány do zařízení. Bezpečnostní tokeny mohou mít podobu certifikátů (například Apple iOS nebo Windows Phone) nebo bezpečnostních klíčů (například Google Android nebo Windows). Musí být nakonfigurovány v rozbočovačích oznámení. Konfigurace se obvykle provádí na úrovni centra oznámení, ale lze provést také na úrovni oboru názvů ve scénáři s více tenanty.

#### <a name="namespaces"></a>Jmenné prostory

Obory názvů lze použít pro seskupení nasazení. Lze je také použít k reprezentaci všech center oznámení pro všechny klienty stejné aplikace ve scénáři s více tenanty.

#### <a name="geo-distribution"></a>Geografická distribuce

Geografická distribuce není vždy kritická ve scénářích nabízených oznámení. Různé PNSes (například APNs nebo FCM), které doručují nabízená oznámení do zařízení nejsou rovnoměrně distribuovány.

Pokud máte aplikaci, která se používá globálně, můžete vytvořit rozbočovače v různých oborech názvů pomocí služby Notification Hubs v různých oblastech Azure po celém světě.

> [!NOTE]
> Toto uspořádání nedoporučujeme, protože zvyšuje náklady na správu, zejména u registrací. Mělo by se tak dělat pouze v případě, že existuje výslovná potřeba.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Mám provést registrace z back-endu aplikace nebo přímo prostřednictvím klientských zařízení?

Registrace z back-endu aplikace jsou užitečné, když máte ověřit klienty před vytvořením registrace. Jsou také užitečné, když máte značky, které musí být vytvořeny nebo upraveny back-endem aplikace na základě logiky aplikace. Další informace naleznete v [pokynech pro registraci back-endu] a [v pokynech pro registraci back-endu 2] stránky.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co je model zabezpečení doručování nabízených oznámení?

Azure Notification Hubs používá model zabezpečení založený na [sdíleném přístupu.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Tokeny sdílených přístupových podpisů můžete použít na úrovni kořenového oboru názvů nebo na úrovni centra podrobných oznámení. Tokeny sdílených přístupových podpisů lze nastavit tak, aby dodržovaly různá autorizační pravidla, například pro odesílání oprávnění ke zprávám nebo naslouchání oprávněním oznámení. Další informace naleznete v dokumentu [modelu zabezpečení Centra oznámení.]

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak mám zpracovat citlivou datovou část v nabízených oznámeních?

Všechna oznámení jsou doručována do cílových zařízení pomocí PNS platformy. Když se oznámení odešle do Centra oznámení Azure, je zpracována a předána příslušné pns.

Všechna připojení, od odesílatele do Centra oznámení Azure na PNS, použijte protokol HTTPS.

> [!NOTE]
> Azure Notification Hubs nezaznamenává datovou část zpráv.

Chcete-li odeslat citlivé datové části, doporučujeme použít vzor zabezpečené nabízení. Odesílatel doručí oznámení ping s identifikátorem zprávy do zařízení bez citlivé datové části. Když aplikace na zařízení obdrží datovou část, aplikace zavolá zabezpečené rozhraní API přímo k načtení podrobností o zprávě. Průvodce implementací tohoto vzoru najdete na stránce [kurzu Zabezpečené nabízení centra oznámení.]

## <a name="operations"></a>Provoz

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jaká podpora je poskytována pro zotavení po havárii?

Na naší straně poskytujeme pokrytí zotavenípo havárii metadat (název centra oznámení, připojovací řetězec a další důležité informace). Při spuštění scénáře zotavení po havárii, registrační data je *jediný segment* infrastruktury Centra oznámení, která je ztracena. Je nutné implementovat řešení znovu naplnit tato data do nového centra po obnovení:

1. Vytvořte sekundární centrum oznámení v jiném datovém centru. Doporučujeme vytvořit jeden od začátku, aby vás chránil před událostí zotavení po havárii, která by mohla ovlivnit vaše možnosti správy. Můžete také vytvořit jeden v době události zotavení po havárii.

2. Naplnit sekundární centrum oznámení s registracemi z primárního centra oznámení. Nedoporučujeme se snažit udržovat registrace na obou rozbočovačích a udržovat je synchronizované, když přicházejí registrace. Tento postup nefunguje dobře z důvodu vlastní tendence registrace vyprší na straně PNS. Centra oznámení je vyčistí, když obdrží zpětnou vazbu PNS o vypršení platnosti nebo neplatných registracích.  

Máme dvě doporučení pro backendy aplikací:

* Použijte back-end aplikace, který udržuje danou sadu registrací na jeho konci. Potom může provést hromadné vložení do sekundárního centra oznámení.
* Použijte back-end aplikace, který získá pravidelný výpis registrací z primárního centra oznámení jako zálohu. Potom může provést hromadné vložení do sekundárního centra oznámení.

> [!NOTE]
> Funkce exportu a importu registrací, které jsou k dispozici na úrovni Standard, jsou popsány v dokladu [Export/Import registrací.]

Pokud nemáte back-end, když se aplikace spustí na cílových zařízeních, provedou novou registraci v sekundárním centru oznámení. Nakonec sekundární centrum oznámení bude mít všechna aktivní zařízení registrována.

Bude existovat časové období, kdy zařízení s neotevřenými aplikacemi nebudou dostávat oznámení.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Jsou všechna moje data uložena v zašifrované podobě?

Azure Notification Hubs šifruje všechna zákaznická data v klidovém stavu s výjimkou registračních značek. Z tohoto důvodu byste neměli ukládat osobní nebo důvěrné údaje pomocí značek.

### <a name="is-there-audit-log-capability"></a>Existuje možnost protokolu auditu?

Ano. Všechny operace správy centra oznámení aktualizují protokol aktivit Azure, kterému se zobrazuje na [webu Azure Portal]. Protokol aktivit Azure nabízí přehled o operacích prováděných s prostředky ve vašich předplatných. Pomocí protokolu aktivit můžete určit, co, kdo a kdy pro všechny operace zápisu (PUT, POST, DELETE) provedené pro prostředky ve vašem předplatném. Můžete také pochopit stav operací a dalších relevantních vlastností. Nicméně. Protokol aktivit nezahrnuje operaci čtení (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení problémů

### <a name="what-troubleshooting-capabilities-are-available"></a>Jaké možnosti řešení potíží jsou k dispozici?

Azure Notification Hubs poskytuje několik funkcí pro řešení potíží, zejména pro nejběžnější scénář zrušených oznámení. Podrobnosti najdete v dokumentu White Paper centra oznámení, která řeší potíže s [centry.]

### <a name="what-telemetry-features-are-available"></a>Jaké funkce telemetrie jsou k dispozici?

Azure Notification Hubs umožňuje zobrazení telemetrických dat na [webu Azure Portal]. Podrobnosti o metrikách jsou k dispozici na stránce [Metriky centra oznámení.]

Můžete také programově přistupovat k metrikám. Další informace najdete v těchto článcích:

- [Načíst metriky Azure Monitor s rozhraním .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Tato ukázka používá uživatelské jméno a heslo. Chcete-li použít certifikát, přetížení FromServicePrincipal metoda poskytnout certifikát, jak je znázorněno v [tomto příkladu](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Získání metrik a protokolů aktivit pro prostředek](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Návod k rozhraní REST monitorování Azure](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Úspěšná oznámení jednoduše znamenají, že nabízená oznámení byla doručena do externího systému PNS (například kódy APN pro iOS a macOS nebo FCM pro zařízení se systémem Android). Je odpovědností PNS doručit oznámení cílovým zařízením. Služba PNS obvykle nezveřejňuje metriky doručení třetím stranám.  

[Portál Azure]: https://portal.azure.com
[Ceny oznamovacích center]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Rest API centra oznámení]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Kurzy Začínáme v centru oznámení]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Pokyny pro registraci back-endu]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Pokyny pro registraci back-endu 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Model zabezpečení centra oznámení]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Kurz zabezpečeného nabízení center oznámení]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Řešení potíží s centry oznámení]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Metriky center oznamovacích center]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registrace Export/Import]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Portál Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
