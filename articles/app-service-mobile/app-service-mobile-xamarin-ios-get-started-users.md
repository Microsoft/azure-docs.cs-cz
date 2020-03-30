---
title: Začínáme s ověřováním v Xamarin iOS
description: Přečtěte si, jak pomocí mobilních aplikací ověřovat uživatele aplikace Xamarin pro iOS s poskytovateli identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461313"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Přidání ověřování do aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled

Toto téma ukazuje, jak ověřit uživatele mobilní aplikace App Service z klientské aplikace. V tomto kurzu přidáte ověřování do projektu rychlého startu Xamarin.iOS pomocí zprostředkovatele identity, který je podporován službou App Service. Po úspěšném ověření a autorizaci mobilní aplikací se zobrazí hodnota ID uživatele a budete mít přístup k datům tabulky s omezeným přístupem.

Musíte nejprve dokončit kurz [Vytvořit aplikaci Xamarin.iOS]. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíček rozšíření ověřování. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a konfigurace služeb app services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL. To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu. Můžete však použít libovolné schéma adres URL, které zvolíte. To by mělo být jedinečné pro vaši mobilní aplikaci. Povolení přesměrování na straně serveru:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V **adresách URL allowed external redirect zadejte** `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizovaný). Selhání je zaznamenána do konzoly ladicího programu. Takže v sadě Visual Studio, měli byste vidět selhání ve výstupním okně.

    K tomuto neoprávněnému selhání dochází, protože se aplikace pokusí o přístup k back-endu mobilní aplikace jako neověřený uživatel. Tabulka *TodoItem* nyní vyžaduje ověření.

Dále aktualizujete klientskou aplikaci tak, aby požadovala prostředky z back-endu mobilní aplikace s ověřeným uživatelem.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
V této části upravíte aplikaci tak, aby před zobrazením dat zobrazila přihlašovací obrazovku. Když se aplikace spustí, nepřipojí se k vaší službě App Service a nezobrazí žádná data. Po prvním provedení gesta aktualizace uživatelese zobrazí přihlašovací obrazovka; po úspěšném přihlášení se zobrazí seznam todo položek.

1. V klientském projektu otevřete soubor **QSTodoService.cs** a `MobileServiceUser` přidejte následující příkaz using a přistupujícího do třídy QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Přidejte novou metodu s názvem **Ověření** do **služby QSTodoService** s následující definicí:

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
    > Pokud používáte jiného poskytovatele identity než Facebook, změňte hodnotu předanou na **LoginAsync** výše na jednu z následujících hodnot: _MicrosoftAccount_, _Twitter_, _Google_nebo _WindowsAzureActiveDirectory_.

3. Otevřít **QSTodoListViewController.cs**. Upravte definici metody **ViewDidLoad** odebrání volání **RefreshAsync()** ke konci:

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

4. Upravte metodu **RefreshAsync** k ověření, pokud je vlastnost **User** null. V horní části definice metody přidejte následující kód:

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

5. Otevřete **AppDelegate.cs**, přidejte následující metodu:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Otevřete soubor **Info.plist,** přejděte na **Typy adres URL** v části **Upřesnit.** Nyní nakonfigurujte **identifikátor** a **schémata adres URL** typu adresy URL a klepněte na tlačítko Přidat typ **adresy URL**. **Schémata adres URL** by měla být stejná jako vaše {url_scheme_of_your_app}.
7. V Sadě Visual Studio, připojené k Mac Host nebo Visual Studio pro Mac, spusťte klientský projekt zaměřený na zařízení nebo emulátor. Ověřte, zda aplikace nezobrazuje žádná data.

    Proveďte gesto aktualizace stažením seznamu položek, což způsobí, že se přihlašovací obrazovka zobrazí. Po úspěšném zadání platných pověření aplikace zobrazí seznam položek úkolů a můžete data aktualizovat.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Vytvoření aplikace Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
