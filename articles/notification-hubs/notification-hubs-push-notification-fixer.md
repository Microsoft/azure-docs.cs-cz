---
title: Diagnostika vynechanými oznámeními ve službě Azure Notification Hubs
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
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925827"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnostika vynechanými oznámeními ve službě Azure Notification Hubs

Běžné otázky o Azure Notification Hubs je postup řešení potíží při oznámení z aplikace nejsou zobrazeny na klientských zařízeních. Zákazníci chtějí vědět, kde a proč byly vyřazeny oznámení a jak vyřešit problém. Tento článek identifikuje proč oznámení může získáte nebo nebudou přijímány zařízení. Také vysvětluje, jak zjistit hlavní příčinu.

Je důležité se napřed seznámit jak Notification Hubs doručí oznámení do zařízení.

![Architektura Notification Hubs][0]

V toku typické odeslat oznámení, je zpráva odeslána z *back-endu aplikace* Notification hubs. Notification Hubs zpracovává všechny registrace. Zohledňuje nakonfigurované značky a výrazy označení k určení cíle. Cíle jsou registrace, které je potřeba dostat nabízené oznámení. Tyto registrace může zahrnovat některé z našich podporované platformy: Vyvolání operačního systému (Amazon), iOS, Windows a Windows Phone, Android Baidu (zařízení s Androidem v Číně).

S cíli navázat, Notification Hubs nabízených oznámení upozornění *push notification service* pro platformu zařízení. Mezi příklady patří Apple Push Notification service (APNs) společnosti Apple a služby Firebase Cloud Messaging (FCM) pro Google. Notification Hubs nabízených oznámení upozornění rozdělit mezi několik dávek registrací. Ověřuje ve službě příslušných nabízená oznámení na základě přihlašovacích údajů, můžete nastavit na webu Azure Portal, v části **Konfigurace centra oznámení**. Nabízené oznámení služby potom předává oznámení k funkcím *klientská zařízení*.

Odeslání oznámení do konečné fáze je mezi služby nabízených oznámení platformy a zařízení. Doručení oznámení může selhat na všechny čtyři fáze procesu nabízených oznámení (klient, back-endu aplikace, Notification Hubs a služby nabízených oznámení platformy). Další informace o architektuře Notification Hubs najdete v tématu [Přehled služby Notification Hubs].

Selhání při poskytování oznámení mohou nastat během počáteční fázi fáze/testování. Vynechanými oznámeními v této fázi může znamenat problém s konfigurací. V případě selhání k poskytování oznámení v produkčním prostředí může být vynechána některá nebo všechna oznámení. V tomto případě je označeno hlubší aplikace nebo vzor problém pro zasílání zpráv.

Další části se probírají scénáře, ve kterých oznámení může dojít ke ztrátě, od běžné výjimečný.

## <a name="notification-hubs-misconfiguration"></a>Nesprávnou konfigurací ve službě Notification Hubs ##

K odesílání oznámení do služby příslušných nabízených oznámení, musí ověřit Notification Hubs v rámci vaší aplikace sám. Musíte vytvořit účet pro vývojáře s cílovou platformu notification service (Microsoft Apple, Google, atd.). Poté je nutné zaregistrovat aplikaci s operačním systémem, kde můžete získat token nebo klíč, který používáte pro práci s cílový systém oznámení platformy.

Je nutné přidat platformy přihlašovací údaje k webu Azure portal. Pokud žádná oznámení jsou tam dostupné pro zařízení, prvním krokem je zajistit, že jsou správné přihlašovací údaje nakonfigurované ve službě Notification Hubs. Přihlašovací údaje musí odpovídat aplikace vytvořené v rámci účet pro vývojáře pro konkrétní platformu.

Podrobné pokyny k dokončení tohoto procesu najdete v tématu [Začínáme s Azure Notification Hubs].

Tady jsou některé běžné chybné konfigurace ke kontrole:

### <a name="notification-hub-name-location"></a>Umístění názvu centra oznámení

Ujistěte se, že název vašeho centra oznámení (bez překlepů) jsou stejné v každé z těchto umístění:
   * Při registraci z klienta
   * Pokud odesílání oznámení z back-endu
   * Pokud jste nakonfigurovali přihlašovací údaje služby nabízených oznámení

Zkontrolujte použití řetězce správné sdíleného přístupového podpisu konfigurace na straně klienta a ukončit aplikaci zpět. Obecně platí, je nutné použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** aplikace back-endu. Tím udělíte oprávnění k odesílání zpráv do Notification Hubs.

### <a name="apn-configuration"></a>Konfigurace APN ###

