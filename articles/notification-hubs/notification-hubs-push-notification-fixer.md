---
title: Diagnostika zrušených oznámení v Azure Notification Hubs
description: Naučte se diagnostikovat běžné problémy s vyřazenými oznámeními v Azure Notification Hubs.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: b5139f75084eb0646db2fc8b05b04aaf3ddb2a12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010779"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostika zrušených oznámení v Azure Notification Hubs

Častým dotazem o Azure Notification Hubs je řešení potíží, když se oznámení z aplikace neobjeví na klientských zařízeních. Zákazníci chtějí zjistit, kde a proč byla oznámení vyřazena, a jak tento problém vyřešit. Tento článek popisuje, proč by mohla být oznámení Vyřazená nebo nepřijímaná zařízeními. Vysvětluje také, jak určit hlavní příčinu.

Nejdřív je důležité porozumět tomu, jak Notification Hubs do zařízení doručovat oznámení.

![Architektura Notification Hubs][0]

V běžném toku oznámení o odesílání se zpráva pošle z *back-endu aplikace* do Notification Hubs. Notification Hubs zpracuje všechny registrace. Pro určení cílů bere v úvahu konfigurované značky a výrazy značek. Cíle jsou registrace, které potřebují přijímat nabízená oznámení. Tyto registrace můžou zahrnovat libovolnou z našich podporovaných platforem: Android, Baidu (zařízení s Androidem v Číně), iOS OS (Amazon) iOS, Windows a Windows Phone.

Po navázání cílů zaNotification Hubs *informování oznámení do služby nabízených oznámení* pro platformu zařízení. Mezi příklady patří služba APNs (Apple Push Notification Service) pro iOS a macOS a Firebase Cloud Messaging (FCM) pro zařízení s Androidem. Notification Hubs nabízená oznámení jsou rozdělená mezi několik dávek registrací. Ověřuje se přes příslušnou službu nabízených oznámení na základě přihlašovacích údajů, které jste nastavili v Azure Portal v části **Konfigurace centra oznámení**. Služba nabízených oznámení pak přepošle oznámení do příslušných *klientských zařízení*.

Poslední nožka doručování oznámení probíhá mezi službou nabízených oznámení platformy a zařízením. Doručení oznámení může selhat v kterékoli ze čtyř fází procesu nabízených oznámení (klient, back-end aplikace, Notification Hubs a služba nabízených oznámení platformy). Další informace o architektuře Notification Hubs najdete v tématu [Notification Hubs Overview].

Během prvotní fáze testu/přípravy může dojít k selhání při doručování oznámení. Vyřazená oznámení v této fázi můžou znamenat problém s konfigurací. Pokud dojde k selhání při doručování oznámení v produkčním prostředí, může dojít k vyřazení některých nebo všech oznámení. V tomto případě je v tomto případě uveden hlubší problém se vzorem aplikace nebo zasílání zpráv.

V další části najdete scénáře, ve kterých je možné vyřadit oznámení od běžných až po vzácná.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs konfigurace

Aby bylo možné odesílat oznámení do příslušné služby nabízených oznámení, Notification Hubs musí být ověřovány v kontextu vaší aplikace. Musíte vytvořit vývojářský účet pomocí služby oznámení cílové platformy (Microsoft, Apple, Google atd.). Pak musíte aplikaci zaregistrovat v operačním systému, kde získáte token nebo klíč, který používáte pro práci s cílovým PNS.

Do Azure Portal musíte přidat pověření platformy. Pokud zařízení nedosáhnou žádné oznámení, je prvním krokem ověření, že jsou v Notification Hubs nakonfigurované správné přihlašovací údaje. Přihlašovací údaje se musí shodovat s aplikací, která je vytvořená v rámci vývojářského účtu specifického pro platformu.

Podrobné pokyny k dokončení tohoto procesu najdete v tématu Začínáme [s Azure Notification Hubs].

