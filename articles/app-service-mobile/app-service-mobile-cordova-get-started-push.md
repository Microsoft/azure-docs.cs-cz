---
title: Přidání nabízených oznámení do aplikace Apache Cordova s funkcí Mobile Apps služby Azure App Service | Dokumentace Microsoftu
description: Naučte se využívat Mobile Apps k odesílání nabízených oznámení do aplikace Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 40a7552ffd0bfcab173d2e35c52313a94ec3d0bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960324"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Přidání nabízených oznámení do aplikace Apache Cordova

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [rychlý start pro Apache Cordova] [ 5] projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete stažený projekt rychlého spuštění serveru, je nutné balíček rozšíření nabízená oznámení. Další informace najdete v tématu [pracovat s back-end .NET server SDK pro Mobile Apps][1].

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že máte aplikaci Apache Cordova, která byla vyvinutá pomocí sady Visual Studio 2015. Toto zařízení by měl spustit na emulátoru Google Android, zařízení se systémem Android, zařízení s Windows nebo zařízení s Iosem.

Pro absolvování tohoto kurzu potřebujete:

* Počítač s nástrojem [Visual Studio Community 2015] [ 2] nebo novější
* [Visual Studio Tools for Apache Cordova][4]
* [Aktivní účet Azure][3]
* Dokončená [rychlý start pro Apache Cordova] [ 5] projektu
* (Android) A [účet Google] [ 6] s ověřenou e-mailovou adresu
* (iOS) [Členství v programu Apple Developer] [ 7] a zařízení se systémem iOS (nabízená oznámení iOS Simulator nepodporuje)
* (Windows) A [vývojářský účet pro Microsoft Store] [ 8] a zařízení s Windows 10

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Podívejte se na video zobrazující postup v této části][9].

## <a name="update-the-server-project"></a>Aktualizovat projekt serveru

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Upravit svou aplikaci Cordova

Chcete-li zajistit projektu aplikace Apache Cordova je připravená k zpracování nabízených oznámení, nainstalujte modul plug-in Cordova nabízených oznámení a jakékoli služby nabízených oznámení pro konkrétní platformu.

#### <a name="update-the-cordova-version-in-your-project"></a>Aktualizace na verzi Cordova ve vašem projektu.

Pokud váš projekt používá verzi Apache Cordova, která je starší než verze 6.1.1, aktualizujte projekt klienta. Aktualizovat projekt, proveďte následující kroky:

* Chcete-li otevřít návrháře konfigurace, klikněte pravým tlačítkem na `config.xml`.
* Vyberte **platformy** kartu.
* V **Cordova CLI** textového pole, vyberte **6.1.1**. 
* Chcete-li aktualizovat projekt, vyberte **sestavení**a pak vyberte **sestavit řešení**.

#### <a name="install-the-push-plugin"></a>Instalace modulu plug-in nabízených oznámení

Aplikace Apache Cordova nezpracovávají nativně možnosti zařízení nebo v síti.  Tyto možnosti jsou poskytovány buď moduly plug-in, které jsou publikovány na [npm] [ 10] nebo na Githubu. `phonegap-plugin-push` Modulu plug-in zpracovává nabízená oznámení sítě.

Modul plug-in nabízených oznámení můžete nainstalovat v jednom z následujících způsobů:

**Z příkazového řádku:**

Spusťte následující příkaz:

    cordova plugin add phonegap-plugin-push

**Ze sady Visual Studio:**

1. V Průzkumníku řešení otevřete `config.xml` souboru. V dalším kroku vyberte **moduly plug-in** > **vlastní**. Potom vyberte **Git** jako zdroj instalace.

2. Zadejte `https://github.com/phonegap/phonegap-plugin-push` jako zdroj.

    ![V Průzkumníku řešení otevřete soubor config.xml][img1]

3. Vyberte šipku vedle zdroje instalace.

