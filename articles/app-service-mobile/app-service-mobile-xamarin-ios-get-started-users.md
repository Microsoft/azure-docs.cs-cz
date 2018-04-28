---
title: Začínáme s ověřováním pro Mobile Apps v Xamarin iOS
description: Další informace o použití mobilní aplikace ověřovat uživatele vaší aplikace Xamarin iOS prostřednictvím řady různých zprostředkovatelů identity, včetně AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: c2b77d7c9caab1c7ac13af5ccd50e992bcce1caf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Přidání ověřování do aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Toto téma ukazuje, jak ověřovat uživatele aplikace služby mobilní aplikace z klientské aplikace. V tomto kurzu přidání ověřování do projektu pro rychlý start Xamarin.iOS pomocí zprostředkovatele identity, která je podporována službou App Service. Poté, co se úspěšně ověří a autorizuje pomocí mobilní aplikace, zobrazí se hodnota ID uživatele a bude mít přístup k datům s omezeným přístupem tabulky.

Musíte nejdřív dokončit tento kurz [vytvoření aplikace Xamarin.iOS]. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a nakonfigurujte aplikační služby
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Přidání aplikace do adresy URL pro povolené externí přesměrování

Zabezpečené ověřování vyžaduje, můžete definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schématu adresy URL _appname_ v průběhu. Můžete však použít žádné schéma adresy URL, které zvolíte. Musí být jedinečné pro mobilní aplikace. Chcete povolit přesměrování na straně serveru:

1. V [portál Azure] vyberte App Service.

2. Klikněte **ověřování / autorizace** možnost nabídky.

3. V **povoleno externí adres URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tento řetězec je schéma adresy URL pro mobilní aplikace.  Měl by splňovat specifikaci normální adresu URL pro určitý protokol (používejte písmena a čísla pouze a začněte s písmenem).  Měli byste si poznamenat řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. V sadě Visual Studio nebo Xamarin Studio spuštění klientského projektu na emulátoru nebo zařízení. Ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. Toto selhání se zaprotokoluje ke konzole ladicího programu. Proto v sadě Visual Studio, měli byste vidět selhání v okně výstupu.

&nbsp;&nbsp;Toto selhání neoprávněným se stane, protože se aplikace pokusí o přístup k váš back-end mobilní aplikace jako neověřený uživatel. *TodoItem* tabulka nyní vyžaduje ověření.

Potom bude aktualizujte klientskou aplikaci pro požadavky na prostředky z back-end mobilní aplikace s ověřeného uživatele.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
V této části upravíte aplikace zobrazíte obrazovka pro přihlášení, než se zobrazí data. Při spuštění aplikace, nebude se připojit do vaší služby App Service a nebudou zobrazovat žádná data. Po prvním uživatel provádí aktualizace gesto, zobrazí se obrazovka pro přihlášení; Po úspěšném přihlášení se zobrazí seznam položek todo.

1. V projektu klienta, otevřete soubor **QSTodoService.cs** a přidejte následující příkaz using a `MobileServiceUser` s přistupujícím k třídě QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Přidat novou metodu s názvem **ověřit** k **QSTodoService** s následující definice:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Pokud používáte zprostředkovatele identity než Facebook, změňte hodnotu předaný **LoginAsync** výše na jednu z následujících: _MicrosoftAccount_, _Twitter_, _Google_, nebo _WindowsAzureActiveDirectory_.

3. Otevřete **QSTodoListViewController.cs**. Upravit definici metoda **ViewDidLoad** odebrání volání **RefreshAsync()** v blízkosti end:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Změňte metodu **RefreshAsync** k ověření, pokud **uživatele** vlastnost má hodnotu null. Přidejte následující kód v horní části definici metody:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Otevřete **AppDelegate.cs**, přidejte následující metodu:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Otevřete **Info.plist** souboru, přejděte na **URL typy** v **Upřesnit** části. Teď nakonfigurovat **identifikátor** a **schémata URL** typ adresy URL a klikněte na tlačítko **přidat adresu URL typu**. **Schémata URL** by měla být stejná jako vaše {url_scheme_of_your_app}.
7. V sadě Visual Studio, připojený k hostiteli Mac nebo Visual Studio pro Mac spuštění klientského projektu cílení na emulátoru nebo zařízení. Ověřte, že aplikace zobrazí žádná data.
   
    Proveďte aktualizaci gesto přidáváním dolů v seznamu položek, které způsobí, že přihlašovací obrazovce se objeví. Jakmile úspěšně jste zadali platné přihlašovací údaje, aplikace se zobrazí seznam položek todo a data můžete provádět aktualizace.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[vytvoření aplikace Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
