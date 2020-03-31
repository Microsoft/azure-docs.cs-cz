---
title: Správa registrace
description: Toto téma vysvětluje, jak zaregistrovat zařízení s centry oznámení, aby bylo možné přijímat nabízená oznámení.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455363"
---
# <a name="registration-management"></a>Správa registrace

Toto téma vysvětluje, jak zaregistrovat zařízení s centry oznámení, aby bylo možné přijímat nabízená oznámení. Téma popisuje registrace na vysoké úrovni, pak zavádí dva hlavní vzory pro registraci zařízení: registrace ze zařízení přímo do centra oznámení a registrace prostřednictvím back-endu aplikace.

## <a name="what-is-device-registration"></a>Co je registrace zařízení

Registrace zařízení pomocí centra oznámení se provádí pomocí **registrace** nebo **instalace**.

### <a name="registrations"></a>Registrace

Registrace přidruží popisovač služby Oznámení platformy (PNS) pro zařízení se značkami a případně šablonou. Popisovač PNS může být ChannelURI, token zařízení nebo ID registrace FCM. Značky se používají k směrování oznámení na správnou sadu popisovačů zařízení. Další informace naleznete v [tématu Routing and Tag Expressions](notification-hubs-tags-segment-push-message.md). Šablony se používají k implementaci transformace pro registraci. Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na jedno zařízení.

### <a name="installations"></a>Instalace

