---
title: Přidání nabízených oznámení do aplikace Apache Cordova
description: Přečtěte si, jak pomocí mobilních aplikací odesílat nabízená oznámení do aplikace Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461602"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Přidání nabízených oznámení do aplikace Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého startu Apache Cordova][5] tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace naleznete [v tématu Práce s sadou SDK back-end serveru .NET pro mobilní aplikace][1].

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že máte aplikaci Apache Cordova, která byla vyvinuta s Visual Studio 2015. Toto zařízení by mělo běžet na Google Android Emulator, zařízení Android, zařízení se systémem Windows nebo zařízení se systémem iOS.

Pro absolvování tohoto kurzu potřebujete:

* Počítač s [visual studio community 2015][2] nebo novější
* [Nástroje Visual Studia pro Apache Cordova][4]
* [Aktivní účet Azure][3]
* Dokončený projekt [rychlého startu Apache Cordova][5]
* (Android) [Účet Google][6] s ověřenou e-mailovou adresou
* (iOS) [Členství v programu pro vývojáře Apple][7] a zařízení se systémem iOS (simulátor iOS nepodporuje nabízená oznámení)
* (Windows) [Vývojářský účet Microsoft Storu][8] a zařízení s Windows 10

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Podívejte se na video s postupem v této části][9].

## <a name="update-the-server-project"></a>Aktualizace projektu serveru

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="modify-your-cordova-app"></a><a name="add-push-to-app"></a>Upravte aplikaci Cordova

Chcete-li zajistit, aby váš projekt aplikace Apache Cordova byl připraven zpracovávat nabízená oznámení, nainstalujte plugin push Cordova a všechny nabízené služby specifické pro platformu.

#### <a name="update-the-cordova-version-in-your-project"></a>Aktualizujte verzi Cordova v projektu.

Pokud váš projekt používá verzi Apache Cordova, která je starší než verze 6.1.1, aktualizujte klientský projekt. Chcete-li projekt aktualizovat, postupujte takto:

* Chcete-li otevřít návrhářkonfigurace, `config.xml`klepněte pravým tlačítkem myši na položku .
* Vyberte kartu **Platformy.**
* V textovém poli **Cordova CLI** vyberte **6.1.1**. 
* Chcete-li projekt aktualizovat, vyberte **build**a pak vyberte **Build Solution**.

#### <a name="install-the-push-plugin"></a>Instalace pluginu push

Aplikace Apache Cordova nezpracovávají nativně funkce zařízení nebo sítě.  Tyto funkce jsou poskytovány pluginy, které jsou publikovány buď na [npm][10] nebo na GitHubu. Plugin `phonegap-plugin-push` zpracovává oznámení o nabízení sítí.

Plugin push můžete nainstalovat jedním z následujících způsobů:

**Z příkazového řádku:**

Spusťte následující příkaz:

    cordova plugin add phonegap-plugin-push

**Z visual studia:**

1. V Průzkumníku řešení `config.xml` otevřete soubor. Dále vyberte **vlastní pluginy** > **.** Pak vyberte **Git** jako zdroj instalace.

2. Zadejte `https://github.com/phonegap/phonegap-plugin-push` jako zdroj.

    ![Otevření souboru config.xml v Průzkumníku řešení][img1]

3. Vyberte šipku vedle zdroje instalace.

4. V **SENDER_ID**, pokud již máte číselné ID projektu pro vývojářskou konzolu Google, můžete ho přidat zde. V opačném případě zadejte zástupnou hodnotu, například 777777. Pokud cílíte na android, můžete tuto hodnotu později aktualizovat v souboru config.xml.

    >[!NOTE]
    >Od verze 2.0.0 je třeba nainstalovaný soubor google-services.json do kořenové složky projektu, aby bylo možné nakonfigurovat ID odesílatele. Další informace naleznete v dokumentaci k [instalaci.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Vyberte **Přidat**.

Push plugin je nyní nainstalován.

#### <a name="install-the-device-plugin"></a>Instalace pluginu zařízení

Postupujte stejným způsobem, který jste použili k instalaci push plugin. Přidejte plugin zařízení ze seznamu základních pluginů. (Chcete-li jej najít, vyberte **pluginy** > **Core**.) Potřebujete tento plugin získat název platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Registrace zařízení při spuštění aplikace 

Zpočátku jsme zahrnout některé minimální kód pro Android. Později můžete upravit aplikaci tak, aby běžela v iOS nebo Windows 10.

1. Přidejte volání **registerForPushNotifications** během zpětného volání pro proces přihlášení. Alternativně můžete přidat v dolní části **onDeviceReady** metoda:

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    Tento příklad ukazuje volání **registerForPushNotifications** po úspěšném ověření. Můžete volat `registerForPushNotifications()` tak často, jak je požadováno.

2. Přidejte novou metodu **registerForPushNotifications** takto:

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android) V předchozím kódu nahraďte `Your_Project_ID` číselným ID projektu pro vaši aplikaci z Google Developer [Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Nepovinné) Konfigurace a spuštění aplikace v systému Android

