---
title: Začínáme s ověřováním pro Mobile Apps v aplikaci Xamarin Forms | Dokumentace Microsoftu
description: Naučte se využívat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin Forms prostřednictvím různých poskytovatelů identit, včetně AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: f7e500fb5856c7eec48a371042244b44dd944779
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063763"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Přidání ověřování do aplikace Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
V tomto tématu se dozvíte, jak ověřovat uživatele aplikace služby App Service Mobile z klientské aplikace. V tomto kurzu přidat ověřování do projektu Xamarin Forms pomocí zprostředkovatele identity, který je podporovaný službou App Service. Poté, co se úspěšně ověří a autorizuje mobilní aplikace, zobrazí se hodnota ID uživatele a bude mít přístup k datům s omezeným přístupem tabulky.

## <a name="prerequisites"></a>Požadavky
Pro nejlepší výsledek s tímto kurzem, doporučujeme, abyste nejdříve dokončili [vytvoření aplikace Xamarin Forms] [ 1] kurzu. Po dokončení tohoto kurzu budete mít projekt Xamarin Forms, který je multiplatformní aplikaci seznamu úkolů.

Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a konfigurace služby App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externího přesměrování

Zabezpečené ověřování, musíte definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schéma adresy URL _appname_ v průběhu. Můžete ale použít jakékoli schéma adresy URL, kterou zvolíte. Musí být jedinečné pro vaši mobilní aplikaci. Pokud chcete povolit přesměrování na straně serveru:

1. V [webu Azure portal][8], vyberte službu App Service.

2. Klikněte na tlačítko **ověřování / autorizace** nabídky.

3. V **povolené externí adresy URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měla by odpovídat specifikaci normální adresu URL pro určitý protokol (použití písmena a čísla jenom a začíná písmenem).  By měl poznamenejte řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Přidání ověřování do knihovny přenosných tříd
Mobile Apps využívá [LoginAsync] [ 3] rozšiřující metody na [MobileServiceClient] [ 4] se přihlásit uživatele pomocí služby App Service ověřování. Tato ukázka používá server spravován ověřování tok, který se zobrazí zprostředkovatele přihlášení rozhraní v aplikaci. Další informace najdete v tématu [spravovaného serveru ověřování][5]. Pokud chcete poskytovat lepší prostředí uživatele v aplikaci pro produkčního prostředí, měli byste zvážit namísto použití [klienta spravovat ověřování][6].

K ověření s projektem Xamarin Forms, definujte **IAuthenticate** rozhraní v knihovně přenosných tříd pro aplikace. Pak přidejte **přihlášení** tlačítko na uživatelské rozhraní definováno v přenosné knihovně tříd, které kliknete začít ověřování. Data se načtou z back-endu mobilní aplikace po úspěšném ověření.

Implementace **IAuthenticate** rozhraní pro každou platformu podporovaný vaší aplikace.

1. V sadě Visual Studio nebo Xamarin Studio, otevřete App.cs z projektu pomocí **přenosné** v názvu, která je přenosná knihovna tříd projektu, pak přidejte následující `using` – příkaz:

        using System.Threading.Tasks;
2. V souboru App.cs, přidejte následující `IAuthenticate` těsně před definici rozhraní `App` definici třídy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Inicializace rozhraní s implementací specifické pro platformu, přidejte následující statické členy **aplikace** třídy.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otevřete TodoList.xaml z projektu knihovny přenosných tříd, přidejte následující **tlačítko** prvek *buttonsPanel* rozložení elementu po existující tlačítko:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Toto tlačítko spustí spravovaného serveru ověřování s back-endu mobilní aplikace.
5. Otevřete TodoList.xaml.cs z projektu knihovny přenosných tříd a přidejte následující pole do `TodoList` třídy:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Nahradit **OnAppearing** metodu s následujícím kódem:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Tento kód zajišťuje, že pouze aktualizaci dat ze služby po ověření můžete.
7. Přidejte následující obslužnou rutinu pro **kliknutí na** událost, abyste **TodoList** třídy:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Uložte změny a znovu sestavit projekt knihovny přenosných tříd žádné chyby ověření.

## <a name="add-authentication-to-the-android-app"></a>Přidání ověřování do aplikace pro Android
Tato část ukazuje, jak implementovat **IAuthenticate** rozhraní v projektu aplikace pro Android. Tuto část přeskočte, pokud nejsou podporuje zařízení s Androidem.

1. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **droid** projekt, a pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že se stavovým kódem 401 (Neautorizováno) neošetřená výjimka je aktivována po spuštění aplikace. Kód 401 je vytvořen, protože přístup na back-end je omezen pouze oprávněným uživatelům.
3. Otevřete MainActivity.cs v projektu pro Android a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizace **MainActivity** třídu pro implementaci **IAuthenticate** rozhraní následujícím způsobem:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizace **MainActivity** třídy tak, že přidáte **MobileServiceUser** pole a **ověřit** metodu, která vyžaduje **IAuthenticate** rozhraní následujícím způsobem:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Pokud používáte zprostředkovatelů identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider][7].