Musíte mít dvěma různými uzly: jeden pro produkci a další položku pro testování. Musíte nahrát certifikát, který používáte v prostředí izolovaného prostoru k rozbočovači samostatné než certifikát/rozbočovače, který budete používat v produkčním prostředí. Nepokoušejte se nahrát různé typy certifikátů na stejném centru. To způsobí selhání oznámení.

Pokud nechtěně nahrát různé typy certifikátů na stejném centru, by měl odstranit. a začít pracovat s nového centra. Pokud z nějakého důvodu nelze odstranit rozbočovače, musíte odstranit všechny existující registrace nejméně z centra.

### <a name="fcm-configuration"></a>Konfigurace FCM ###

1. Ujistěte se, *klíč serveru* jste získali z Firebase shod klíč serveru jste zaregistrovali na webu Azure Portal.

   ![Klíč serveru firebase][3]

2. Ujistěte se, že jste nakonfigurovali **ID projektu** na straně klienta. Můžete získat hodnotu pro **ID projektu** z řídicího panelu Firebase.

   ![ID projektu firebase][1]

## <a name="application-issues"></a>Problémy s aplikací ##

### <a name="tags-and-tag-expressions"></a>Značky a výrazy označení ###

Pokud pomocí značky nebo výrazy označení segmentovat cílovou skupinu, je možné, že při odesílání oznámení se nenašel žádný cíl. Tato chyba je na základě zadaným značkám nebo výrazy označení v odesílání volání.

Zkontrolujte registraci k zajištění, že značky budou porovnávány názvy při odesílání oznámení. Ověřte příjem oznámení z klientů, které mají tyto registrace.

Předpokládejme například, že všechny registrace s Notification Hubs pomocí značky "Politika." Pokud pak odešlete oznámení se značkou "Sports", upozornění nebude odesláno do libovolného zařízení. Komplexní případ může zahrnovat výrazy označení, kde jste zaregistrovali pomocí "Značky A" *nebo* "Značka B", ale cílové "Značky A & & značka B." V části Tipy pro vlastní diagnózu později v tomto článku se dozvíte, jak zkontrolovat registraci a jejich značky.

### <a name="template-issues"></a>Problémy se šablonou ###

Pokud používáte šablony, ujistěte se, postupujte podle pokynů popsaných v [šablony].

### <a name="invalid-registrations"></a>Neplatná registrace ###

Pokud správnou konfiguraci centra oznámení a značky nebo výrazy označení byly správně použity, budou vyhledány platné cíle. Oznámení se mají posílat do těchto cílů. Notification Hubs poté vyvolá několik zpracování dávky paralelně. Každá dávka odesílá zprávy do sadu registrací.

> [!NOTE]
> Vzhledem k tomu Notification Hubs zpracovává dávky paralelně, není zaručeno pořadí, ve kterém jsou doručovány oznámení.

Notification Hubs je optimalizovaná pro model doručování zpráv "na většinu jednou". Odstranění duplicitních dat, snažíme tak, aby žádná oznámení se doručují více než jednou na zařízení. Ujistěte se, že pouze konkrétní zprávu je před odesláním do služby nabízených oznámení odeslaných za identifikátor zařízení se kontroluje registrace.

Každá dávka je odesílat služby nabízených oznámení, která pak přijme a ověří registrace. Během tohoto procesu je možné, že služba nabízených oznámení zjistí chybu pomocí jedné nebo více registrací v dávce. Služba nabízených oznámení potom vrátí chybu do Notification Hubs a proces se zastaví. Služba nabízených oznámení úplně zahodí této služby batch. To platí zejména s APNs, který používá protokol TCP datového proudu.

Chyba registrace v tomto případě se odebere z databáze. Opakujte jsme doručení oznámení pro ostatní zařízení v této dávce.

