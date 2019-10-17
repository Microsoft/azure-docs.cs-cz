---
title: Začínáme s ověřováním pro Mobile Apps v aplikaci Xamarin Forms | Microsoft Docs
description: Naučte se používat Mobile Apps k ověřování uživatelů vaší aplikace Xamarin Forms prostřednictvím různých poskytovatelů identit, jako jsou AAD, Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d9bd698535b09ecb5c484eefcbe31228eb99e04f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388412"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Přidání ověřování do aplikace Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dnes.

## <a name="overview"></a>Přehled
V tomto tématu se dozvíte, jak ověřit uživatele App Service mobilní aplikace z klientské aplikace. V tomto kurzu přidáte do projektu pro rychlý Start formulářů Xamarin ověřování pomocí zprostředkovatele identity, který je podporován App Service. Po úspěšném ověření a autorizaci vaší mobilní aplikace se zobrazí hodnota ID uživatele a budete mít přístup k omezeným datům tabulky.

## <a name="prerequisites"></a>Předpoklady
Pro dosažení co nejlepších výsledků v tomto kurzu doporučujeme, abyste nejdřív dokončili kurz [Vytvoření aplikace pro tvorbu formulářů Xamarin][1] . Po dokončení tohoto kurzu budete mít projekt Xamarin Forms, který je aplikací TodoList s více platformami.

Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, je nutné přidat balíček rozšíření ověřování do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal][8]vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** v tomto řetězci představuje schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Přidání ověřování do přenositelné knihovny tříd
Mobile Apps používá metodu rozšíření [LoginAsync][3] na [MobileServiceClient][4] k přihlášení uživatele s ověřováním App Service. Tato ukázka používá tok ověřování spravovaný serverem, který zobrazuje rozhraní pro přihlášení zprostředkovatele v aplikaci. Další informace najdete v tématu [ověřování spravované serverem][5]. Pro zajištění lepšího uživatelského prostředí v produkční aplikaci byste měli zvážit místo toho, abyste používali [ověřování spravovaného klientem][6].

Pro ověření pomocí projektu Xamarin Forms definujte rozhraní **IAuthenticate** v přenositelné knihovně tříd pro aplikaci. Pak přidejte tlačítko pro **přihlášení** k uživatelskému rozhraní definovanému v knihovně přenosných tříd, na kterou kliknete, chcete-li spustit ověřování. Data se po úspěšném ověření načítají z back-endu mobilní aplikace.

Implementujte rozhraní **IAuthenticate** pro každou platformu, kterou vaše aplikace podporuje.

1. V aplikaci Visual Studio nebo Xamarin Studio otevřete App.cs z projektu s **přenosným** v názvu, který je Přenosná knihovna tříd projektu, a přidejte následující příkaz `using`:

        using System.Threading.Tasks;
2. V App.cs přidejte následující definici rozhraní `IAuthenticate` bezprostředně před definicí třídy `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Chcete-li inicializovat rozhraní s implementací specifickou pro konkrétní platformu, přidejte do třídy **App** následující statické členy.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otevřete TodoList. XAML z přenositelného projektu knihovny tříd, přidejte následující element **Button** v elementu rozložení *buttonsPanel* po existujícím tlačítku:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Toto tlačítko aktivuje ověřování spravované serverem pomocí back-endu vaší mobilní aplikace.
5. Otevřete TodoList.xaml.cs z projektu přenositelné knihovny tříd a pak přidejte následující pole do třídy `TodoList`:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Nahraďte metodu při **zobrazení** následujícím kódem:

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

    Tento kód zajistí, že data se po ověření aktualizují jenom ze služby.
7. Přidejte následující obslužnou rutinu pro událost **Click** do třídy **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Uložte změny a znovu sestavte projekt přenositelné knihovny tříd, který neověřuje žádné chyby.

## <a name="add-authentication-to-the-android-app"></a>Přidání ověřování do aplikace pro Android
V této části se dozvíte, jak implementovat rozhraní **IAuthenticate** v projektu aplikace pro Android. Pokud zařízení s Androidem nepodporujete, přeskočte tuto část.

1. V sadě Visual Studio nebo Xamarin Studio klikněte pravým tlačítkem na projekt **Droid** a pak **nastavte jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a pak ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Kód 401 se vytvoří, protože přístup k back-endu je omezený jenom na autorizované uživatele.
3. V projektu pro Android otevřete MainActivity.cs a přidejte následující příkazy `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizujte třídu **MainActivity** pro implementaci rozhraní **IAuthenticate** následujícím způsobem:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizujte třídu **MainActivity** přidáním pole **MobileServiceUser** a metody **ověřování** , která je požadována rozhraním **IAuthenticate** , následovně:

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

    Pokud používáte jiného poskytovatele identity než Facebook, vyberte pro [MobileServiceAuthenticationProvider][7]jinou hodnotu.

6. Aktualizujte soubor **souboru AndroidManifest. XML** přidáním následujícího kódu XML do prvku `<application>`:

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
    Nahraďte `{url_scheme_of_your_app}` vaším schématem URL.
