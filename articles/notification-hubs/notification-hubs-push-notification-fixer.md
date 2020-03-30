---
title: Diagnostika zrušených oznámení v azure notification hubech
description: Zjistěte, jak diagnostikovat běžné problémy s zrušenými oznámeními v Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657518"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostika zrušených oznámení v azure notification hubech

Častá otázka o Azure Notification Hubs je, jak řešit problémy, když oznámení z aplikace nezobrazují na klientských zařízeních. Zákazníci chtějí vědět, kde a proč byla oznámení zrušena a jak problém vyřešit. Tento článek identifikuje, proč oznámení může dostat vynechány nebo nejsou přijaty zařízeními. Také vysvětluje, jak určit hlavní příčinu.

Je důležité nejprve pochopit, jak Centra oznámení odesílá oznámení do zařízení.

![Architektura centra oznámení][0]

V typické odeslání oznámení toku zprávy je odeslána z *back-endu aplikace* do centra oznámení. Centra oznámení zpracuje všechny registrace. Bere v úvahu nakonfigurované značky a výrazy značek k určení cílů. Cíle jsou registrace, které je třeba obdržet nabízené oznámení. Tyto registrace mohou span všechny naše podporované platformy: Android, Baidu (Android zařízení v Číně), Fire OS (Amazon) iOS, Windows a Windows Phone.

Se stanovenými cíli centra oznámení odesílá oznámení do *služby nabízených oznámení* pro platformu zařízení. Mezi příklady patří služba Nabízených oznámení Apple (APNs) pro iOS a macOS a Firebase Cloud Messaging (FCM) pro zařízení se systémem Android. Centra oznámení odesílá oznámení rozdělit na více dávek registrací. Ověřuje se pomocí příslušné služby nabízených oznámení na základě přihlašovacích údajů nastavených na webu Azure Portal v části **Konfigurovat centrum oznámení**. Služba nabízených oznámení pak předá oznámení příslušným *klientským zařízením*.

Poslední část doručení oznámení je mezi službou nabízených oznámení platformy a zařízením. Doručení oznámení může selhat v kterékoli ze čtyř fází procesu nabízených oznámení (klient, back-end aplikace, centra oznámení a služba nabízených oznámení platformy). Další informace o architektuře Centra oznámení najdete v tématu [Přehled centra oznámení].

Selhání doručení oznámení může dojít během počáteční fáze testu/přípravy. Zrušená oznámení v této fázi může znamenat problém s konfigurací. Pokud dojde k selhání doručení oznámení v produkčním prostředí, některá nebo všechna oznámení mohou být zrušena. V tomto případě je uveden problém s hlubšíaplikací nebo vzorem zasílání zpráv.

V další části se zabývá scénáři, ve kterých může být zrušena oznámení, od běžných až po vzácné.

## <a name="notification-hubs-misconfiguration"></a>Chybná konfigurace center oznámení

Chcete-li odeslat oznámení do příslušné služby nabízených oznámení, centra oznámení musí ověřit sám v kontextu vaší aplikace. Musíte vytvořit vývojářský účet se službou oznámení cílové platformy (Microsoft, Apple, Google atd.). Potom je nutné zaregistrovat aplikaci s os, kde získáte token nebo klíč, který používáte pro práci s cílovým PNS.

Na portál Azure je nutné přidat přihlašovací údaje platformy. Pokud se k zařízení nedostanou žádná oznámení, prvním krokem je zajistit, aby byla v centru oznámení nakonfigurována správná pověření. Pověření musí odpovídat aplikaci, která je vytvořena pod vývojářský účet specifický pro platformu.

Podrobné pokyny k dokončení tohoto procesu najdete v tématu [Začínáme s Azure Notification Hubs].

Zde jsou některé běžné chybné konfigurace, které je třeba zkontrolovat:

### <a name="notification-hub-name-location"></a>Umístění názvu centra oznámení

Ujistěte se, že název centra oznámení (bez překlepů) je stejný v každém z těchto umístění:

* Kde se zaregistrujete z klienta
* Kde odesíláte oznámení ze zadního konce
* Kde jste nakonfigurovali pověření služby nabízených oznámení

Ujistěte se, že používáte správné řetězce konfigurace sdíleného přístupového podpisu v klientovi a back-endu aplikace. Obecně je nutné použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** na back-end aplikace. To uděluje oprávnění k odesílání oznámení do centra oznámení.

### <a name="apn-configuration"></a>Konfigurace APN

Je nutné udržovat dva různé rozbočovače: jeden pro produkční a druhý pro testování. Certifikát, který používáte v prostředí izolovaného prostoru, musíte nahrát do samostatného rozbočovače, než je certifikát nebo rozbočovač, který budete používat v produkčním prostředí. Nepokoušejte se nahrát různé typy certifikátů do stejného centra. To způsobí selhání oznámení.

