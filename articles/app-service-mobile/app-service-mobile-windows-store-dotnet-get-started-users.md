---
title: Přidání ověřování do aplikace Univerzální platforma Windows (UWP) | Microsoft Docs
description: Naučte se používat Azure App Service Mobile Apps k ověřování uživatelů aplikace Univerzální platforma Windows (UWP) pomocí různých poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5012ccc503e48785e23ff00564bbc9f6735eea8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388539"
---
# <a name="add-authentication-to-your-windows-app"></a>Přidání ověřování do aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dnes.

## <a name="overview"></a>Přehled
V tomto tématu se dozvíte, jak do mobilní aplikace přidat cloudové ověřování. V tomto kurzu přidáte ověřování do projektu pro rychlý Start Univerzální platforma Windows (UWP) pro Mobile Apps pomocí zprostředkovatele identity, který je podporován Azure App Service. Po úspěšném ověření a autorizaci back-endu mobilní aplikace se zobrazí hodnota ID uživatele.

Tento kurz je založený na rychlém startu Mobile Apps. Nejprve musíte dokončit kurz [Začínáme s Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal](https://ms.portal.azure.com)vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci představuje schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, zda byl zakázán anonymní přístup k back-endu. S nastavením projektu aplikace UWP jako spouštěcího projektu nasaďte a spusťte aplikaci. Ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). K tomu dochází, protože se aplikace pokusí získat přístup k vašemu kódu mobilní aplikace jako neověřeného uživatele, ale tabulka *TodoItem* nyní vyžaduje ověření.

V dalším kroku aplikaci aktualizujete, aby ověřovala uživatele před vyžádáním prostředků z vašeho App Service.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. V souboru projektu aplikace UWP MainPage.xaml.cs a přidejte následující fragment kódu:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Tento kód ověřuje uživatele s přihlášením na Facebooku. Pokud používáte jiného poskytovatele identity než Facebook, změňte hodnotu **MobileServiceAuthenticationProvider** výše na hodnotu pro vašeho poskytovatele.
2. Nahraďte metodu **OnNavigatedTo ()** v MainPage.XAML.cs. Potom do aplikace přidáte tlačítko pro **přihlášení** , které aktivuje ověřování.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Do MainPage.xaml.cs přidejte následující fragment kódu:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Otevřete soubor projektu MainPage. XAML, vyhledejte element definující tlačítko **Uložit** a nahraďte ho následujícím kódem:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Do App.xaml.cs přidejte následující fragment kódu:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Otevřete soubor Package. appxmanifest, přejděte na **deklarace**, v rozevíracím seznamu **dostupná deklarace** vyberte **protokol** a klikněte na tlačítko **Přidat** . Nyní nakonfigurujte **vlastnosti** deklarace **protokolu** . Do pole **Zobrazovaný název**přidejte název, který chcete zobrazit uživatelům vaší aplikace. Do **název**přidejte {url_scheme_of_your_app}.
7. Stisknutím klávesy F5 spusťte aplikaci, klikněte na tlačítko **Přihlásit** se a přihlaste se k aplikaci pomocí zvoleného poskytovatele identity. Po úspěšném přihlášení se aplikace spustí bez chyb a můžete se dotazovat na back-end a provést aktualizace dat.

## <a name="tokens"></a>Uložení ověřovacího tokenu na klienta
Předchozí příklad ukázal standardní přihlášení, které vyžaduje, aby klient kontaktoval poskytovatele identity i App Service při každém spuštění aplikace. Nejenom Tato metoda je neefektivní, ale můžete se pustit do problematiky využití-vzdálení, pokud se mnoho zákazníků pokusí spustit aplikaci ve stejnou dobu. Lepším řešením je ukládání autorizačního tokenu, který vrátila vaše App Service, a pokus o jeho použití jako první, než použijete přihlášení založené na poskytovateli.

> [!NOTE]
> Token vydaný App Services můžete ukládat do mezipaměti bez ohledu na to, jestli používáte ověřování spravovaného klientem nebo službou. V tomto kurzu se používá ověřování spravované službou.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento kurz základního ověřování, zvažte pokračování v jednom z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