6. Aktualizace **AndroidManifest.xml** tak, že přidáte následující kód XML v souboru `<application>` element:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Nahraďte `{url_scheme_of_your_app}` schématem adresy URL.
7. Přidejte následující kód, který **OnCreate** metodu **MainActivity** třídy před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Tento kód zajistí, že si aplikaci authenticator se inicializuje před zatížení aplikace.
8. Znovu sestavit aplikaci, spusťte ho a pak přihlásit pomocí zprostředkovatele ověřování, vybrali a ověřte, že se že vám daří získat přístup k datům jako ověřený uživatel.

### <a name="troubleshooting"></a>Řešení potíží

**Aplikace došlo k chybě s `Java.Lang.NoSuchMethodError: No static method startActivity`**

V některých případech je v konfliktu v podpůrných balíčků zobrazí jako varování v sadě Visual studio, ale selhání aplikace s touto výjimkou za běhu. V tomto případě musíte Ujistěte se, že všechny podporu balíčky v projektu mají stejnou verzi. [Balíčku NuGet pro Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) má `Xamarin.Android.Support.CustomTabs` závislostí pro platformy Android, tak pokud váš projekt používá novější podporu balíčky je nutné nainstalovat tento balíček s požadovanou verzi přímo, aby nedocházelo ke konfliktům.

## <a name="add-authentication-to-the-ios-app"></a>Přidání ověřování do aplikace pro iOS
Tato část ukazuje, jak implementovat **IAuthenticate** rozhraní v projektu aplikace pro iOS. Tuto část přeskočte, pokud nejsou podporuje zařízení s Iosem.

1. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **iOS** projekt, a pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že se stavovým kódem 401 (Neautorizováno) neošetřená výjimka je aktivována po spuštění aplikace. Odpovědi na 401 je vytvořen, protože přístup na back-end je omezen pouze oprávněným uživatelům.
3. Otevřete AppDelegate.cs v projektu pro iOS a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizace **AppDelegate** třídu pro implementaci **IAuthenticate** rozhraní následujícím způsobem:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizace **AppDelegate** třídy tak, že přidáte **MobileServiceUser** pole a **ověřit** metodu, která vyžaduje **IAuthenticate** rozhraní následujícím způsobem:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Pokud používáte zprostředkovatelů identity než Facebook, zvolte jinou hodnotu [MobileServiceAuthenticationProvider].
    
6. Aktualizace **AppDelegate** třídy tak, že přidáte **OpenUrl** přetížení metody, následujícím způsobem:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Přidejte následující řádek kódu, který **FinishedLaunching** před voláním metody `LoadApplication()`:

        App.Init(this);

    Tento kód zajistí, že si aplikaci authenticator je inicializován před načtení aplikace.

8. Otevřete soubor Info.plist a přidejte **typ adresy URL**. Nastavte **identifikátor** název podle vlastního výběru, **schémata URL** na schéma adresy URL pro vaši aplikaci a **Role** na hodnotu None.

9. Znovu sestavit aplikaci, spusťte ho a pak přihlásit pomocí zprostředkovatele ověřování, vybrali a ověřte, že se že vám daří získat přístup k datům jako ověřený uživatel.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Přidání ověřování do projektů aplikací pro Windows 10 (včetně Phone)
Tato část ukazuje, jak implementovat **IAuthenticate** rozhraní v projekty aplikací pro Windows 10. Stejný postup platí pro projekty pro univerzální platformu Windows (UPW), ale používat **UPW** projekt (s uvedené změny). Tuto část přeskočte, pokud nejsou podporuje zařízení s Windows.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **UPW** projekt, a pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že se stavovým kódem 401 (Neautorizováno) neošetřená výjimka je aktivována po spuštění aplikace. Odpovědi na 401 se stane, protože přístup na back-end je omezen pouze oprávněným uživatelům.
3. Otevřete MainPage.xaml.cs pro projekt aplikace Windows a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Nahraďte `<your_Portable_Class_Library_namespace>` s oborem názvů pro knihovny přenosných tříd.
4. Aktualizace **MainPage** třídu pro implementaci **IAuthenticate** rozhraní následujícím způsobem:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizace **MainPage** třídy tak, že přidáte **MobileServiceUser** pole a **ověřit** metodu, která vyžaduje **IAuthenticate**rozhraní následujícím způsobem:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Pokud používáte zprostředkovatelů identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider][7].

1. Přidejte následující řádek kódu v konstruktoru pro **MainPage** třídy před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Nahraďte `<your_Portable_Class_Library_namespace>` s oborem názvů pro knihovny přenosných tříd.

3. Pokud používáte **UPW**, přidejte následující **OnActivated** metody přepsání nastavení za účelem **aplikace** třídy:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Otevřete v Package.appxmanifest a přidejte **protokol** deklarace. Nastavte **zobrazovaný název** název podle svého výběru a **název** na schéma adresy URL pro aplikaci.

4. Znovu sestavit aplikaci, spusťte ho a pak přihlásit pomocí zprostředkovatele ověřování, vybrali a ověřte, že se že vám daří získat přístup k datům jako ověřený uživatel.

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračováním jednu z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-xamarin-forms-get-started-push.md)

  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i v případě, že není žádné síťové připojení.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
