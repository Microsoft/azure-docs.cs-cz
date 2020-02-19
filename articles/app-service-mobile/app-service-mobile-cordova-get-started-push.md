---
title: Přidání nabízených oznámení do aplikace Apache Cordova
description: Naučte se používat Mobile Apps k odesílání nabízených oznámení do aplikace Apache Cordova.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 08260437076728421cb6fa393f481d27b95b1782
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461602"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Přidání nabízených oznámení do aplikace Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu pro [rychlý start Apache Cordova][5] , takže se do zařízení pošle nabízené oznámení pokaždé, když se záznam vloží.

Pokud nepoužíváte stažený projekt serveru pro rychlý Start, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce s back-end serverem sady .NET pro Mobile Apps][1].

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte Apache Cordova aplikaci, která byla vyvinuta se sadou Visual Studio 2015. Toto zařízení by mělo běžet na Google Android Emulator, zařízení s Androidem, zařízení s Windows nebo zařízení s iOS.

Pro absolvování tohoto kurzu potřebujete:

* POČÍTAČ se sadou [Visual Studio Community 2015][2] nebo novější
* [Visual Studio Tools for Apache Cordova][4]
* [Aktivní účet Azure][3]
* Dokončený [rychlý úvodní Apache Cordova][5] projekt
* Svém [Účet Google][6] s ověřenou e-mailovou adresou
* iOS [Členství v programu Apple Developer][7] a zařízení s iOS (simulátor iOS nepodporuje nabízená oznámení)
* Systému [Vývojářský účet Microsoft Store][8] a zařízení s Windows 10

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Podívejte se na video se zobrazením kroků v této části][9].

## <a name="update-the-server-project"></a>Aktualizovat serverový projekt

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Úprava aplikace Cordova

Chcete-li zajistit, aby byl projekt aplikace Apache Cordova připravený na zpracování nabízených oznámení, nainstalujte modul plug-in Cordova push a všechny služby nabízených oznámení pro konkrétní platformu.

#### <a name="update-the-cordova-version-in-your-project"></a>Aktualizujte verzi Cordova ve vašem projektu.

Pokud váš projekt používá verzi Apache Cordova, která je starší než verze 6.1.1, aktualizujte projekt klienta. Chcete-li aktualizovat projekt, proveďte následující kroky:

* Chcete-li otevřít návrháře konfigurace, klikněte pravým tlačítkem na `config.xml`.
* Vyberte kartu **platformy** .
* V textovém poli **Cordova CLI** vyberte **6.1.1**. 
* Chcete-li aktualizovat projekt, vyberte možnost **sestavit**a pak vyberte možnost **Sestavit řešení**.

#### <a name="install-the-push-plugin"></a>Instalace modulu plug-in push

Apache Cordova aplikace nezpracovávají nativně možnosti zařízení nebo sítě.  Tyto možnosti nabízí moduly plug-in, které jsou publikované buď na [npm][10] nebo na GitHubu. Modul plug-in `phonegap-plugin-push` zpracovává nabízená oznámení ze sítě.

Modul plug-in push můžete nainstalovat jedním z následujících způsobů:

**Z příkazového řádku:**

Spusťte následující příkaz:

    cordova plugin add phonegap-plugin-push

**Z aplikace Visual Studio:**

1. V Průzkumník řešení otevřete `config.xml` soubor. V dalším kroku vyberte **moduly plug-in** > **vlastní**. Pak jako zdroj instalace vyberte **Git** .

2. Jako zdroj zadejte `https://github.com/phonegap/phonegap-plugin-push`.

    ![Otevřete soubor config. XML v Průzkumník řešení][img1]

3. Vyberte šipku vedle zdroje instalace.

