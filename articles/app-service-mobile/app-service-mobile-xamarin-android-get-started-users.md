---
title: Začínáme s ověřováním pro Mobile Apps v rámci Xamarin Android
description: Naučte se využívat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin Android prostřednictvím různých poskytovatelů identit, včetně AAD, Google, Facebook, Twitter a Microsoft.
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
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: d496801894560310a4225eae8a32fced52bcc428
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063536"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Přidání ověřování do aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

V tomto tématu se dozvíte, jak ověřovat uživatele aplikace pro mobilní zařízení z klientské aplikace. V tomto kurzu přidejte do projektu ověřování pomocí zprostředkovatele identity, který podporuje Azure Mobile Apps ověřování. Po se úspěšně ověří a autorizuje v mobilní aplikaci, zobrazí se hodnota ID uživatele.

Tento kurz je založený na mobilní aplikace tohoto rychlého startu. Musíte nejprve dokončit kurz [vytvoření aplikace Xamarin.Android]. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby App Services
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

V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru. Ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). K tomu dochází, protože se aplikace pokusí o přístup k back-endu mobilní aplikace jako neověřené uživatele. *TodoItem* tabulka nyní vyžaduje ověřování.

Dále budete aktualizovat klientskou aplikaci na požadavky na prostředky z back-endu mobilní aplikace s ověřeného uživatele.

## <a name="add-authentication"></a>Přidání ověřování do aplikace
Aplikace se aktualizuje tak, aby vyžadovala uživatelům umožní klepnout na **přihlášení** tlačítko a ověření předtím, než se zobrazí data.

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
   
    Tím se vytvoří novou metodu k ověření uživatele a metoda obslužné rutiny pro nový **přihlášení** tlačítko. Ověření uživatele ve výše uvedeném příkladu kódu pomocí přihlašovacích údajů služby Facebook. Dialogové okno se používá k zobrazení po ověření ID uživatele.
   
   > [!NOTE]
   > Pokud používáte zprostředkovatelů identity než Facebook, změňte hodnotu předanou **LoginAsync** výše na jednu z následujících akcí: *MicrosoftAccount*, *Twitter*,  *Google*, nebo *WindowsAzureActiveDirectory*.
   > 
   > 
2. V **OnCreate** metody, odstranit nebo okomentujte následující řádek kódu:
   
        OnRefreshItemsSelected ();
3. V souboru Activity_To_Do.axml, přidejte následující *LoginUser* tlačítko definice před existující *AddItem* tlačítka:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Přidejte následující prvek do souboru prostředků Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Otevřete soubor AndroidManifest.xml, přidejte následující kód `<application>` – element XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. V sadě Visual Studio nebo Xamarin Studio spusťte klientský projekt na zařízení nebo emulátoru a přihlaste se pomocí zprostředkovatele identity zvolená. Po úspěšném přihlášení se zobrazí přihlašovací ID a seznam položek todo a dat můžete provádět aktualizace.

## <a name="troubleshooting"></a>Řešení potíží

**Aplikace došlo k chybě s `Java.Lang.NoSuchMethodError: No static method startActivity`**

V některých případech je v konfliktu v podpůrných balíčků zobrazí jako varování v sadě Visual studio, ale selhání aplikace s touto výjimkou za běhu. V tomto případě musíte Ujistěte se, že všechny podporu balíčky v projektu mají stejnou verzi. [Balíčku NuGet pro Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) má `Xamarin.Android.Support.CustomTabs` závislostí pro platformy Android, tak pokud váš projekt používá novější podporu balíčky je nutné nainstalovat tento balíček s požadovanou verzi přímo, aby nedocházelo ke konfliktům.

<!-- URLs. -->
[Vytvoření aplikace Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
