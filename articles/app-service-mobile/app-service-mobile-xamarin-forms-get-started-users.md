---
title: Začínáme s ověřováním v aplikaci Xamarin Forms
description: Přečtěte si, jak pomocí mobilních aplikací ověřovat uživatele aplikace Xamarin Forms u poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458762"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Přidání ověřování do aplikace Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Přehled
Toto téma ukazuje, jak ověřit uživatele mobilní aplikace App Service z klientské aplikace. V tomto kurzu přidáte ověřování do projektu rychlého startu Xamarin Forms pomocí zprostředkovatele identity, který je podporován službou App Service. Po úspěšném ověření a autorizaci mobilní aplikací se zobrazí hodnota ID uživatele a budete mít přístup k datům tabulky s omezeným přístupem.

## <a name="prerequisites"></a>Požadavky
Pro dosažení nejlepšího výsledku v tomto kurzu doporučujeme nejprve dokončit kurz [vytvoření aplikace Xamarin Forms.][1] Po dokončení tohoto kurzu budete mít projekt Xamarin Forms, který je víceplatformovou aplikací TodoList.

Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíček rozšíření ověřování. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrace aplikace pro ověřování a konfigurace služeb app services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL. To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu. Můžete však použít libovolné schéma adres URL, které zvolíte. To by mělo být jedinečné pro vaši mobilní aplikaci. Povolení přesměrování na straně serveru:

