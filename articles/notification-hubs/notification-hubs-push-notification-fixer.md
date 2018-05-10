---
title: Služba Azure Notification Hubs vyřadit diagnostiku oznámení
description: Zjistěte, jak diagnostikovat běžné problémy s vynechaných oznámení v Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bc9ef70560f0485da81c1f54aa955cee76d280ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnostika vynechaných oznámení v Notification Hubs

Jedním z nejčastějších dotazů od zákazníků Azure Notification Hubs je řešení problémů s při nezobrazí oznámení odesílaných z aplikace na klientských zařízeních. Chtějí vědět, kde a proč byly vyřazeny oznámení a informace o vyřešení problému. V tomto článku jsou uvedeny proč oznámení může získat vyřadit nebo nebyla přijata zařízení. Zjistěte, jak analyzovat a určit hlavní příčinu. 

Je důležité nejdřív pochopíte, jak služba Notification Hubs doručí oznámení do zařízení.

![Architektura centra oznámení][0]

V toku typické odesílání oznámení, je zpráva odeslána z *back-end aplikace* do centra oznámení. Centra oznámení nepodporuje některé zpracování na všechny registrace. Zpracování bere v úvahu nakonfigurované značky a značky výrazy k určení "cíle." Cíle jsou všechny registrace, které je potřeba příjem nabízených oznámení. Tyto registrace může zahrnovat všechny nebo naše podporovaných platforem: iOS, Google, Windows, Windows Phone, Kindle a Baidu pro Android v Číně.

S cíle navázat, služba Notification Hubs doručí oznámení *nabízená oznámení služby* pro platformu zařízení. Mezi příklady patří služby Apple Push Notification (APNs) Apple a zasílání zpráv cloudu Firebase (FCM) pro Google. Oznámení centra oznámení oznámení rozdělit do několika dávek registrací. Centra oznámení ověřuje službou příslušných nabízená oznámení na základě pověření, která jste nastavili na portálu Azure v části **Konfigurace centra oznámení**. Nabízená oznámení služby pak dopředný oznámení k příslušné *klientských zařízení*. 

Konečné fáze doručení dochází mezi služby nabízených oznámení platformy a zařízení. Všechny čtyři hlavní součásti v procesu nabízené oznámení (klient, back-end aplikace, centra oznámení a službu platformy push notification service) může způsobit oznámení, které chcete vyřadit. Další informace o architektuře Notification Hubs najdete v tématu [Notification Hubs přehled].

Nepodařilo se doručit oznámení mohou nastat během počáteční testovací/pracovní fáze. Vyřazené oznámení v této fázi může znamenat problém konfigurace. Pokud dojde k selhání doručovat oznámení v provozním prostředí, může být vyřazen některá nebo všechna oznámení. V takovém případě je uvedené hlubší aplikace nebo vzor problém pro zasílání zpráv. 

V další části zjistí scénáře, ve kterých oznámení může dojít ke ztrátě, od běžné až více výjimečných.

## <a name="notification-hubs-misconfiguration"></a>Chybné konfigurace centra oznámení
Úspěšně odesílat oznámení do služby příslušných nabízených oznámení, služba Notification Hubs potřebuje ke svému ověření v kontextu pro vývojáře aplikace. Pro tuto funkci používat vytvoří vývojář vývojářského účtu s příslušné platformy (Google, Apple, Windows a tak dále). Vývojář pak zaregistruje jejich používání s platformou, kde získat přihlašovací údaje. 

Přihlašovací údaje platformy je nutné přidat na portál Azure. Pokud nejsou žádná oznámení se blíží zařízení, musí být prvním krokem k zajištění, že správné přihlašovací údaje jsou nakonfigurované v Notification Hubs. Přihlašovací údaje musí odpovídat aplikaci, která se vytvoří pod účtem vývojáře specifické pro platformu. 