Instalace je vylepšená registrace, která obsahuje sáček vlastností souvisejících s nabízeným odkazem. Jedná se o nejnovější a nejlepší přístup k registraci vašich zařízení. Však není podporován na straně klienta .NET SDK ([Notification Hub SDK pro operace back-endu](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) jako dosud.  To znamená, že pokud se registrujete ze samotného klientského zařízení, budete muset použít přístup [rozhraní REST API oznamovacích center](/rest/api/notificationhubs/create-overwrite-installation) pro podporu instalací. Pokud používáte back-endovou službu, měli byste být schopni použít [sadku SDK centra oznámení pro operace back-endu](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Následující jsou některé klíčové výhody použití instalací:

- Vytvoření nebo aktualizace instalace je plně idempotentní. Takže jej můžete opakovat bez obav o duplicitní registrace.
- Instalační model podporuje speciální formát`$InstallationId:{INSTALLATION_ID}`značky ( ), který umožňuje odeslání oznámení přímo do konkrétního zařízení. Pokud například kód aplikace nastaví ID `joe93developer` instalace pro toto konkrétní zařízení, může vývojář zaměřit `$InstallationId:{joe93developer}` toto zařízení při odesílání oznámení na značku. To umožňuje cílit na konkrétní zařízení bez nutnosti dalšího kódování.
- Pomocí instalace také umožňuje provést částečné aktualizace registrace. Částečná aktualizace instalace je požadována metodou PATCH pomocí [standardu JSON-Patch](https://tools.ietf.org/html/rfc6902). To je užitečné, pokud chcete aktualizovat značky na registraci. Nemusíte strhnout celou registraci a pak znovu odeslat všechny předchozí značky znovu.

Instalace může obsahovat následující vlastnosti. Úplný seznam vlastností instalace naleznete v tématu [Vytvoření nebo přepsání instalace pomocí rozhraní REST API](/rest/api/notificationhubs/create-overwrite-installation) nebo [Vlastnosti instalace](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Ve výchozím nastavení registrace a instalace nevyprší.

Registrace a instalace musí obsahovat platný popisovač PNS pro každé zařízení nebo kanál. Vzhledem k tomu, že popisovače Služby PNS lze získat pouze v klientské aplikaci v zařízení, jeden vzor je zaregistrovat přímo na tomto zařízení s klientskou aplikací. Na druhou stranu aspekty zabezpečení a obchodní logika související se značkami mohou vyžadovat, abyste měli spravovat registraci zařízení v back-endu aplikace.

> [!NOTE]
> Rozhraní API instalace nepodporuje službu Baidu (i když registrace rozhraní API ano). 

### <a name="templates"></a>Šablony

Pokud chcete použít [šablony](notification-hubs-templates-cross-platform-push-messages.md), instalace zařízení také obsahuje všechny šablony přidružené k tomuto zařízení ve formátu JSON (viz ukázka výše). Názvy šablon pomáhají cílit na různé šablony pro stejné zařízení.

Každý název šablony se mapuje na tělo šablony a volitelnou sadu značek. Kromě toho může mít každá platforma další vlastnosti šablony. Pro Windows Store (pomocí WNS) a Windows Phone 8 (pomocí mpns) může být součástí šablony další sada záhlaví. V případě APNs můžete nastavit vlastnost vypršení platnosti buď konstanta nebo výraz šablony. Úplný seznam vlastností instalace najdete v [tématu Vytvoření nebo přepsání instalace pomocí](/rest/api/notificationhubs/create-overwrite-installation) tématu REST.

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundární dlaždice pro aplikace pro Windows Store

U klientských aplikací pro Windows Store je odesílání oznámení na sekundární dlaždice stejné jako jejich odesílání na primární. To je podporováno také v instalacích. Sekundární dlaždice mají jiný ChannelUri, který sdk v klientské aplikaci zpracovává transparentně.

SekundárníTile slovník používá stejné TileId, který se používá k vytvoření SecondaryTiles objekt u aplikace pro Windows Store. Stejně jako u primární channeluri, ChannelUris sekundárních dlaždic můžete kdykoli změnit. Chcete-li zachovat instalace v centru oznámení aktualizovány, zařízení musí aktualizovat je s aktuální ChannelUris sekundární dlaždice.

## <a name="registration-management-from-the-device"></a>Správa registrace ze zařízení

Při správě registrace zařízení z klientských aplikací je back-end zodpovědný pouze za odesílání oznámení. Klientské aplikace udržují popisovače PNS aktuální a registrují značky. Následující obrázek ilustruje tento vzor.

![Registrace ze zařízení](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zařízení nejprve načte popisovač PNS z PNS a pak se zaregistruje přímo v centru oznámení. Po úspěšné registraci může back-end aplikace odeslat oznámení s cílem tuto registraci. Další informace o odesílání oznámení naleznete v [tématu Routing and Tag Expressions](notification-hubs-tags-segment-push-message.md).

V takovém případě používáte pouze práva naslouchání pro přístup k centru oznámení ze zařízení. Další informace naleznete v tématu [Zabezpečení](notification-hubs-push-notification-security.md).

Registrace ze zařízení je nejjednodušší metodou, ale má některé nevýhody:

- Klientská aplikace může aktualizovat své značky pouze v případě, že je aplikace aktivní. Například pokud má uživatel dvě zařízení, která registrují značky související se sportovními týmy, když se první zařízení zaregistruje pro další značku (například Seahawks), druhé zařízení neobdrží oznámení o Seahawks, dokud aplikace na druhém zařízení není provedeny podruhé. Obecněji platí, že pokud jsou značky ovlivněny více zařízeními, je žádoucí správa značek z back-endu.
- Vzhledem k tomu, že aplikace mohou být napadeny hackery, zabezpečení registrace na konkrétní značky vyžaduje zvláštní péči, jak je vysvětleno v článku [Bezpečnost](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Příklad kódu pro registraci v centru oznámení ze zařízení pomocí instalace

V současné době je to podporováno pouze pomocí [rozhraní REST API centra oznámení](/rest/api/notificationhubs/create-overwrite-installation).

Metodu PATCH můžete také použít pro aktualizaci instalace [pomocí standardu JSON-Patch.](https://tools.ietf.org/html/rfc6902)

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation ID in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Příklad kódu pro registraci v centru oznámení ze zařízení používajícího registraci

Tyto metody vytvořit nebo aktualizovat registraci pro zařízení, na kterém jsou volány. To znamená, že chcete-li aktualizovat popisovač nebo značky, musíte přepsat celou registraci. Nezapomeňte, že registrace jsou přechodné, takže byste měli mít vždy spolehlivé úložiště s aktuálními značkami, které konkrétní zařízení potřebuje.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Správa registrace z back-endu

Správa registrací z back-endu vyžaduje psaní dalšího kódu. Aplikace ze zařízení musí poskytnout aktualizovaný popisovač PNS back-endu při každém spuštění aplikace (spolu se značkami a šablonami) a back-end musí tento popisovač aktualizovat v centru oznámení. Následující obrázek ilustruje tento návrh.

![Správa registrace](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Mezi výhody správy registrací z back-endu patří možnost upravit značky na registrace i v případě, že odpovídající aplikace v zařízení je neaktivní, a ověřit klientskou aplikaci před přidáním značky k jeho registraci.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Příklad kódu pro registraci v centru oznámení z back-endu pomocí instalace

Klientské zařízení stále získá popisovač PNS a příslušné vlastnosti instalace jako dříve a volá vlastní rozhraní API v back-endu, které může provádět registraci a autorizovat značky atd. Back-end může využít chartu [SDK centra oznámení pro operace back-endu](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Metodu PATCH můžete také použít pro aktualizaci instalace [pomocí standardu JSON-Patch.](https://tools.ietf.org/html/rfc6902)

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Příklad kódu pro registraci v centru oznámení ze zařízení pomocí ID registrace

Z back-endu aplikace můžete provádět základní operace CRUDS s registracemi. Například:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Back-end musí zpracovat souběžnost mezi aktualizacemi registrace. Service Bus nabízí optimistické řízení souběžnosti pro správu registrace. Na úrovni HTTP je to implementováno s použitím ETag na operacích správy registrace. Tato funkce je transparentně používána sadami Microsoft SDK, které vyvolány výjimku, pokud je aktualizace odmítnuta z důvodů souběžnosti. Back-end aplikace je zodpovědný za zpracování těchto výjimek a opakování aktualizace v případě potřeby.
