---
title: Nejčastější dotazy k Azure Notification Hubsu (FAQ) | Microsoft Docs
description: Nejčastější dotazy týkající se návrhu a implementace řešení v Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: nabízené oznámení, nabízená oznámení, nabízená oznámení iOS, nabízená oznámení Androidu, nabízená oznámení Androidu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 0f79402956148c566bc34faa88e10895657883c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "100591735"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Nabízená oznámení s Azure Notification Hubs: nejčastější dotazy

## <a name="general"></a>Obecné

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Jaká je struktura prostředků Notification Hubs?

Azure Notification Hubs má dvě úrovně prostředků: centra a obory názvů. Centrum je jeden prostředek pro nabízení oznámení, který může uchovávat nabízené informace pro různé platformy v jedné aplikaci. Obor názvů je kolekce Center v jedné oblasti. Doporučené mapování odpovídá jednomu oboru názvů s jednou aplikací. V rámci oboru názvů můžete mít provozní centrum, které funguje s vaší produkční aplikací, testovacím centrem, které funguje s vaší zkušební aplikací, a tak dále.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Jaký je cenový model pro Notification Hubs?

Nejnovější informace o cenách najdete na stránce s [cenami Notification Hubs] . Notification Hubs se účtuje na úrovni oboru názvů. (Pro definici oboru názvů, přečtěte si téma "Co je struktura prostředků Notification Hubs?") Notification Hubs nabízí tři úrovně:

* **Zdarma**: Tato úroveň je dobrým výchozím bodem pro zkoumání nabízených funkcí. Nedoporučuje se pro produkční aplikace. Získáte 500 zařízení a 1 000 000 nabízených oznámení na obor názvů za měsíc bez záruky smlouvy o úrovni služeb (SLA).
* **Basic**: Tato vrstva (nebo úroveň Standard) se doporučuje pro menší produkční aplikace. Dostanete 200 000 zařízení a 10 000 000 nabízených oznámení na obor názvů za měsíc jako standardní hodnoty.
* **Standard**: Tato úroveň se doporučuje pro střední až velké produkční aplikace. Dostanete 10 000 000 zařízení a 10 000 000 nabízených oznámení na obor názvů za měsíc jako standardní hodnoty. Obsahuje bohatou telemetrii (další údaje o nabízeném stavu push).

Funkce úrovně Standard:

* **Bohatá telemetrie**: pomocí Notification Hubs telemetrie zpráv můžete sledovat všechny žádosti o nabízené oznámení a systém oznámení platformy zpětnou vazbu pro ladění.
* **Víceklientská architektura: můžete** pracovat s přihlašovacími údaji systém oznámení platformy na úrovni oboru názvů. Tato možnost umožňuje snadno rozdělit klienty do Center v rámci stejného oboru názvů.
* **Naplánovaná nabízená** oznámení: můžete naplánovat, aby se oznámení odesílala kdykoli.
* **Hromadné operace**: umožňuje registraci funkcí exportu/importu, jak je popsáno v dokumentu [Export/Import registrací] .

### <a name="what-is-the-notification-hubs-sla"></a>Jaká je smlouva SLA pro Notification Hubs?