4. V **SENDER_ID**, pokud už máte ID číselné projektu pro projekt vývojářské konzole Google, můžete přidat tady. V opačném případě zadejte hodnotu zástupného symbolu, jako je například 777777. Pokud se zaměřujete na Android, můžete aktualizovat tuto hodnotu v souboru config.xml později.

    >[!NOTE]
    >Od verze 2.0.0 souboru google-services.json musí být nainstalovaný v kořenové složce vašeho projektu nakonfigurovat ID odesílatele. Další informace najdete v tématu [dokumentaci k instalaci.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)

5. Vyberte **Přidat**.

Modul plug-in nabízených oznámení je nyní nainstalována.

#### <a name="install-the-device-plugin"></a>Instalace modulu plug-in zařízení

Postupujte stejným způsobem, který jste použili k instalaci modulu plug-in nabízených oznámení. Přidání modulu plug-in zařízení z seznam základních modulů plug-in. (Chcete-li ji najít, vyberte **moduly plug-in** > **Core**.) Je nutné tento modul plug-in získat název platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Registrace zařízení při spuštění aplikace 

Standardně zahrnujeme minimální kódu pro Android. Později můžete upravit aplikaci spouštět na iOS nebo Windows 10.

1. Přidejte volání do **registerForPushNotifications** během zpětného volání pro proces přihlašování. Alternativně můžete přidat v dolní části **onDeviceReady** metody:

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

    Tento příklad ukazuje volání **registerForPushNotifications** po úspěšném ověření. Můžete volat `registerForPushNotifications()` tak často, jak je povinný.

2. Přidejte nové **registerForPushNotifications** metodu následujícím způsobem:

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
3. (Android) V předchozím kódu nahraďte `Your_Project_ID` s číselné projektu pro aplikaci z ID [konzole pro vývojáře Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Volitelné) Konfigurace a spuštění aplikace v Androidu

Dokončení této části ke zprovoznění nabízených oznámení pro Android.

#### <a name="enable-gcm"></a>Povolení služby Firebase Cloud Messaging

Protože cílíte na platformu Google Android původně, je nutné povolit službu Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurace mobilních back-end aplikací k odesílání nabízených oznámení požadavků pomocí FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Nakonfigurujte svoji aplikaci Cordova pro Android

Otevřít v aplikaci Cordova **config.xml**. Potom nahraďte `Your_Project_ID` s číselné projektu pro aplikaci z ID [konzole pro vývojáře Google][18].

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

Otevřít **index.js**. Aktualizujte kód, který použije vaše ID číselné projektu.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>Konfigurace zařízení s Androidem pro ladění USB

Před nasazením aplikace do zařízení s Androidem, je potřeba povolit, aby ladění USB. Proveďte následující kroky na telefonu s Androidem:

1. Přejděte na **nastavení** > **o telefonu**. Klepněte **číslo sestavení** vývojářský režim je povoleno (přibližně sedminásobně).
2. Zpět v **nastavení** > **možnosti pro vývojáře**, povolte **ladění USB**. K vývoji počítači pomocí kabelu USB připojte telefonu s Androidem.

Otestovali jsme to pomocí zařízení Google Nexus 5 X se systémem Android 6.0 (Marshmallow). Postupy jsou však společné pro všechny moderní Androidu pro vydání.

#### <a name="install-google-play-services"></a>Nainstalujte služby Google Play

Modul plugin push spoléhá na Android služby Google Play pro nabízená oznámení.

1. V sadě Visual Studio, vyberte **nástroje** > **Android** > **správce sady Android SDK**. Pak rozbalte **funkce** složky. Zaškrtněte příslušná políčka a ujistěte se, že každý z následujících sad SDK je nainstalovaná:

   * Android 2.3 nebo novější
   * Úložiště Google verze 27 nebo novější
   * Služby Google Play 9.0.2 nebo novější

2. Vyberte **nainstalujte balíčky**. Potom počkejte na dokončení instalace.