Pokud chcete získat další informace o chybě týkající se selhání doručení pokusu o proti zápisu, můžete použít rozhraní REST API pro Notification Hubs [Telemetrii jednotlivých zpráv: Získat telemetrii o zprávy oznámení](https://msdn.microsoft.com/library/azure/mt608135.aspx) a [zpětná vazba systému oznámení platformy](https://msdn.microsoft.com/library/azure/mt705560.aspx). Ukázkový kód, naleznete v tématu [příklad odesílání REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Nabízená oznámení problémy se službou

Jakmile služba nabízených oznámení, obdrží oznámení, doručí oznámení do zařízení. V tomto okamžiku Notification Hubs nemá žádnou kontrolu nad doručování oznámení do zařízení.

Protože službám oznamování platformy jsou odolnější, oznámení se většinou kontaktovat zařízení během několika sekund. Pokud je omezování služby nabízených oznámení, Notification Hubs platí exponenciální regresní strategie. Pokud služba nabízených oznámení zůstane po dobu 30 minut nedostupná, je zajistit pro vypršení platnosti a vyřadit zprávy trvale zásada.

Pokud služba nabízených oznámení pokusí doručit oznámení, ale je zařízení offline, oznámení se ukládá pomocí služby nabízených oznámení. Uloží se pouze po omezenou dobu času. Oznámení se doručí do zařízení, když zařízení přestane být k dispozici.

Každé aplikaci, která ukládá pouze jedno poslední upozornění. Pokud více oznámení se posílají, když je zařízení offline, každé nové oznámení způsobí, že poslední z nich budou zahozeny. Zachovat pouze nejnovější oznámení se nazývá *slučování* v APNs a *sbalení* v FCM. (FCM používá sbalení klíč). Když zařízení zůstane offline po dlouhou dobu, oznámení, které byly uloženy pro zařízení se zahodí. Další informace najdete v tématu [Přehled služby APN] a [O zpráv FCM].

S Notification Hubs můžete předat slučovací klíč prostřednictvím hlavičky protokolu HTTP pomocí obecného rozhraní API SendNotification. Například pro sadu .NET SDK můžete využít `SendNotificationAsync`. Rozhraní API SendNotification využívá taky hlavičky HTTP, které jsou předány jako služba příslušných nabízených oznámení.

## <a name="self-diagnosis-tips"></a>Tipy pro vlastní diagnózu

Tady jsou cesty k diagnostikovat původní příčinu vynechané oznámení v Notification Hubs.

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů ###

#### <a name="push-notification-service-developer-portal"></a>Portál pro vývojáře služby nabízených oznámení ####

Ověřte přihlašovací údaje v příslušných nabízená oznámení služby portálu pro vývojáře (APNs, FCM, Windows Notification Service a tak dále). Další informace najdete v tématu [kurzu: Odesílat oznámení do aplikací pro univerzální platformu Windows pomocí Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>portál Azure ####

Ke kontrole a odpovídat přihlašovacím údajům s těmi, která jste získali z portálu pro vývojáře služby nabízených oznámení, přejděte **zásady přístupu** karta na portálu Azure portal.

![Zásady přístupu k webu Azure portal][4]

### <a name="verify-registrations"></a>Ověření registrace

#### <a name="visual-studio"></a>Visual Studio ####

V sadě Visual Studio můžete připojit k Azure prostřednictvím Průzkumníka serveru pro zobrazení a správa několika služeb Azure, včetně Notification Hubs. Tento zástupce je to užitečné hlavně pro vaše vývojové a testovací prostředí.

![Průzkumníka serveru Visual Studio][9]

Můžete zobrazit a spravovat všechny registrace ve vašem Centru. Registrace lze rozdělit podle platformy, nativní nebo šablona registrace, značky, identifikátor služby nabízených oznámení, ID registrace a datum vypršení platnosti. Můžete také upravit registraci na této stránce. To je užitečné zejména pro úpravy značky.

Klikněte pravým tlačítkem na vaše Centrum oznámení v **Průzkumníka serveru**a vyberte **Diagnostika**. 

![Průzkumníka serveru Visual Studio: Diagnostikujte nabídek](./media/notification-hubs-diagnosing/diagnose-menu.png)

Zobrazí se následující stránka:

![Visual Studio: Diagnostika stránky](./media/notification-hubs-diagnosing/diagnose-page.png)

Přepněte **registrace zařízení** stránky:

![Visual Studio: Registrace zařízení](./media/notification-hubs-diagnosing/VSRegistrations.png)

Můžete použít **testovací odeslání** stránku odeslat testovací oznámení:

![Visual Studio: Poslat na zkoušku](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Pomocí sady Visual Studio k úpravě registrace pouze během vývoje a testování a s omezený počet registrací. Pokud je potřeba upravit vaše registrace hromadně, zvažte použití export a import registrace funkcí popsaných v [How To: Export a změny registrací hromadné](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Mnozí uživatelé používají [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer) k zobrazení a správě jejich notification hubs. Service Bus Exploreru je projekt open source. 

### <a name="verify-message-notifications"></a>Ověření zprávy s oznámením

#### <a name="azure-portal"></a>portál Azure ####

Chcete-li odeslat testovací oznámení vašim klientům bez služby back-end provoz, v části **podpora a řešení potíží**vyberte **testovací odeslání**.

![Funkce odeslání testování v Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Můžete také odeslat testovací oznámení ze sady Visual Studio.

![Testování funkce Odeslat v sadě Visual Studio][10]

Další informace o používání Notification Hubs pomocí Průzkumníka serveru Visual Studia najdete v těchto článcích:

* [Postup zobrazení registrace zařízení pro notification hubs](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]
* [Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění neúspěšných oznámení a zkontrolujte výsledek oznámení

#### <a name="enabletestsend-property"></a>Vlastnost EnableTestSend ####

Při odesílání oznámení pomocí Notification Hubs oznámení je zpočátku zařadí do fronty. Notification Hubs určuje cíle, které správný a potom odešle oznámení služby nabízených oznámení. Pokud používáte rozhraní REST API nebo některou z klientské sady SDK, návrat volání odesílání znamená, že pouze, že je zpráva ve frontě, pomocí Notification Hubs. Neposkytuje přehled o tom, co se stalo při Notification Hubs nakonec odeslána oznámení do služby nabízených oznámení.

Pokud vaše oznámení nedorazí v klientském zařízení, může mít došlo k chybě při pokusu o doručování do služby nabízených oznámení pomocí Notification Hubs. Například velikost datové části může být delší než maximální povolenou služby nabízených oznámení nebo přihlašovacím údajům nakonfigurovaným v Notification Hubs může být neplatný.

Získat přehled o tom, nabízená oznámení služby chyby, můžete použít [EnableTestSend] vlastnost. Tato vlastnost je automaticky povolené, při odesílání zkušebních zpráv z klienta Visual Studio nebo portálu. Chcete-li zobrazit podrobné informace o ladění můžete použít tuto vlastnost a také přes rozhraní API. V současné době můžete použít v sadě .NET SDK. Přidá se na všechny klientské sady SDK nakonec.

Použít `EnableTestSend` vlastnost s volání REST přidat parametr řetězce dotazu s názvem *testování* za účelem odeslání volání. Příklad:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Příklad sady .NET SDK ####

Tady je příklad použití sady .NET SDK k odesílání oznámení nativní automaticky otevírané okno (informační zpráva):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Na konci spuštění `result.State` jednoduše stavy `Enqueued`. Výsledky neposkytují žádné přehled o tom, co se stalo se nabízené oznámení.

V dalším kroku můžete použít `EnableTestSend` vlastnost typu Boolean. Použití `EnableTestSend` vlastnost při inicializaci `NotificationHubClient` získat podrobný stav o nabízená oznámení služby chyby, ke kterým dochází při odesílání oznámení. Odeslání volání bude vyžadovat čas navíc k vrácení, protože je nejprve nutné Notification Hubs k poskytování oznámení služby nabízených oznámení.

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

#### <a name="sample-output"></a>Ukázkový výstup ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Tato zpráva znamená, že přihlašovací údaje nakonfigurované ve službě Notification Hubs jsou neplatné nebo že se vyskytl problém s registrací v centru. Odstranit tuto registraci a nechat klienta znovu vytvořit registrace před odesláním zprávy.

> [!NOTE]
> Použití `EnableTestSend` vlastnost bude výrazně omezený. Tuto možnost použijte pouze v prostředí vývoje a testování a s omezenou sadu registrací. Ladění oznámení se posílají jenom 10 zařízení. Je také omezení na zpracování ladění odešle na 10 za minutu.

### <a name="review-telemetry"></a>Zkontrolujte telemetrická data ###

#### <a name="azure-portal"></a>portál Azure ####

Na portálu můžete získat rychlý přehled o všech aktivit v centru oznámení.

1. Na **přehled** kartě, zobrazí souhrnný náhled na registraci, upozornění a chyb podle platformy.

   ![Řídicího panelu s přehledem Notification Hubs][5]

2. Na **monitorování** kartu, můžete přidat řadu jiné metriky specifické pro platformu pro podrobnější pohled. Můžete si prohlédnout konkrétně chyby, které se vrátí, když se pokusí Notification Hubs k odesílání oznámení do služby nabízených oznámení.

   ![Protokol aktivit Azure portal][6]

3. Začněte tím, že zkontrolujete **příchozí zprávy**, **registrace operace**, a **úspěšná oznámení**. Potom přejděte na kartu podle platformy ke kontrole chyb, které jsou specifické pro služby nabízených oznámení.

4. Pokud nastavení ověřování pro vaše Centrum oznámení je nesprávné, zprávy **chyba systému oznámení platformy ověřování** se zobrazí. Je dobrá indikace toho zkontrolujte přihlašovací údaje služby nabízených oznámení.

#### <a name="programmatic-access"></a>Programový přístup ####

Další informace o programový přístup, najdete v části [programový přístup](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Některé funkce související s telemetrií jako Export a import registrace a telemetrie přístup přes rozhraní API, jsou k dispozici pouze v rámci úrovně služeb Standard. Při pokusu o použití těchto funkcí z bezplatné nebo základní úroveň služeb, pokud používáte sadu SDK zobrazí se zprávou výjimky. Pokud používáte funkce přímo z rozhraní REST API, zobrazí se chyba HTTP 403 (zakázáno).
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
[Šablony]: https://msdn.microsoft.com/library/dn530748.aspx
[Přehled služby APN]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[O zpráv FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
