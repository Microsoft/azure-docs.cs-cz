---
title: Začínáme s ověřováním v Xamarin Androidu
description: Naučte se používat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin Android pomocí zprostředkovatelů identity, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458949"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Přidání ověřování do aplikace Xamarin. Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
V tomto tématu se dozvíte, jak ověřit uživatele mobilní aplikace z klientské aplikace. V tomto kurzu přidáte do projektu pro rychlé zprovoznění ověřování pomocí zprostředkovatele identity, který podporuje Azure Mobile Apps. Po úspěšném ověření a autorizaci v mobilní aplikaci se zobrazí hodnota ID uživatele.

Tento kurz je založený na rychlém startu mobilní aplikace. Musíte taky nejdřív dokončit kurz [Vytvoření aplikace Xamarin. Android]. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, je nutné přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V části [Azure Portal] vyberte svůj App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na možnost **Uložit**.

## <a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V aplikaci Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). K tomu dochází, protože se aplikace pokusí získat přístup k back-endu mobilní aplikace jako neověřeného uživatele. Tabulka *TodoItem* nyní vyžaduje ověření.

V dalším kroku aktualizujete klientskou aplikaci tak, aby požadovala prostředky z back-endu mobilní aplikace pomocí ověřeného uživatele.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
Aplikace je aktualizovaná a vyžaduje, aby uživatelé po zobrazení dat museli klepnout na tlačítko **Přihlásit** a ověřit.

1. Do třídy **TodoActivity** přidejte následující kód:
   
        // Define an authenticated user.
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
   
    Tím se vytvoří nová metoda pro ověření uživatele a obslužné rutiny metody pro nové tlačítko pro **přihlášení** . Uživatel v příkladu kódu výše se ověřuje pomocí přihlašovacího jména na Facebooku. Dialogové okno se používá k zobrazení ID uživatele po ověření.
   
   > [!NOTE]
   > Pokud používáte jiného poskytovatele identity než Facebook, změňte hodnotu předanou na **LoginAsync** výše na jednu z následujících hodnot: *MicrosoftAccount*, *Twitter*, *Google*nebo *WindowsAzureActiveDirectory*.
   > 
   > 
2. V metodě **Create** odstraňte nebo odkomentujte následující řádek kódu:
   
        OnRefreshItemsSelected ();
3. V souboru Activity_To_Do. axml přidejte následující definici tlačítka *LoginUser* před existující tlačítko pro *AddItem* :
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Do souboru strings. XML Resources přidejte následující element:
   
        <string name="login_button_text">Sign in</string>
5. Otevřete soubor souboru AndroidManifest. XML a přidejte následující kód do `<application>` XML element:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. V aplikaci Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru a přihlaste se pomocí zvoleného poskytovatele identity. Po úspěšném přihlášení se v aplikaci zobrazí vaše přihlašovací ID a seznam položek TODO a můžete provádět aktualizace dat.

## <a name="troubleshooting"></a>Řešení potíží

**V aplikaci došlo k chybě `Java.Lang.NoSuchMethodError: No static method startActivity`**

V některých případech jsou konflikty v balíčcích pro podporu zobrazeny jako upozornění v aplikaci Visual Studio, ale aplikace při běhu selže s touto výjimkou. V takovém případě je nutné se ujistit, že všechny balíčky podpory, na které se odkazuje v projektu, mají stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) má závislost `Xamarin.Android.Support.CustomTabs` pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

<!-- URLs. -->
[Vytvoření aplikace Xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
