---
title: Diagnostika Azure Notification Hubs – vynechaná oznámení
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
ms.openlocfilehash: 4af86025e714c65d0ae225b271a2d0970bb96ee8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281637"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Diagnostika Azure Notification Hubs – vynechaná oznámení

Jedním z nejčastějších dotazů od zákazníků Azure Notification Hubs je řešení potíží při oznámení, které se odesílají z aplikace nejsou zobrazeny na klientských zařízeních. Chtějí vědět, kde a proč oznámení, byly vyřazeny a jak vyřešit problém. Tento článek identifikuje proč oznámení může získáte nebo nebudou přijímány zařízení. Zjistěte, jak analyzovat a určit hlavní příčinu.

Je důležité nejdřív pochopíte, jak službu Notification Hubs doručí oznámení do zařízení.

![Architektura Notification Hubs][0]

V toku typické odeslat oznámení, je zpráva odeslána z *back-endu aplikace* Notification hubs. Notification Hubs provádí nějaké zpracování na všechny registrace. Zpracování bere v úvahu nakonfigurované značky a výrazy označení k určení "cíle." Cíle jsou všechny registrace, které je potřeba dostat nabízené oznámení. Tyto registrace může zahrnovat některé nebo všechny naše podporované platformy: iOS, Google, Windows, Windows Phone, Kindle a Baidu pro China Android.

S cíli navázat, službu Notification Hubs nabízených oznámení upozornění *push notification service* pro platformu zařízení. Mezi příklady patří Apple Push Notification service (APNs) společnosti Apple a služby Firebase Cloud Messaging (FCM) pro Google. Notification Hubs nabízených oznámení upozornění rozdělit mezi několik dávek registrací. Notification Hubs se ověřuje pomocí služby příslušných nabízených oznámení na základě přihlašovacích údajů, které jste nastavili na webu Azure Portal, v části **Konfigurace centra oznámení**. Nabízené oznámení služby potom předává oznámení k funkcím *klientská zařízení*.

Odeslání oznámení do konečné fáze dojde mezi služby nabízených oznámení platformy a zařízení. Některé z těchto čtyř hlavních komponent v procesu nabízených oznámení (klient, back-endu aplikace, Notification Hubs a služby nabízených oznámení platformy) může způsobit oznámení chcete vyřadit. Další informace o architektuře Notification Hubs najdete v tématu [Přehled služby Notification Hubs].

Nedodání oznámení mohou nastat během počáteční fázi fáze/testování. Vynechanými oznámeními v této fázi může znamenat problém s konfigurací. Pokud dojde k selhání k poskytování oznámení v produkčním prostředí, může být vyřazen některá nebo všechna oznámení. V takovém případě je označeno hlubší aplikace nebo vzor problém pro zasílání zpráv.

Další části se probírají scénáře, ve kterých oznámení může dojít ke ztrátě, od běžné do více výjimečný.

## <a name="notification-hubs-misconfiguration"></a>Nesprávnou konfigurací ve službě Notification Hubs

Chcete-li úspěšně odesílat oznámení do služby příslušných nabízených oznámení, službu Notification Hubs potřebuje ke svému ověření v kontextu aplikace pro vývojáře. Vývojář vytvoří účet pro vývojáře se příslušné platformy (Google, Apple, Windows a tak dále). Vývojář potom zaregistruje jejich aplikace platformy, kde získají přihlašovací údaje.

Je nutné přidat platformy přihlašovací údaje k webu Azure portal. Pokud žádná oznámení jsou tam dostupné pro zařízení, prvním krokem by mělo být zajištění, že jsou správné přihlašovací údaje nakonfigurované ve službě Notification Hubs. Přihlašovací údaje musí odpovídat aplikace vytvořené v rámci účet pro vývojáře pro konkrétní platformu.

Podrobné pokyny k dokončení tohoto procesu najdete v tématu [Začínáme s Azure Notification Hubs].

Tady jsou některé běžné chybné konfigurace ke kontrole:

**Obecné**

Ujistěte se, že název vašeho centra oznámení (bez překlepů) jsou stejné v každé z těchto umístění:
   * Lze uvést registraci od klienta.
   * Pokud odesílání oznámení z back-endu.
   * Pokud jste nakonfigurovali přihlašovací údaje služby nabízených oznámení.

