---
title: Přidání ověřování do aplikace UPW
description: Přečtěte si, jak pomocí mobilních aplikací Služby Azure App Service ověřovat uživatele vaší aplikace pro univerzální platformu Windows (UPW) pomocí poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458932"
---
# <a name="add-authentication-to-your-windows-app"></a>Přidání ověřování do aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
Toto téma ukazuje, jak do mobilní aplikace přidat cloudové ověřování. V tomto kurzu přidáte ověřování do projektu rychlého startu rychlého startu platformy Windows (UPW) pro mobilní aplikace pomocí poskytovatele identity, který je podporovaný službou Azure App Service. Po úspěšném ověření a autorizaci back-endem mobilní aplikace se zobrazí hodnota ID uživatele.

Tento kurz je založen na rychlém startu mobilních aplikací. Musíte nejprve dokončit výukový program [Začínáme s mobilními aplikacemi](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL. To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu. Můžete však použít libovolné schéma adres URL, které zvolíte. To by mělo být jedinečné pro vaši mobilní aplikaci. Povolení přesměrování na straně serveru:

1. Na [webu Azure Portal](https://ms.portal.azure.com)vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V **adresách URL allowed external redirect zadejte** `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

4. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, že anonymní přístup k back-endu byl zakázán. S projektem aplikace UP nastavit jako start-up projektu, nasadit a spustit aplikaci; ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizovaný). K tomu dochází, protože aplikace se pokusí o přístup ke kódu mobilní aplikace jako neověřený uživatel, ale tabulka *TodoItem* nyní vyžaduje ověření.

Dále budete aktualizovat aplikaci k ověření uživatelů před vyžádáním prostředků z vaší služby App Service.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Přidání ověřování do aplikace
1. V souboru projektu aplikace UPW MainPage.xaml.cs a přidejte následující fragment kódu:
   
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
   
    Tento kód ověřuje uživatele pomocí přihlášení na Facebooku. Pokud používáte jiného poskytovatele identity než Facebook, změňte výše uvedenou hodnotu **MobileServiceAuthenticationProvider** na hodnotu pro vašeho poskytovatele.
2. Nahraďte metodu **OnNavigatedTo()** v MainPage.xaml.cs. Dále přidáte tlačítko **Přihlásit** se do aplikace, která aktivuje ověřování.

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
4. Otevřete soubor projektu MainPage.xaml, vyhledejte prvek, který definuje tlačítko **Uložit,** a nahraďte ho následujícím kódem:
   
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
6. Otevřete soubor Package.appxmanifest, přejděte do **seznamu Deklarace**, v rozevíracím seznamu **Dostupná prohlášení,** vyberte **Protokol** a klikněte na **tlačítko Přidat.** Nyní nakonfigurujte **vlastnosti** deklarace **protokolu.** Do **pole Zobrazovaný název**přidejte jméno, které chcete zobrazit uživatelům aplikace. Do **pole Název**přidejte {url_scheme_of_your_app}.
7. Stisknutím klávesy F5 aplikaci spusťte, klikněte na tlačítko **Přihlásit se** a přihlaste se k aplikaci pomocí zvoleného poskytovatele identity. Po úspěšném přihlášení se aplikace spustí bez chyb a budete moct dotazovat back-end a aktualizovat data.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>Uložení ověřovacího tokenu na straně klienta
V předchozím příkladu se ukázalo standardní přihlášení, které vyžaduje, aby klient kontaktovat zprostředkovatele identity a služby App Service při každém spuštění aplikace. Nejen, že je tato metoda neefektivní, můžete spustit do využití týká problémy by mnoho zákazníků se pokusí spustit aplikaci ve stejnou dobu. Lepším přístupem je ukládat autorizační token vrácený vaší službou App Service do mezipaměti a pokusit se použít nejprve před použitím přihlášení na základě zprostředkovatele.

> [!NOTE]
> Token vydaný službou App Services můžete ukládat do mezipaměti bez ohledu na to, zda používáte ověřování spravované klientem nebo službou. Tento kurz používá ověřování spravované službou.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento základní kurz ověřování, zvažte pokračování na jeden z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
