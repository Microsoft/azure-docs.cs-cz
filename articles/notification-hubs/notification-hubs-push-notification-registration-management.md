---
title: Správa registrací
description: Toto téma vysvětluje, jak zaregistrovat zařízení pomocí služby notification hubs, abyste mohli přijímat nabízená oznámení.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: da1cad5c949579e0c66d9cc49f99b7cee9d53f35
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452237"
---
# <a name="registration-management"></a>Správa registrací

## <a name="overview"></a>Přehled

Toto téma vysvětluje, jak zaregistrovat zařízení pomocí služby notification hubs, abyste mohli přijímat nabízená oznámení. Téma popisuje registrace na vysoké úrovni a pak zavádí dva hlavní způsoby registrace zařízení: registrace ze zařízení přímo k centru oznámení a registrace prostřednictvím back-endu aplikace.

## <a name="what-is-device-registration"></a>Co je registrace zařízení

Registrace zařízení ve službě Centrum oznámení se provádí pomocí **registrace** nebo **instalace**.

### <a name="registrations"></a>Registrace

Registrace přidruží popisovač služby oznamování platformy (PNS) pro zařízení se značkami a případně šablony. Popisovač systému oznámení platformy může být parametr ChannelURI, token zařízení nebo id registrace služby GCM. Značky se používají k oznámení směrovat na správná sada popisovače zařízení. Další informace najdete v tématu [směrování a výrazy označení](notification-hubs-tags-segment-push-message.md). Šablony slouží k provedení transformace za registraci. Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

### <a name="installations"></a>Instalace