Podrobné pokyny k dokončení tohoto procesu naleznete v tématu [Začínáme s Azure Notification Hubs].

Zde jsou některé běžné chybné konfigurace zkontrolujte:

* **Obecné**
   
    * Zajistěte, aby vaším jménem centra oznámení (bez překlepům) stejný v každé z těchto umístění:

        * Při registraci od klienta.
        * Kde odesílání oznámení z back-end.
        * Kam jste nakonfigurovali pověření služby nabízených oznámení.
    
    * Ujistěte se, že používáte řetězce správné sdíleného přístupového podpisu konfigurace na straně klienta a na back-end aplikace. Obecně platí, musíte použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** na aplikaci back-end (uděluje oprávnění k odesílání oznámení Centra oznámení).

* **Konfigurace služby APN**
   
    Musíte udržovat dva různé rozbočovače: jeden rozbočovače pro produkční prostředí a jiné centra pro testování. To znamená, že musíte nahrát certifikát, který používáte v prostředí izolovaného prostoru k rozbočovači samostatné než certifikát a rozbočovače, který budete používat v produkčním prostředí. Nemáte zkuste nahrát různé typy certifikátů na stejném centru. To může způsobit selhání oznámení. 
    
    Pokud nechtěně nahrajete různé typy certifikátů na stejném centru, doporučujeme odstranit rozbočovače a začít pracovat s nového centra. Pokud z nějakého důvodu nelze odstranit rozbočovače, minimálně, je nutné odstranit všechny existující registrace z rozbočovače. 

* **Konfigurace FCM** 
   
    1. Ujistěte se, že *klíč serveru* který jste získali od Firebase shoduje s klíčem serveru, který jste zaregistrovali na webu Azure portal.
   
    ![Klíč serveru firebase][3]
   
    2. Ujistěte se, že jste nakonfigurovali **ID projektu** na straně klienta. Můžete získat hodnotu **ID projektu** na řídicím panelu Firebase.
   
    ![ID firebase projektu][1]

## <a name="application-issues"></a>Problémy s aplikací
* **Značky a značky výrazy**

    Pokud používáte značky nebo značky výrazy segmentovat cílovou skupinu, je možné, že při odesílání oznámení žádný cíl je na základě značky nebo značky výrazů, které zadáte v odesílání volání. 
    
    Zkontrolujte vaše registrace do zajistěte, aby odpovídající značky při odesílání oznámení. Ověřte, přijetí oznámení pouze z klientů, které mají tyto registrace. 
    
    Jako příklad Pokud byly provedeny všechny registrace pomocí Notification Hubs pomocí značky "Politika" a odesílání oznámení pomocí značky "Sportu," neposílají oznámení pro všechna zařízení. Komplexní případu mohou zahrnovat značky výrazy, ve kterých jste zaregistrovali pomocí "Značky A" nebo "Značky B", ale při odesílání oznámení, cíle "Značky A a a značky B." V části Tipy sebetestování později v článku jsme ukazují, jak zkontrolovat vaše registrace a jejich značky. 

* **Problémy se šablonou**

    Pokud používáte šablony, ujistěte se, postupujte podle pokynů popsaných v [šablony]. 

