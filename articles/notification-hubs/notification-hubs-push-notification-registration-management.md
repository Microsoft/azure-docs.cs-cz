---
title: Správa registrací
description: Toto téma vysvětluje, jak registrovat zařízení pomocí Centra oznámení, aby bylo možné přijímat nabízená oznámení.
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
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001565"
---
# <a name="registration-management"></a>Správa registrací

Toto téma vysvětluje, jak registrovat zařízení pomocí Centra oznámení, aby bylo možné přijímat nabízená oznámení. Téma popisuje registrace na vysoké úrovni a pak zavádí dva hlavní vzory pro registraci zařízení: registraci ze zařízení přímo do centra oznámení a jejich registraci prostřednictvím back-endu aplikace.

## <a name="what-is-device-registration"></a>Co je registrace zařízení

Registrace zařízení pomocí Centra oznámení se provádí pomocí **registrace** nebo **instalace**.

### <a name="registrations"></a>Registrace

Registrace přidružuje popisovač PNS (Platform Notification Service) pro zařízení s značkami a pravděpodobně šablonou. Popisovač PNS může být parametr channeluri, token zařízení nebo ID registrace FCM. Značky se používají ke směrování oznámení na správnou sadu popisovačů zařízení. Další informace najdete v tématu [výrazy směrování a značek](notification-hubs-tags-segment-push-message.md). Šablony slouží k implementaci transformace na jednu registraci. Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs podporuje maximálně 60 značek na jedno zařízení.

### <a name="installations"></a>Instalace

