---
title: Začínáme s ověřováním mobilních aplikací v aplikaci Xamarin Forms | Microsoft Docs
description: Další informace o použití mobilní aplikace ověřovat uživatele vaší aplikace Xamarin Forms prostřednictvím řady různých zprostředkovatelů identity, včetně AAD, Google, Facebook, Twitter a Microsoft.
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
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: e3e8c843437558c6d5d3a3c39bed1e647f852b18
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593394"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Přidání ověřování do aplikace Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
Toto téma ukazuje, jak ověřovat uživatele aplikace služby mobilní aplikace z klientské aplikace. V tomto kurzu přidání ověřování do projektu pro rychlý start Xamarin Forms pomocí zprostředkovatele identity, která je podporována službou App Service. Poté, co se úspěšně ověří a autorizuje pomocí mobilní aplikace, zobrazí se hodnota ID uživatele a bude mít přístup k datům s omezeným přístupem tabulky.

## <a name="prerequisites"></a>Požadavky
Nejlepších výsledků v tomto kurzu, doporučujeme, abyste nejdříve dokončili [vytvoření aplikace pro Xamarin Forms] [ 1] kurzu. Po dokončení tohoto kurzu budete mít projekt Xamarin Forms, který je aplikace seznamu úkolů více platformami.

Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a nakonfigurujte aplikační služby
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externí přesměrování

Zabezpečené ověřování vyžaduje, můžete definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schématu adresy URL _appname_ v průběhu. Můžete však použít žádné schéma adresy URL, které zvolíte. Musí být jedinečné pro mobilní aplikace. Chcete povolit přesměrování na straně serveru:

1. V [portál Azure][8], vyberte svoji službu aplikace.

2. Klikněte **ověřování / autorizace** možnost nabídky.

3. V **povoleno externí adres URL pro přesměrování**, zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tento řetězec je schéma adresy URL pro mobilní aplikace.  Měl by splňovat specifikaci normální adresu URL pro určitý protokol (používejte písmena a čísla pouze a začněte s písmenem).  Měli byste si poznamenat řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Přidání ověřování do knihovny přenosných tříd
Mobilní aplikace používá [LoginAsync] [ 3] rozšiřující metody na [MobileServiceClient] [ 4] k přihlášení uživatele pomocí ověřování služby App Service. Tato ukázka používá toku spravovaný server ověřování, který zobrazí poskytovatele přihlášení rozhraní v aplikaci. Další informace najdete v tématu [spravovaný Server ověřování][5]. Chcete-li zajistit lepší prostředí uživatele ve vaší aplikaci produkční, měli byste zvážit namísto použití [klienta spravovat ověřování][6].

K ověření s projektem Xamarin Forms, definovat **IAuthenticate** rozhraní na přenosné knihovny tříd pro aplikaci. Pak přidejte **přihlášení** tlačítko uživatelského rozhraní definované v přenosných třída knihovny, která kliknutí na tlačítko spustit ověřování. Data jsou načítána z back-end mobilní aplikace po úspěšném ověření.

Implementace **IAuthenticate** rozhraní pro každou platformu podporovanou nástrojem vaší aplikace.

1. V sadě Visual Studio nebo Xamarin Studio otevřete App.cs z projektu s **přenosné** v názvu, který je projektu knihovny přenosných tříd, přidejte následující `using` příkaz:

        using System.Threading.Tasks;
2. V App.cs, přidejte následující `IAuthenticate` definici rozhraní bezprostředně před `App` definici třídy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. K chybě při inicializaci rozhraní s implementací specifických pro platformy, přidejte následující statické členy **aplikace** třídy.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otevřete TodoList.xaml z projektu knihovny přenosných tříd, přidejte následující **tlačítko** element v *buttonsPanel* element rozložení po existující tlačítko:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Toto tlačítko se aktivuje server spravovaný ověřování s váš back-end mobilní aplikace.
5. Otevřete TodoList.xaml.cs z projektu knihovny přenosných tříd a pak přidejte následující pole, které chcete `TodoList` třídy:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Nahraďte **OnAppearing** metoda následujícím kódem:

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

    Tento kód je zajištěno, že data se pouze aktualizují ze služby po jste ověření.
7. Přidejte následující obslužnou rutinu pro **místní** událost, která má **TodoList** třídy:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Uložte změny a znovu sestavte projekt knihovny přenosných tříd žádné chyby ověření.

## <a name="add-authentication-to-the-android-app"></a>Přidání ověřování do aplikace pro Android
V této části ukazuje, jak implementovat **IAuthenticate** rozhraní v projektu aplikace pro Android. Tuto část přeskočte, pokud nejsou podpora zařízení se systémem Android.

1. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **droid** projekt, pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. Kód 401 je vytvořen, protože přístup na back-end je omezený pouze autorizovaným uživatelům.
3. Otevřete MainActivity.cs v projektu pro Android a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizace **MainActivity** třídu pro implementaci **IAuthenticate** rozhraní, následujícím způsobem:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizace **MainActivity** třída přidáním **MobileServiceUser** pole a **ověřit** metoda, který je požadován **IAuthenticate** rozhraní, následujícím způsobem:

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

    Pokud používáte zprostředkovatele identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider][7].

6. Aktualizace **AndroidManifest.xml** přidáním následující kód XML v souboru `<application>` element:

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
    Nahraďte `{url_scheme_of_your_app}` s vaše schéma adresy URL.
7. Přidejte následující kód, který **OnCreate** metodu **MainActivity** třída před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Tento kód zajistí, že ověřovacích se inicializuje před zatížení aplikace.
8. Znovu sestavte aplikaci, spusťte ji a pak přihlásit se pomocí zprostředkovatele ověřování, které jste zvolili a ověřte, zda že budete moci získat přístup k datům jako ověřený uživatel.

## <a name="add-authentication-to-the-ios-app"></a>Přidání ověřování do aplikace pro iOS
V této části ukazuje, jak implementovat **IAuthenticate** rozhraní v projektu aplikace pro iOS. Tuto část přeskočte, pokud nejsou podporujete zařízení s iOS.

1. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **iOS** projekt, pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. Odpovědi 401 je vytvořen, protože přístup na back-end je omezený pouze autorizovaným uživatelům.
3. Otevřete v projektu iOS AppDelegate.cs a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizace **AppDelegate** třídu pro implementaci **IAuthenticate** rozhraní, následujícím způsobem:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizace **AppDelegate** třída přidáním **MobileServiceUser** pole a **ověřit** metoda, který je požadován **IAuthenticate** rozhraní, následujícím způsobem:

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

    Pokud používáte zprostředkovatele identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider].
    
6. Aktualizace **AppDelegate** třída přidáním **OpenUrl** metoda přetížení následujícím způsobem:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Přidejte následující řádek kódu **FinishedLaunching** metody před volání `LoadApplication()`:

        App.Init(this);

    Tento kód zajistí, že je ověřovacích inicializovat před načtením aplikace.

8. Otevřete Info.plist a přidejte **adresa URL typu**. Nastavte **identifikátor** název dle vlastního výběru, **schémata URL** na schéma adresy URL pro aplikaci a **Role** na hodnotu None.

9. Znovu sestavte aplikaci, spusťte ji a pak přihlásit se pomocí zprostředkovatele ověřování, které jste zvolili a ověřte, zda že budete moci získat přístup k datům jako ověřený uživatel.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Přidání ověřování do aplikace projekty Windows 10 (včetně Phone)
V této části ukazuje, jak implementovat **IAuthenticate** rozhraní v projektech aplikace Windows 10. Stejný postup platí pro univerzální platformu Windows (UWP) projekty, ale pomocí **UWP** projektu (s uvedené změny). Tuto část přeskočte, pokud nejsou podpora zařízení se systémem Windows.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **UWP** projekt, pak **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a potom ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace. Odpovědi 401 se stane, protože přístup na back-end je omezený pouze autorizovaným uživatelům.
3. Otevřete MainPage.xaml.cs pro projekt aplikace Windows a přidejte následující `using` příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Nahraďte `<your_Portable_Class_Library_namespace>` s oborem názvů pro knihovny přenosných tříd.
4. Aktualizace **MainPage** třídu pro implementaci **IAuthenticate** rozhraní, následujícím způsobem:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizace **MainPage** třída přidáním **MobileServiceUser** pole a **ověřit** metoda, který je požadován **IAuthenticate** rozhraní, následujícím způsobem:

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

    Pokud používáte zprostředkovatele identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider][7].

1. Přidejte následující řádek kódu v konstruktoru pro **MainPage** třída před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Nahraďte `<your_Portable_Class_Library_namespace>` s oborem názvů pro knihovny přenosných tříd.

3. Pokud používáte **UWP**, přidejte následující **OnActivated** metoda přepsání nastavení za účelem **aplikace** třídy:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Otevřete Package.appxmanifest a přidejte **protokol** deklarace. Nastavte **zobrazovaný název** na název vašeho výběru a **název** na schéma adresy URL pro aplikaci.

4. Znovu sestavte aplikaci, spusťte ji a pak přihlásit se pomocí zprostředkovatele ověřování, které jste zvolili a ověřte, zda že budete moci získat přístup k datům jako ověřený uživatel.

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračovat na jednu z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-xamarin-forms-get-started-push.md)

  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Informace o postupu přidání vaší aplikace pomocí back-end mobilní aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací - zobrazení, přidávat a upravovat data - i v případě, že není žádné síťové připojení.

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