Instalace je vylepšený vlastnosti související s registrací, který obsahuje kontejner nabízených oznámení. Je nejnovější a nejlepší přístup k registraci zařízení. Však není podporována sadou SDK pro .NET na straně klienta ([SDK centra oznámení pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ještě.  To znamená, že pokud se registrace ze samotného klientského zařízení, je třeba použít [rozhraní REST API pro Notification Hubs](https://msdn.microsoft.com/library/mt621153.aspx) přístup pro podporu instalace. Pokud používáte službu back-endu, byste měli moct používat [SDK centra oznámení pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Tady jsou některé klíčové výhody použití zařízení:

- Vytvoření nebo aktualizace instalace je plně idempotentní. Takže můžete opakovat bez jakékoli obavy o duplicitní registrace.
- Instalační model usnadňuje provést individuální nabízená oznámení – cílení na konkrétní zařízení. Systémovou značku **"$InstallationId: [hodnota installationId]"** se automaticky přidá s každé registraci na základě instalace. Takže můžete volat odeslání na tuto značku cílit na konkrétní zařízení bez nutnosti vytvářet další kód.
- Použití zařízení také vám umožní registraci částečné aktualizace. Částečné aktualizace instalace je požadováno pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902). To je užitečné, pokud chcete aktualizovat značky na registraci. Není nutné stáhnout celý registrace a pak znovu odeslat všechny předchozí značky.

Instalace může obsahovat následující vlastnosti. Úplný seznam vlastností instalace, najdete v části [vytvoření nebo instalaci přepsat rozhraní REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) nebo [vlastnosti instalace](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

```javascript
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
> Ve výchozím nastavení registrace a instalace, nevyprší platnost.

Registrace a instalace musí obsahovat platný popisovač systému oznámení platformy pro každé zařízení nebo kanálu. Protože popisovačů systému PNS lze získat pouze v klientské aplikaci na zařízení, jeden vzor je zaregistrovat přímo na tomto zařízení pomocí klientské aplikace. Na druhé straně důležité informace o zabezpečení a obchodní logiky související s značek může vyžadovat ke správě při registraci zařízení v back-end aplikace.

### <a name="templates"></a>Šablony

Pokud chcete použít [šablony](notification-hubs-templates-cross-platform-push-messages.md), instalace zařízení obsahuje také všechny šablony, které jsou spojené s tímto zařízením v JSON formátu (viz ukázka výše). Názvy šablon pomoct cílové různé šablony služby pro stejné zařízení.

Název každé šablony se mapuje na šablony textu a volitelná sada značky. Kromě toho jednotlivé platformy, může mít vlastnosti další šablony. Pro Windows Store (s použitím služby nabízených oznámení Windows) a Windows Phone 8 (pomocí MPNS) další sadu záhlaví může být součástí šablony. V případě služby APN můžete nastavit vlastnost vypršení platnosti buď konstanta, nebo výraz šablony. Úplný seznam najdete v tématu instalace vlastnosti [vytvoření nebo instalaci přepsat REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) tématu.

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundární dlaždice pro aplikace Windows Store

Pro Windows Store klientské aplikace odesíláním oznámení na sekundární dlaždice je stejný jako posílá do primární. To je podporováno také v zařízení. Sekundární dlaždice mají různé parametr ChannelUri, která zpracovává sadu SDK na vaší klientské aplikace transparentní.

Slovník SecondaryTiles používá stejné TileId, který se používá k vytvoření objektu SecondaryTiles v aplikaci pro Windows Store.
Stejně jako u primární parametr ChannelUri ChannelUris sekundární dlaždice můžete změnit v daném okamžiku provádějí. Aby bylo možné zachovat zařízení v centru oznámení, aktualizovat, musíte aktualizovat zařízení s aktuální ChannelUris sekundární dlaždice je.

## <a name="registration-management-from-the-device"></a>Správa registrací zařízení

Při správě registraci zařízení z klientských aplikací, je odpovědná za zasílání oznámení pouze back-endu. Klientské aplikace aktualizovat popisovačů systému PNS a zaregistrujte značky. Tento model je znázorněný na následujícím obrázku.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zařízení nejdřív načte popisovač systému oznámení platformy z systém oznámení platformy a pak zaregistruje v centru oznámení přímo. Po úspěšné registraci se back-end aplikace může odeslat oznámení cílení na registraci. Další informace o tom, jak odesílat oznámení, naleznete v tématu [směrování a výrazy označení](notification-hubs-tags-segment-push-message.md).

V tomto případě používáte pouze oprávnění k naslouchání pro přístup k vaší notification hubs ze zařízení. Další informace najdete v tématu [zabezpečení](notification-hubs-push-notification-security.md).

Registrace zařízení je nejjednodušší způsob, ale má určité nevýhody:

- Klientská aplikace lze aktualizovat pouze její klíčová slova, když je aplikace aktivní. Například pokud má uživatel dvě zařízení, které registrují značky související s týmy sportu, během prvního zařízení zaregistruje pro další značka (třeba Seahawks), ve druhém zařízení nebudou přijímat oznámení o Seahawks dokud je aplikace na druhém zařízení spustit znovu. Obecně platí když značky jsou ovlivněny více zařízení, správě značek z back-endu je žádoucí možnost.
- Protože aplikace mohou být zneužity, zabezpečení registrace konkrétní značky vyžaduje velmi opatrně, jak je popsáno v části "zabezpečení na úrovni značku."

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Ukázkový kód pro registraci centra oznámení ze zařízení pomocí instalace

V tuto chvíli je podporováno pouze pomocí [rozhraní REST API pro Notification Hubs](https://msdn.microsoft.com/library/mt621153.aspx).

Můžete také pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) pro aktualizaci instalaci.

```
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

// If we have not stored a installation id in application data, create and store as application data.
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Ukázkový kód pro registraci centra oznámení ze zařízení pomocí registrace

Tyto metody vytvořit nebo aktualizovat registraci pro zařízení, na kterém jsou volány. To znamená, že za účelem aktualizace popisovač nebo značky, musí přepsat celý registrace. Mějte na paměti, že registrace jsou přechodné, takže byste měli vždy mít spolehlivé úložiště s aktuální značky, které jsou v konkrétní zařízení.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
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

## <a name="registration-management-from-a-backend"></a>Správa registrace z back-endu.

Správa registrace z back-end vyžaduje psaním dalšího kódu. Aplikace ze zařízení musíte zadat zpracování aktualizace systému oznámení platformy na back-end při každém spuštění aplikace (spolu s značky a šablony) a back-end musíte aktualizovat tento ovladač v centru oznámení. Tento návrh je znázorněný na následujícím obrázku.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Výhody Správa registrace z back-endu patří schopnost upravit značky pro registrace i v případě, že odpovídající aplikace na zařízení není aktivní a ověření klienta aplikace před přidáním značky k jeho registraci.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Ukázkový kód pro registraci centra oznámení z back-end pomocí instalace

Klientské zařízení stále získá jeho popisovač systému oznámení platformy a vlastnosti relevantní instalace jako před a volání vlastních rozhraní API na back-end, který může provádět registraci a autorizujte značek atd. Můžete využívat back-endu [SDK centra oznámení pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Můžete také pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) pro aktualizaci instalaci.

```
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
        case "gcm":
            installation.Platform = NotificationPlatform.Gcm;
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Ukázkový kód pro registraci centra oznámení ze zařízení s použitím ID registrace

Z back-endu aplikace můžete provádět základní operace CRUDS při registraci. Příklad:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Back-endu, musí umět zpracovat souběžnosti mezi aktualizací registrací. Service Bus nabízí optimistického řízení souběžnosti pro správu registrace. Na úrovni protokolu HTTP to je implementováno s použitím značky ETag na operací správy registrace. Tato funkce slouží transparentně Microsoft SDKs, která je vyvolána výjimka, pokud aktualizace byl odmítnut z důvodu souběžnosti. Back-endu aplikace je zodpovědná za zpracování těchto výjimek a opakování pokusu o aktualizaci v případě potřeby.