Tady je několik běžných chybových konfigurací, které byste měli kontrolovat:

### <a name="notification-hub-name-location"></a>Umístění názvu centra oznámení

Ujistěte se, že název centra oznámení (bez překlepů) je stejný v každém z těchto umístění:

* Kam se zaregistrujete z klienta
* Místo odesílání oznámení z back-endu
* Místo, kde jste nakonfigurovali pověření služby nabízených oznámení

Ujistěte se, že jste na klientovi a v back-endu aplikace používali správné konfigurační řetězce pro sdílený přístupový podpis. Obecně je nutné použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** na back-endu aplikace. Tím se udělí oprávnění odesílat oznámení Notification Hubs.

### <a name="apn-configuration"></a>Konfigurace APN

Musíte udržovat dvě různá centra: jednu pro produkční prostředí a jinou pro testování. Certifikát, který použijete v prostředí izolovaného prostoru (sandbox), musíte nahrát do samostatného centra, než je certifikát nebo rozbočovač, který budete používat v produkčním prostředí. Nepokoušejte se nahrávat různé typy certifikátů do stejného rozbočovače. Způsobí selhání oznámení.

Pokud nechtěně nahrajete různé typy certifikátů do stejného rozbočovače, měli byste centrum odstranit a začít znovu s novým centrem. Pokud z nějakého důvodu nemůžete centrum odstranit, musíte aspoň odstranit všechny existující registrace z centra.

### <a name="fcm-configuration"></a>Konfigurace FCM

1. Zajistěte, aby byl *klíč serveru* , který jste získali ze Firebase, shodný s klíčem serveru, který jste zaregistrovali v Azure Portal.

   ![Firebase Server Key][3]

2. Ujistěte se, že jste na klientovi nakonfigurovali **ID projektu** . Hodnotu pro **ID projektu** můžete získat z řídicího panelu Firebase.

   ![ID projektu Firebase][1]

## <a name="application-issues"></a>Problémy s aplikací

### <a name="tags-and-tag-expressions"></a>Výrazy značek a značek

Použijete-li značky nebo výrazy značek k segmentaci cílové skupiny, je možné, že při odeslání oznámení nebude nalezen žádný cíl. Tato chyba je založena na zadaných značkách nebo výrazech značek v volání Send.

Zkontrolujte své registrace a zajistěte, aby se značky shodovaly při odeslání oznámení. Pak ověřte příjem oznámení pouze z klientů, kteří mají tyto registrace.

Předpokládejme například, že všechny registrace pomocí Notification Hubs používají značku "politika". Pokud pak odešlete oznámení se značkou "Sport", oznámení se nepošle na žádné zařízení. Složitý případ může zahrnovat výrazy značky, u kterých jste zaregistrováni pomocí "tag A" *nebo* "tag B", ale jste Cíleni na značku && tag b. " V části Tipy pro vlastní diagnostiku dále v článku se dozvíte, jak zkontrolovat registrace a jejich značky.

### <a name="template-issues"></a>Problémy s šablonou

Používáte-li šablony, ujistěte se, že jste postupují podle pokynů popsaných v tématu [šablony].

### <a name="invalid-registrations"></a>Neplatné registrace

Pokud centrum oznámení bylo nakonfigurováno správně a značky nebo výrazy značek byly použity správně, jsou nalezeny platné cíle. Oznámení by se měla odesílat těmto cílům. Notification Hubs pak v paralelním spuštění několika dávkových dávek. Každá dávka odesílá zprávy do sady registrací.

> [!NOTE]
> Vzhledem k tomu, že Notification Hubs zpracovává souběžné dávkové zpracování, není zaručeno pořadí, ve kterém jsou oznámení doručena.

Notification Hubs je optimalizován pro model doručení zpráv "" na nejvyšší úrovni ". Zkusíme odstranění duplicitních dat, aby se zařízení nedoručilo více než jednou. Registrace se kontrolují, aby se zajistilo, že se každému identifikátoru zařízení pošle jenom jedna zpráva, než se pošle do služby nabízených oznámení.

