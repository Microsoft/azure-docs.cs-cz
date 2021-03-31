---
title: Posílání nabízených oznámení pomocí Azure Notification Hubs a prostorových dat Bingu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Azure Notification Hubs a Bing Spatial Data doručovat nabízená oznámení na základě polohy.
services: notification-hubs
documentationcenter: windows
keywords: nabízená oznámení, nabízená oznámení
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91577998"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Kurz: odeslání nabízených oznámení na základě polohy pomocí Notification Hubs a prostorových dat Bingu

V tomto kurzu se dozvíte, jak pomocí Azure Notification Hubs a Bing Spatial Data doručovat nabízená oznámení na základě polohy.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Nastavení zdroje dat
> * Nastavení aplikace pro UPW
> * Nastavení back-endu
> * Test nabízených oznámení v aplikaci pro Univerzální platformu Windows (UPW)

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si  [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) nebo novější ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409))
* Nejnovější verze [Azure SDK](https://azure.microsoft.com/downloads/)
* [Účet na webu Dev Center pro Mapy Bing](https://www.bingmapsportal.com/) (Je možné si jej vytvořit zdarma a přidružit si ho k účtu Microsoft.)

## <a name="set-up-the-data-source"></a>Nastavení zdroje dat

1. Přihlaste se k webu [Dev Center pro Mapy Bing](https://www.bingmapsportal.com/).
2. V horním navigačním panelu vyberte **Zdroje dat** a vyberte **Spravovat zdroje dat**.

    ![Snímek webu Dev Center pro mapy Bing na stránce Správa zdrojů dat s možností nahrát data jako zdroj dat, která je označena červeně.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Pokud nemáte žádný zdroj dat, zobrazí se odkaz na jeho vytvoření. Vyberte **Nahrát data jako zdroj dat**. Můžete také použít nabídku **zdroje dat**  >  **Odeslat data** .

    ![Snímek obrazovky dialogového okna nahrát zdroj dat](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. `NotificationHubsGeofence.pipe`Na pevném disku vytvořte soubor s následujícím obsahem: v tomto kurzu použijete ukázkový soubor založený na kanálu, který je v oblasti sítě San Francisco Waterfront:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Soubor kanálu představuje tuto entitu:

    ![Snímek obrazovky s mapou sítě San Francisco Waterfront s červeným mnohoúhelníkem, který je v oblasti Piers.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Na stránce **Nahrát zdroj dat** proveďte následující akce:
   1. Jako **Formát dat** vyberte **kanál**.
   2. Procházejte a vyberte `NotificationHubGeofence.pipe` soubor, který jste vytvořili v předchozím kroku.
   3. Vyberte tlačítko **Nahrát**.

      > [!NOTE]
      > Může se zobrazit výzva k zadání nového klíče jako **hlavního klíče**, který se bude lišit od **klíče dotazu**. Nový klíč jednoduše vytvořte přes řídicí panel a aktualizujte stránku pro nahrání zdroje dat.
6. Jakmile nahrajete datový soubor, bude nezbytné publikovat zdroj dat. Vyberte **zdroje dat**  ->  **Správa zdrojů dat** , jako jste to předtím.
7. V seznamu vyberte váš zdroj dat a ve sloupci **Akce** zvolte **Publikovat**.

    ![Snímek webu Dev Center pro mapy Bing na stránce Správa zdrojů dat s vybranou kartou Data Sources a možností publikování popsaných červeně.](./media/notification-hubs-geofence/publish-button.png)
8. Přepněte na kartu **Publikované zdroje dat** a zkontrolujte, že se v seznamu zobrazí váš zdroj dat.

    ![Snímek webu Dev Center pro mapy Bing na stránce Spravovat zdroje dat se zvolenou kartou publikovaných zdrojů dat.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Vyberte **Upravit**. Uvidíte (na první pohled), jaká umístění jsou součástí dat.

    ![Snímek obrazovky se stránkou upravit data entity znázorňující mapu západních Spojených států a purpurovou tečku přes síť San Francisco Waterfront.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    V tuto chvíli portál nezobrazuje hranice monitorované geografické zóny, kterou jste vytvořili – stačí vám pouze potvrzení, že zadaná lokalita je ve správném okolí.
10. Nyní máte všechny požadavky na zdroj dat. Pokud chcete získat podrobnosti o adrese URL žádosti pro volání rozhraní API, na webu Dev Center pro Mapy Bing zvolte **Zdroje dat** a vyberte **Informace o zdroji dat**.

    ![Snímek webu Dev Center pro mapy Bing na stránce informace o zdroji dat](./media/notification-hubs-geofence/bing-maps-data-info.png)

    **Adresa URL dotazu** je koncový bod, proti kterému můžete spouštět dotazy, abyste zjistili, jestli se zařízení aktuálně nachází uvnitř lokality nebo ne. K provedení této kontroly stačí na adresu URL dotazu provést volání GET s připojenými následujícími parametry:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Služba Mapy Bing automaticky provádí výpočty a kontroluje, jestli se zařízení nachází uvnitř monitorované geografické zóny. Jakmile přes prohlížeč (nebo nástroj cURL) provedete požadavek, obdržíte standardní odpověď JSON:

    ![Snímek obrazovky se standardní odpovědí JSON.](./media/notification-hubs-geofence/bing-maps-json.png)

    Tato odpověď se odesílá jen v případě, že se bod nachází v určené oblasti. Pokud se tam nenachází, obdržíte prázdný kontejner **výsledku**:

    ![Snímek obrazovky s odpovědí JSON s prázdným kontejnerem výsledků](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Nastavení aplikace pro UPW

1. V nástroji Visual Studio vytvořte nový projekt typu **Prázdná aplikace (univerzální pro Windows)**.

    ![Snímek obrazovky dialogového okna Nový projekt aplikace Visual Studio s prázdnou aplikací (zvýrazní se možnost univerzální pro Windows Visual C#.](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Jakmile se vytváření projektu dokončí, měli byste mít základ samotné aplikace. Teď nastavili všechno pro infrastrukturu geografické oplocení. Vzhledem k tomu, že pro toto řešení využijete služby Bing, je k dispozici veřejný koncový bod rozhraní REST API, který umožňuje dotazovat se na konkrétní oblasti lokality:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Zprovozníte ho zadáním následujících parametrů:

   * **ID zdroje dat** a **Název zdroje dat** – v rozhraní API Map Bing zdroje dat obsahují různá kategorizovaná metadata, například lokality a pracovní doby provozu.  
   * **Název entity** – entita, kterou chcete použít jako referenční bod pro oznámení.
   * **Klíč rozhraní API Map Bing** – klíč, který jste dříve získali při vytváření účtu Dev Center pro Bing.

     Když teď máte připravený zdroj dat, můžete začít pracovat na aplikaci pro UPW.
2. Povolte pro vaši aplikaci zjišťování polohy. V **Průzkumníku řešení** otevřete soubor `Package.appxmanifest`.

    ![Snímek obrazovky Průzkumník řešení se zvýrazněným souborem Package. appxmanifest.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Na kartě vlastností balíčku, která se právě otevřela, přepněte na kartu **Schopnosti** a vyberte **Poloha**.

    ![Snímek obrazovky dialogového okna vlastností balíčku zobrazující kartu Možnosti s zvýrazněnou možností umístění](./media/notification-hubs-geofence/vs-package-location.png)
4. Vytvořte v řešení novou složku `Core` a přidejte do ní nový soubor `LocationHelper.cs`:

    ![Snímek obrazovky Průzkumník řešení se zvýrazněnou novou základní složkou](./media/notification-hubs-geofence/vs-location-helper.png)

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

    Další informace o tom, jak získat umístění uživatele v aplikacích pro UWP, najdete v tématu[získání umístění uživatele](/windows/uwp/maps-and-location/get-location).
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

    ![Snímek obrazovky Notification Hubs geografického plotu povolit přístup k vašemu umístění](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Jakmile se aplikace spustí, měli byste v okně **Výstup** vidět souřadnice:

    ![Snímek obrazovky okna výstup zobrazující souřadnice](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Nyní víte, že získání umístění funguje, a pokud chcete, můžete načtenou obslužnou rutinu události odebrat, protože ji už nebudete používat.
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

1. Stáhněte si [ukázku back-endu .NET z GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. Až se stahování dokončí, otevřete složku `NotifyUsers` a pak v sadě Visual otevřete soubor `NotifyUsers.sln`.
3. Nastavte projekt `AppBackend` jako **Spouštěný projekt** a spusťte jej.

    ![Snímek obrazovky se stejným kliknutím pravým tlačítkem myši v nabídce nastavit jako spouštěný projekt je zvýrazněný.](./media/notification-hubs-geofence/vs-startup-project.png)

    Projekt je již nakonfigurovaný tak, aby do cílových zařízení odesílal nabízená oznámení, proto bude potřeba udělat jen dvě věci – zadat správný připojovací řetězec centra oznámení a přidat identifikaci hranice, aby se oznámení odesílalo jenom v případě, že se uživatel nachází uvnitř monitorové geografické zóny.

4. Pro konfiguraci připojovacího řetězce otevřete ve složce `Models` soubor `Notifications.cs`. Funkce `NotificationHubClient.CreateClientFromConnectionString` by měla obsahovat informace o centru oznámení, které můžete získat na webu [Azure Portal](https://portal.azure.com) (podívejte se na stránku **Zásady přístupu** v **Nastavení**). Uložte aktualizovaný konfigurační soubor.
5. Vytvořte model pro výsledek rozhraní API Map Bing. Nejjednodušší způsob, jak to provést, je otevřít `Models` složku a zvolit **Přidat**  >  **třídu**. Pojmenujte ji `GeofenceBoundary.cs`. Pak zkopírujte JSON z odpovědi rozhraní API, kterou jste obdrželi v první části. V aplikaci Visual Studio použijte příkaz **Upravit**  >  **vložení speciální**  >  **vložení JSON jako třídy**.

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
    > Nastavte `POST_URL` na umístění vaší nasazené webové aplikace. Prozatím ji můžete spustit místně, ale při práci na nasazení veřejné verze ho musíte hostovat s externím poskytovatelem.
2. Zaregistrujte aplikaci pro UPW k nabízeným oznámením. V aplikaci Visual Studio vyberte možnost **Project**  >  **Store**  >  **přidružit aplikaci ke Storu**.

    ![Snímek obrazovky s vybraným řešením klikněte pravým tlačítkem myši na nabídku Store a přidružte aplikaci s zvýrazněnými možnostmi obchodu.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Jakmile se přihlásíte ke svému účtu vývojáře, ujistěte se, že jste vybrali existující aplikaci, nebo vyberte novou a přidružte k ní balíček.
4. Přejděte na Dev Center a otevřete aplikaci, kterou jste vytvořili. Vyberte **služby**  >  **nabízená oznámení**  >  **Web služeb Live Services**.

    ![Snímek obrazovky Windows Dev Center se zvýrazněnou stránkou nabízených oznámení se zvýrazněným webem služby Live Services.](./media/notification-hubs-geofence/ms-live-services.png)
5. Na webu si poznamenejte **Tajný klíč aplikace** a **SID balíčku**. Potřebujete oba Azure Portal – otevřete Centrum oznámení, vyberte **Nastavení**  >  **Notification Services**  >  **Windows (WNS)** a zadejte informace do požadovaných polí.

    ![Snímek obrazovky se stránkou nastavení s zvýrazněnými možnostmi služby Notification Services a Windows (WNS) a identifikátorem SID a bezpečnostním klíčem balíčku.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Klikněte na tlačítko **Uložit**.
7. V **Průzkumníku řešení** otevřete **Odkazy** a vyberte **Spravovat balíčky NuGet**. Přidejte odkaz na **spravovanou knihovnu Microsoft Azure Service Bus** – jednoduše vyhledejte balíček `WindowsAzure.Messaging.Managed` a přidejte ho do projektu.

    ![Snímek obrazovky dialogového okna spravovat balíčky NuGet se zvýrazněným balíčkem WindowsAzure. Messaging. Managed.](./media/notification-hubs-geofence/vs-nuget.png)
8. Pro účely testování znovu vytvořte obslužnou rutinu události `MainPage_Loaded` a přidejte do ní tento fragment kódu:

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
9. V `LocationHelper` uvnitř obslužné rutiny `Geolocator_PositionChanged` můžete přidat testovací kód, který bod nuceně umístí do monitorové geografické zóny:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Jelikož nepředáváte skutečné souřadnice (které by v tuto chvíli nemusely odpovídat místu uvnitř oblasti) a používáte předdefinované testovací hodnoty, uvidíte, že se při aktualizaci zobrazí oznámení:

    ![Snímek obrazovky plochy systému Windows zobrazující zkušební zprávu](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete řešení připravit na reálný provoz, může být zapotřebí podniknout několik kroků.

1. Nejprve je potřeba zajistit, že monitorované geografické zóny jsou dynamické. To vyžaduje další práci s rozhraním API služby Bing, aby bylo možné nahrávat nové hranice do existujícího zdroje dat. Další informace najdete v [dokumentaci rozhraní API pro Bing Spatial Data Services](/bingmaps/spatial-data-services/).
2. Abyste zajistili doručování správným účastníkům, můžete požadovat cílení pomocí [tagování](notification-hubs-tags-segment-push-message.md).

Řešení uvedené v tomto kurzu popisuje scénář, při kterém můžete mít širokou škálu cílových platforem, proto se monitorování geografické zóny neomezuje schopnostmi specifickými pro daný systém. Je ale nutné dodat, že Univerzální platforma Windows nabízí integrované možnosti pro [detekci monitorované geografické zóny](/windows/uwp/maps-and-location/set-up-a-geofence).