U úrovní Basic a Standard Notification Hubs mohou správně nakonfigurované aplikace odesílat nabízená oznámení nebo provádět operace správy registrace alespoň 99,9% času. Další informace o smlouvě SLA najdete na stránce [Notification HUBS SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Vzhledem k tomu, že nabízená oznámení závisí na systémech pro oznamování platforem třetích stran, jako je služba APNs (Apple Push Notification Service) a Google Firebase Cloud Messaging (FCM), není pro doručení těchto zpráv žádná záruka SLA. Po Notification Hubs odesílá dávky do systémů oznámení platforem (zaručených smlouvou SLA), zodpovídá za to, že systémy oznámení platformy doručí nabízená oznámení (bez zaručené smlouvy SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Návody upgradovat nebo downgradovat můj rozbočovač nebo obor názvů na jinou úroveň?

Přejít na **[Azure Portal]**  >  **obory názvů Notification Hubs** nebo **Notification Hubs**. Vyberte prostředek, který chcete aktualizovat, a pak přejít na **cenovou úroveň**. Vezměte na vědomí následující požadavky:

* Aktualizovaná cenová úroveň se vztahuje na *všechna* centra v oboru názvů, se kterým pracujete.
* Pokud počet zařízení překročí limit vrstvy, na kterou se chystáte přejít, budete muset před přechodem do downgradu odstranit zařízení.

## <a name="design-and-development"></a>Návrh a vývoj

### <a name="which-server-side-platforms-do-you-support"></a>Které platformy na straně serveru podporujete?

Sady SDK serveru jsou k dispozici pro .NET, Java, Node.js, PHP a Python. Rozhraní API pro Notification Hubs jsou založená na rozhraních REST, takže můžete pracovat přímo s rozhraními REST API, pokud používáte různé platformy nebo nechcete mít zvláštní závislost. Další informace najdete na stránce [Notification Hubs rozhraní REST API] .

### <a name="which-client-platforms-do-you-support"></a>Které klientské platformy podporujete?

Nabízená oznámení jsou podporovaná pro [iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (prostřednictvím Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) a [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)a [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Další informace najdete na stránce [Notification Hubsch kurzů Začínáme](ios-sdk-get-started.md) .

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Podporujete textovou zprávu, e-mail nebo webová oznámení?

Notification Hubs odesílá oznámení do zařízení s mobilními aplikacemi. Neposkytuje možnosti e-mailu nebo textové zprávy. Notification Hubs také neposkytuje službu doručování nabízených oznámení v prohlížeči. Zákazníci mohou tuto funkci implementovat pomocí nástroje Signal nad podporovanými platformami na straně serveru. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Kolik zařízení můžu podporovat při odesílání nabízených oznámení prostřednictvím Notification Hubs?

Podrobnosti o počtu podporovaných zařízení najdete na stránce s [cenami Notification Hubs] .

Pokud potřebujete podporu více než 10 000 000 registrovaných zařízení, je nutné rozdělit zařízení do více oborů názvů.

### <a name="how-many-push-notifications-can-i-send-out"></a>Kolik nabízených oznámení můžu odeslat?

V závislosti na vybrané úrovni se Azure Notification Hubs automaticky škáluje podle počtu oznámení, která v systému přecházejí.

> [!NOTE]
> Celkové náklady na využití se můžou zvýšit na základě počtu odeslaných nabízených oznámení. Ujistěte se, že jste si vědomi limitů vrstev popsaných na stránce s [cenami Notification Hubs] .

Naši zákazníci používají Notification Hubs k posílání milionů nabízených oznámení denně. Pokud používáte Azure Notification Hubs, nemusíte dělat žádné speciální kroky, abyste mohli škálovat nabízená oznámení.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak dlouho trvá odeslání nabízených oznámení, aby se zařízení dostalo?

V případě normálního použití, kde je příchozí zatížení konzistentní a dokonce i, může Azure Notification Hubs zpracovat aspoň *1 000 000 nabízených oznámení a pošle minutu*. Tato frekvence se může lišit v závislosti na počtu značek, povaze příchozích odesílání a dalších externích faktorech.

Během Odhadované doby doručení služba vypočítá cíle na platformu a směruje zprávy do služby nabízených oznámení (PNS) na základě registrovaných výrazů nebo výrazů značek. Zodpovídá za to, že PNS odesílá oznámení do zařízení.

PNS nezaručuje smlouvu SLA pro doručování oznámení. Většina nabízených oznámení se ale doručuje do cílových zařízení během několika minut (obvykle během 10 minut) od času, kdy se odešlou do Notification Hubs. Několik oznámení může trvat delší dobu.

> [!NOTE]
> Azure Notification Hubs obsahuje zásady, které zahodí všechna nabízená oznámení, která nejsou Doručená do PNS do 30 minut. Tato prodleva se může vyskytnout z mnoha důvodů, ale většinou vzhledem k tomu, že PNS omezuje vaši aplikaci.

### <a name="is-there-any-latency-guarantee"></a>Je nějaká záruka na latenci?

Vzhledem k povaze nabízených oznámení (jsou dodávány externím PNS specifickým pro konkrétní platformu) není zaručena latence. Většina nabízených oznámení je obvykle dodána během několika minut.

### <a name="where-does-azure-notification-hubs-store-data"></a>Kde Azure Notification Hubs ukládá data?

Azure Notification Hubs ukládá data o registraci zákazníka v oblasti vybrané zákazníkem. Notification Hubs poskytuje pokrytí zotavení po havárii metadat (název Notification Hubs, připojovací řetězec a další důležité informace). U všech oblastí s výjimkou Brazílie – jih a jihovýchodní Asie je zálohování metadat hostované v jiné oblasti (obvykle spárováno s Azure). Pro oblast Brazílie – jih a jihovýchodní Asie jsou zálohy uložené ve stejné oblasti, aby vyhovovaly požadavkům na umístění dat pro tyto oblasti.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co je potřeba vzít v úvahu při navrhování řešení pomocí oborů názvů a Center oznámení?

#### <a name="mobile-appenvironment"></a>Mobilní aplikace/prostředí

* Použijte jedno centrum oznámení na mobilní aplikaci na jedno prostředí.
* Ve scénáři s více klienty by měl mít každý tenant samostatné centrum.
* Nikdy nesdílejte stejné centrum oznámení pro produkční a testovací prostředí. Tento postup může způsobit problémy při posílání oznámení. (Apple nabízí izolovaný prostor a koncový bod nabízených oznámení, každý s oddělenými přihlašovacími údaji.)
* Ve výchozím nastavení můžete odesílat zkušební oznámení do registrovaných zařízení prostřednictvím Azure Portal nebo integrované součásti Azure v aplikaci Visual Studio. Prahová hodnota je nastavená na 10 zařízení, která jsou náhodně vybraná z fondu registrací.

> [!NOTE]
> Pokud byl váš rozbočovač původně nakonfigurovaný s certifikátem Apple Sandbox a pak se přenakonfiguroval na použití produkčního certifikátu Apple, původní tokeny zařízení jsou neplatné. Neplatné tokeny způsobí selhání nabízených oznámení. Oddělení produkčních a testovacích prostředí a používání různých Center pro různá prostředí.

#### <a name="pns-credentials"></a>Přihlašovací údaje PNS

Při registraci mobilní aplikace portálu pro vývojáře platformy (například Apple nebo Google) se odesílají identifikátory aplikace a tokeny zabezpečení. Back-end aplikace poskytuje tyto tokeny PNS platformy, aby bylo možné odesílat nabízená oznámení do zařízení. Tokeny zabezpečení můžou být ve formě certifikátů (například Apple iOS nebo Windows Phone) nebo klíčů zabezpečení (například Google Android nebo Windows). Musí být nakonfigurovány v centrech oznámení. Konfigurace se obvykle provádí na úrovni centra oznámení, ale je možné ji také provést na úrovni oboru názvů ve scénáři s více klienty.

#### <a name="namespaces"></a>Obory názvů

Obory názvů lze použít pro seskupení nasazení. Můžou se také použít k reprezentaci všech Center oznámení pro všechny klienty stejné aplikace ve scénáři s více klienty.

#### <a name="geo-distribution"></a>Geografická distribuce

Geografická distribuce není vždy kritická ve scénářích nabízených oznámení. Různé PNSes (například APNs nebo FCM), které doručují nabízená oznámení do zařízení, nejsou rovnoměrně distribuovány.

Pokud máte aplikaci, která se používá globálně, můžete vytvořit centra v různých oborech názvů pomocí služby Notification Hubs v různých oblastech Azure po celém světě.

> [!NOTE]
> Toto uspořádání nedoporučujeme, protože zvyšuje náklady na správu, zejména pro registrace. Měla by se provést pouze v případě, že existuje explicitní nutnost.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Mám provést registraci z back-endu aplikace nebo přímo na klientských zařízeních?

Registrace z back-endu aplikace jsou užitečné v případě, že je nutné ověřit klienty před vytvořením registrace. Jsou také užitečné, pokud máte značky, které musí vytvořit nebo upravit pomocí back-endu aplikace na základě logiky aplikace. Další informace najdete v [pokynech k registraci back-endu] a na stránkách 2 – pokyny k registraci [back-endu] .

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co je model zabezpečení doručení nabízených oznámení?

Azure Notification Hubs používá model zabezpečení na základě [sdíleného přístupového podpisu](../storage/common/storage-sas-overview.md). Tokeny sdíleného přístupového podpisu můžete použít na úrovni kořenového oboru názvů nebo na úrovni podrobného centra oznámení. Tokeny sdíleného přístupového podpisu je možné nastavit tak, aby se lišily od různých autorizačních pravidel, například pro odesílání oprávnění zpráv nebo pro naslouchání oprávněním pro oznámení. Další informace najdete v dokumentu [modelu zabezpečení Notification Hubs] .

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak můžu v nabízených oznámeních zpracovat citlivou datovou část?

Všechna oznámení se doručují do cílových zařízení PNS platformy. Když se pošle oznámení do Azure Notification Hubs, zpracuje se a předává se příslušnému PNS.

Všechna připojení, od odesilatele k Azure Notification Hubs do PNS, používají protokol HTTPS.

> [!NOTE]
> Azure Notification Hubs neprotokoluje datovou část zpráv.

K posílání citlivých datových částí doporučujeme použít zabezpečený vzor nabízení. Odesílatel doručí oznámení s identifikátorem zprávy pomocí testu na zařízení bez citlivé datové části. Když aplikace v zařízení obdrží datovou část, aplikace volá zabezpečené rozhraní API přímo k načtení podrobností zprávy. Návod, jak tento model implementovat, najdete na stránce [Notification Hubs zabezpečený kurz nabízení oznámení] .

## <a name="operations"></a>Operace

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jaká podpora je k dispozici pro zotavení po havárii?

Poskytujeme na našem konci pokrytí metadat pro zotavení po havárii (Notification Hubs název, připojovací řetězec a další důležité informace). Když se spustí scénář zotavení po havárii, je registrační data *jediným segmentem* Notification Hubs infrastruktury, která se ztratí. K opětovnému naplnění těchto dat do nového centra po obnovení musíte implementovat řešení:

1. Vytvořte sekundární centrum oznámení v jiném datovém centru. Doporučujeme vytvořit jeden od začátku, abyste si ho převedli na událost zotavení po havárii, která může mít vliv na možnosti správy. Můžete ho také vytvořit v době události zotavení po havárii.

2. Používejte sekundární centrum oznámení v synchronizaci s primárním centrem oznámení pomocí jedné z následujících možností:

   * Použijte back-end aplikace, který současně vytvoří a aktualizuje instalace v obou centrech oznámení. Instalace umožňují zadat vlastní jedinečný identifikátor zařízení, což je vhodnější pro scénář replikace. Další informace najdete v tomto [ukázkovém kódu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Použijte back-end aplikace, který získá standardní výpis registrací z primárního centra oznámení jako zálohu. Pak může provést hromadné vložení do sekundárního centra oznámení.

Sekundární centrum oznámení může skončit s prošlými instalacemi nebo registrací. Když se nahraje na popisovač s vypršenou platností, Notification Hubs automaticky vyčistí přidružený záznam instalace nebo registrace na základě odpovědi přijaté na serveru PNS. Chcete-li vyčistit záznamy s vypršenou platností ze sekundárního centra oznámení, přidejte vlastní logiku, která zpracovává zpětnou vazbu od každého odeslání. Pak vypršení platnosti instalace/registrace v sekundárním centru oznámení.

Pokud nemáte back-end, aplikace při spuštění na cílových zařízeních provede novou registraci v sekundárním centru oznámení. Sekundární centrum oznámení bude mít zaregistrovaná všechna aktivní zařízení.

V případě, že zařízení s neotevřenými aplikacemi nebudou dostávat oznámení, bude toto časové období.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Jsou všechna moje data uložena v šifrované podobě?

Azure Notification Hubs šifruje všechna neaktivní zákaznická data s výjimkou registračních značek. Z tohoto důvodu byste neměli ukládat osobní nebo důvěrné údaje pomocí značek.

### <a name="is-there-audit-log-capability"></a>Je k dispozici možnost protokolu auditu?

Ano. Všechny operace správy Notification Hubs aktualizují protokol aktivit Azure, ke kterému se v [Azure Portal]zveřejňuje. Protokol aktivit Azure nabízí přehledy o operacích provedených u prostředků ve vašich předplatných. Pomocí protokolu aktivit můžete určit, kdo a kdy se mají u prostředků ve vašem předplatném provádět operace zápisu (PUT, POST, DELETE). Můžete také pochopit stav operací a dalších relevantních vlastností. Naopak. Protokol aktivit nezahrnuje operaci čtení (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

### <a name="what-troubleshooting-capabilities-are-available"></a>Jaké možnosti řešení potíží jsou k dispozici?

Azure Notification Hubs poskytuje několik funkcí pro řešení potíží, zejména v nejběžnějším scénáři odhozených oznámení. Podrobnosti najdete v dokumentu White Paper [věnovaném řešení potíží s Notification Hubs] .

### <a name="what-telemetry-features-are-available"></a>Jaké funkce telemetrie jsou k dispozici?

Azure Notification Hubs umožňuje zobrazit data telemetrie v [Azure Portal]. Podrobnosti o metrikách jsou k dispozici na stránce [Notification Hubs metriky] .

Můžete také programově přistupovat k metrikám. Další informace najdete v následujících článcích:

- [Načtěte metriky Azure monitor pomocí .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). V této ukázce se používá uživatelské jméno a heslo. Chcete-li použít certifikát, přetížením metody FromServicePrincipal poskytněte certifikát, jak je znázorněno v [tomto příkladu](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Získání metrik a protokolů aktivit pro prostředek](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Průvodce rozhraním REST API pro monitorování Azure](../azure-monitor/essentials/rest-api-walkthrough.md)

> [!NOTE]
> Úspěšná oznámení znamenají jednoduše, že nabízená oznámení byla doručena externímu PNS (například APNs pro iOS a macOS nebo FCM pro zařízení s Androidem). PNS je zodpovědný za doručování oznámení na cílová zařízení. PNS obvykle nevystavuje metriky doručení třetím stranám.  

[Azure Portal]: https://portal.azure.com
[Ceny Notification Hubs]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Rozhraní REST API pro Notification Hubs]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Pokyny k registraci back-endu]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Pokyny k registraci back-endu 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Model zabezpečení Notification Hubs]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Kurz zabezpečeného nabízení oznámení Notification Hubs]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Řešení potíží s Notification Hubs]: ./notification-hubs-push-notification-fixer.md
[Notification Hubs metriky]: ../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registrace – export/import]: ./export-modify-registrations-bulk.md
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
