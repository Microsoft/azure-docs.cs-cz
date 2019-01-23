---
title: Vyřadit oznámení diagnostiku Azure Notification Hubs
description: Zjistěte, jak diagnostikovat běžné problémy s vynechanými oznámeními ve službě Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 9dd6a66ea416ad61682b8e33c6163db3ac345d92
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447713"
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnostika vynechanými oznámeními ve službě Notification Hubs

Jedním z nejčastějších dotazů od zákazníků Azure Notification Hubs je řešení potíží při oznámení, které se odesílají z aplikace nejsou zobrazeny na klientských zařízeních. Chtějí vědět, kde a proč oznámení, byly vyřazeny a jak vyřešit problém. Tento článek identifikuje proč oznámení může získáte nebo nebudou přijímány zařízení. Zjistěte, jak analyzovat a určit hlavní příčinu.

Je důležité nejdřív pochopíte, jak službu Notification Hubs doručí oznámení do zařízení.

![Architektura Notification Hubs][0]

V toku typické odeslat oznámení, je zpráva odeslána z *back-endu aplikace* Notification hubs. Notification Hubs provádí nějaké zpracování na všechny registrace. Zpracování bere v úvahu nakonfigurované značky a výrazy označení k určení "cíle." Cíle jsou všechny registrace, které je potřeba dostat nabízené oznámení. Tyto registrace může zahrnovat některé nebo všechny naše podporované platformy: iOS, Google, Windows, Windows Phone, Kindle a Baidu pro China Android.

S cíli navázat, službu Notification Hubs nabízených oznámení upozornění *push notification service* pro platformu zařízení. Mezi příklady patří Apple Push Notification service (APNs) společnosti Apple a služby Firebase Cloud Messaging (FCM) pro Google. Notification Hubs nabízených oznámení upozornění rozdělit mezi několik dávek registrací. Notification Hubs se ověřuje pomocí služby příslušných nabízených oznámení na základě přihlašovacích údajů, které jste nastavili na webu Azure Portal, v části **Konfigurace centra oznámení**. Nabízené oznámení služby potom předává oznámení k funkcím *klientská zařízení*.

Odeslání oznámení do konečné fáze dojde mezi služby nabízených oznámení platformy a zařízení. Některé z těchto čtyř hlavních komponent v procesu nabízených oznámení (klient, back-endu aplikace, Notification Hubs a služby nabízených oznámení platformy) může způsobit oznámení chcete vyřadit. Další informace o architektuře Notification Hubs najdete v tématu [Přehled služby Notification Hubs].

Nedodání oznámení mohou nastat během počáteční fázi fáze/testování. Vynechanými oznámeními v této fázi může znamenat problém s konfigurací. Pokud dojde k selhání k poskytování oznámení v produkčním prostředí, může být vyřazen některá nebo všechna oznámení. V takovém případě je označeno hlubší aplikace nebo vzor problém pro zasílání zpráv.

Další části se probírají scénáře, ve kterých oznámení může dojít ke ztrátě, od běžné do více výjimečný.

## <a name="notification-hubs-misconfiguration"></a>Nesprávnou konfigurací ve službě Notification Hubs

Chcete-li úspěšně odesílat oznámení do služby příslušných nabízených oznámení, službu Notification Hubs potřebuje ke svému ověření v kontextu aplikace pro vývojáře. Pro tuto funkci používat vývojář vytvoří účet pro vývojáře s příslušné platformy (Google, Apple, Windows a tak dále). Vývojář potom zaregistruje jejich aplikace platformy, kde získají přihlašovací údaje.

Je nutné přidat platformy přihlašovací údaje k webu Azure portal. Pokud žádná oznámení jsou tam dostupné pro zařízení, prvním krokem by mělo být zajištění, že jsou správné přihlašovací údaje nakonfigurované ve službě Notification Hubs. Přihlašovací údaje musí odpovídat aplikace vytvořené v rámci účet pro vývojáře pro konkrétní platformu.