7. Do metody **Create** třídy **MainActivity** přidejte následující kód před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Tento kód zajišťuje inicializaci ověřovatele před tím, než se aplikace načte.
8. Znovu sestavte aplikaci, spusťte ji a pak se přihlaste pomocí poskytovatele ověřování, kterého jste zvolili, a ověřte, že máte přístup k datům jako ověřený uživatel.

### <a name="troubleshooting"></a>Řešení potíží

**V aplikaci došlo k chybě s `Java.Lang.NoSuchMethodError: No static method startActivity`.**

V některých případech jsou konflikty v balíčcích pro podporu zobrazeny jako upozornění v aplikaci Visual Studio, ale aplikace při běhu selže s touto výjimkou. V takovém případě je nutné se ujistit, že všechny balíčky podpory, na které se odkazuje v projektu, mají stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) má závislost `Xamarin.Android.Support.CustomTabs` pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

## <a name="add-authentication-to-the-ios-app"></a>Přidání ověřování do aplikace pro iOS
V této části se dozvíte, jak implementovat rozhraní **IAuthenticate** v projektu aplikace pro iOS. Pokud nepodporujete zařízení s iOS, přeskočte tuto část.

1. V sadě Visual Studio nebo Xamarin Studio klikněte pravým tlačítkem na projekt pro **iOS** a pak **nastavte jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a pak ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). Odpověď 401 se vytvoří, protože přístup k back-endu je omezený jenom na autorizované uživatele.
3. Otevřete AppDelegate.cs v projektu iOS a přidejte následující příkazy `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizujte třídu **AppDelegate** pro implementaci rozhraní **IAuthenticate** následujícím způsobem:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizujte třídu **AppDelegate** přidáním pole **MobileServiceUser** a metody **ověřování** , která je požadována rozhraním **IAuthenticate** , následovně:

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

    Pokud používáte jiného poskytovatele identity než Facebook, vyberte pro [MobileServiceAuthenticationProvider] jinou hodnotu.
    
6. Aktualizujte třídu **AppDelegate** přidáním přetížení metody **OpenURL** následujícím způsobem:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Přidejte následující řádek kódu do metody **FinishedLaunching** před voláním `LoadApplication()`:

        App.Init(this);

    Tento kód zajišťuje inicializaci ověřovatele před načtením aplikace.

8. Otevřete info. plist a přidejte **typ adresy URL**. Nastavte **identifikátor** na název podle vašeho výběru, **schémat adres URL** na schéma URL vaší aplikace a **roli** na žádné.

9. Znovu sestavte aplikaci, spusťte ji a pak se přihlaste pomocí poskytovatele ověřování, kterého jste zvolili, a ověřte, že máte přístup k datům jako ověřený uživatel.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Přidání ověřování do projektů aplikací pro Windows 10 (včetně telefonních)
V této části se dozvíte, jak implementovat rozhraní **IAuthenticate** v projektech aplikací pro Windows 10. Stejný postup platí pro projekty Univerzální platforma Windows (UWP), ale použití projektu **UWP** (s popsanými změnami). Pokud nepodporujete zařízení s Windows, přeskočte tuto část.

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt **UWP** a pak **nastavte jako spouštěný projekt**.
2. Stisknutím klávesy F5 spusťte projekt v ladicím programu a pak ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno). K odpovědi 401 dochází, protože přístup k back-endu je omezený jenom na autorizované uživatele.
3. Otevřete MainPage.xaml.cs pro projekt aplikace pro Windows a přidejte následující příkazy `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Nahraďte `<your_Portable_Class_Library_namespace>` oborem názvů vaší přenosné knihovny tříd.
4. Aktualizujte třídu **MainPage** pro implementaci rozhraní **IAuthenticate** následujícím způsobem:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizujte třídu **MainPage** přidáním pole **MobileServiceUser** a metody **ověřování** , která je požadována rozhraním **IAuthenticate** , následovně:

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

    Pokud používáte jiného poskytovatele identity než Facebook, vyberte pro [MobileServiceAuthenticationProvider][7]jinou hodnotu.

1. Přidejte následující řádek kódu do konstruktoru třídy **MainPage** před voláním `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Nahraďte `<your_Portable_Class_Library_namespace>` oborem názvů vaší přenosné knihovny tříd.

3. Pokud používáte **UWP**, přidejte do třídy **App** následující přepsání metody **Activate** :

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Otevřete Package. appxmanifest a přidejte deklaraci **protokolu** . Nastavte **zobrazované jméno** na požadovaný název a **název** schématu URL pro vaši aplikaci.

4. Znovu sestavte aplikaci, spusťte ji a pak se přihlaste pomocí poskytovatele ověřování, kterého jste zvolili, a ověřte, že máte přístup k datům jako ověřený uživatel.

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento kurz základního ověřování, zvažte pokračování v jednom z následujících kurzů:

* [Přidání nabízených oznámení do aplikace](app-service-mobile-xamarin-forms-get-started-push.md)

  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací a zobrazovat nebo upravovat data, a to i v případě, že není k dispozici žádné síťové připojení.

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