1. Na [webu Azure Portal][8]vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V **adresách URL allowed external redirect zadejte** `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci je schéma adresy URL pro mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Přidání ověřování do knihovny přenosných tříd
Mobilní aplikace používá metodu rozšíření [LoginAsync][3] na [klientovi MobileServiceClient][4] k přihlášení uživatele pomocí ověřování služby App Service. Tato ukázka používá tok ověřování spravované houštinou spravovaným serverem, který zobrazuje přihlašovací rozhraní zprostředkovatele v aplikaci. Další informace naleznete v [tématu Ověřování spravované serverem][5]. Chcete-li poskytnout lepší uživatelské prostředí v produkční aplikaci, měli byste zvážit použití [ověřování spravovaného klientem][6].

Chcete-li se ověřit pomocí projektu Xamarin Forms, definujte rozhraní **IAuthenticate** v knihovně přenosných tříd pro aplikaci. Potom přidejte tlačítko **přihlášení** do uživatelského rozhraní definovaného v knihovně přenosných tříd, na které klepnete a zahájíte ověřování. Data se načtou z back-endu mobilní aplikace po úspěšném ověření.

Implementujte rozhraní **IAuthenticate** pro každou platformu podporovanou vaší aplikací.

1. V sadě Visual Studio nebo Xamarin Studio otevřete App.cs z projektu s **přenosným** v `using` názvu, což je projekt knihovny přenosných tříd, a přidejte následující příkaz:

        using System.Threading.Tasks;
2. V App.cs přidejte `IAuthenticate` následující definici `App` rozhraní bezprostředně před definici třídy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Chcete-li inicializovat rozhraní s implementací specifickou pro platformu, přidejte následující statické členy do třídy **App.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otevřete soubor TodoList.xaml z projektu Knihovna přenosných tříd, za existující tlačítko přidejte do elementu *rozložení buttonsPanel* následující prvek **Button:**

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Toto tlačítko aktivuje ověřování spravované serverem pomocí back-endu mobilní aplikace.
5. Otevřete TodoList.xaml.cs z projektu Knihovna přenosných tříd `TodoList` a přidejte do třídy následující pole:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Nahraďte metodu **OnAppearing** následujícím kódem:

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

    Tento kód zajišťuje, že data jsou aktualizována ze služby až po ověření.
7. Do třídy **TodoList** přidejte následující obslužnou rutinu pro událost **Clicked:**

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Uložte změny a znovu vytvořte projekt knihovny přenosných tříd a ověřte žádné chyby.

## <a name="add-authentication-to-the-android-app"></a>Přidání ověřování do aplikace pro Android
Tato část ukazuje, jak implementovat rozhraní **IAuthenticate** v projektu aplikace pro Android. Pokud zařízení Se systémem Android nepodporujete, tuto část přeskočte.

1. V sadě Visual Studio nebo Xamarin Studio klikněte pravým tlačítkem myši na projekt **droida** a potom **nastavte jako počáteční projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Kód 401 je vytvořen, protože přístup k back-endu je omezen pouze na oprávněné uživatele.
3. Otevřete MainActivity.cs v projektu Android `using` a přidejte následující příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizujte **mainactivity** třídy k implementaci **rozhraní IAuthenticate** takto:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizujte třídu **MainActivity** přidáním pole **MobileServiceUser** a metody **Authenticate,** která je vyžadována rozhraním **IAuthenticate,** následujícím způsobem:

        // Define an authenticated user.
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

    Pokud používáte jiného poskytovatele identity než Facebook, zvolte pro [MobileServiceAuthenticationProvider][7]jinou hodnotu .

6. Aktualizujte soubor **AndroidManifest.xml** přidáním následujícího xml uvnitř elementu: `<application>`

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
    Nahraďte `{url_scheme_of_your_app}` schéma adres URL.
7. Přidejte následující kód do metody **OnCreate** třídy **MainActivity** před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Tento kód zajišťuje, že ověřovatel je inicializován před načtením aplikace.
8. Znovu sestavte aplikaci, spusťte ji a přihlaste se pomocí vybraného poskytovatele ověřování a ověřte, že máte přístup k datům jako ověřený uživatel.

### <a name="troubleshooting"></a>Řešení potíží

**Aplikace havarovala s`Java.Lang.NoSuchMethodError: No static method startActivity`**

V některých případech konflikty v balíčcích podpory zobrazeny jako pouze upozornění v sadě Visual Studio, ale aplikace dojde k chybě s touto výjimkou za běhu. V takovém případě se musíte ujistit, že všechny balíčky podpory odkazované v projektu mají stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)má`Xamarin.Android.Support.CustomTabs`závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

## <a name="add-authentication-to-the-ios-app"></a>Přidání ověřování do aplikace pro iOS
Tato část ukazuje, jak implementovat rozhraní **IAuthenticate** v projektu aplikace pro iOS. Pokud zařízení s iOS nepodporujete, tuto část přeskočte.

1. V sadě Visual Studio nebo Xamarin Studio klikněte pravým tlačítkem myši na projekt **iOS** a potom **nastavte jako počáteční projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Odpověď 401 je vytvořena, protože přístup k back-endu je omezen pouze na oprávněné uživatele.
3. Otevřete AppDelegate.cs v projektu iOS `using` a přidejte následující příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizujte třídu **AppDelegate,** abyste implementovali rozhraní **IAuthenticate,** a to následovně:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizujte třídu **AppDelegate** přidáním pole **MobileServiceUser** a metody **Authenticate,** která je vyžadována rozhraním **IAuthenticate,** následujícím způsobem:

        // Define an authenticated user.
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
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Pokud používáte jiného poskytovatele identity než Facebook, zvolte jinou hodnotu pro [MobileServiceAuthenticationProvider].
    
6. Aktualizujte třídu **AppDelegate** přidáním přetížení metody **OpenUrl** následujícím způsobem:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Přidejte následující řádek kódu do **finishedlaunch** metoda `LoadApplication()`před voláním :

        App.Init(this);

    Tento kód zajišťuje, že ověřovatel je inicializován před načtením aplikace.

8. Otevřete soubor Info.plist a přidejte **typ adresy URL**. Nastavte **identifikátor** na název, který si vyberete, **schémata adres URL** na schéma adres URL pro vaši aplikaci a **roli** k žádné.

9. Znovu sestavte aplikaci, spusťte ji a přihlaste se pomocí vybraného poskytovatele ověřování a ověřte, že máte přístup k datům jako ověřený uživatel.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Přidání ověřování do projektů aplikací pro Windows 10 (včetně telefonu)
Tato část ukazuje, jak implementovat rozhraní **IAuthenticate** v projektech aplikací pro Windows 10. Stejné kroky platí pro projekty univerzální platformy Windows (UPW), ale pomocí projektu **UPW** (s poznámkami změny). Pokud zařízení se systémem Windows nepodporujete, tuto část přeskočte.

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt **UPW** a potom **na Nastavit jako počáteční projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). 401 odpověď se stane, protože přístup na back-endu je omezena pouze na oprávněné uživatele.
3. Otevřete MainPage.xaml.cs pro projekt aplikace `using` pro Windows a přidejte následující příkazy:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Nahraďte `<your_Portable_Class_Library_namespace>` oborem názvů pro přenosnou knihovnu tříd.
4. Aktualizujte třídu **MainPage,** abyste implementovali rozhraní **IAuthenticate,** a to následovně:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizujte třídu **MainPage** přidáním pole **MobileServiceUser** a metody **Authenticate,** která je vyžadována rozhraním **IAuthenticate,** následujícím způsobem:

        // Define an authenticated user.
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

    Pokud používáte jiného poskytovatele identity než Facebook, zvolte pro [MobileServiceAuthenticationProvider][7]jinou hodnotu .

1. Přidejte následující řádek kódu do konstruktoru pro třídu `LoadApplication()` **MainPage** před voláním :

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Nahraďte `<your_Portable_Class_Library_namespace>` oborem názvů pro přenosnou knihovnu tříd.

3. Pokud používáte **UPW**, přidejte do třídy **App** následující přepsání metody **OnActivated:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Otevřete Soubor Package.appxmanifest a přidejte **deklaraci protokolu.** Nastavte **zobrazovaný název** na název, který si vyberete, a **název** na schéma adres URL pro vás.

4. Znovu sestavte aplikaci, spusťte ji a přihlaste se pomocí vybraného poskytovatele ověřování a ověřte, že máte přístup k datům jako ověřený uživatel.

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento základní kurz ověřování, zvažte pokračování na jeden z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-xamarin-forms-get-started-push.md)

  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – prohlížení, přidávání nebo úpravy dat – i když neexistuje žádné síťové připojení.

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
