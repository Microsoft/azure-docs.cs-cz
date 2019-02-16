---
title: Začínáme s ověřováním pro Mobile Apps v Xamarin pro iOS
description: Naučte se využívat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin iOS prostřednictvím různých poskytovatelů identit, včetně AAD, Google, Facebook, Twitter a Microsoft.
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
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326166"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Přidání ověřování do aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

V tomto tématu se dozvíte, jak ověřovat uživatele aplikace služby App Service Mobile z klientské aplikace. V tomto kurzu přidat ověřování do projektu Xamarin.iOS pomocí zprostředkovatele identity, který je podporovaný službou App Service. Poté, co se úspěšně ověří a autorizuje mobilní aplikace, zobrazí se hodnota ID uživatele a nebudete mít přístup k datům s omezeným přístupem tabulky.

Musíte nejdřív dokončit tento kurz [vytvoření aplikace Xamarin.iOS]. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a konfigurace služby App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Přidání aplikace do adresy URL pro povolené externího přesměrování

Zabezpečené ověřování, musíte definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schéma adresy URL _appname_ v průběhu. Můžete ale použít jakékoli schéma adresy URL, kterou zvolíte. Musí být jedinečné pro vaši mobilní aplikaci. Pokud chcete povolit přesměrování na straně serveru:

1. V [webu Azure portal](https://portal.azure.com/), vyberte službu App Service.

2. Klikněte na tlačítko **ověřování / autorizace** nabídky.

3. V **povolené externí adresy URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měla by odpovídat specifikaci normální adresu URL pro určitý protokol (použití písmena a čísla jenom a začíná písmenem).  By měl poznamenejte řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Selhání se protokoluje do konzoly ladicího programu. Proto v sadě Visual Studio, měli byste vidět chyby v okně výstup.

    Dojde k selhání této neoprávněným vzhledem k tomu, že aplikace pokusí o přístup k back-endu mobilní aplikace jako neověřené uživatele. *TodoItem* tabulka nyní vyžaduje ověřování.

Dále budete aktualizovat klientskou aplikaci na požadavky na prostředky z back-endu mobilní aplikace s ověřeného uživatele.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
V této části upravíte aplikaci, která zobrazí obrazovka pro přihlášení, než se zobrazí data. Při spuštění aplikace, se nebudou připojovat k vaší službě App Service a nebudou zobrazovat žádná data. Po prvním uživatel provede gesta aktualizace se zobrazí přihlašovací obrazovka; Po úspěšném přihlášení se zobrazí seznam položek todo.

1. V klientském projektu otevřete soubor **QSTodoService.cs** a přidejte následující příkaz using a `MobileServiceUser` pomocí přístupového objektu QSTodoService třídy:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Přidejte novou metodu s názvem **ověřit** k **QSTodoService** s následující definice:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Pokud používáte zprostředkovatelů identity než Facebook, změňte hodnotu předanou **LoginAsync** výše na jednu z následujících akcí: _MicrosoftAccount_, _Twitter_, _Google_, nebo _WindowsAzureActiveDirectory_.

3. Otevřít **QSTodoListViewController.cs**. Upravit definici metody **ViewDidLoad** odebrání volání **RefreshAsync()** poblíž konce:

    ```csharp
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
    ```

4. Upravte metodu **RefreshAsync** k ověření, pokud **uživatele** vlastnost má hodnotu null. V horní části definice metody přidejte následující kód:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Otevřít **AppDelegate.cs**, přidejte následující metodu:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Otevřít **Info.plist** souboru, přejděte na **typy adres URL** v **Upřesnit** oddílu. Teď nakonfigurovat **identifikátor** a **schémata URL** typ adresy URL a klikněte na tlačítko **přidat typ adresy URL**. **Schémata adres URL** by měl být stejný jako vaše {url_scheme_of_your_app}.
7. V sadě Visual Studio, připojený k hostiteli Mac nebo Visual Studio pro Mac spusťte klientský projekt cílí na zařízení nebo emulátoru. Ověřte, že aplikace nezobrazí žádná data.

    Proveďte gesto aktualizace to potažením dolů seznam položek, které způsobí, že přihlašovací obrazovka se zobrazí. Po úspěšném zadání platné přihlašovací údaje, aplikace se zobrazí seznam položek todo a dat můžete provádět aktualizace.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Vytvoření aplikace Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