* **Neplatný registrace**

    Pokud do centra oznámení byla nakonfigurována správně, a pokud všechny značky nebo výrazy, značky se používaly správně, platný cíle nebyly nalezeny. Oznámení by měly být odeslány na tyto cíle. Služba Notification Hubs poté vyvolá několik zpracování dávky paralelně. Každé dávky odesílá zprávy sadu registrací. 

    > [!NOTE]
    > Protože je zpracovávána paralelně, pořadí, ve kterém oznámení se doručují není zaručena. 

    Centra oznámení je optimalizovaná pro model doručení zprávy "jednou na většinu". Jsme pokus odstranění duplicit, tak, aby žádná oznámení se doručují více než jednou na zařízení. Aby, jsme Zkontrolujte registrace a ujistěte se, že pouze jedna zpráva je odeslaných za identifikátor zařízení předtím, než odešle zprávu služby nabízených oznámení. 

    Každé dávky posílá službu nabízených oznámení, která zase je přijetí a ověření registrace, je možné, že služba nabízených oznámení zjistí chybu s jedním nebo více registrace v dávce. V takovém případě služby nabízených oznámení do centra oznámení chybovou zprávu a zastaví proces. Služba nabízených oznámení zahodí této batch úplně. To platí hlavně službou APNS, který používá protokol TCP datového proudu. 

    Jsme jsou optimalizované pro na většinu po doručení. Ale v takovém případě chybující registrace odstraněna z databáze. Opakujte jsme doručení pro zbytek zařízení v této dávce.

    Chcete-li získat další informace o selhání doručení pokus proti registrace, můžete použít rozhraní API REST centra oznámení [za zpráva Telemetrie: získat telemetrické zprávy oznámení](https://msdn.microsoft.com/library/azure/mt608135.aspx) a [zpětnou vazbu systém PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Ukázkový kód, najdete [příklad odesílání REST](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Nabízená oznámení problémů služby
Po obdržení zprávy oznámení pomocí služby nabízených oznámení platformy, je zodpovědností služby nabízených oznámení pro doručení oznámení do zařízení. Služba Notification Hubs v tomto okamžiku je mimo na obrázku a nemá žádnou kontrolu nad, když nebo oznámení je doručit do zařízení. 

Protože oznámení služby platformy robustní, zpravidla oznámení k dosažení zařízení ze služby nabízených oznámení za několik sekund. Pokud službu nabízených oznámení je omezování, platí Notification Hubs exponenciální strategie back vypnout. Pokud služby nabízených oznámení zůstane nedostupný 30 minut, jsme zavedené zásady vypršení platnosti a trvale vyřadit tyto zprávy. 

Pokud se pokusí doručit oznámení služby nabízených oznámení, ale zařízení je offline, ukládá oznámení služby nabízených oznámení po omezenou dobu. Oznámení je doručit do zařízení, když zařízení je dostupná. 

Pro každou aplikaci se ukládají pouze jedno oznámení. poslední. Pokud více oznámení se odesílají, když je zařízení offline, každé nové oznámení způsobí, že předchozí oznámení budou zahozeny. Zachování pouze nejnovější oznámení se označuje jako *slučování oznámení* v APNs a *sbalení* v FCM (který se používá ztenčeného klíč). Pokud zařízení zůstane v režimu offline delší dobu, žádné oznámení, které jste právě uložili pro zařízení se zahodí. Další informace najdete v tématu [APNs přehled] a [zprávy o FCM].

Pomocí Azure Notification Hubs můžete předat slučování klíč prostřednictvím záhlaví HTTP pomocí rozhraní API obecné SendNotification. Například pro .NET SDK služby byste použili **SendNotificationAsync**. Rozhraní API SendNotification také trvá hlavičky protokolu HTTP, které se předávají jako-je služba odpovídající nabízených oznámení. 

## <a name="self-diagnosis-tips"></a>Sebetestování tipy
Zde jsou cesty k určení kořenové příčiny vynechaných oznámení v Notification Hubs:

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů
* **Portál pro vývojáře služby nabízených oznámení**
   
    Ověřte přihlašovací údaje v příslušných nabízená oznámení služby portál pro vývojáře (APNs, FCM, oznámení služby systému Windows a tak dále). Další informace najdete v tématu [Začínáme s Azure Notification Hubs].

* **Azure Portal**
   
    Zkontrolujte a shodovat s pověřeními s těmi, které jste získali z nabízené oznámení služby portálu pro vývojáře na portálu Azure přejděte na **zásady přístupu** kartě. 
   
    ![Portál Azure zásady přístupu][4]

### <a name="verify-registrations"></a>Ověření registrace
* **Visual Studio**
   
    Pokud používáte Visual Studio pro vývoj, můžete připojit k Azure pomocí Průzkumníka serveru můžete zobrazit a spravovat více služeb Azure, včetně centra oznámení. To je užitečné hlavně pro vaše prostředí pro vývoj/testování. 
   
    ![Průzkumníka serveru Visual Studio][9]
   
    Můžete zobrazit a spravovat všechny registrace ve službě hub zařazené do kategorie podle platformy, nativní nebo šablony registrace, všechny značky, identifikátor služby nabízených oznámení, ID registrace a datum vypršení platnosti. Můžete taky upravit registraci na této stránce. To je obzvláště užitečné pro úpravy značky. 
   
    ![Registrace zařízení sady Visual Studio][8]
   
   > [!NOTE]
   > Upravit registrace pouze během vývoje/testování a s omezený počet registrací pomocí sady Visual Studio. Pokud potřebujete upravit vaše registrace hromadně, zvažte použití exportu a importu registrace funkcí popsaných v [exportovat a upravovat registrace hromadně](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Service Bus Explorer**
   
    Mnoho zákazníků použít [Service Bus Explorer] zobrazení a správa jejich centra oznámení. Service Bus Explorer je otevřený projekt. Ukázky najdete [Service Bus Explorer kód].

### <a name="verify-message-notifications"></a>Ověřte oznamování pomocí zpráv
* **Azure Portal**
   
    Odeslat zkušební oznámení pro klienty bez nutnosti služby back-end fungovaly, v části **podporu + Poradce při potížích s**, vyberte **testovací odeslání**. 
   
    ![Funkce odeslání testu v Azure][7]
* **Visual Studio**
   
    Ze sady Visual Studio můžete také odeslat testovací oznámení.
   
    ![Test odeslání funkce v sadě Visual Studio][10]
   
    Další informace o Notification Hubs pomocí Průzkumníka serveru Visual Studia najdete v těchto článcích: 
   
   * [Zobrazit registrace zařízení pro centra oznámení]
   * [Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]
   * [Uvedení verzi Visual Studio 2013 Update 3 a Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění chybných oznámení a zkontrolujte výsledek oznámení
**Vlastnost EnableTestSend**

Nejdřív odešlete oznámení prostřednictvím centra oznámení, oznámení je zařadit do fronty pro zpracování v Notification Hubs. Centra oznámení určuje správné cíle a poté odešle oznámení do služby nabízených oznámení. Pokud používáte rozhraní REST API nebo některou z klienta sady SDK, úspěšné návrat volání odesílání znamená jen, zpráva se úspěšně zařazen do fronty pomocí Notification Hubs. Nemáte žádné aspekty co se stalo při Notification Hubs nakonec odeslána zpráva do služby nabízených oznámení. 

Pokud není oznámení přicházejí na klientském zařízení, je možné, že došlo k chybě při pokusu o doručení zprávy do služby nabízených oznámení pomocí centra oznámení. Například velikost datové části pravděpodobně přesahuje maximální povolenou službu nabízených oznámení, nebo přihlašovací údaje nakonfigurované v Notification Hubs může být neplatný. 

Chcete-li získat přehled o nabízená oznámení služby chyby, můžete použít [EnableTestSend] vlastnost. Tato vlastnost je automaticky povolené, při odesílání zkušebních zpráv z portálu nebo klienta Visual Studia. Chcete-li zobrazit podrobné informace o ladění můžete tuto vlastnost. Můžete také použít vlastnost prostřednictvím rozhraní API. V současné době můžete použít v sadě SDK .NET. Nakonec bude přidána na všechny klientské sady SDK. 

Použít **EnableTestSend** vlastnost s volání REST připojit parametr řetězce dotazu s názvem *testování* na konec volání odesílání. Příklad: 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Příklad (.NET SDK)**

Tady je příklad pomocí sady .NET SDK k odesílání oznámení nativní automaticky otevírané okno (informační):

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

Na konci provádění **výsledek. Stav** jednoduše stavy **zařazených do fronty**. Výsledky neposkytují všechny aspekty co se stalo s nabízených oznámení. 

Potom můžete použít **EnableTestSend** vlastnost typu Boolean. Použití **EnableTestSend** vlastnost při inicializaci **NotificationHubClient** získat podrobný stav o nabízená oznámení služby chyb, které nastat při odesílání oznámení. Volání odesílání bude vyžadovat čas navíc vrátit vzhledem k tomu, že se vrátí jenom po Notification Hubs má doručit oznámení ke službě nabízených oznámení a určit výsledek. 

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

**Ukázkový výstup**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Tato zpráva znamená, že buď neplatné přihlašovací údaje jsou nakonfigurované v Notification Hubs, nebo se vyskytl problém s registrací v centru. Doporučujeme, abyste tato registrace odstranit a znovu vytvořit registrace před odesláním zprávy klienta. 

> [!NOTE]
> Použití **EnableTestSend** vlastnost výraznou je omezen. Tuto možnost použijte pouze v prostředí pro vývoj/testování a s omezenou sadou registrací. Odešleme ladění oznámení na 10 pouze zařízení. Máme také limit zpracování ladění zasílá do 10 za minutu. 
> 
> 

### <a name="review-telemetry"></a>Zkontrolujte telemetrie
* **Použití portálu Azure Portal**
   
    Na portálu můžete získat rychlý přehled všechny aktivity v centru oznámení. 
   
    1. Na **přehled** kartě uvidíte agregovaným zobrazením registrace, upozornění a chyby platformou. 
   
        ![Řídicí panel Přehled centra oznámení][5]
   
    2. Na **monitorování** kartě, můžete přidat mnoho další metriky specifické pro platformu pro nemůže podrobně. Můžete si prohlédnout konkrétně všechny chyby související s služby nabízených oznámení, které se vrátí, když se služba Notification Hubs pokusí odeslat oznámení služby nabízených oznámení. 
   
        ![Protokol činnosti Azure portálu][6]
   
    3. Začněte tím, že kontrola **příchozí zprávy**, **registrace Operations**, a **úspěšné oznámení**. Potom přejděte na kartu na platformě ke kontrole chyb, které jsou specifické pro službu nabízených oznámení. 
   
    4. Pokud jsou nesprávné, nastavení ověřování pro vaše Centrum oznámení zprávu **chyby ověřování systému PNS** se zobrazí. Toto je dobrá indikace toho zkontrolujte pověření služby nabízených oznámení. 

* **Programový přístup**

    Další informace o programový přístup najdete v těchto článcích: 

    * [Telemetrie programový přístup]  
    * [Telemetrie přístup přes rozhraní API ukázka] 

    > [!NOTE]
    > Některé funkce související s telemetrií jako Export a import registrace a telemetrie přístup přes rozhraní API, jsou k dispozici pouze na úrovni služby na úrovni Standard. Pokud pokus o použití těchto funkcí z volné nebo základní úrovně služby, pokud používáte sady SDK a chybu HTTP 403 (zakázáno), pokud funkce přímo z rozhraní REST API se zobrazí zpráva o výjimce. 
    >
    >Pokud chcete používat funkce související s telemetrií, nejdříve se ujistěte, na portálu Azure, že používáte vrstvě služby na úrovni Standard.  
> 
> 

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
[Notification Hubs přehled]: notification-hubs-push-notification-overview.md
[Začínáme s Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[šablony]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs přehled]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[zprávy o FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer kód]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Zobrazit registrace zařízení pro centra oznámení]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Uvedení verzi Visual Studio 2013 Update 3 a Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetrie programový přístup]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetrie přístup přes rozhraní API ukázka]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

