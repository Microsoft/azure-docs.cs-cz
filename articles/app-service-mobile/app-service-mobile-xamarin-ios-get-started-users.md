---
title: Začínáme s ověřováním v Xamarin iOS
description: Naučte se používat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin iOS pomocí zprostředkovatelů identity, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 0f2c78c3d4b18e7c662c4f7345938ddab377229b
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668268"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Přidání ověřování do aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled

V tomto tématu se dozvíte, jak ověřit uživatele App Service mobilní aplikace z klientské aplikace. V tomto kurzu přidáte do projektu pro rychlé zprovoznění Xamarin. iOS ověřování pomocí zprostředkovatele identity, který podporuje App Service. Po úspěšném ověření a autorizaci vaší mobilní aplikace se zobrazí hodnota ID uživatele a budete mít přístup k omezeným datům tabulky.

Nejdřív musíte dokončit kurz [Vytvoření aplikace Xamarin. iOS]. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, je nutné přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal](https://portal.azure.com/)vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V aplikaci Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Selhání je zaznamenáno do konzoly ladicího programu. Takže v aplikaci Visual Studio by se chyba měla zobrazit v okně výstup.

    K této neautorizované chybě dochází, protože se aplikace pokusí získat přístup k back-endu mobilní aplikace jako neověřeného uživatele. Tabulka *TodoItem* nyní vyžaduje ověření.

V dalším kroku aktualizujete klientskou aplikaci tak, aby požadovala prostředky z back-endu mobilní aplikace pomocí ověřeného uživatele.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
V této části upravíte aplikaci tak, aby před zobrazením dat zobrazovala přihlašovací obrazovku. Po spuštění aplikace se nepřipojí k vašemu App Service a nezobrazí se žádná data. Po prvním spuštění gesta obnovení uživatelem se zobrazí přihlašovací obrazovka. Po úspěšném přihlášení se zobrazí seznam položek todo.

1. V projektu klienta otevřete soubor **QSTodoService.cs** a přidejte následující příkaz using a `MobileServiceUser` s přistupujícím objektem do třídy QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Přidejte novou metodu s názvem **Authenticate** do **QSTodoService** s následující definicí:

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

3. Otevřete **QSTodoListViewController.cs**. Upravte definici metody **ViewDidLoad** odebrání volání **RefreshAsync ()** poblíž konce:

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

4. Upravte metodu **RefreshAsync** na ověřování, pokud má vlastnost **User** hodnotu null. Do horní části definice metody přidejte následující kód:

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

5. Otevřete **AppDelegate.cs**a přidejte následující metodu:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Otevřete soubor **info. plist** , v části **Upřesnit** přejděte na **typy adres URL** . Nyní nakonfigurujte **identifikátor** a **schémata adres** URL typu adresy URL a klikněte na tlačítko **Přidat typ adresy URL**. **Schémata URL** by měla být stejná jako vaše {url_scheme_of_your_app}.
7. V aplikaci Visual Studio připojené k hostiteli Mac nebo Visual Studio pro Mac spusťte projekt klienta cílící na zařízení nebo emulátor. Ověřte, že aplikace nezobrazuje žádná data.

    Pomocí gesta aktualizace můžete zobrazit seznam položek, což způsobí zobrazení přihlašovací obrazovky. Po úspěšném zadání platných přihlašovacích údajů se v aplikaci zobrazí seznam položek TODO a aktualizace dat můžete provést.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Vytvoření aplikace Xamarin. iOS]: app-service-mobile-xamarin-ios-get-started.md
