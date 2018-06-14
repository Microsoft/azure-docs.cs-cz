---
title: Přidání ověřování do aplikace pro univerzální platformu Windows (UWP) | Microsoft Docs
description: 'Naučte se používat Azure App Service Mobile Apps ověřovat uživatele vaší aplikace univerzální platformu Windows (UWP) pomocí různých zprostředkovatelů identity, včetně: AAD, Google, Facebook, Twitter a společnosti Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592140"
---
# <a name="add-authentication-to-your-windows-app"></a>Přidání ověřování do aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Toto téma ukazuje, jak přidat cloudové ověřování do mobilní aplikace. V tomto kurzu jste přidání ověřování do projektu pro rychlý start univerzální platformu Windows (UWP) pro mobilní aplikace pomocí zprostředkovatele identity, která je podporována službou Azure App Service. Po se úspěšně ověří a autorizuje váš back-end mobilní aplikace, se zobrazí hodnota ID uživatele.

V tomto kurzu vychází z rychlého startu mobilní aplikace. Musíte nejdřív dokončit tento kurz [Začínáme s Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrace aplikace pro ověřování a nakonfigurovat App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externí přesměrování

Zabezpečené ověřování vyžaduje, můžete definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schématu adresy URL _appname_ v průběhu. Můžete však použít žádné schéma adresy URL, které zvolíte. Musí být jedinečné pro mobilní aplikace. Chcete povolit přesměrování na straně serveru:

1. V [portál Azure] vyberte App Service.

2. Klikněte **ověřování / autorizace** možnost nabídky.

3. V **povoleno externí adres URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tento řetězec je schéma adresy URL pro mobilní aplikace.  Měl by splňovat specifikaci normální adresu URL pro určitý protokol (používejte písmena a čísla pouze a začněte s písmenem).  Měli byste si poznamenat řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nyní můžete ověřit, že byl zakázán anonymní přístup k vaší back-end. S projekt aplikace UPW nastavit jako projekt spuštění, nasazení a spuštění aplikace; Ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. K tomu dojde, protože se aplikace pokusí o přístup k kódu mobilní aplikace jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověření.

Potom aktualizujte aplikace k ověření uživatelů před vyžádáním prostředky z vaší služby App Service.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. Aplikace UWP souboru MainPage.xaml.cs projektu a přidejte následující fragment kódu:
   
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
   
    Tento kód ověřuje uživatele s přihlašovacími údaji, Facebook. Pokud používáte zprostředkovatele identity než Facebook, změňte hodnotu **MobileServiceAuthenticationProvider** výše na hodnotu pro poskytovatele.
2. Nahraďte **OnNavigatedTo()** metoda v MainPage.xaml.cs. V dalším kroku přidáte **přihlášení** tlačítko na aplikaci, která aktivuje ověřování.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. MainPage.xaml.cs přidejte následující fragment kódu:
   
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
4. Otevřete soubor projektu MainPage.xaml, vyhledejte elementu, který definuje **Uložit** tlačítko a nahraďte ji následujícím kódem:
   
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
5. Souboru App.xaml.cs přidejte následující fragment kódu:

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
6. Otevřete soubor Package.appxmanifest, přejděte na **deklarace**v **dostupné deklarace** rozevíracího seznamu vyberte **protokol** a klikněte na tlačítko **přidat** tlačítko. Teď nakonfigurovat **vlastnosti** z **protokol** deklarace. V **zobrazovaný název**, přidejte název si přejete zobrazit uživatelům vaší aplikace. V **název**, přidejte vaše {url_scheme_of_your_app}.
7. Stisknutím klávesy F5 spusťte aplikaci, klikněte na tlačítko **přihlášení** tlačítko a přihlaste se k aplikaci pomocí zprostředkovatele identity vybrané. Po přihlášení s úspěšné aplikace běží bez chyb a je možné provést aktualizace dat a dotaz na váš back-end.

## <a name="tokens"></a>Ukládání tokenu ověřování na straně klienta
Předchozí příklad ukázal standardní přihlášení, která vyžaduje, aby klient kontaktovat poskytovatele identit a App Service při každém spuštění aplikace. Ne jenom je neefektivní, můžete spustit tuto metodu do využití-má vztah problémy mnoho zákazníků využít při spuštění aplikace ve stejnou dobu. Lepším řešením je ukládat do mezipaměti autorizační token vrácený App Service a pokuste se použít tento první před použitím u založenou na poskytovateli přihlášení.

> [!NOTE]
> Můžete mezipaměti tokenem vydaným službou App Services bez ohledu na to, jestli používáte ověřování klienta spravovat nebo spravované služby. Tento kurz používá službu spravovat ověřování.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračovat na jednu z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