Podrobné pokyny k dokončení tohoto procesu najdete v tématu [Začínáme s Azure Notification Hubs].

Tady jsou některé běžné chybné konfigurace ke kontrole:

**Obecné:**

    * Ujistěte se, že název vašeho centra oznámení (bez překlepů) jsou stejné v každé z těchto umístění:
        * Lze uvést registraci od klienta.
        * Pokud odesílání oznámení z back-endu.
        * Pokud jste nakonfigurovali přihlašovací údaje služby nabízených oznámení.
    * Ujistěte se, že používáte řetězce správné sdíleného přístupového podpisu konfigurace na straně klienta a na back-endu aplikace. Obecně platí, je nutné použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** aplikace back-endu (uděluje oprávnění k odesílání oznámení Notification Hubs).

**Konfigurace služby APNs:**

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**FCM konfigurace:**

    1. Ujistěte se, že *klíč serveru* , který jste získali z Firebase odpovídající klíči serveru, které jste zaregistrovali na webu Azure Portal.

    ![Klíč serveru firebase][3]

    2. Ujistěte se, že jste nakonfigurovali **ID projektu** na straně klienta. Můžete získat hodnotu pro **ID projektu** z řídicího panelu Firebase.

    ![ID projektu firebase][1]

## <a name="application-issues"></a>Problémy s aplikací

**Značky a výrazy označení:**

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

** Problémy se šablonou: **

    If you use templates, ensure that you follow the guidelines described in [Templates].

**Neplatná registrace:**

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Nabízená oznámení problémy se službou

Po obdržení zprávy oznámení pomocí služby nabízených oznámení platformy, se odpovědnost služby nabízených oznámení pro doručení oznámení do zařízení. V tomto okamžiku službu Notification Hubs je mimo na obrázku a nemá žádnou kontrolu nad, když nebo oznámení je doručit do zařízení.

Protože službám oznamování platformy jsou odolnější, mají tendenci oznámení na zařízení ze služby nabízených oznámení za pár sekund. Pokud je omezování služby nabízených oznámení, Notification Hubs platí exponenciální regresní strategie. Pokud služba nabízených oznámení zůstanou nedostupné po dobu 30 minut, máme zásadu zajistit pro vypršení platnosti a trvale vyřadit tyto zprávy.

Pokud služba nabízených oznámení pokusí doručit oznámení, ale je zařízení offline, oznámení se ukládá pomocí služby nabízených oznámení po omezenou dobu. Oznámení se doručí do zařízení, když zařízení přestane být k dispozici.

Pro každou aplikaci se ukládají pouze jedno poslední upozornění. Pokud více oznámení se posílají, když je zařízení offline, každé nové oznámení způsobí, že předchozí oznámení budou zahozeny. Zachovat pouze nejnovější oznámení se označuje jako *slučovací oznámení* v APNs, a *sbalení* v FCM, (ta používá sbalení klíč). Pokud zařízení zůstane offline po dlouhou dobu, všechna naše oznámení, které byly uložené pro zařízení se zahodí. Další informace najdete v tématu [Přehled služby APN] a [O zpráv FCM].

Pomocí Azure Notification Hubs můžete předat slučovací klíč prostřednictvím hlavičky protokolu HTTP pomocí obecného rozhraní API SendNotification. Například pro sadu .NET SDK můžete využít `SendNotificationAsync`. Rozhraní API SendNotification také využívá hlavičky HTTP, které jsou předány jako-je služba příslušných nabízených oznámení.

## <a name="self-diagnosis-tips"></a>Tipy pro vlastní diagnózu

Tady jsou cesty pro diagnostiku původní příčinu možných vynechanými oznámeními ve službě Notification Hubs:

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů

**Portál pro vývojáře služby nabízených oznámení:**

Ověřte přihlašovací údaje v příslušných nabízená oznámení služby portálu pro vývojáře (APNs, FCM, Windows Notification Service a tak dále). Další informace najdete v tématu [Začínáme s Azure Notification Hubs].

