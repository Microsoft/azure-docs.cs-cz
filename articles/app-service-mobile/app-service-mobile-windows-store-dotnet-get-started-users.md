---
title: Přidání ověřování do aplikace pro univerzální platformu Windows (UPW) | Dokumentace Microsoftu
description: 'Zjistěte, jak používat Azure App Service Mobile Apps k ověřování uživatelů vaší aplikace univerzální platformy Windows (UPW) pomocí různých poskytovatelů identit, včetně: AAD, Google, Facebook, Twitter a Microsoft.'
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38306570"
---
# <a name="add-authentication-to-your-windows-app"></a>Přidání ověřování do aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Toto téma ukazuje, jak přidat cloudové ověřování do vaší mobilní aplikace. V tomto kurzu přidáte ověřování do projektu univerzální platformu Windows (UPW) pomocí zprostředkovatele identity, který je podporovaný službou Azure App Service Mobile Apps. Po se úspěšně ověří a autorizuje back-endu mobilní aplikace, zobrazí se hodnota ID uživatele.

Tento kurz je založený na rychlý start Mobile Apps. Musíte nejdřív dokončit tento kurz [Začínáme s Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externího přesměrování

Zabezpečené ověřování, musíte definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schéma adresy URL _appname_ v průběhu. Můžete ale použít jakékoli schéma adresy URL, kterou zvolíte. Musí být jedinečné pro vaši mobilní aplikaci. Pokud chcete povolit přesměrování na straně serveru:

1. Na [webu Azure Portal] vyberte službu App Service.

2. Klikněte na tlačítko **ověřování / autorizace** nabídky.

3. V **povolené externí adresy URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měla by odpovídat specifikaci normální adresu URL pro určitý protokol (použití písmena a čísla jenom a začíná písmenem).  By měl poznamenejte řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teď můžete ověřit, že byl zakázán anonymní přístup k back-endu. Projekt aplikace UPW nastavit jako spouštěcí projekt nasazení a spuštění aplikace; Ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). K tomu dojde, protože se aplikace pokusí o přístup k kód mobilní aplikace jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověřování.

V dalším kroku se aktualizace aplikace k ověření uživatelů před požadováním prostředky ze služby App Service.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
1. Aplikace na UPW souboru MainPage.xaml.cs projektu a přidejte následující fragment kódu:
   
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
   
    Tento kód se ověřuje uživatele přihlášení k Facebooku. Pokud používáte zprostředkovatelů identity než Facebook, změňte hodnotu vlastnosti **MobileServiceAuthenticationProvider** výše na hodnotu pro poskytovatele.
2. Nahradit **OnNavigatedTo()** metoda v MainPage.xaml.cs. V dalším kroku přidáte **přihlášení** tlačítko aplikaci, která aktivuje ověřování.

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
4. Otevřete soubor projektu souboru MainPage.xaml, vyhledejte element, který definuje **Uložit** tlačítko a nahraďte ho následujícím kódem:
   
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
6. Otevřete soubor Package.appxmanifest, přejděte na **deklarace**v **dostupné deklarace** rozevíracího seznamu vyberte **protokol** a klikněte na tlačítko **přidat** tlačítko. Teď nakonfigurovat **vlastnosti** z **protokol** deklarace. V **zobrazovaný název**, přidejte název, které chcete zobrazit uživatelům vaší aplikace. V **název**, přidat vaši {url_scheme_of_your_app}.
7. Stisknutím klávesy F5 spusťte aplikaci, klikněte na tlačítko **přihlášení** tlačítko a přihlaste se k aplikaci pomocí zprostředkovatele identity zvolená. Po přihlášení úspěšné, aplikace běží bez chyb a je možné provést aktualizace dat a dotazování back-endu.

## <a name="tokens"></a>Store ověřovací token na straně klienta
Předchozí příklad ukázal standardní přihlášení, které vyžaduje klient kontaktovat zprostředkovatele identity a služby App Service při každém spuštění aplikace. Nejenže je tato metoda, můžete spustit do využití souvisí problémy mnoho zákazníků pokuste se spustit aplikaci ve stejnou dobu. Lepším řešením je ukládat do mezipaměti autorizační token vrácený službou App Service a snažte se používat tento první před použitím u založené na zprostředkovatele přihlášení.

> [!NOTE]
> Můžete ukládat do mezipaměti tokenu vydaného službou App Services bez ohledu na to, jestli používáte ověřování klienta spravovat nebo spravované služby. Tento kurz používá spravovaný službou ověřování.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračováním jednu z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