Ujistěte se, že používáte řetězce správné sdíleného přístupového podpisu konfigurace na straně klienta a na back-endu aplikace. Obecně platí, je nutné použít **DefaultListenSharedAccessSignature** na straně klienta a **DefaultFullSharedAccessSignature** aplikace back-endu (uděluje oprávnění k odesílání oznámení Notification Hubs).

**Konfigurace APN**

Musíte mít dvěma různými uzly: jedno centrum pro produkční prostředí a jiné centra testování. To znamená, že musíte nahrát certifikát, který používáte v prostředí izolovaného prostoru k rozbočovači samostatné než certifikát a rozbočovače, který budete používat v produkčním prostředí. Nepokoušejte se nahrát různé typy certifikátů na stejném centru. To může vést k selhání oznámení.

Pokud neúmyslně nahrát různé typy certifikátů na stejném centru, doporučujeme odstranit centra a začněte od začátku s nového centra. Pokud z nějakého důvodu nelze odstranit centrum, minimálně, je nutné odstranit všechny existující registrace z centra.

**Konfigurace FCM**

1. Ujistěte se, že *klíč serveru* , který jste získali z Firebase odpovídající klíči serveru, které jste zaregistrovali na webu Azure Portal.

   ![Klíč serveru firebase][3]

2. Ujistěte se, že jste nakonfigurovali **ID projektu** na straně klienta. Můžete získat hodnotu pro **ID projektu** z řídicího panelu Firebase.

   ![ID projektu firebase][1]

## <a name="application-issues"></a>Problémy s aplikací

**Značky a výrazy označení**

Pokud pomocí značky nebo výrazy označení segmentovat cílovou skupinu, je možné, že při odesílání oznámení žádný cíl nenajde na základě značky nebo výrazy označení, které zadáte v odesílání volání.

Zkontrolujte registraci k zajištění, že existují odpovídající značky při odesílání oznámení. Ověřte příjem oznámení pouze od klientů, které mají tyto registrace.

Pokud byly provedeny všechny registrace s Notification Hubs pomocí značky "Politika" a odesílání oznámení se značkou "Sports", jako například neodešle oznámení do libovolného zařízení. Komplexní případ může zahrnovat výrazy označení, ve kterých jste zaregistrovali pomocí "Značky A" nebo "Značka B", ale při odesílání oznámení, je cílem "Značky A & & značka B." V části Tipy pro vlastní diagnózu později v tomto článku ukážeme, jak kontrolovat vaše registrace a jejich značky.

**Problémy se šablonou**

Pokud používáte šablony, ujistěte se, postupujte podle pokynů popsaných v [šablony].

**Neplatná registrace**

Pokud v centru oznámení byla správně nakonfigurovaná, a pokud jsou správně byly použity všechny značky nebo výrazy, značky, jsou v ní platné cíle. Oznámení se mají posílat do těchto cílů. Služba Notification Hubs pak vyvolá několik zpracování dávky paralelně. Každá dávka odesílá zprávy do sadu registrací.

> [!NOTE]
> Protože je zpracovávána paralelně, není zaručeno pořadí, ve kterém jsou doručovány oznámení.

Notification Hubs je optimalizovaná pro model doručování zpráv "na většinu jednou". Odstranění duplicitních dat, snažíme tak, aby žádná oznámení se doručují více než jednou na zařízení. Aby toto bylo zajištěno, jsme Zkontrolujte registrace a ujistěte se, že tento pouze jedna zpráva se pošle na identifikátor zařízení předtím, než je zpráva odeslána do služby nabízených oznámení.

Každá dávka je odeslané do služby nabízených oznámení, která zase je přijetí a ověření registrace, je možné, že služba nabízených oznámení zjistí chybu s jednou nebo více registrací v dávce. V tomto případě služba nabízených oznámení do Notification Hubs vrátí chybu, a proces se zastaví. Služba nabízených oznámení úplně zahodí této služby batch. To platí zejména s APNS, který používá protokol TCP datového proudu.

Na většině optimalizovali jsme jednou. Ale v takovém případě je Chyba zápisu z databáze odebráno. Opakujte jsme doručení oznámení pro ostatní zařízení v této dávce.