Pokud neúmyslně nahrajete různé typy certifikátů do stejného centra, měli byste centrum odstranit a začít znovu s novým centrem. Pokud z nějakého důvodu nemůžete centrum odstranit, musíte alespoň odstranit všechny existující registrace z centra.

### <a name="fcm-configuration"></a>Konfigurace FCM

1. Ujistěte se, že *klíč serveru,* který jste získali z Firebase, odpovídá klíči serveru, který jste zaregistrovali na webu Azure Portal.

   ![Klíč serveru Firebase][3]

2. Ujistěte se, že jste na konfigurovali **ID projektu** na straně klienta. Hodnotu **ID projektu** můžete získat z řídicího panelu Firebase.

   ![ID projektu Firebase][1]

## <a name="application-issues"></a>Problémy s aplikacemi

### <a name="tags-and-tag-expressions"></a>Značky a výrazy značek

Pokud k segmentaci okruhu uživatelů používáte značky nebo výrazy značek, je možné, že při odeslání oznámení se nenašel žádný cíl. Tato chyba je založena na zadaných značkách nebo výrazech značek v odesílaném volání.

Zkontrolujte své registrace, abyste zajistili, že značky budou při odeslání oznámení odpovídat. Potom ověřte příjem oznámení pouze od klientů, kteří mají tyto registrace.

Předpokládejme například, že všechny vaše registrace pomocí center oznámení používají značku "Politika". Pokud pak odešlete oznámení se značkou "Sport", nebude odesláno na žádné zařízení. Složitý případ může zahrnovat výrazy značek, ve kterých jste se zaregistrovali pomocí označení A *nebo* "Značky B", ale zacílili jste na značku A && značka B. Část s tipy pro vlastní diagnostiku, která je uvedena dále v článku, ukazuje, jak zkontrolovat registrace a jejich značky.

### <a name="template-issues"></a>Problémy se šablonami

Pokud používáte šablony, ujistěte se, že dodržujete pokyny popsané v [části Šablony].

### <a name="invalid-registrations"></a>Neplatné registrace

Pokud bylo centrum oznámení správně nakonfigurováno a značky nebo výrazy značek byly použity správně, jsou nalezeny platné cíle. Oznámení by měla být zaslána na tyto cíle. Centra oznámení pak aktivuje několik výpočetních dávek paralelně. Každá dávka odesílá zprávy do sady registrací.

> [!NOTE]
> Vzhledem k tomu, že centra oznámení zpracovává dávky paralelně, pořadí, ve kterém jsou doručovány oznámení není zaručena.

Centra oznámení je optimalizována pro model doručování zpráv "na většině jednou". Pokoušíme se o odstranění duplicit, takže do zařízení nejsou doručována žádná oznámení více než jednou. Registrace jsou kontrolovány, aby bylo zajištěno, že pouze jedna zpráva je odeslána na identifikátor zařízení před odesláním do služby nabízených oznámení.

Každá dávka je odeslána do služby nabízených oznámení, která zase přijímá a ověřuje registrace. Během tohoto procesu je možné, že služba nabízených oznámení zjistí chybu s jednou nebo více registracemi v dávce. Služba nabízených oznámení pak vrátí chybu do centra oznámení a proces se zastaví. Služba nabízených oznámení tuto dávku úplně klesne. To platí zejména u apnů, které používají protokol datového proudu Protokolu TCP.

V tomto případě chybující registrace je odebrána z databáze. Potom zopakujíme doručení oznámení pro zbytek zařízení v této dávce.

