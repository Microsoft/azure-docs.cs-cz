---
title: Registrace správy
description: Toto téma vysvětluje, jak k registraci zařízení s centry oznámení, aby bylo možné přijímat nabízená oznámení.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7f9052da066fcc0021151bf3b547484859cf216d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="registration-management"></a>Správa registrací
## <a name="overview"></a>Přehled
Toto téma vysvětluje, jak k registraci zařízení s centry oznámení, aby bylo možné přijímat nabízená oznámení. Téma popisuje registrace na vysoké úrovni a potom zavádí dvě hlavní vzory pro registraci zařízení: registraci ze zařízení přímo k centru oznámení a registraci prostřednictvím back-end aplikace. 

## <a name="what-is-device-registration"></a>Co je registrace zařízení
Registrace zařízení s centrem oznámení se provádí pomocí **registrace** nebo **instalace**.

#### <a name="registrations"></a>Registrace
Registrace přidruží popisovač služby oznámení platformy (PNS) pro zařízení značky a případně šablonu. Popisovače systému PNS může být ChannelURI, token zařízení nebo id registrace GCM. Značky se používají k oznámení směrovat na správná sada popisovačů zařízení. Další informace najdete v tématu [směrování a značky výrazy](notification-hubs-tags-segment-push-message.md). Šablony slouží k implementaci za registraci transformace. Další informace najdete v tématu [šablony](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalace
Instalace je vylepšený registrace, který zahrnuje kontejner nabízené souvisejících vlastností. Je nejnovější a nejlepší způsob registrace zařízení. Však není podporována sadou SDK .NET na straně klienta ([SDK centra oznámení pro back-end operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ještě.  To znamená, že pokud registrujete ze samotného klientského zařízení, je třeba použít [rozhraní API REST centra oznámení](https://msdn.microsoft.com/library/mt621153.aspx) přístup k podpoře instalaci. Pokud používáte back-end službu, byste měli použít [SDK centra oznámení pro back-end operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Tady jsou některé klíčové výhody pomocí instalace:

* Vytvoření nebo aktualizace instalace je plně idempotent. Proto ji můžete zopakovat bez jakékoli pochybnostmi duplicitní registrace.
* Instalační model usnadňuje provést jednotlivé nabízených oznámení - cílení na konkrétní zařízení. Značku systému **"$InstallationId: [installationId]"** se automaticky přidá s každou instalace na základě registrace. Proto můžete volat odeslání na tuto značku zaměřit na konkrétní zařízení bez nutnosti psaní Další.
* Pomocí instalace také umožňuje provést registraci částečné aktualizace. Částečné aktualizace instalace se požaduje pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902). To je užitečné, pokud chcete aktualizovat značky na registraci. Nemáte stahují celý registrace a pak znovu odeslat všechny předchozí značky.

Instalace může obsahovat následující vlastnosti. Úplný seznam vlastností instalace, najdete v části [vytvoření nebo instalaci přepsat REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) nebo [vlastnosti instalace](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

    // Example installation format to show some supported properties
    {,
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



Je důležité si uvědomit, že registrace a instalace ve výchozím nastavení už platnost.

Registrace a instalace musí obsahovat platný popisovače systému PNS pro každé zařízení nebo kanál. Vzhledem k tomu, že popisovačů systému PNS se dá získat jenom v klientskou aplikaci na zařízení, je jeden vzor zaregistrovat přímo na daném zařízení s klientské aplikace. Na druhé straně aspekty zabezpečení a obchodní logiku související s značky může vyžadovat umožňuje spravovat registraci zařízení v back-end aplikace. 

#### <a name="templates"></a>Šablony
Pokud chcete použít [šablony](notification-hubs-templates-cross-platform-push-messages.md), instalace zařízení také obsahuje všechny šablony, které jsou spojené s tímto zařízením v JSON formátu (viz ukázka výše). Názvy šablon pomoci cíl různé šablony pro stejné zařízení.

Každý název šablony se mapuje na text šablony a volitelná sada značky. Kromě toho každou platformu můžete mít další šablony vlastnosti. Pro Windows Store (pomocí WNS) a Windows Phone 8 (pomocí MPNS) může být další sadu hlaviček součástí šablony. V případě služby APN můžete nastavit vlastnost vypršení platnosti buď konstanta, nebo výraz šablony. Úplný seznam najdete v tématu instalace vlastnosti [vytvoření nebo instalaci přepsat REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) tématu.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundární dlaždice pro aplikace pro Windows Store
Pro klientské aplikace Windows Store odesílání oznámení do sekundární dlaždice je stejný jako je pošlete na primární. To je podporováno také v instalaci. Sekundární dlaždice mít jiný ChannelUri, která zpracovává sadu SDK na vaší klientské aplikace transparentně.

Slovník SecondaryTiles používá stejné TileId, který se používá k vytvoření objektu SecondaryTiles v aplikaci Windows Store.
Stejně jako u primární ChannelUri ChannelUris sekundární dlaždice můžete změnit v každém okamžiku. Chcete-li zachovat instalace portálu v centru oznámení, aktualizovat, musíte aktualizovat zařízení s aktuální ChannelUris sekundární dlaždic je.

## <a name="registration-management-from-the-device"></a>Správa registraci ze zařízení
Při správě registraci zařízení z klientské aplikace, je odpovědná za zasílání oznámení pouze back-end. Klientské aplikace zachovávat aktuálnost popisovačů systému PNS a zaregistrujte značky. Následující obrázek znázorňuje tento vzor.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zařízení se nejdřív načte popisovače systému PNS z systém PNS a potom zaregistruje s centrem oznámení přímo. Po úspěšné registraci back-end aplikace může odesílat oznámení cílení k registraci. Další informace o tom, jak odesílat oznámení najdete v tématu [směrování a značky výrazy](notification-hubs-tags-segment-push-message.md).
V takovém případě použijete pouze naslouchání práva pro přístup k vaší centra oznámení ze zařízení. Další informace najdete v tématu [zabezpečení](notification-hubs-push-notification-security.md).

Nejjednodušším způsobem je registraci ze zařízení, ale má některé nevýhody.
První nevýhodou je, že klientská aplikace lze aktualizovat pouze jeho značky když je aplikace aktivní. Například pokud má uživatel dvě zařízení, které registrují značky související s sport týmy během první zařízení zaregistruje pro další značky (například Seahawks), druhé zařízení nebude dostávat oznámení o Seahawks aplikace na druhé zařízení je proveden ještě jednou. Obecně platí když značky jsou ovlivněné více zařízení, správa značky z back-end je žádoucí možnost.
Druhý nevýhodou registrace správy z klientské aplikace je, že vzhledem k tomu, že aplikace může hacker, zabezpečení, aby se registrace konkrétními značkami vyžaduje zvláštní pozornost, jak je popsáno v části "zabezpečení na úrovni značky."

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Ukázkový kód pro registraci se Centrum oznámení ze zařízení pomocí instalace
V tuto chvíli je podporováno pouze pomocí [rozhraní API REST centra oznámení](https://msdn.microsoft.com/library/mt621153.aspx).

Můžete taky pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) pro aktualizaci instalaci.

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



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Ukázkový kód pro registraci se Centrum oznámení ze zařízení pomocí registrace
Tyto metody vytvořit nebo aktualizovat registrace pro zařízení, na které se nazývají. To znamená, musí přepsat za účelem aktualizace popisovač nebo značky, celý registrace. Mějte na paměti, že jsou registrace přechodný, a proto byste měli mít vždy spolehlivé úložiště s aktuální značky, které potřebuje určité zařízení.

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


## <a name="registration-management-from-a-backend"></a>Registrace správy z back-end
Správa registrace z back-end vyžaduje zápis další kód. Aplikace ze zařízení musíte zadat zpracovat aktualizovaný systém PNS a back-end při každém spuštění aplikace (společně se značkami a šablony) a back-end musíte aktualizovat tento popisovač v centru oznámení. Následující obrázek znázorňuje tento návrh.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Výhody správy registrace z back-end, zahrnují možnost Upravit značky na registrací i v případě, že odpovídající aplikace na zařízení je neaktivní a k ověření klientské aplikace před přidáním značky k jeho registraci.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Ukázkový kód pro registraci se centra oznámení z back-end pomocí instalace
Klientské zařízení stále získá jeho popisovače systému PNS a vlastnosti relevantní instalace jako před a volání vlastní rozhraní API na back-end, které můžete provést registraci a autorizaci značky atd. Můžete využít back-end [SDK centra oznámení pro back-end operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Můžete taky pomocí metody PATCH [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) pro aktualizaci instalaci.

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


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Ukázkový kód pro registraci se Centrum oznámení ze zařízení pomocí ID registrace
Z vašeho back-end aplikace můžete provádět základní operace CRUDS při registraci. Příklad:

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


Back-end musí zpracovávat souběžnosti mezi aktualizace registrace. Service Bus nabízí optimistické řízení souběžného pro správu registrace. Na úrovni protokolu HTTP tato možnost je implementovaná pomocí použití značky ETag na operace správy registrace. Tato funkce slouží transparentně SDKs Microsoft, která je vyvolána výjimka, pokud aktualizace byl odmítnut z důvodů souběžnosti. Back-end aplikace je zodpovědná za zpracování těchto výjimek a v případě potřeby opakováním aktualizace.