Pokud chcete získat další informace o chybě týkající se selhání doručení pokusu o proti zápisu, můžete použít rozhraní REST API pro Notification Hubs [Telemetrii jednotlivých zpráv: Získat telemetrii o zprávy oznámení](https://msdn.microsoft.com/library/azure/mt608135.aspx) a [zpětná vazba systému oznámení platformy](https://msdn.microsoft.com/library/azure/mt705560.aspx). Ukázkový kód, naleznete v tématu [příklad odesílání REST](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Nabízená oznámení problémy se službou

Po obdržení zprávy oznámení pomocí služby nabízených oznámení platformy, se odpovědnost služby nabízených oznámení pro doručení oznámení do zařízení. V tomto okamžiku službu Notification Hubs je mimo na obrázku a nemá žádnou kontrolu nad, když nebo oznámení je doručit do zařízení.

Protože službám oznamování platformy jsou odolnější, mají tendenci oznámení na zařízení ze služby nabízených oznámení za pár sekund. Pokud je omezování služby nabízených oznámení, platí službu Notification Hubs exponenciální regresní strategie. Pokud služba nabízených oznámení zůstanou nedostupné po dobu 30 minut, máme zásadu zajistit pro vypršení platnosti a trvale vyřadit zprávy.

Pokud služba nabízených oznámení pokusí doručit oznámení, ale je zařízení offline, oznámení se ukládá pomocí služby nabízených oznámení po omezenou dobu. Oznámení se doručí do zařízení, když zařízení přestane být k dispozici.

Pro každou aplikaci se ukládají pouze jedno poslední upozornění. Pokud více oznámení se posílají, když je zařízení offline, každé nové oznámení způsobí, že předchozí oznámení budou zahozeny. Zachovat pouze nejnovější oznámení se označuje jako *slučovací oznámení* v APN, a *sbalení* v FCM, (ta používá sbalení klíč). Pokud zařízení zůstane offline po dlouhou dobu, všechna naše oznámení, které byly uložené pro zařízení se zahodí. Další informace najdete v článku [přehled APN] a [O zpráv FCM].

Pomocí Azure Notification Hubs můžete předat slučovací klíč prostřednictvím hlavičky protokolu HTTP pomocí obecného rozhraní API SendNotification. Například pro sadu .NET SDK můžete využít `SendNotificationAsync`. Rozhraní API SendNotification také využívá hlavičky HTTP, které jsou předány jako-je služba příslušných nabízených oznámení.

## <a name="self-diagnosis-tips"></a>Tipy pro vlastní diagnózu

Tady jsou cesty k diagnostikovat původní příčinu vynechané oznámení v Notification Hubs.

### <a name="verify-credentials"></a>Ověření přihlašovacích údajů

**Portál pro vývojáře služby nabízených oznámení**

Ověřte přihlašovací údaje v příslušných nabízená oznámení služby portálu pro vývojáře (APNs, FCM, Windows Notification Service a tak dále). Další informace najdete v tématu [Začínáme s Azure Notification Hubs].

**Azure Portal**

Ke kontrole a odpovídat přihlašovacím údajům s těmi, které jste získali z nabízených oznámení služby portálu pro vývojáře, na webu Azure Portal, přejděte **zásady přístupu** kartu.

![Zásady přístupu k webu Azure portal][4]

### <a name="verify-registrations"></a>Ověření registrace

**Visual Studio**

Pokud používáte sadu Visual Studio pro vývoj, můžete připojit k Azure prostřednictvím Průzkumníka serveru pro zobrazení a správa několika služeb Azure, včetně Notification Hubs. To je užitečné především pro vaše prostředí pro vývoj/testování.

![Průzkumníka serveru Visual Studio][9]

Můžete zobrazit a spravovat všechny registrace ve vašem Centru zařazených do kategorií podle platformy, nativní nebo šablona registrace, všechny značky, identifikátor služby nabízených oznámení, ID registrace a datum vypršení platnosti. Můžete také upravit registraci na této stránce. To je užitečné zejména pro úpravy značky.

Klikněte pravým tlačítkem na váš **centra oznámení** v **Průzkumníka serveru**a vyberte **Diagnostika**. 

![Visual Studio – Průzkumník serveru – Diagnostika nabídky](./media/notification-hubs-diagnosing/diagnose-menu.png)

Zobrazí se následující stránka: 

![Visual Studio – Diagnostika stránky](./media/notification-hubs-diagnosing/diagnose-page.png)

Přepněte **registrace zařízení** stránky: 

![Registrace zařízení sady Visual Studio](./media/notification-hubs-diagnosing/VSRegistrations.png)

Můžete použít **testovací odeslání** stránku odeslat testovací oznámení:

![Visual Studio – testovací odeslání](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Pomocí sady Visual Studio k úpravě registrace pouze během vývoje a testování a s omezený počet registrací. Pokud je potřeba upravit vaše registrace hromadně, zvažte použití export a import registrace funkcí popsaných v [Export a změny registrací hromadné](https://msdn.microsoft.com/library/dn790624.aspx).

**Service Bus Explorer**

Mnozí uživatelé používají [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer) můžete zobrazit a spravovat své Centrum oznámení. Service Bus Exploreru je projekt open source. 

### <a name="verify-message-notifications"></a>Ověření zprávy s oznámením

**Azure Portal**

Chcete-li odeslat testovací oznámení vašim klientům bez služby back-end provoz, v části **podpora a řešení potíží**vyberte **testovací odeslání**.

![Funkce odeslání testování v Azure][7]

**Visual Studio**

Můžete také odeslat testovací oznámení ze sady Visual Studio.

![Testování funkce Odeslat v sadě Visual Studio][10]

Další informace o používání Notification Hubs pomocí Průzkumníka serveru Visual Studia najdete v těchto článcích:

* [Zobrazit registrace zařízení pro notification hubs]
* [Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]
* [Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Ladění neúspěšných oznámení a zkontrolujte výsledek oznámení

**Vlastnost EnableTestSend**

Při odesílání oznámení pomocí Notification Hubs, původně, je oznámení zařadí do fronty pro zpracování ve službě Notification Hubs. Notification Hubs určuje cíle, které správný a potom odešle oznámení služby nabízených oznámení. Pokud používáte rozhraní REST API nebo některou z klientské sady SDK, úspěšné návrat volání odesílání znamená, že pouze, že zpráva má se úspěšně zařadil do fronty pomocí Notification Hubs. Nemáte žádné přehled o tom, co se stalo při Notification Hubs nakonec odeslala zprávu do služby nabízených oznámení.

Pokud vaše oznámení nedorazí v klientském zařízení, je možné, že došlo k chybě při pokusu o doručení zprávy do služby nabízených oznámení pomocí Notification Hubs. Například velikost datové části může být delší než maximální povolenou služby nabízených oznámení nebo přihlašovacím údajům nakonfigurovaným v Notification Hubs může být neplatný.

Získat přehled o tom, nabízená oznámení služby chyby, můžete použít [EnableTestSend] vlastnost. Tato vlastnost je automaticky povolené, při odesílání zkušebních zpráv z klienta Visual Studio nebo portálu. Chcete-li zobrazit podrobné informace o ladění můžete použít tuto vlastnost. Můžete také použít vlastnost přes rozhraní API. V současné době můžete použít v sadě .NET SDK. Nakonec přidá se na všechny klientské sady SDK.

Použít `EnableTestSend` vlastnost s volání REST přidat parametr řetězce dotazu s názvem *testování* za účelem odeslání volání. Příklad:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Příklad (sadu .NET SDK)**

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

**Ukázkový výstup**

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

**Azure Portal**

Na portálu můžete získat rychlý přehled o všech aktivit v centru oznámení.

1. Na **přehled** kartě, zobrazí souhrnný náhled na registraci, upozornění a chyb podle platformy.

   ![Řídicího panelu s přehledem Notification Hubs][5]

2. Na **monitorování** kartu, můžete přidat řadu jiné metriky specifické pro platformu pro podrobnější pohled. Můžete si prohlédnout konkrétně chyby související se služba nabízených oznámení, které jsou vráceny, když se pokusí službu Notification Hubs k odesílání oznámení do služby nabízených oznámení.

   ![Protokol aktivit Azure portal][6]

3. Začněte tím, že zkontrolujete **příchozí zprávy**, **registrace operace**, a **úspěšná oznámení**. Potom přejděte na kartu podle platformy ke kontrole chyb, které jsou specifické pro služby nabízených oznámení.

4. Pokud nastavení ověřování pro vaše Centrum oznámení je nesprávné, zprávy **chyba systému oznámení platformy ověřování** se zobrazí. Je dobrá indikace toho zkontrolujte přihlašovací údaje služby nabízených oznámení.

**Programový přístup**

Další informace o programový přístup, najdete v části [telemetrie programový přístup].

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
[Šablony]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[O zpráv FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Zobrazit registrace zařízení pro notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Podrobné informace: Visual Studio 2013 Update 2 RC a Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Oznamujeme vydání sady Visual Studio 2013 Update 3 a Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetrie programový přístup]: https://msdn.microsoft.com/library/azure/dn458823.aspx