**Azure portal:**

Ke kontrole a odpovídat přihlašovacím údajům s těmi, které jste získali z nabízených oznámení služby portálu pro vývojáře, na webu Azure Portal, přejděte **zásady přístupu** kartu.

![Zásady přístupu k webu Azure portal][4]

### <a name="verify-registrations"></a>Ověření registrace

**Visual Studio:**

Pokud používáte sadu Visual Studio pro vývoj, můžete připojit k Azure prostřednictvím Průzkumníka serveru pro zobrazení a správa několika služeb Azure, včetně Notification Hubs. To je užitečné především pro vaše prostředí pro vývoj/testování.

![Průzkumníka serveru Visual Studio][9]

Můžete zobrazit a spravovat všechny registrace ve vašem Centru zařazených do kategorií podle platformy, nativní nebo šablona registrace, všechny značky, identifikátor služby nabízených oznámení, ID registrace a datum vypršení platnosti. Můžete také upravit registraci na této stránce. To je užitečné zejména pro úpravy značky.

![Registrace zařízení sady Visual Studio][8]

> [!NOTE]
> Pomocí sady Visual Studio k úpravě registrace pouze během vývoje a testování a s omezený počet registrací. Pokud je potřeba upravit vaše registrace hromadně, zvažte použití export a import registrace funkcí popsaných v [Export a změny registrací hromadné](https://msdn.microsoft.com/library/dn790624.aspx).

**Service Bus Exploreru:**

Mnozí uživatelé používají [Service Bus Explorer] můžete zobrazit a spravovat své Centrum oznámení. Service Bus Exploreru je projekt open source. Ukázky najdete v tématu [Service Bus Exploreru kódu].

### <a name="verify-message-notifications"></a>Ověření zprávy s oznámením

 **Azure portal:**

Chcete-li odeslat testovací oznámení vašim klientům bez služby back-end provoz, v části **podpora a řešení potíží**vyberte **testovací odeslání**.

![Funkce odeslání testování v Azure][7]

**Visual Studio:**

Můžete také odeslat testovací oznámení ze sady Visual Studio.

![Testování funkce Odeslat v sadě Visual Studio][10]

Další informace o používání Notification Hubs pomocí Průzkumníka serveru Visual Studia najdete v těchto článcích:

* [Zobrazit registrace zařízení pro notification hubs]
* [Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]
* [Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění neúspěšných oznámení a zkontrolujte výsledek oznámení

**`EnableTestSend` Vlastnost:**

Při odesílání oznámení pomocí Notification Hubs, původně, je oznámení zařadí do fronty pro zpracování ve službě Notification Hubs. Notification Hubs určuje cíle, které správný a potom odešle oznámení služby nabízených oznámení. Pokud používáte rozhraní REST API nebo některou z klientské sady SDK, úspěšné návrat volání odesílání znamená, že pouze, že zpráva má se úspěšně zařadil do fronty pomocí Notification Hubs. Nemáte žádné přehled o tom, co se stalo při Notification Hubs nakonec odeslala zprávu do služby nabízených oznámení.

Pokud vaše oznámení nedorazí v klientském zařízení, je možné, že došlo k chybě při pokusu o doručení zprávy do služby nabízených oznámení pomocí Notification Hubs. Například velikost datové části může být delší než maximální povolenou služby nabízených oznámení nebo přihlašovacím údajům nakonfigurovaným v Notification Hubs může být neplatný.

Získat přehled o tom, nabízená oznámení služby chyby, můžete použít [EnableTestSend] vlastnost. Tato vlastnost je automaticky povolené, při odesílání zkušebních zpráv z klienta Visual Studio nebo portálu. Chcete-li zobrazit podrobné informace o ladění můžete použít tuto vlastnost. Můžete také použít vlastnost přes rozhraní API. V současné době můžete použít v sadě .NET SDK. Nakonec přidá se na všechny klientské sady SDK.

Použít `EnableTestSend` vlastnost s volání REST přidat parametr řetězce dotazu s názvem *testování* za účelem odeslání volání. Příklad:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Příklad (sadu .NET SDK):**

Tady je příklad použití sady .NET SDK k odesílání oznámení nativní automaticky otevírané okno (informační zpráva):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Na konci spuštění `result.State` jednoduše stavy `Enqueued`. Výsledky neposkytují žádné přehled o tom, co se stalo se nabízené oznámení.

V dalším kroku můžete použít `EnableTestSend` vlastnost typu Boolean. Použití `EnableTestSend` vlastnost při inicializaci `NotificationHubClient` získat podrobný stav o nabízená oznámení služby chyby, ke kterým dochází při odesílání oznámení. Odeslání volání bude vyžadovat čas navíc k vrátit, protože se vrátí jenom po Notification Hubs se doručí do služby nabízených oznámení, chcete-li zjistit výsledek oznámení.

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

**Ukázkový výstup:**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Tato zpráva znamená, že buď neplatné přihlašovací údaje jsou nakonfigurované v Notification Hubs, nebo se vyskytl problém s registrací v centru. Doporučujeme vám odstranit tuto registraci a nechat klienta znovu vytvořit registrace před odesláním zprávy.

> [!NOTE]
> Použití `EnableTestSend` vlastnost bude výrazně omezený. Tuto možnost použijte pouze v prostředí pro vývoj/testování a s omezenou sadu registrací. Jsme ladění oznámení odesílat jenom 10 zařízení. Máme také limit zpracování ladění odešle do 10 za minutu.

### <a name="review-telemetry"></a>Zkontrolujte telemetrická data

**Pomocí webu Azure portal:**

Na portálu můžete získat rychlý přehled o všech aktivit v centru oznámení.

1. Na **přehled** kartě, zobrazí souhrnný náhled na registraci, upozornění a chyb podle platformy.

    ![Řídicího panelu s přehledem Notification Hubs][5]

2. Na **monitorování** kartu, můžete přidat řadu jiné metriky specifické pro platformu pro podrobnější pohled. Můžete si prohlédnout konkrétně chyby související se služba nabízených oznámení, které jsou vráceny, když se pokusí službu Notification Hubs k odesílání oznámení do služby nabízených oznámení.

    ![Protokol aktivit Azure portal][6]

3. Začněte tím, že zkontrolujete **příchozí zprávy**, **registrace operace**, a **úspěšná oznámení**. Potom přejděte na kartu podle platformy ke kontrole chyb, které jsou specifické pro služby nabízených oznámení.

4. Pokud nastavení ověřování pro vaše Centrum oznámení je nesprávné, zprávy **chyba systému oznámení platformy ověřování** se zobrazí. Toto je dobrá indikace toho zkontrolujte přihlašovací údaje služby nabízených oznámení.

* **Programový přístup**

Další informace o programový přístup najdete v těchto článcích:

* [Telemetrie programový přístup]  
* [Telemetrie přístup přes rozhraní API vzorku]

> [!NOTE]
> Některé funkce související s telemetrií jako Export a import registrace a telemetrie přístup přes rozhraní API, jsou k dispozici pouze v rámci úrovně služeb Standard. Při pokusu o použití těchto funkcí z Free nebo Basic úroveň služby, obdržíte zprávu o výjimce, pokud používáte sadu SDK a chybu HTTP 403 (zakázáno), pokud používáte funkce přímo z rozhraní REST API.
>
> Pokud chcete používat funkce související s telemetrií, nejprve ověřte na webu Azure Portal, že používáte úrovně služeb Standard.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Přehled služby Notification Hubs]: notification-hubs-push-notification-overview.md
[Začínáme s Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[Přehled služby APN]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[O zpráv FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Exploreru kódu]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Zobrazit registrace zařízení pro notification hubs]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetrie programový přístup]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetrie přístup přes rozhraní API vzorku]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