Instalace je vylepšená registrace, která zahrnuje kontejner souvisejících vlastností nabízených oznámení. Je to nejnovější a nejlepší přístup k registraci vašich zařízení. Není ale podporována na straně klienta .NET SDK ([oznámení SDK pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)).  To znamená, že pokud provádíte registraci z samotného klientského zařízení, budete muset použít [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation) přístup k podpoře instalací. Pokud používáte back-end službu, měli byste být schopni použít [sadu SDK centra oznámení pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Níže jsou uvedeny některé klíčové výhody použití instalací:

- Vytvoření nebo aktualizace instalace je plně idempotentní. Takže ho můžete opakovat bez jakýchkoli obav o duplicitních registracích.
- Model instalace podporuje speciální formát značek ( `$InstallationId:{INSTALLATION_ID}` ), který umožňuje posílání oznámení přímo na konkrétní zařízení. Například pokud kód aplikace nastaví ID instalace `joe93developer` pro toto konkrétní zařízení, může vývojář toto zařízení cílit při odesílání oznámení do `$InstallationId:{joe93developer}` značky. To vám umožní zaměřit se na konkrétní zařízení, aniž byste museli provádět žádné další kódování.
- Použití instalací také umožňuje provádět částečné aktualizace registrace. Částečná aktualizace instalace se vyžaduje pomocí metody PATCH [standardu JSON-patch](https://tools.ietf.org/html/rfc6902). To je užitečné, když chcete aktualizovat značky v registraci. Nemusíte si vyžádat celou registraci a znovu znovu odeslat všechny předchozí značky.

Instalace může obsahovat následující vlastnosti. Úplný seznam vlastností instalace najdete v tématu [Vytvoření nebo přepsání instalace pomocí REST API](/rest/api/notificationhubs/create-overwrite-installation) nebo [vlastností instalace](/dotnet/api/microsoft.azure.notificationhubs.installation).

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
> Ve výchozím nastavení nevyprší platnost registrací a instalací.

Registrace a instalace musí obsahovat platný popisovač PNS pro každé zařízení nebo kanál. Vzhledem k tomu, že popisovače PNS se dají získat jenom v klientské aplikaci na zařízení, jeden ze vzorů se registruje přímo na tomto zařízení pomocí klientské aplikace. Na druhé straně, aspekty zabezpečení a obchodní logiky týkající se značek můžou vyžadovat, abyste mohli spravovat registraci zařízení v back-endu aplikace.

> [!NOTE]
> Rozhraní API pro instalaci nepodporuje službu Baidu (i když rozhraní API registrací dělá). 

### <a name="templates"></a>Šablony

Pokud chcete používat [šablony](notification-hubs-templates-cross-platform-push-messages.md), má instalace zařízení také všechny šablony přidružené k danému zařízení ve formátu JSON (viz Ukázka výše). Názvy šablon můžou pro stejné zařízení cílit na jiné šablony.

Každý název šablony se mapuje na tělo šablony a volitelná sada značek. Každá platforma navíc může mít další vlastnosti šablony. Pro Windows Store (s použitím WNS) a Windows Phone 8 (pomocí MPNS) může být součástí šablony další sada hlaviček. V případě APNs můžete nastavit vlastnost vypršení platnosti buď na konstantu, nebo na výraz šablony. Úplný seznam vlastností instalace najdete v tématu [Vytvoření nebo přepsání instalace pomocí tématu REST](/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundární dlaždice pro aplikace pro Windows Store

Pro klientské aplikace pro Windows Store je odesílání oznámení do sekundárních dlaždic stejné jako odeslání do primární dlaždice. To je podporováno i v instalacích. Sekundární dlaždice mají jiný parametr channeluri, který sada SDK v klientské aplikaci transparentně zpracovává.

Slovník SecondaryTiles používá stejný TileId, který se používá k vytvoření objektu SecondaryTiles v aplikaci pro Windows Store. Podobně jako u primárního parametr channeluri se může ChannelUris sekundární dlaždice kdykoli změnit. Aby se instalace v centru oznámení aktualizovala, zařízení je musí aktualizovat s aktuální ChannelUris sekundárních dlaždic.

## <a name="registration-management-from-the-device"></a>Správa registrace ze zařízení

Při správě registrace zařízení z klientských aplikací zodpovídá back-end jenom za posílání oznámení. Klientské aplikace udržují PNS a zaregistrují značky. Tento model je znázorněn na následujícím obrázku.

![Registrace ze zařízení](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zařízení nejdřív načte popisovač PNS z PNS a potom se zaregistruje přímo do centra oznámení. Po úspěšné registraci může back-end aplikace odeslat oznámení cílené na registraci. Další informace o tom, jak odesílat oznámení, najdete v tématu [výrazy směrování a značek](notification-hubs-tags-segment-push-message.md).

V takovém případě použijete pro přístup k centrům oznámení ze zařízení pouze práva naslouchání. Další informace najdete v tématu [zabezpečení](notification-hubs-push-notification-security.md).

Registrace ze zařízení je nejjednodušší způsob, ale má několik nevýhod:

- Klientská aplikace může aktualizovat pouze své značky, pokud je aplikace aktivní. Pokud má uživatel například dvě zařízení, která registrují značky související s sport teams, když první zařízení zaregistruje pro další značku (například Seahawks), druhé zařízení neobdrží oznámení o Seahawks, dokud se aplikace na druhém zařízení nespustí podruhé. Obecně platí, že pokud jsou značky ovlivněné více zařízeními, je žádoucí možnost spravovat značky z back-endu.
- Vzhledem k tomu, že aplikace mohou být napadeny, zabezpečení registrace u konkrétních značek vyžaduje zvláštní péči, jak je vysvětleno v článku [zabezpečení](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Ukázkový kód pro registraci v centru oznámení ze zařízení pomocí instalace

V současné době je tato podpora podporována pouze pomocí [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation).

K aktualizaci instalace můžete použít také metodu PATCH s použitím [standardu JSON-patch](https://tools.ietf.org/html/rfc6902) .

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

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
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
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
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Ukázkový kód pro registraci v centru oznámení ze zařízení pomocí registrace

Tyto metody vytvoří nebo aktualizují registraci pro zařízení, na kterém se nazývají. To znamená, že aby bylo možné aktualizovat popisovač nebo značky, je nutné přepsat celou registraci. Nezapomeňte, že registrace jsou přechodné, takže byste měli mít vždy spolehlivý obchod s aktuálními značkami, které konkrétní zařízení potřebuje.

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

Správa registrací ze služby back-end vyžaduje zápis dalšího kódu. Aplikace ze zařízení musí k back-endu poskytovat aktualizovaný PNS pokaždé, když se aplikace spustí (spolu se značkami a šablonami), a back-end musí tento popisovač aktualizovat v centru oznámení. Tento návrh je znázorněn na následujícím obrázku.

![Správa registrací](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Výhody správy registrací z back-endu zahrnují možnost upravit značky na registrace i v případě, že je odpovídající aplikace v zařízení neaktivní a ověřit klientskou aplikaci před přidáním značky k registraci.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Ukázkový kód pro registraci v centru oznámení z back-endu pomocí instalace

Klientské zařízení stále získá svůj PNS popisovač a relevantní vlastnosti instalace a zavolá vlastní rozhraní API na back-end, které může provést registraci a autorizovat značky atd. Back-end může využít [sadu SDK centra oznámení pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

K aktualizaci instalace můžete použít také metodu PATCH s použitím [standardu JSON-patch](https://tools.ietf.org/html/rfc6902) .

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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Ukázkový kód pro registraci v centru oznámení ze zařízení s použitím ID registrace

Z back-endu vaší aplikace můžete provádět základní operace CRUD při registracích. Například:

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

Back-end musí zvládnout souběžnost mezi aktualizacemi registrace. Service Bus nabízí optimistické řízení souběžnosti pro správu registrací. Na úrovni HTTP je tato implementace implementována s použitím značky ETag při operacích správy registrace. Tato funkce je transparentně používána Microsoft SDK, což vyvolá výjimku, pokud se aktualizace zamítla z důvodů souběžnosti. Back-end aplikace zodpovídá za zpracování těchto výjimek a v případě potřeby opakuje pokus o aktualizaci.