Každá dávka se odešle do služby nabízených oznámení, která zase přijme a ověří registrace. Během tohoto procesu je možné, že služba nabízených oznámení detekuje chybu s jednou nebo více registrací v dávce. Služba nabízených oznámení pak při Notification Hubs vrátí chybu a proces se zastaví. Služba nabízených oznámení tuto dávku ponechá kompletně. To je obzvláště pravdivé u služby APN, která používá protokol TCP Stream.

V takovém případě je registrace z databáze odebrána. Pak zopakujeme doručení oznámení pro zbývající zařízení v této dávce.

Pokud chcete získat další informace o chybách při neúspěšném pokusu o doručení proti registraci, můžete použít Notification Hubs rozhraní REST API [na telemetrii zpráv: získat telemetrii zpráv s oznámeními](/rest/api/notificationhubs/get-notification-message-telemetry) a [PNS zpětnou vazbu](/previous-versions/azure/reference/mt705560(v=azure.100)). Vzorový kód najdete v [příkladu odeslání REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problémy se službou nabízených oznámení

Jakmile služba nabízených oznámení obdrží oznámení, doručí oznámení zařízení. V tuto chvíli Notification Hubs nemá žádnou kontrolu nad doručením oznámení do zařízení.

Protože jsou služby upozorňování platformy robustní, oznámení v několika sekundách mají za následek přístup k zařízením. Pokud se služba nabízených oznámení omezuje, Notification Hubs použije exponenciální strategii pro zpětnou strategii. Pokud služba nabízených oznámení zůstane nedosažitelná po dobu 30 minut, je k dispozici zásada, která vyprší a trvale vynechává zprávy.

Pokud se služba nabízených oznámení pokusí doručovat oznámení, ale zařízení je v režimu offline, oznámení je uložené ve službě nabízených oznámení. Ukládá se jenom po omezené časové období. Oznámení se doručí do zařízení, jakmile bude zařízení k dispozici.

Každá aplikace ukládá jenom jedno nedávné oznámení. Pokud je v době, kdy je zařízení v režimu offline, posíláno více oznámení, každé nové oznámení způsobí, že poslední z nich bude zrušeno. Udržování pouze nejnovějšího oznámení *se nazývá sloučení* v APNs a *sbalení* v FCM. (FCM používá klíč sbalení.) Pokud zařízení po dlouhou dobu zůstane offline, budou oznámení uložená pro zařízení zahozena. Další informace najdete v tématu [Přehled služby APN] a [o zprávách FCM].

Pomocí Notification Hubs můžete předat slučovací klíč přes hlavičku HTTP pomocí obecného rozhraní SendNotification API. Například pro sadu .NET SDK můžete použít `SendNotificationAsync` . Rozhraní SendNotification API také přijímá hlavičky protokolu HTTP, které se předávají do příslušné služby nabízených oznámení.

## <a name="self-diagnosis-tips"></a>Tipy pro samočinnou diagnostiku

Tady jsou cesty pro diagnostiku hlavní příčiny odhozených oznámení v Notification Hubs.

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů

#### <a name="push-notification-service-developer-portal"></a>Portál pro vývojáře služby nabízených oznámení

Ověřte přihlašovací údaje v příslušném portálu pro vývojáře služby nabízených oznámení (APNs, FCM, Služba oznamování systému Windows atd.). Další informace najdete v tématu [kurz: posílání oznámení do Univerzální platforma Windows aplikací pomocí Azure Notification Hubs](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>portál Azure

Pokud chcete zkontrolovat přihlašovací údaje, které jste získali z portálu pro vývojáře služby nabízených oznámení, přejděte na kartu **zásady přístupu** v Azure Portal.

![Zásady přístupu Azure Portal][4]

### <a name="verify-registrations"></a>Ověřit registrace

#### <a name="visual-studio"></a>Visual Studio

V aplikaci Visual Studio se můžete připojit k Azure prostřednictvím Průzkumník serveru, abyste mohli zobrazit a spravovat několik služeb Azure, včetně Notification Hubs. Tato zkratka je primárně užitečná pro vývojové a testovací prostředí.

![Průzkumník serveru sady Visual Studio][9]

![Průzkumník serveru](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Můžete zobrazit a spravovat všechny registrace v centru. Registrace lze rozdělit do kategorií podle Platform, nativního nebo registračního zápisu, značky, identifikátoru nabízených oznámení, ID registrace a data vypršení platnosti. Na této stránce můžete také upravit registraci. To je zvlášť užitečné pro úpravy značek.

V **Průzkumník serveru**klikněte pravým tlačítkem na centrum oznámení a vyberte **Diagnostika**. 

![Visual Studio Průzkumník serveru: Diagnostika nabídky](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Zobrazí se následující stránka:

![Visual Studio: stránka diagnostiky](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Přepněte na stránku **registrace zařízení** :

![Visual Studio: registrace zařízení](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

K odeslání zprávy testovacího oznámení můžete použít stránku pro **odeslání testů** :

![Visual Studio: odeslání testu](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Pomocí sady Visual Studio můžete upravovat registraci pouze během vývoje a testování a s omezeným počtem registrací. Pokud potřebujete upravovat registrace hromadně, zvažte použití funkcí pro export a import, které jsou popsány v tématu [Postupy: Export a změna hromadných](/previous-versions/azure/azure-services/dn790624(v=azure.100))zápisů.

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Mnoho zákazníků používá [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer) k zobrazení a správě Center oznámení. Service Bus Explorer je open source projekt. 

### <a name="verify-message-notifications"></a>Ověření oznámení zprávy

#### <a name="azure-portal"></a>portál Azure

Pokud chcete vašim klientům poslat testovací oznámení, aniž byste museli ukončit a spustit službu back-end, vyberte v části **Podpora a řešení potíží**možnost **Odeslat test odeslat**.

![Testování funkcí Send v Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Můžete také odeslat testovací oznámení ze sady Visual Studio.

![Testování funkcí Send v aplikaci Visual Studio][10]

Další informace o použití Notification Hubs se sadou Visual Studio Průzkumník serveru najdete v těchto článcích:

* [Jak zobrazit registrace zařízení pro centra oznámení](/previous-versions/windows/apps/dn792122(v=win.10))
* [Hluboká podrobně: Visual Studio 2013 Update 2 RC a Azure SDK 2,3]
* [Oznamujeme vydání verze Visual Studio 2013 Update 3 a Azure SDK 2,4.]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění neúspěšných oznámení a kontrola výsledku oznámení

#### <a name="enabletestsend-property"></a>Vlastnost EnableTestSend

Když odešlete oznámení prostřednictvím Notification Hubs, oznámení se zpočátku zařadí do fronty. Notification Hubs určuje správné cíle a poté pošle oznámení službě nabízených oznámení. Pokud používáte REST API nebo kteroukoli z klientských sad SDK, vrátí volání odeslání pouze zprávu, že zpráva je zařazena do fronty pomocí Notification Hubs. Neposkytuje vám přehled o tom, co se stalo, když Notification Hubs nakonec oznámení odeslali službě nabízených oznámení.

Pokud oznámení v klientském zařízení nepřijde, může při Notification Hubs pokusu o doručení do služby nabízených oznámení došlo k chybě. Například velikost datové části může překročit maximum povolené službou nabízených oznámení nebo přihlašovací údaje nakonfigurované v Notification Hubs mohou být neplatné.

Pokud chcete získat přehled o chybách služby nabízených oznámení, můžete použít vlastnost [EnableTestSend] . Tato vlastnost je automaticky povolena při odesílání zkušebních zpráv z portálu nebo klienta sady Visual Studio. Tato vlastnost slouží k zobrazení podrobných informací o ladění a také prostřednictvím rozhraní API. V současné době je možné ji použít v sadě .NET SDK. Bude nakonec přidáno do všech klientských sad SDK.

Chcete-li použít `EnableTestSend` vlastnost se voláním REST, přidejte parametr řetězce dotazu s názvem *test* na konec volání odeslání. Například:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Příklad sady .NET SDK

Tady je příklad použití sady .NET SDK k odeslání nativního informačního oznámení (informační zprávy):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Na konci spuštění `result.State` jednoduše stavy `Enqueued` . Výsledky neposkytují žádný přehled o tom, co se stalo s nabízeným oznámením.

Dále můžete použít `EnableTestSend` vlastnost Boolean. `EnableTestSend`Při inicializaci použijte vlastnost, `NotificationHubClient` abyste získali podrobný stav o chybách služby nabízených oznámení, ke kterým dojde při odeslání oznámení. Volání Send trvá déle, než se vrátí, protože nejprve potřebuje Notification Hubs pro doručení oznámení službě nabízených oznámení.

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

Tato zpráva znamená, že přihlašovací údaje nakonfigurované v Notification Hubs jsou neplatné nebo že došlo k potížím s registracemi v centru. Odstraňte tuto registraci a umožněte klientovi, aby registraci znovu vytvořil před odesláním zprávy.

> [!NOTE]
> Použití `EnableTestSend` vlastnosti je silně omezené. Tuto možnost použijte pouze v prostředí pro vývoj a testování a s omezené sady registrací. Oznámení ladění se odesílají jenom na 10 zařízení. K dispozici je také omezení zpracování ladění na 10 za minutu.

### <a name="review-telemetry"></a>Kontrola telemetrie

#### <a name="azure-portal"></a>portál Azure

Na portálu můžete získat rychlý přehled o všech aktivitách v centru oznámení.

1. Na kartě **Přehled** uvidíte agregované zobrazení registrací, oznámení a chyb podle platformy.

   ![Řídicí panel přehledu Notification Hubs][5]

2. Na kartě **monitorování** můžete pro hlubší vzhled přidat mnoho dalších metrik specifických pro konkrétní platformu. Můžete se podívat na chyby, které se vrátí, když se Notification Hubs pokusí odeslat oznámení službě nabízených oznámení.

   ![Protokol aktivit Azure Portal][6]

3. Začněte tím, že zkontrolujete **příchozí zprávy**, **operace registrace**a **úspěšná oznámení**. Pak přejdete na kartu platforma na platformu, kde můžete zkontrolovat chyby, které jsou specifické pro službu nabízených oznámení.

4. Pokud není nastavení ověřování pro vaše centrum oznámení správné, zobrazí se **Chyba ověření PNS** zprávy. Je dobré se podívat, jak přihlašovací údaje služby nabízených oznámení kontrolovat.

#### <a name="programmatic-access"></a>Programový přístup

Další informace o programovém přístupu najdete v tématu [programový přístup](/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Několik funkcí souvisejících s telemetriemi, jako je export a import registrací a přístup k telemetrie prostřednictvím rozhraní API, jsou k dispozici pouze na úrovni služby Standard. Pokud se pokusíte použít tyto funkce z úrovně služby Free nebo Basic, zobrazí se zpráva o výjimce, pokud použijete sadu SDK. Pokud používáte funkce přímo z rozhraní REST API, zobrazí se chyba HTTP 403 (zakázáno).
>
> Chcete-li používat funkce související se telemetrie, nejprve zajistěte Azure Portal, že používáte úroveň služby Standard.  

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
[Přehled Notification Hubs]: notification-hubs-push-notification-overview.md
[Začínáme s Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Šablony]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[Přehled služby APN]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[O zprávách FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Hluboká podrobně: Visual Studio 2013 Update 2 RC a Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Oznamujeme vydání verze Visual Studio 2013 Update 3 a Azure SDK 2,4.]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
