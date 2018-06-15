---
title: Nabízená oznámení v monitorované geografické zóně s Azure Notification Hubs a Bing Spatial Data | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak pomocí Azure Notification Hubs a Bing Spatial Data doručovat nabízená oznámení na základě polohy.
services: notification-hubs
documentationcenter: windows
keywords: nabízené oznámení,nabízená oznámení
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 27f978fac1f8aa68aa0eb1a6ffcec4e0e81b0df5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778439"
---
# <a name="tutorial-push-location-based-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Kurz: Zasílání nabízených oznámení na základě polohy pomocí Azure Notification Hubs a Bing Spatial Data
V tomto kurzu se dozvíte, jak pomocí Azure Notification Hubs a Bing Spatial Data doručovat nabízená oznámení na základě polohy. 

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Nastavení zdroje dat
> * Nastavení aplikace pro UPW
> * Nastavení back-endu
> * Test nabízených oznámení v aplikaci pro Univerzální platformu Windows (UPW)


## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) nebo novější ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)) 
- Nejnovější verze [Azure SDK](https://azure.microsoft.com/downloads/) 
- [Účet na webu Dev Center pro Mapy Bing](https://www.bingmapsportal.com/) (Je možné si jej vytvořit zdarma a přidružit si ho k účtu Microsoft.) 

## <a name="set-up-the-data-source"></a>Nastavení zdroje dat

1. Přihlaste se k webu [Dev Center pro Mapy Bing](https://www.bingmapsportal.com/). 
2. V horním navigačním panelu vyberte **Zdroje dat** a vyberte **Spravovat zdroje dat**. 

    ![](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Pokud nemáte žádný zdroj dat, zobrazí se odkaz na jeho vytvoření. Vyberte **Nahrát data jako zdroj dat**. Můžete použít také nabídku **Zdroje dat** > **Nahrát data**. 

    ![](./media/notification-hubs-geofence/bing-maps-create-data.png)

4. Na pevném disku vytvořte soubor **NotificationHubsGeofence.pipe** s následujícím obsahem: V tomto kurzu použijete jednoduchý soubor založený na kanálu, který ohraničuje oblast pobřeží San Francisca:

    ```
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Soubor kanálu představuje tuto entitu:
    
    ![](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Na stránce **Nahrát zdroj dat** proveďte následující akce:
    1. Jako **Formát dat** vyberte **kanál**.
    2. Vyhledejte a vyberte soubor **NotificationHubGeofence.pipe**, který jste vytvořili v předchozím kroku. 
    3. Vyberte tlačítko **Nahrát**. 
    
    > [!NOTE]
    > Může se zobrazit výzva k zadání nového klíče jako **hlavního klíče**, který se bude lišit od **klíče dotazu**. Nový klíč jednoduše vytvořte přes řídicí panel a aktualizujte stránku pro nahrání zdroje dat.
6. Jakmile nahrajete datový soubor, bude nezbytné publikovat zdroj dat. Stejně jako předtím vyberte **Zdroje dat** -> **Spravovat zdroje dat**. 
7. V seznamu vyberte váš zdroj dat a ve sloupci **Akce** zvolte **Publikovat**. 

    ![](./media/notification-hubs-geofence/publish-button.png)
8. Přepněte na kartu **Publikované zdroje dat** a zkontrolujte, že se v seznamu zobrazí váš zdroj dat.

    ![](./media/notification-hubs-geofence/bing-maps-published-data.png)

9. Vyberte **Upravit**. Uvidíte (na první pohled), jaká umístění jsou součástí dat.

    ![](./media/notification-hubs-geofence/bing-maps-data-details.png)

    V tuto chvíli portál nezobrazuje hranice monitorované geografické zóny, kterou jste vytvořili – stačí vám pouze potvrzení, že zadaná lokalita je ve správném okolí.
8. Nyní máte všechny požadavky na zdroj dat. Pokud chcete získat podrobnosti o adrese URL žádosti pro volání rozhraní API, na webu Dev Center pro Mapy Bing zvolte **Zdroje dat** a vyberte **Informace o zdroji dat**.

    ![](./media/notification-hubs-geofence/bing-maps-data-info.png)

    **Adresa URL dotazu** je koncový bod, proti kterému můžete spouštět dotazy, abyste zjistili, jestli se zařízení aktuálně nachází uvnitř lokality nebo ne. K provedení této kontroly stačí na adresu URL dotazu provést volání GET s připojenými následujícími parametry:

    ```
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Služba Mapy Bing automaticky provádí výpočty a kontroluje, jestli se zařízení nachází uvnitř monitorované geografické zóny. Jakmile přes prohlížeč (nebo nástroj cURL) provedete požadavek, obdržíte standardní odpověď JSON:

    ![](./media/notification-hubs-geofence/bing-maps-json.png)

    Tato odpověď se odesílá jen v případě, že se bod nachází v určené oblasti. Pokud se tam nenachází, obdržíte prázdný kontejner **výsledku**:

    ![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Nastavení aplikace pro UPW

1. V nástroji Visual Studio vytvořte nový projekt typu **Prázdná aplikace (univerzální pro Windows)**.

    ![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Jakmile se vytváření projektu dokončí, měli byste mít základ samotné aplikace. Nyní nastavme vše pro monitorovanou geografickou zónu. Vzhledem k tomu, že pro toto řešení využijete služby Bing, je k dispozici veřejný koncový bod rozhraní REST API, který umožňuje dotazovat se na konkrétní oblasti lokality:

    http://spatial.virtualearth.net/REST/v1/data/

    Zprovozníte ho zadáním následujících parametrů:

    - **ID zdroje dat** a **Název zdroje dat** – v rozhraní API Map Bing zdroje dat obsahují různá kategorizovaná metadata, například lokality a pracovní doby provozu.  
    - **Název entity** – entita, kterou chcete použít jako referenční bod pro oznámení. 
    - **Klíč rozhraní API Map Bing** – klíč, který jste dříve získali při vytváření účtu Dev Center pro Bing.

    Když teď máte připravený zdroj dat, můžete začít pracovat na aplikaci pro UPW.
2. Povolte pro vaši aplikaci zjišťování polohy. V **Průzkumníku řešení** otevřete soubor `Package.appxmanifest`.

    ![](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Na kartě vlastností balíčku, která se právě otevřela, přepněte na kartu **Schopnosti** a vyberte **Poloha**.

    ![](./media/notification-hubs-geofence/vs-package-location.png)

4. Vytvořte v řešení novou složku `Core` a přidejte do ní nový soubor `LocationHelper.cs`:

    ![](./media/notification-hubs-geofence/vs-location-helper.png)

    Třída `LocationHelper` obsahuje kód pro získání polohy uživatele prostřednictvím systémového rozhraní API:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Další informace o získávání polohy uživatele v aplikacích pro UPW najdete v tématu [Získání polohy uživatele](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

5. Pokud chcete zkontrolovat, že získávání polohy skutečně funguje, otevřete kód hlavní stránky (`MainPage.xaml.cs`). Vytvořte novou obslužnou rutinu události pro událost `Loaded` v konstruktoru `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Implementace obslužné rutiny události bude následující:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Spusťte aplikaci a povolte jí přístup k informacím o vaší poloze.

    ![](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Jakmile se aplikace spustí, měli byste v okně **Výstup** vidět souřadnice:

    ![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Teď víte, že získávání polohy funguje. Pokud chcete, můžete odstranit obslužnou rutinu události Loaded, protože už ji nebudete používat.
8. Dalším krokem je zachycení změň v poloze. Do třídy `LocationHelper` přidejte obslužnou rutinu události pro `PositionChanged`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    Implementace zobrazuje souřadnice polohy v okně **Výstup**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Nastavení back-endu
1. Stáhněte si [ukázku back-endu .NET z GitHubu](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 
2. Až se stahování dokončí, otevřete složku `NotifyUsers` a pak v sadě Visual otevřete soubor `NotifyUsers.sln`. 
3. Nastavte projekt `AppBackend` jako **Spouštěný projekt** a spusťte jej.

    ![](./media/notification-hubs-geofence/vs-startup-project.png)

    Projekt je již nakonfigurovaný tak, aby do cílových zařízení odesílal nabízená oznámení, proto bude potřeba udělat jen dvě věci – zadat správný připojovací řetězec centra oznámení a přidat identifikaci hranice, aby se oznámení odesílalo jenom v případě, že se uživatel nachází uvnitř monitorové geografické zóny.
4. Pro konfiguraci připojovacího řetězce otevřete ve složce `Models` soubor `Notifications.cs`. Funkce `NotificationHubClient.CreateClientFromConnectionString` by měla obsahovat informace o centru oznámení, které můžete získat na webu [Azure Portal](https://portal.azure.com) (podívejte se na stránku **Zásady přístupu** v **Nastavení**). Uložte aktualizovaný konfigurační soubor.
5. Vytvořte model pro výsledek rozhraní API Map Bing. Nejjednodušší způsob, jak toho docílit, je otevřít složku `Models` a zvolit **Přidat** > **Třída**. Pojmenujte ji `GeofenceBoundary.cs`. Pak zkopírujte JSON z odpovědi rozhraní API, kterou jste obdrželi v první části. V sadě Visual Studio použijte možnost **Upravit** > **Vložit jinak** > **Vložit formát JSON jako třídy**. 

    Tímto způsobem zajistíte, že se objekt deserializuje přesně podle očekávání. Výsledná sada tříd by měla vypadat přibližně jako následující třída:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Dále otevřete `Controllers` > `NotificationsController.cs`. Upravte volání Post tak, aby používalo cílovou zeměpisnou délku a šířku. Provedete to tak, že do signatury funkce přidáte dva řetězce – `latitude` a `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Vytvořte v projektu novou třídu `ApiHelper.cs`. Použijete ji k připojení k Bingu pro kontrolu průsečíků hranic. Implementujte funkci `IsPointWithinBounds`, jak je znázorněno v následujícím kódu:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Je nezbytné nahradit koncový bod rozhraní API adresou URL dotazu, kterou jste získali dříve z webu Dev Center pro Bing (totéž platí pro klíč rozhraní API). 

    Pokud dotaz vrací výsledky, znamená to, že se zadaný bod nachází uvnitř monitorované geografické zóny, proto funkce vrací `true`. Pokud žádné výsledky nejsou, Bing vám oznamuje, že se bod nachází mimo rámec vyhledávání, proto funkce vrací `false`.
8. V souboru `NotificationsController.cs` vytvořte kontrolu přímo před příkazem switch:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Test nabízených oznámení v aplikaci pro UPW

1. V aplikaci pro UPW byste nyní měli být schopni oznámení otestovat. Ve třídě `LocationHelper` vytvořte novou funkci – `SendLocationToBackend`:

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Nastavte `POST_URL` na umístění vaší nasazené webové aplikace. V tuto chvíli je možné ji spouštět lokálně, ale během nasazování veřejné verze bude nutné ji hostovat pomocí externího poskytovatele.
1. Zaregistrujte aplikaci pro UPW k nabízeným oznámením. V sadě Visual Studio zvolte **Projekt** > **Store** > **Přidružit aplikaci ve Store**.

    ![](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Jakmile se přihlásíte ke svému účtu vývojáře, ujistěte se, že jste vybrali existující aplikaci, nebo vyberte novou a přidružte k ní balíček. 
4. Přejděte na Dev Center a otevřete aplikaci, kterou jste vytvořili. Zvolte **Služby** > **Nabízená oznámení** > **Web služeb Live Services**.

    ![](./media/notification-hubs-geofence/ms-live-services.png)
5. Na webu si poznamenejte **Tajný klíč aplikace** a **SID balíčku**. Obojí budete potřebovat na webu Azure Portal – otevřete své centrum oznámení, zvolte **Nastavení** > **Notification Services** > **Windows (WNS)** a do požadovaných polí zadejte příslušné informace.

    ![](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Zvolte **Uložit**.
7. V **Průzkumníku řešení** otevřete **Odkazy** a vyberte **Spravovat balíčky NuGet**. Přidejte odkaz na **spravovanou knihovnu Microsoft Azure Service Bus** – jednoduše vyhledejte balíček `WindowsAzure.Messaging.Managed` a přidejte ho do projektu.

    ![](./media/notification-hubs-geofence/vs-nuget.png)

7. Pro účely testování znovu vytvořte obslužnou rutinu události `MainPage_Loaded` a přidejte do ní tento fragment kódu:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Kód zaregistruje aplikaci do centra oznámení. Jste připraveni! 
8. V `LocationHelper` uvnitř obslužné rutiny `Geolocator_PositionChanged` můžete přidat testovací kód, který bod nuceně umístí do monitorové geografické zóny:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

9. Jelikož nepředáváte skutečné souřadnice (které by v tuto chvíli nemusely odpovídat místu uvnitř oblasti) a používáte předdefinované testovací hodnoty, uvidíte, že se při aktualizaci zobrazí oznámení:

    ![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Další kroky
Pokud chcete řešení připravit na reálný provoz, může být zapotřebí podniknout několik kroků.

1. Nejprve je potřeba zajistit, že monitorované geografické zóny jsou dynamické. To vyžaduje další práci s rozhraním API služby Bing, aby bylo možné nahrávat nové hranice do existujícího zdroje dat. Další informace najdete v [dokumentaci rozhraní API pro Bing Spatial Data Services](https://msdn.microsoft.com/library/ff701734.aspx).
2. Abyste zajistili doručování správným účastníkům, můžete požadovat cílení pomocí [tagování](notification-hubs-tags-segment-push-message.md).

Řešení uvedené v tomto kurzu popisuje scénář, při kterém můžete mít širokou škálu cílových platforem, proto se monitorování geografické zóny neomezuje schopnostmi specifickými pro daný systém. Je ale nutné dodat, že Univerzální platforma Windows nabízí integrované možnosti pro [detekci monitorované geografické zóny](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).