Aktuální požadované knihovny jsou uvedeny v [dokumentaci k instalaci nabízené oznámení phonegap plugin][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Nabízená oznámení v aplikaci v Androidu

Můžete teď nabízená oznámení tak, že aplikaci spustíte a vložení položky do tabulky TodoItem. Ze stejné zařízení nebo z druhé zařízení, můžete otestovat tak dlouho, dokud používáte stejnou back-endu. Otestujte aplikaci Cordova na platformě Android v jednom z následujících způsobů:

* *Na fyzickém zařízení:* připojit zařízení s Androidem na vývojovém počítači pomocí kabelu USB.  Místo **emulátor Google Android**vyberte **zařízení**. Visual Studio nasadí aplikaci do zařízení a spuštění aplikace. Pak můžete pracovat s aplikací na zařízení.

  Sdílení obrazovky aplikací, jako [Mobizen] [ 20] vám může pomoci při vývoji aplikací pro Android. Mobizen projekty Android obrazovky do webového prohlížeče ve vašem počítači.

* *V emulátoru Androidu:* existují další kroky konfigurace, které jsou požadovány, pokud používáte emulátor.

    Ujistěte se, že nasazujete do virtuálního zařízení, která má rozhraní Google API nastavit jako cíl, jak je znázorněno v nástroji Android Virtual Device (AVD) manager.

    ![Správce virtuálních zařízení s androidem](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Pokud chcete použít rychlejší x86 emulátor, [nainstalovat modul HAXM ovladač][11]a potom nakonfigurujte emulátor používat.

    Přidat účet Google do zařízení s Androidem tak, že vyberete **aplikace** > **nastavení** > **přidat účet**. Postupujte podle pokynů.

    ![Přidat účet Google do zařízení s Androidem](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Spuštění aplikace seznamu úkolů stejně jako dříve a vložit novou položku seznamu úkolů. Tentokrát ikonu oznámení se zobrazí v oznamovací oblasti. Je-li otevřít panel oznámení k zobrazení textu v plném znění oznámení.

    ![Zobrazení oznámení](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Volitelné) Nakonfigurujte a spusťte v Iosu

Tato část se týká spuštění projektu Cordova na zařízeních s Iosem. Pokud nepracujete se zařízeními s Iosem, můžete tuto část přeskočit.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalace a spuštění agenta vzdáleného buildu iOS na Mac nebo cloudovou službu

Před spuštěním aplikace Cordova v iOS pomocí sady Visual Studio, projděte si postup v [iOS Instalační příručka] [ 12] k instalaci a spuštění agenta vzdáleného buildu.

Ujistěte se, že můžete vytvářet aplikace pro iOS. Kroky v Průvodci instalací se vyžadují pro vytváření aplikací pro iOS ze sady Visual Studio. Pokud nemáte počítač Mac, můžete vytvořit pro iOS pomocí vzdáleného sestavovacího agenta na službě, jako je MacInCloud. Další informace najdete v tématu [spouštět aplikace pro iOS v cloudu][21].

> [!NOTE]
> Xcode 7 nebo novější, je nutné použít modul plug-in nabízených oznámení v iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Najít ID se má použít jako ID aplikace

Předtím, než zaregistrujete svoji aplikaci pro nabízená oznámení, otevřete soubor config.xml v aplikaci Cordova zjistit `id` hodnotu v elementu widgetu atributu a potom ho zkopírujte pro pozdější použití. V následujícím souboru XML je ID `io.cordova.myapp7777777`.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

Později pomocí tohoto identifikátoru při vytvoření ID aplikace na portálu pro vývojáře Apple. Pokud jste vytvořili jiným ID aplikace na portálu pro vývojáře, je nutné provést několik kroků navíc později v tomto kurzu. Identifikátor v elementu widgetu musí odpovídat ID aplikace na portálu pro vývojáře.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrace aplikace pro nabízená oznámení na portálu pro vývojáře Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Ověřte, že ID aplikace odpovídá aplikaci Cordova

Pokud ID aplikace, kterou jste vytvořili v účtu Apple Developer už odpovídá ID widgetu prvku v souboru config.xml, můžete tento krok přeskočit. Pokud ID neshodují, proveďte následující kroky:

1. Odstraňte složku platforms z projektu.
2. Odstraňte složku moduly plug-in z projektu.
3. Odstraňte složku node_modules z projektu.
4. Aktualizujte atribut id widgetu prvku v souboru config.xml použít ID aplikace, kterou jste vytvořili v účtu Apple developer.
5. Znovu sestavte projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Nabízená oznámení v aplikaci pro iOS

1. V sadě Visual Studio, ujistěte se, že **iOS** je vybrána jako cíl nasazení. Potom vyberte **zařízení** ke spuštění nabízená oznámení na zařízení s Iosem připojené.

    Nabízená oznámení můžete spustit na zařízení s Iosem, která je připojená k počítači pomocí iTunes. Simulátor Iosu nepodporuje nabízených oznámení.

2. Vyberte **spustit** tlačítko nebo **F5** v sadě Visual Studio se projekt sestavil a spustil aplikaci v zařízení s Iosem. Potom vyberte **OK** přijímat nabízená oznámení.

   > [!NOTE]
   > Aplikace požádá o potvrzení pro nabízená oznámení při prvním spuštění.

3. V aplikaci, zadejte úlohu a pak vyberte plus **(+)** ikonu.
4. Ověřte, že bylo přijato oznámení. Potom vyberte **OK** chcete oznámení zavřít.

## <a name="optional-configure-and-run-on-windows"></a>(Volitelné) Konfigurovat a spouštět na Windows

Tato část popisuje, jak spouštět projekt aplikace Apache Cordova na zařízení s Windows 10 (modul plugin push PhoneGap je podporovaná ve Windows 10). Pokud nepracujete se zařízeními s Windows, můžete tuto část přeskočit.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Zaregistrovat aplikaci Windows pro nabízená oznámení pomocí WNS

Použití možností Store v sadě Visual Studio, vyberte cíl Windows ze seznamu platformy řešení, jako je například **Windows x64** nebo **Windows x86**. (Vyhněte **Windows AnyCPU** pro nabízená oznámení.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Podívejte se na video zobrazující podobný postup.][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro služby nabízených oznámení Windows

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurace aplikace Cordova pro podporu nabízených oznámení Windows

Otevření Návrháře konfigurace kliknutím pravým tlačítkem myši **config.xml**. Potom vyberte **Návrhář zobrazení**. V dalším kroku vyberte **Windows** kartu a potom vyberte **Windows 10** pod **cílovou verzi Windows**.

Pro podporu nabízených oznámení v sestavení výchozí (ladění), otevřete **vývojový tým** souboru. Zkopírujte do konfigurace ladění konfigurace "verze".

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

Po aktualizaci **vývojový tým** soubor by měl obsahovat následující kód:

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

Sestavení aplikace a ověřte, že máte žádné chyby. Klientská aplikace by měla nyní registrace k oznámením z back-endu Mobile Apps. Tato část opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Nabízená oznámení v aplikaci pro Windows

V sadě Visual Studio, ujistěte se, že je na platformě Windows zvolen jako cíl nasazení, jako **Windows x64** nebo **Windows x86**. Chcete-li aplikaci spustit na počítači s Windows 10, který je hostitelem aplikace Visual Studio, zvolte **místního počítače**.

1. Vyberte **spustit** tlačítka se projekt sestavil a aplikace se spustila.

2. V aplikaci, zadejte název nového úkolu a pak vyberte plus **(+)** ikonu a přidejte ji.

Ověřte, že je přijato oznámení, když je položka přidána.

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [Notification Hubs] [ 17] Další informace o nabízených oznámeních.
* Pokud jste tak již neučinili, pokračujte podle tohoto kurzu [přidání ověřování] [ 14] do aplikace Apache Cordova.

Další informace o použití následujících sad SDK:

* [Sada SDK Apache Cordova][15]
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
