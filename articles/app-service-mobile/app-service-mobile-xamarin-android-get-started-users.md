---
title: Začínáme s ověřováním v Xamarin Android
description: Přečtěte si, jak pomocí mobilních aplikací ověřovat uživatele aplikace Xamarin pro Android pomocí poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458949"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Přidání ověřování do aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
Toto téma ukazuje, jak ověřit uživatele mobilní aplikace z klientské aplikace. V tomto kurzu přidáte ověřování do projektu rychlého startu pomocí zprostředkovatele identity, který je podporován Azure Mobile Apps. Po úspěšném ověření a autorizaci v mobilní aplikaci se zobrazí hodnota ID uživatele.

Tento kurz je založen na rychlém startu mobilní aplikace. Musíte také nejprve dokončit výukový program [Vytvořit aplikaci Xamarin.Android]. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíček rozšíření ověřování. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Registrace aplikace pro ověřování a konfigurace služeb app services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL. To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu. Můžete však použít libovolné schéma adres URL, které zvolíte. To by mělo být jedinečné pro vaši mobilní aplikaci. Povolení přesměrování na straně serveru:

1. Na [Portálu Azure] vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V **adresách URL allowed external redirect zadejte** `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizovaný). K tomu dochází, protože se aplikace pokusí o přístup k back-endu mobilní aplikace jako neověřený uživatel. Tabulka *TodoItem* nyní vyžaduje ověření.

Dále aktualizujete klientskou aplikaci tak, aby požadovala prostředky z back-endu mobilní aplikace s ověřeným uživatelem.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Přidání ověřování do aplikace
Aplikace je aktualizována tak, aby vyžadovala, aby uživatelé před zobrazením dat klepněte na tlačítko **Přihlásit** se a ověřili se.

1. Přidejte do třídy **TodoActivity** následující kód:
   
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
   
    Tím se vytvoří nová metoda ověření uživatele a obslužná rutina metody pro nové tlačítko **Přihlásit.** Uživatel ve výše uvedeném ukázkovém kódu je ověřen pomocí přihlášení na Facebooku. Dialogové okno se používá k zobrazení ID uživatele po ověření.
   
   > [!NOTE]
   > Pokud používáte jiného poskytovatele identity než Facebook, změňte hodnotu předanou na **LoginAsync** výše na jednu z následujících hodnot: *MicrosoftAccount*, *Twitter*, *Google*nebo *WindowsAzureActiveDirectory*.
   > 
   > 
2. V **onCreate** metoda odstranit nebo komentář-out následující řádek kódu:
   
        OnRefreshItemsSelected ();
3. Do souboru Activity_To_Do.axml přidejte před existující tlačítko *AddItem* následující definici tlačítka *LoginUser:*
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Do souboru prostředků Strings.xml přidejte následující prvek:
   
        <string name="login_button_text">Sign in</string>
5. Otevřete soubor AndroidManifest.xml, přidejte `<application>` do elementu XML následující kód:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru a přihlaste se pomocí zvoleného poskytovatele identity. Když jste úspěšně přihlášeni, aplikace zobrazí vaše přihlašovací ID a seznam položek todo a můžete aktualizovat data.

## <a name="troubleshooting"></a>Řešení potíží

**Aplikace havarovala s`Java.Lang.NoSuchMethodError: No static method startActivity`**

V některých případech konflikty v balíčcích podpory zobrazeny jako pouze upozornění v sadě Visual Studio, ale aplikace dojde k chybě s touto výjimkou za běhu. V takovém případě se musíte ujistit, že všechny balíčky podpory odkazované v projektu mají stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)má`Xamarin.Android.Support.CustomTabs`závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

<!-- URLs. -->
[Vytvoření aplikace Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