4. V **SENDER_ID**, pokud už máte ID číselného projektu pro projekt vývojářské konzoly Google, můžete ho přidat tady. V opačném případě zadejte hodnotu zástupného symbolu, například 777777. Pokud cílíte na Android, můžete tuto hodnotu aktualizovat později v souboru config. XML.

    >[!NOTE]
    >Od verze 2.0.0 je nutné nainstalovat Google-Services. JSON do kořenové složky projektu, aby bylo možné konfigurovat ID odesílatele. Další informace najdete v [dokumentaci k instalaci.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Vyberte **Přidat**.

Modul plug-in Push je teď nainstalovaný.

#### <a name="install-the-device-plugin"></a>Instalace modulu plug-in zařízení

Postupujte stejným způsobem, jako jste použili k instalaci modulu plug-in push. Přidejte modul plug-in zařízení ze seznamu základních modulů plug-in. (Pokud ho chcete najít, vyberte **moduly plug-in** > **jádro**.) Tento modul plug-in potřebujete k získání názvu platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Registrace zařízení při spuštění aplikace 

Zpočátku jsme zahrnuli minimální kód pro Android. Později můžete aplikaci upravit tak, aby běžela v iOS nebo Windows 10.

1. Přidání volání **registerForPushNotifications** během zpětného volání pro proces přihlášení. Případně ho můžete přidat na konec metody **onDeviceReady** :

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

2. Přidejte novou metodu **registerForPushNotifications** následujícím způsobem:

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
3. Svém V předchozím kódu nahraďte `Your_Project_ID` číslem ID projektu vaší aplikace z [konzole pro vývojáře Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Volitelné Konfigurace a spuštění aplikace v Androidu

Dokončete tuto část, pokud chcete povolit nabízená oznámení pro Android.

#### <a name="enable-gcm"></a>Povolení zasílání zpráv Firebase do cloudu

Vzhledem k tomu, že cílíte na platformu Google Android, musíte povolit zasílání zpráv Firebase do cloudu.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurace back-endu mobilní aplikace pro posílání nabízených žádostí pomocí FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurace aplikace Cordova pro Android

V aplikaci Cordova otevřete **soubor config. XML**. Potom nahraďte `Your_Project_ID` číslem ID projektu vaší aplikace z [konzole pro vývojáře Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otevřete **index. js**. Pak aktualizujte kód tak, aby používal vaše číselné ID projektu.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurace zařízení s Androidem pro ladění USB

Než budete moct nasadit aplikaci na zařízení s Androidem, musíte povolit ladění USB. Na telefonu s Androidem proveďte následující kroky:

1. Přejít na **nastavení** > **o telefonu**. Pak klepněte na **číslo sestavení** , dokud není povolen režim pro vývojáře (přibližně sedm).
2. Zpátky v **nastavení** > **Možnosti pro vývojáře**, povolit **ladění USB**. Pak připojte telefon s Androidem k vývojovému počítači pomocí kabelu USB.

Otestovali jsme to pomocí zařízení Google Nexus pětinásobné se systémem Android 6,0 (Marshmallow). Nicméně postupy jsou společné v rámci moderní verze Androidu.

#### <a name="install-google-play-services"></a>Nainstalovat Služby Google Play

Modul plug-in push spoléhá na Služby Google Play Androidu pro nabízená oznámení.

1. V aplikaci Visual Studio vyberte **nástroje** > **Android** > **Android SDK Manager**. Pak rozbalte složku **Další** . Zaškrtněte příslušná políčka, abyste měli jistotu, že jsou nainstalované všechny následující sady SDK:

   * Android 2,3 nebo vyšší
   * Revize úložiště Google 27 nebo vyšší
   * Služby Google Play 9.0.2 nebo vyšší

2. Vyberte **instalovat balíčky**. Potom počkejte na dokončení instalace.

Aktuální požadované knihovny jsou uvedené v dokumentaci k [instalaci PhoneGap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Testování nabízených oznámení v aplikaci v Androidu

Nabízená oznámení teď můžete testovat spuštěním aplikace a vložením položek do tabulky TodoItem. Můžete testovat ze stejného zařízení nebo z druhého zařízení, pokud používáte stejný back-end. Otestujte aplikaci Cordova na platformě Android jedním z následujících způsobů:

* *Na fyzickém zařízení:* Připojte své zařízení s Androidem k vývojovému počítači pomocí kabelu USB.  Místo **Google Android Emulator**vyberte **zařízení**. Visual Studio nasadí aplikaci do zařízení a spustí aplikaci. Pak můžete s aplikací pracovat na zařízení.

  Aplikace pro sdílení obrazovky, jako je [Mobizen][20] , vám můžou pomoct při vývoji aplikací pro Android. Mobizen projekty na obrazovku Androidu do webového prohlížeče na svém počítači.

* *V emulátoru Androidu:* Při použití emulátoru jsou potřeba další kroky konfigurace.

    Ujistěte se, že nasazujete na virtuální zařízení, které má rozhraní Google API nastavené jako cíl, jak je znázorněno na AVD (Virtual Device Manager) pro Android.

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Pokud chcete použít rychlejší emulátor x86, [nainstalujte ovladač modul HAXM][11]a potom nakonfigurujte emulátor tak, aby ho používal.

    Vyberte **aplikace** > **Nastavení** > **Přidat účet**a přidejte na zařízení s Androidem účet Google. Pak postupujte podle pokynů.

    ![Přidání účtu Google na zařízení s Androidem](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Spusťte aplikaci ToDoList jako dřív a vložte novou položku todo. Tentokrát se v oznamovací oblasti zobrazí ikona oznámení. K zobrazení úplného textu oznámení můžete otevřít zásuvku oznámení.

    ![Zobrazit oznámení](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Volitelné Konfigurace a spuštění v iOS

Tato část je určena pro spuštění projektu Cordova na zařízeních s iOS. Pokud nepracujete se zařízeními s iOS, můžete tuto část přeskočit.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalace a spuštění agenta vzdáleného sestavení iOS na Macu nebo cloudové službě

Před spuštěním aplikace Cordova v systému iOS pomocí sady Visual Studio Projděte kroky v [Průvodci nastavením iOS][12] a nainstalujte a spusťte agenta vzdáleného sestavení.

Ujistěte se, že můžete sestavit aplikaci pro iOS. Kroky v příručce pro instalaci se vyžadují pro sestavení aplikace pro iOS ze sady Visual Studio. Pokud nemáte počítač Mac, můžete sestavit pro iOS pomocí vzdáleného agenta sestavení na službě, jako je MacInCloud. Další informace najdete v tématu [spuštění aplikace pro iOS v cloudu][21].

> [!NOTE]
> K použití modulu plug-in Push v iOS se vyžaduje Xcode 7 nebo novější.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Najít ID, které se má použít jako ID aplikace

Před registrací aplikace pro nabízená oznámení otevřete soubor config. XML v aplikaci Cordova, vyhledejte v prvku widget hodnotu atributu `id` a pak ho zkopírujte pro pozdější použití. V následujícím kódu XML je `io.cordova.myapp7777777`ID.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Později tento identifikátor použijte při vytváření ID aplikace na portálu pro vývojáře od společnosti Apple. Pokud na portálu pro vývojáře vytvoříte jiné ID aplikace, musíte provést několik dalších kroků, které jsou dále v tomto kurzu. ID v prvku widget se musí shodovat s ID aplikace na portálu pro vývojáře.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na portálu pro vývojáře od společnosti Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Ověřte, že ID aplikace odpovídá vaší aplikaci Cordova.

Pokud ID aplikace, které jste vytvořili v účtu Apple Developer, už odpovídá ID prvku widgetu v souboru config. XML, můžete tento krok přeskočit. Pokud se však ID neshodují, proveďte následující kroky:

1. Odstraňte složku Platforms z projektu.
2. Odstraňte složku plugins z projektu.
3. Odstraňte složku node_modules z projektu.
4. Aktualizujte atribut ID prvku widget v souboru config. XML tak, aby používal ID aplikace, které jste vytvořili v účtu Apple Developer.
5. Znovu sestavte projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Testování nabízených oznámení v aplikaci pro iOS

1. V aplikaci Visual Studio se ujistěte, že je jako cíl nasazení vybrán systém **iOS** . Pak vyberte **zařízení** a spusťte nabízená oznámení na připojeném zařízení s iOS.

    Nabízená oznámení můžete spustit na zařízení s iOS, které je připojené k počítači přes iTunes. Simulátor iOS nepodporuje nabízená oznámení.

2. Vyberte tlačítko **Spustit** nebo **F5** v aplikaci Visual Studio a sestavte projekt a spusťte aplikaci na zařízení se systémem iOS. Pak vyberte **OK** pro příjem nabízených oznámení.

   > [!NOTE]
   > Aplikace žádá o potvrzení nabízených oznámení během prvního spuštění.

3. V aplikaci zadejte úlohu a potom vyberte ikonu plus **(+)** .
4. Ověřte, že bylo přijato oznámení. Pak kliknutím na **OK** zavřete oznámení.

## <a name="optional-configure-and-run-on-windows"></a>Volitelné Konfigurace a spuštění ve Windows

Tato část popisuje, jak spustit projekt Apache Cordova aplikace na zařízeních s Windows 10 (modul plug-in PhoneGap Push je podporován ve Windows 10). Pokud nepracujete se zařízeními s Windows, můžete tuto část přeskočit.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrace aplikace pro Windows pro nabízená oznámení pomocí WNS

Chcete-li použít možnosti úložiště v aplikaci Visual Studio, vyberte cílový systém Windows ze seznamu platformy řešení, například **Windows-x64** nebo **Windows-x86**. (Nepoužívejte **Windows-anycpu** pro nabízená oznámení.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Podívejte se na video zobrazující podobný postup.][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurace aplikace Cordova pro podporu nabízených oznámení systému Windows

Otevřete návrháře konfigurace tak, že kliknete pravým tlačítkem na **soubor config. XML**. Pak vyberte **Zobrazit návrháře**. Potom vyberte kartu **Windows** a v části **cílová verze Windows**vyberte **Windows 10** .

Aby bylo možné podporovat nabízená oznámení ve vašich výchozích (ladicích) sestaveních, otevřete soubor **Build. JSON** . Pak zkopírujte konfiguraci "Release" do konfigurace ladění.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizaci by měl soubor **Build. JSON** obsahovat následující kód:

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

Sestavte aplikaci a ověřte, že nedošlo k chybám. Klientská aplikace by teď měla zaregistrovat oznámení z Mobile Apps back-endu. Tento oddíl opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testování nabízených oznámení v aplikaci pro Windows

V aplikaci Visual Studio se ujistěte, že je jako cíl nasazení vybraná platforma Windows, jako je například **Windows-x64** nebo **Windows-x86**. Chcete-li spustit aplikaci na počítači s Windows 10, který je hostitelem sady Visual Studio, vyberte možnost **místní počítač**.

1. Vyberte tlačítko **Spustit** a sestavte projekt a spusťte aplikaci.

2. V aplikaci zadejte název nového TodoItem a potom vyberte ikonu plus **(+)** , kterou chcete přidat.

Ověřte, že je při přidání položky přijata oznámení.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [Notification Hubs][17] , kde se dozvíte o nabízených oznámeních.
* Pokud jste to ještě neudělali, pokračujte v kurzu [přidáním ověřování][14] do aplikace Apache Cordova.

Naučte se používat tyto sady SDK:

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
