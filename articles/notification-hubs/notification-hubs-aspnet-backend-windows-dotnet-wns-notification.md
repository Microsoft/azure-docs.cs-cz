---
title: Zasílání oznámení konkrétním uživatelům službou Azure Notification Hubs | Microsoft Docs
description: Zjistěte, jak posílat oznámení konkrétním uživatelům používajícím aplikace pro Univerzální platformu Windows (UPW).
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019476"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Kurz: Zasílání oznámení konkrétním uživatelům službou Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení na konkrétního uživatele aplikace na konkrétní zařízení. K ověřování klientů se používá back-end ASP.NET WebAPI. Když back-end ověří uživatele klientské aplikace, automaticky přidá značku k registraci oznámení. Back-end tuto značku používá k posílání oznámení konkrétnímu uživateli.

> [!NOTE]
> Dokončený kód pro tento kurz najdete na [GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření projektu WebAPI
> * Ověřování klientů v back-endu WebAPI
> * Registrace k oznámením pomocí back-endu WebAPI
> * Odesílání oznámení z back-endu WebAPI
> * Publikování nového back-endu WebAPI
> * Aktualizace kódu klientského projektu
> * Testování aplikace

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se používá centrum oznámení a projekt sady Visual Studio, které jste vytvořili v [kurzu Zasílání oznámení do aplikací pro Univerzální platformu Windows službou Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Proto ho dokončete před zahájením tohoto kurzu.

> [!NOTE]
> Pokud jako back-endovou službu používáte Mobile Apps ve službě Azure App Service, prostudujte si část [Verze Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) tohoto kurzu.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>Aktualizace kódu pro klienta UWP

V této části aktualizujete kód v projektu, který jste vytvořili v [kurzu Zasílání oznámení do aplikací pro Univerzální platformu Windows službou Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Projekt už by měl být přidružený k Windows Storu. Také už by měl být nakonfigurovaný tak, aby používal vaše centrum oznámení. V této části přidáte kód, který volá nový back-end WebAPI a používá ho k registraci a zasílání oznámení.

1. V sadě Visual Studio otevřete řešení, které jste vytvořili v rámci [kurzu Zasílání oznámení do aplikací pro Univerzální platformu Windows službou Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. V Průzkumník řešení klikněte pravým tlačítkem na projekt Univerzální platforma Windows (UWP) a pak klikněte na **Spravovat balíčky NuGet**.
3. Na levé straně vyberte **Procházet**.
4. Do pole **Hledat** zadejte **Http Client**.
5. V seznamu výsledků klikněte na **System.Net.Http** a pak klikněte na **Nainstalovat**. Dokončete instalaci.
6. Vraťte se do pole **Hledat** pro balíčky NuGet a zadejte **Json.net**. Nainstalujte balíček **Newtonsoft.json** a pak zavřete okno Správce balíčků NuGet.
7. V Průzkumníku řešení v projektu **WindowsApp** dvakrát klikněte na soubor **MainPage.xaml** a otevřete ho v editoru Visual Studio.
8. V `MainPage.xaml` souboru nahraďte `<Grid>` oddíl následujícím kódem: Tento kód přidá textové pole uživatelské jméno a heslo, se kterým se uživatel ověřuje. Také přidá textová pole pro zprávu oznámení a značku uživatelského jména, které by měly dostávat oznámení:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. V Průzkumník řešení otevřete `MainPage.xaml.cs` soubor pro projekty **(Windows 8.1)** a **(Windows Phone 8,1)** . Na začátek obou souborů přidejte následující příkazy `using`:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. V nástroji `MainPage.xaml.cs` pro projekt **WindowsApp** přidejte do třídy následující člen `MainPage` . Nezapomeňte nahradit `<Enter Your Backend Endpoint>` skutečným koncovým bodem vašeho back-endu, který jste předtím získali. Například, `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. Přidejte následující kód do třídy MainPage v `MainPage.xaml.cs` pro projekty **(Windows 8.1)** a **(Windows Phone 8,1)** .

    Metoda `PushClick` je obslužná rutina události kliknutí pro tlačítko **Send Push** (Odeslat nabízené oznámení). Metoda volá back-end za účelem aktivace odeslání oznámení do všech zařízení, jejichž značka uživatelského jména odpovídá parametru `to_tag`. Zpráva oznámení se odešle jako obsah JSON v textu požadavku.

    Metoda `LoginAndRegisterClick` je obslužná rutina události kliknutí pro tlačítko **Login and register** (Přihlášení a registrace). Metoda do místního úložiště uloží základní autorizační token (reprezentuje jakýkoli token, který používá vaše schéma autorizace) a pak pomocí metody `RegisterClient` provede registraci oznámení v back-endu.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```

12. Otevřete `App.xaml.cs` a vyhledejte volání `InitNotificationsAsync()` v `OnLaunched()` obslužné rutině události. Okomentujte nebo odstraňte volání metody `InitNotificationsAsync()`. Obslužná rutina tlačítka inicializuje registrace oznámení:

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. Klikněte pravým tlačítkem na projekt **WindowsApp**, **Přidat** a potom na **Třída**. Pojmenujte třídu `RegisterClient.cs` a potom kliknutím na tlačítko **OK** vygenerujte třídu.

    Tato třída zabalí potřebná volání REST ke kontaktování back-endu aplikace za účelem registrace nabízených oznámení. Kromě toho místně ukládá *ID registrací* vytvořená centrem oznámení, jak je podrobně popsáno v tématu popisujícím [registraci z back-endu aplikace](/previous-versions/azure/azure-services/dn743807(v=azure.100)). Po kliknutí na tlačítko **Login and register** (Přihlášení a registrace) použije autorizační token uložený v místním úložišti.
14. Na začátek souboru RegisterClient.cs přidejte následující příkazy `using`:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. Do definice třídy přidejte následující kód `RegisterClient` :

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```

16. Uložte všechny provedené změny.

## <a name="test-the-application"></a>Test aplikace

1. Spusťte aplikaci v obou systémech Windows.
2. Zadejte **Username** (Uživatelské jméno) a **Password** (Heslo), jak je znázorněno na následující obrazovce. Zadané hodnoty by se měly lišit od uživatelského jména a hesla, které jste zadali na Windows Phone.
3. Klikněte na **Login and register** (Přihlášení a registrace) a ověřte, že se zobrazí dialogové okno s oznámením o přihlášení. Tento kód také aktivuje tlačítko **Send Push** (Odeslat nabízené oznámení).

    ![Snímek obrazovky aplikace Notification Hubs zobrazující uživatelské jméno a heslo vyplněné.][14]
4. Dále do pole **Recipient Username Tag** (Značka uživatelského jména příjemce) zadejte zaregistrované uživatelské jméno. Zadejte zprávu oznámení a klikněte na **Send Push** (Odeslat nabízené oznámení).
5. Zprávu oznámení obdrží pouze zařízení, která se zaregistrovala s použitím odpovídající značky uživatelského jména.

    ![Snímek obrazovky Notification Hubs aplikace ukazující zprávu, která byla vložena.][15]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak posílat nabízená oznámení konkrétním uživatelům, k jejichž registracím jsou přidružené značky. V dalším kurzu se dozvíte, jak posílat nabízená oznámení na základě polohy:

> [!div class="nextstepaction"]
>[Odesílání oznámení na základě polohy](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
