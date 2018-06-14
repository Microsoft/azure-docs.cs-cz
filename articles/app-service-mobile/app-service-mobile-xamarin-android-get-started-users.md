---
title: Začínáme s ověřováním pro Mobile Apps v Xamarin Android
description: Další informace o použití mobilní aplikace ověřovat uživatele vaší aplikace Xamarin Android prostřednictvím řady různých zprostředkovatelů identity, včetně AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 97207b722b65ccf98c57304cd559b0927aacd5a4
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595291"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Přidání ověřování do aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Toto téma ukazuje, jak ověřovat uživatele mobilní aplikace z klientské aplikace. V tomto kurzu přidání ověřování do projektu pro rychlý start pomocí zprostředkovatele identity, který podporuje Azure Mobile Apps. Po se úspěšně ověří a autorizuje v mobilní aplikace, se zobrazí hodnota ID uživatele.

V tomto kurzu vychází z rychlého startu mobilní aplikace. Musíte také nejdřív dokončit tento kurz [vytvoření aplikace Xamarin.Android]. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrace aplikace pro ověřování a nakonfigurujte aplikační služby
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

V sadě Visual Studio nebo Xamarin Studio spuštění klientského projektu na emulátoru nebo zařízení. Ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. K tomu dochází, protože se aplikace pokusí o přístup k váš back-end mobilní aplikace jako neověřený uživatel. *TodoItem* tabulka nyní vyžaduje ověření.

Potom bude aktualizujte klientskou aplikaci pro požadavky na prostředky z back-end mobilní aplikace s ověřeného uživatele.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
Aplikace se aktualizuje tak, aby vyžadovala uživatelům umožní klepnout **přihlášení** tlačítko a ověření, než se zobrazí data.

1. Přidejte následující kód, který **TodoActivity** třídy:
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Tím se vytvoří novou metodu k ověření uživatele a metoda obslužnou rutinu pro novou **přihlášení** tlačítko. Ověření uživatele ve výše uvedeném příkladu kódu pomocí přihlášení Facebook. Zobrazí se dialogové okno se používá k zobrazení ID uživatele po ověření.
   
   > [!NOTE]
   > Pokud používáte zprostředkovatele identity než Facebook, změňte hodnotu předaný **LoginAsync** výše na jednu z následujících: *MicrosoftAccount*, *Twitter*, *Google*, nebo *WindowsAzureActiveDirectory*.
   > 
   > 
2. V **OnCreate** metoda, odstranit nebo okomentujte následující řádek kódu:
   
        OnRefreshItemsSelected ();
3. V souboru Activity_To_Do.axml, přidejte následující *LoginUser* tlačítko definice před existující *AddItem* tlačítko:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Přidejte následující element do souboru prostředků Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Otevření souboru AndroidManifest.xml, přidejte následující kód do `<application>` – element XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. V sadě Visual Studio nebo Xamarin Studio spuštění klientského projektu na emulátoru nebo zařízení a přihlaste se pomocí zprostředkovatele identity vybrané. Pokud jste úspěšně přihlášeni, aplikace se zobrazí přihlašovací ID a seznam položek todo a data můžete provádět aktualizace.

<!-- URLs. -->
[vytvoření aplikace Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md