Chcete-li získat další informace o chybě o neúspěšném pokusu o doručení proti registraci, můžete použít rozhraní REST API oznámení [na zprávu telemetrie: Získat telemetrii zpráv oznámení](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) a [zpětnou vazbu PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Ukázkový kód naleznete v [příkladu Odeslat REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problémy se službou nabízených oznámení

Poté, co služba nabízených oznámení obdrží oznámení, doručí oznámení do zařízení. V tomto okamžiku Centra oznámení nemá žádnou kontrolu nad doručení oznámení do zařízení.

Vzhledem k tomu, že služby oznámení platformy jsou robustní, oznámení mají tendenci oslovit zařízení během několika sekund. Pokud služba nabízených oznámení je omezení, Centra oznámení použije exponenciální back-off strategie. Pokud služba nabízených oznámení zůstane nedostupná po dobu 30 minut, je zavedena zásada, která vyprší a zprávy trvale vypustí.

Pokud se služba nabízených oznámení pokusí doručit oznámení, ale zařízení je offline, oznámení je uloženo službou nabízených oznámení. Je uložen pouze po omezenou dobu. Oznámení se dozobrazí zařízení, jakmile bude zařízení k dispozici.

Každá aplikace ukládá pouze jedno nedávné oznámení. Pokud je v době, kdy je zařízení offline, odesíláno více oznámení, každé nové oznámení způsobí, že poslední oznámení bude zahozeno. Zachování pouze nejnovější oznámení se nazývá *koalescing* v APN s *a sbalení* v FCM. (FCM používá sbalící klíč.) Pokud zařízení zůstane offline po dlouhou dobu, oznámení, která byla uložena pro zařízení, jsou zahozena. Další informace naleznete v [tématech Přehled apns] a [o zprávách FCM].

Pomocí centra oznámení můžete předat slučovací klíč přes hlavičku HTTP pomocí obecného rozhraní API SendNotification. Například pro .NET SDK byste použili `SendNotificationAsync`. Rozhraní API SendNotification také přebírá hlavičky HTTP, které jsou předány jako je do příslušné služby nabízených oznámení.

## <a name="self-diagnosis-tips"></a>Tipy pro vlastní diagnózu

Tady jsou cesty k diagnostice hlavní příčiny zrušených oznámení v centru oznámení.

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů

#### <a name="push-notification-service-developer-portal"></a>Portál pro vývojáře služby nabízených oznámení

Ověřte pověření na příslušném portálu pro vývojáře služby nabízených oznámení (APNs, FCM, Windows Notification Service a tak dále). Další informace najdete [v tématu Výuka: Odesílání oznámení do aplikací univerzální platformy Windows pomocí Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>portál Azure

Pokud chcete zkontrolovat a porovnat přihlašovací údaje s přihlašovacími údaji získanými z portálu pro vývojáře služby nabízených oznámení, přejděte na kartu **Zásady přístupu** na webu Azure Portal.

![Zásady přístupu k portálu Azure Portal][4]

### <a name="verify-registrations"></a>Ověření registrací

#### <a name="visual-studio"></a>Visual Studio

Ve Visual Studiu se můžete připojit k Azure přes Průzkumník serveru a zobrazit a spravovat více služeb Azure, včetně center oznámení. Tato zkratka je užitečná především pro vývojové/testovací prostředí.

![Průzkumník serveru Sady Visual Studio][9]

![Průzkumník serveru](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Můžete zobrazit a spravovat všechny registrace ve vašem centru. Registrace lze kategorizovat podle registrace platformy, nativní nebo šablony, značky, identifikátoru služby nabízených oznámení, ID registrace a data vypršení platnosti. Můžete také upravit registraci na této stránce. To je užitečné zejména pro úpravy značek.

Klepněte pravým tlačítkem myši na centrum oznámení v **Průzkumníkovi serveru**a vyberte **příkaz Diagnostikovat**. 

![Průzkumník serveru Visual Studio: Nabídka Diagnostikovat](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Zobrazí se následující stránka:

![Visual Studio: Stránka diagnostiky](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Přepněte na stránku **Registrace zařízení:**

![Visual Studio: Registrace zařízení](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Pomocí stránky **Test Send** můžete odeslat zprávu s oznámením testu:

![Visual Studio: Testování odesílání](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Visual Studio slouží k úpravám registrací pouze během vývoje/testování a s omezeným počtem registrací. Pokud potřebujete hromadně upravit registrace, zvažte použití funkce exportu a importu registrace popsané v [části Postup: Export a úpravy registrací hromadně](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Mnoho zákazníků používá [Aplikaci Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) k zobrazení a správě svých center oznámení. Service Bus Explorer je open source projekt. 

### <a name="verify-message-notifications"></a>Ověření oznámení o zprávách

#### <a name="azure-portal"></a>portál Azure

Chcete-li klientům odeslat testovací oznámení, aniž byste měli službu, aby byla spuštěna, vyberte v části **PODPORA + PORADCE PŘI POTÍŽÍCH**možnost Testovat **odeslat**.

![Testování funkcí odesílání v Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Můžete také odeslat testovací oznámení z visual studia.

![Testování funkcí odesílání v sadě Visual Studio][10]

Další informace o používání center oznámení v Průzkumníkovi serveru Sady Visual Studio naleznete v těchto článcích:

* [Jak zobrazit registrace zařízení pro centra oznámení](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]
* [Oznámení vydání Visual Studia 2013 Update 3 a Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění neúspěšných oznámení a výsledek oznámení o kontrole

#### <a name="enabletestsend-property"></a>EnableTestSend, vlastnost

Když odešlete oznámení prostřednictvím centra oznámení, oznámení je zpočátku zařazendo fronty. Centra oznámení určí správné cíle a potom odešle oznámení službě nabízených oznámení. Pokud používáte rozhraní REST API nebo některý z klientských sad SDK, vrácení volání odeslat znamená pouze, že zpráva je zařazena do fronty s centra oznámení. Neposkytuje přehled o tom, co se stalo, když centra oznámení nakonec odeslala oznámení službě nabízených oznámení.

Pokud vaše oznámení nedorazí do klientského zařízení, mohlo dojít k chybě, když se ho centra oznámení pokusila doručit do služby nabízených oznámení. Velikost datové části může například překročit maximální povolenou službou nabízených oznámení nebo pověření nakonfigurovaná v centrech oznámení mohou být neplatná.

Chcete-li získat přehled o chybách služby nabízených oznámení, můžete použít [Vlastnost EnableTestSend.] Tato vlastnost je automaticky povolena při odesílání testovacích zpráv z portálu nebo klienta sady Visual Studio. Tuto vlastnost můžete použít k zobrazení podrobných informací o ladění a také prostřednictvím api. V současné době jej můžete použít v sdk .NET. Nakonec bude přidána do všech sad SDK klienta.

Chcete-li `EnableTestSend` použít vlastnost s voláním REST, přidejte parametr řetězce dotazu nazvaný *test* na konec volání odeslat. Například:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Příklad sady .NET SDK

Tady je příklad použití sady .NET SDK k odeslání nativního automaticky otevíraného oznámení:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Na konci popravy, `result.State` jednoduše `Enqueued`uvádí . Výsledky neposkytují žádný přehled o tom, co se stalo s nabízeným oznámením.

Dále můžete použít `EnableTestSend` logickou vlastnost. Pomocí `EnableTestSend` této vlastnosti při `NotificationHubClient` inicializaci získáte podrobný stav o chybách služby nabízených oznámení, ke kterým dochází při odeslání oznámení. Odeslání volání trvá další čas vrátit, protože nejprve potřebuje centra oznámení doručit oznámení do služby nabízených oznámení.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Ukázkový výstup

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Tato zpráva označuje, že pověření nakonfigurovaná v centru oznámení jsou neplatná nebo že došlo k problému s registracemi v centru. Odstraňte tuto registraci a nechte klienta znovu vytvořit registraci před odesláním zprávy.

> [!NOTE]
> Použití vlastnosti `EnableTestSend` je silně omezena. Tuto možnost použijte pouze ve vývojovém/testovacím prostředí a s omezenou sadou registrací. Oznámení o ladění jsou odesílána pouze na 10 zařízení. K dispozici je také limit pro zpracování ladění odešle, na 10 za minutu.

### <a name="review-telemetry"></a>Zkontrolovat telemetrii

#### <a name="azure-portal"></a>portál Azure

Na portálu můžete získat rychlý přehled o všech aktivitách v centru oznámení.

1. Na kartě **Přehled** můžete zobrazit agregované zobrazení registrací, oznámení a chyb podle platformy.

   ![Řídicí panel přehledu center oznámení][5]

2. Na kartě **Monitor** můžete přidat mnoho dalších metrik specifických pro platformu pro hlubší pohled. Můžete se podívat konkrétně na chyby, které jsou vráceny při oznámení centra se pokusí odeslat oznámení služby nabízených oznámení.

   ![Protokol aktivit na portálu Azure][6]

3. Začněte kontrolou **příchozích zpráv**, **registračních operací**a **úspěšných oznámení**. Potom přejděte na kartu pro jednotlivé platformy a zkontrolujte chyby, které jsou specifické pro službu nabízených oznámení.

4. Pokud je nastavení ověřování pro centrum oznámení nesprávné, zobrazí se zpráva **Chyba ověřování služby PNS.** Je to dobrý údaj pro kontrolu pověření služby nabízených oznámení.

#### <a name="programmatic-access"></a>Programový přístup

Další informace o programovém přístupu naleznete [v tématu Programový přístup](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Několik funkcí souvisejících s telemetrií, jako je export a import registrací a přístup u telemetrie prostřednictvím rozhraní API, je k dispozici pouze na úrovni služby Standard. Pokud se pokusíte použít tyto funkce z úrovně free nebo základní služby, zobrazí se zpráva o výjimce, pokud používáte sadu SDK. Pokud používáte funkce přímo z rozhraní REST API, zobrazí se chyba HTTP 403 (Forbidden).
>
> Chcete-li používat funkce související s telemetrií, nejprve se ujistěte, že na webu Azure Portal používáte úroveň služby Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Přehled center oznámení]: notification-hubs-push-notification-overview.md
[Začínáme s Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Šablony]: https://msdn.microsoft.com/library/dn530748.aspx
[Přehled přístupových přístupových ponesek]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[O zprávách FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Oznámení vydání Visual Studia 2013 Update 3 a Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[Povolitodeslání testu]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
