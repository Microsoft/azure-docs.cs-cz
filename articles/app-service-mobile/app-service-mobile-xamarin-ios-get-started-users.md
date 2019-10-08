---
title: Začínáme s ověřováním pro Mobile Apps v Xamarin iOS
description: Naučte se používat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin iOS prostřednictvím různých poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1af488d4f170508bbf586621d00e9a92657983ca
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024813"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Přidání ověřování do aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="overview"></a>Přehled

V tomto tématu se dozvíte, jak ověřit uživatele App Service mobilní aplikace z klientské aplikace. V tomto kurzu přidáte do projektu pro rychlé zprovoznění Xamarin. iOS ověřování pomocí zprostředkovatele identity, který podporuje App Service. Po úspěšném ověření a autorizaci vaší mobilní aplikace se zobrazí hodnota ID uživatele a budete mít přístup k omezeným datům tabulky.

Nejdřív musíte dokončit kurz [Vytvoření aplikace Xamarin. iOS]. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, je nutné přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal](https://portal.azure.com/)vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci představuje schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V aplikaci Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Selhání je zaznamenáno do konzoly ladicího programu. Takže v aplikaci Visual Studio by se chyba měla zobrazit v okně výstup.

    K této neautorizované chybě dochází, protože se aplikace pokusí získat přístup k back-endu mobilní aplikace jako neověřeného uživatele. Tabulka *TodoItem* nyní vyžaduje ověření.

V dalším kroku aktualizujete klientskou aplikaci tak, aby požadovala prostředky z back-endu mobilní aplikace pomocí ověřeného uživatele.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
V této části upravíte aplikaci tak, aby před zobrazením dat zobrazovala přihlašovací obrazovku. Po spuštění aplikace se nepřipojí k vašemu App Service a nezobrazí se žádná data. Po prvním spuštění gesta obnovení uživatelem se zobrazí přihlašovací obrazovka. Po úspěšném přihlášení se zobrazí seznam položek todo.

1. V projektu klienta otevřete soubor **QSTodoService.cs** a přidejte následující příkaz using a `MobileServiceUser` s přistupujícím objektem ke třídě QSTodoService:

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

6. Otevřete soubor **info. plist** , v části **Upřesnit** přejděte na **typy adres URL** . Nyní nakonfigurujte **identifikátor** a **schémata adres** URL typu adresy URL a klikněte na tlačítko **Přidat typ adresy URL**. **Schémata URL** by měla být stejná jako vaše aplikace {url_scheme_of_your_app}.
7. V aplikaci Visual Studio připojené k hostiteli Mac nebo Visual Studio pro Mac spusťte projekt klienta cílící na zařízení nebo emulátor. Ověřte, že aplikace nezobrazuje žádná data.

    Pomocí gesta aktualizace můžete zobrazit seznam položek, což způsobí zobrazení přihlašovací obrazovky. Po úspěšném zadání platných přihlašovacích údajů se v aplikaci zobrazí seznam položek TODO a aktualizace dat můžete provést.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Vytvoření aplikace Xamarin. iOS]: app-service-mobile-xamarin-ios-get-started.md