Chcete-li povolit nabízená oznámení pro Android, vyplňte tuto část.

#### <a name="enable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>Povolení služby Firebase Cloud Messaging

Vzhledem k tomu, že zpočátku cílíte na platformu Google Android, musíte povolit Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-the-mobile-app-back-end-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>Konfigurace back-endu mobilní aplikace pro odesílání nabízených požadavků pomocí FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurace aplikace Cordova pro Android

V aplikaci Cordova otevřete **soubor config.xml**. Potom `Your_Project_ID` nahraďte číselným ID projektu pro vaši aplikaci z [Google Developer Console][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otevřete **soubor index.js**. Potom aktualizujte kód tak, aby používal číselné ID projektu.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>Konfigurace zařízení Android pro ladění USB

Než budete moci nasadit aplikaci do zařízení Android, musíte povolit ladění USB. V telefonu s Androidem postupujte takto:

1. Přejděte na **Nastavení** > **o telefonu**. Pak klepněte na **číslo sestavení,** dokud není povolen režim vývojáře (asi sedmkrát).
2. Zpět v **nastavení** > **možnosti pro vývojáře**, povolit **ladění USB**. Poté připojte telefon Android k vývojovému počítači pomocí kabelu USB.

Testovali jsme to pomocí zařízení Google Nexus 5X se systémem Android 6.0 (Marshmallow). Nicméně, techniky jsou společné v každém moderním android verzi.

#### <a name="install-google-play-services"></a>Instalace služeb Google Play

Push plugin se spoléhá na služby Android Google Play pro nabízená oznámení.

1. V sadě Visual Studio vyberte **Nástroje** > **Správce** > **sady Android SDK**. Pak rozbalte složku **Extras.** Zkontrolujte příslušná políčka a ujistěte se, že je nainstalována každá z následujících sad SDK:

   * Android 2.3 nebo vyšší
   * Revize úložiště Google 27 nebo vyšší
   * Služby Google Play 9.0.2 nebo vyšší

2. Vyberte **možnost Instalovat balíčky**. Poté počkejte na dokončení instalace.

Aktuální požadované knihovny jsou uvedeny v [dokumentaci k instalaci phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testování nabízených oznámení v aplikaci na Androidu

Nyní můžete testovat nabízená oznámení spuštěním aplikace a vložením položek do tabulky TodoItem. Můžete testovat ze stejného zařízení nebo z druhého zařízení, pokud používáte stejný back-end. Otestujte svou aplikaci Cordova na platformě Android jedním z následujících způsobů:

* *Na fyzickém zařízení:* Připojte zařízení Android k vývojovému počítači pomocí kabelu USB.  Místo **emulátoru Google Android**vyberte **možnost Zařízení**. Visual Studio nasazuje aplikaci do zařízení a spouští aplikaci. Potom můžete pracovat s aplikací v zařízení.

  Aplikace pro sdílení obrazovky, jako je [Mobizen,][20] vám mohou pomoci při vývoji aplikací pro Android. Mobizen promítá obrazovku Android do webového prohlížeče v počítači.

* *Na emulátoru Android:* Existují další kroky konfigurace, které jsou vyžadovány při použití emulátoru.

    Ujistěte se, že nasazujete do virtuálního zařízení, které má jako cíl nastavená rozhraní API Google, jak je znázorněno ve správci virtuálního zařízení Android (AVD).

    ![Správce virtuálních zařízení android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Pokud chcete použít rychlejší emulátor x86, [nainstalujte ovladač HAXM][11]a nakonfigurujte emulátor tak, aby jej používal.

    Přidejte účet Google do zařízení Android výběrem**nastavení** >  **aplikace** > **Přidat účet**. Pak postupujte podle pokynů.

    ![Přidání účtu Google do zařízení Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Spusťte aplikaci todolist jako dříve a vložte novou položku úkolů. Tentokrát se v oznamovací oblasti zobrazí ikona oznámení. Můžete otevřít zásuvku oznámení a zobrazit úplný text oznámení.

    ![Zobrazit oznámení](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Nepovinné) Konfigurace a spuštění v iOS

Tato část je určen pro spuštění projektu Cordova na zařízeních se systémem iOS. Pokud nepracujete se zařízeními se systémem iOS, můžete tuto část přeskočit.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalace a spuštění agenta vzdáleného sestavení iOS na Macu nebo cloudové službě

Než budete moci spustit aplikaci Cordova v iOS pomocí sady Visual Studio, projděte si kroky v [průvodci nastavením iOS][12] a nainstalujte a spusťte agenta vzdáleného sestavení.

Ujistěte se, že můžete vytvořit aplikaci pro iOS. Kroky v průvodci nastavením jsou vyžadovány pro vytváření aplikace pro iOS z Visual Studia. Pokud mac nemáte, můžete vytvořit pro iOS pomocí vzdáleného agenta sestavení ve službě, jako je MacInCloud. Další informace najdete [v tématu Spuštění aplikace pro iOS v cloudu][21].

> [!NOTE]
> Xcode 7 nebo vyšší je nutné použít push plugin na iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Najděte ID, které chcete použít jako ID aplikace

Než aplikaci zaregistrujete pro nabízená oznámení, otevřete v aplikaci Cordova soubor config.xml, najděte hodnotu `id` atributu v elementu widgetu a zkopírujte ji pro pozdější použití. V následujícím jazyce XML `io.cordova.myapp7777777`je ID .

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Později použijte tento identifikátor při vytváření ID aplikace na portálu pro vývojáře společnosti Apple. Pokud na portálu pro vývojáře vytvoříte jiné ID aplikace, musíte provést několik dalších kroků později v tomto kurzu. ID v elementu widgetu se musí shodovat s ID aplikace na portálu pro vývojáře.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na vývojářském portálu Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Ověřte, zda vaše ID aplikace odpovídá vaší aplikaci Cordova

Pokud ID aplikace, které jste vytvořili ve svém vývojářském účtu Apple, již odpovídá ID prvku widgetu v souboru config.xml, můžete tento krok přeskočit. Pokud se však ID neshodují, postupujte takto:

1. Odstraňte složku platformy z projektu.
2. Odstraňte složku pluginů z projektu.
3. Odstraňte složku node_modules z projektu.
4. Aktualizujte atribut id prvku widgetu v souboru config.xml tak, aby používal ID aplikace, které jste vytvořili ve svém vývojářském účtu Apple.
5. Znovu sestavte projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testování nabízených oznámení v aplikaci pro iOS

1. V sadě Visual Studio se ujistěte, že je jako cíl nasazení vybrán **iOS.** Pak vyberte **Zařízení,** které chcete spustit nabízená oznámení na připojeném iOS zařízení.

    Nabízená oznámení můžete spouštět na iOS zařízení, které je připojené k počítači s iTunes. Simulátor iOS nepodporuje nabízená oznámení.

2. Vyberte tlačítko **Spustit** nebo **F5** v Sadě Visual Studio k sestavení projektu a spuštění aplikace v zařízení se systémem iOS. Pak vyberte **OK,** chcete-li přijmout nabízená oznámení.

   > [!NOTE]
   > Aplikace požaduje potvrzení nabízených oznámení během prvního spuštění.

3. V aplikaci zadejte úkol a pak vyberte ikonu plus **(+).**
4. Ověřte, zda bylo přijato oznámení. Pak vyberte **OK,** chcete-li oznámení zavřít.

## <a name="optional-configure-and-run-on-windows"></a>(Nepovinné) Konfigurace a spuštění v systému Windows

Tato část popisuje, jak spustit projekt aplikace Apache Cordova na zařízeních s Windows 10 (push plugin PhoneGap je podporován ve Windows 10). Pokud nepracujete se zařízeními se systémem Windows, můžete tuto část přeskočit.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrace aplikace pro Windows pro nabízená oznámení pomocí služby WNS

Chcete-li použít možnosti obchodu v sadě Visual Studio, vyberte cíl systému Windows ze seznamu Platformy řešení, například **Windows-x64** nebo **Windows-x86**. (Vyhněte se **Windows AnyCPU** pro nabízená oznámení.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Podívejte se na video zobrazující podobný postup.][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro službu WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurace aplikace Cordova pro podporu nabízených oznámení ve Windows

Otevřete návrháře konfigurace klepnutím pravým tlačítkem myši na **soubor config.xml**. Pak vyberte **Zobrazit Návrhář**. Dále vyberte kartu **Windows** a pak v části **Windows Target Version**vyberte Windows **10** .

Chcete-li podporovat nabízená oznámení ve výchozích (ladicích) sestaveních, otevřete soubor **build.json.** Pak zkopírujte konfiguraci "release" do konfigurace ladění.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizaci by měl soubor **build.json** obsahovat následující kód:

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

Vytvořte aplikaci a ověřte, že nemáte žádné chyby. Vaše klientská aplikace by se teď měla zaregistrovat pro oznámení z back-endu mobilních aplikací. Opakujte tuto část pro každý projekt systému Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testování nabízených oznámení v aplikaci pro Windows

V sadě Visual Studio zkontrolujte, zda je jako cíl nasazení vybrána platforma Windows, například **Windows-x64** nebo **Windows-x86**. Pokud chcete aplikaci spustit na počítači s Windows 10, který je hostitelem Visual Studia, zvolte **Místní počítač**.

1. Vyberte tlačítko **Spustit** pro sestavení projektu a spuštění aplikace.

2. V aplikaci zadejte název nové todopoložky a pak ji přidejte vyberte ikonu plus **(+).**

Ověřte, zda je přijato oznámení při přidání položky.

## <a name="next-steps"></a><a name="next-steps"></a>Další kroky

* Přečtěte si o [centrech oznámení,][17] kde se dozvíte o nabízených oznámeních.
* Pokud jste tak ještě neučinili, pokračujte v kurzu [přidáním ověřování][14] do aplikace Apache Cordova.

Přečtěte si, jak používat následující sady SDK:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
