---
title: Přidání nabízených oznámení do aplikace Apache Cordova pomocí funkce Mobile Apps služby Azure App Service | Microsoft Docs
description: Naučte se používat k odesílání nabízených oznámení do vaší aplikace Apache Cordova Mobile Apps.
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
ms.openlocfilehash: 13c1a53cfa3f998c9e3fa3ee1ee2dcec37357095
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597990"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Přidání nabízených oznámení do vaší aplikace Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled
V tomto kurzu přidáte nabízená oznámení [rychlý start Apache Cordova] [ 5] projektu tak, aby nabízených oznámení se odešle do zařízení pokaždé, když vložení záznamu.

Pokud použijete stažený projekt rychlého spuštění serveru, je třeba balíček rozšíření nabízená oznámení. Další informace najdete v tématu [pracovat s .NET back-end server SDK pro Mobile Apps][1].

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že máte aplikaci Apache Cordova, která byla vyvinuta pomocí sady Visual Studio 2015. Toto zařízení by neměl být spuštěný emulátor Google Android, zařízení se systémem Android, zařízení se systémem Windows nebo zařízení s iOS.

Pro absolvování tohoto kurzu potřebujete:

* Počítač s nástrojem [Visual Studio Community 2015] [ 2] nebo novější
* [Nástroje sady Visual Studio pro Apache Cordova][4]
* [Aktivní účet Azure][3]
* Dokončené [rychlý start Apache Cordova] [ 5] projektu
* (Android) A [účet Google] [ 6] s ověřenou e-mailová adresa
* (iOS) [Programu pro vývojáře Apple členství] [ 7] a zařízení se systémem iOS (nabízená oznámení nepodporuje simulátoru iOS)
* (Windows) A [Microsoft úložiště vývojářský účet] [ 8] a zařízením s Windows 10

## <a name="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Přehrát video, zobrazující kroky v této části][9].

## <a name="update-the-server-project"></a>Aktualizace server project
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Upravit aplikaci Cordova
Zajistit, že je připraven ke zpracování nabízených oznámení projektu aplikace Apache Cordova, nainstalujte nabízené cordovu plus žádné specifické pro platformu nabízených služeb.

#### <a name="update-the-cordova-version-in-your-project"></a>Aktualizujte verzi Cordova ve vašem projektu.
Pokud váš projekt používá verzi Apache Cordova, která je starší než verze 6.1.1, aktualizujte projektu klienta. Pokud chcete aktualizovat projektu, proveďte následující kroky: 

* Chcete-li otevřít návrháři konfigurace, klikněte pravým tlačítkem na `config.xml`.
* Vyberte **platformy** kartě.
* V **Cordova CLI** textového pole, vyberte **6.1.1**. 
* Chcete-li aktualizovat projekt, vyberte **sestavení**a potom vyberte **sestavit řešení**.

#### <a name="install-the-push-plugin"></a>Instalace modulu plug-in push
Aplikace Apache Cordova nezpracuje nativně možnosti sítě nebo zařízení.  Tyto možnosti jsou poskytovány buď modulů plug-in, které jsou publikovány na [npm] [ 10] nebo na Githubu. `phonegap-plugin-push` Modul plug-in zpracovává sítě nabízená oznámení.

Modul plug-in nabízené můžete nainstalovat v jednom z následujících způsobů:

**Z příkazového řádku:**

Spusťte následující příkaz:

    cordova plugin add phonegap-plugin-push

**Z v sadě Visual Studio:**

1. V Průzkumníku řešení otevřete `config.xml` souboru. Potom vyberte **modulů plug-in** > **vlastní**. Potom vyberte **Git** jako zdroj instalace. 
    
2. Zadejte `https://github.com/phonegap/phonegap-plugin-push` jako zdroj.

    ![V Průzkumníku řešení otevřete soubor config.xml][img1]

3. Vyberte šipku vedle zdroje instalace.

4. V **SENDER_ID**, pokud již máte ID číselné projektu pro projekt vývojářské konzole Google, můžete přidat sem. Jinak zadejte hodnotu zástupného symbolu, jako je například 777777. Pokud cílíte na Android, můžete je aktualizovat tuto hodnotu v souboru config.xml později.

    >[!NOTE]
    >Od verze 2.0.0 google services.json musí být nainstalovaný v kořenové složce projektu pro konfiguraci ID odesílatele. Další informace najdete v tématu [dokumentaci k instalaci.](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)
5. Vyberte **Přidat**.

Modul plug-in nabízené je nyní nainstalován.

#### <a name="install-the-device-plugin"></a>Instalace modulu plug-in zařízení
Postupujte podle stejného postupu, který jste použili k instalaci modulu plug-in push. Přidání modulu plug-in zařízení ze seznamu modulů plug-in jádra. (Chcete-li ji najít, vyberte **modulů plug-in** > **základní**.) Je nutné tento modul plug-in získat název platformy.

#### <a name="register-your-device-when-the-application-starts"></a>Zaregistrovat zařízení při spuštění aplikace 
Na začátku jsme obsahovat určitý minimální kód pro Android. Později můžete upravit aplikaci spustit v iOS nebo Windows 10.

1. Přidejte volání **registerForPushNotifications** během zpětného volání pro proces přihlášení. Alternativně můžete přidat v dolní části **onDeviceReady** metoda:

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

    Tento příklad ukazuje volání **registerForPushNotifications** po úspěšném provedení ověřování. Můžete volat `registerForPushNotifications()` tak často, jako je povinný.

2. Přidejte nové **registerForPushNotifications** metoda následujícím způsobem:

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
3. (Android) V předchozím kódu, nahraďte `Your_Project_ID` s numerická projektu ID pro vaši aplikaci z [vývojářské konzole Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Volitelné) Konfigurace a spuštění aplikace v systému Android
Dokončení této části ke zprovoznění nabízených oznámení pro Android.

#### <a name="enable-gcm"></a>Povolit Firebase cloudu zasílání zpráv
Vzhledem k tomu, že původně cílíte na platformu Google Android, musíte povolit zasílání zpráv cloudu Firebase.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Konfigurace mobilní aplikace back-endu odesílat žádosti o nabízenou pomocí FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Konfigurace aplikace Cordova pro Android
V aplikaci Cordova otevřete config.xml. Potom můžete nahradit `Your_Project_ID` s numerická projektu ID pro vaši aplikaci z [vývojářské konzole Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Otevřete index.js. Aktualizujte kód, který použije vaše ID číselné projektu.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Konfigurace zařízení s Androidem pro ladění USB
Než bude možné nasadit aplikace do zařízení se systémem Android, budete muset povolit ladění USB. Pomocí následujících kroků na váš telefon se systémem Android:

1. Přejděte na **nastavení** > **o telefonu**. Klepněte **číslo sestavení** dokud režim vývojáře je povolen (o sedm časy).
2. Zpět v **nastavení** > **možnosti pro vývojáře**, povolit **ladění USB**. Váš telefon se systémem Android se potom připojují k vaší vývoj počítači pomocí kabelu USB.

Jsme testovali to pomocí Google Nexus 5 X zařízení se systémem Android 6.0 (Marshmallow). Technik jsou však společné pro všechny moderní Android verze.

#### <a name="install-google-play-services"></a>Nainstalujte služby Google Play
Modul plug-in nabízené spoléhá na Android služby Google Play pro nabízená oznámení.

1. V sadě Visual Studio, vyberte **nástroje** > **Android** > **Android SDK Manager**. Potom rozbalte **funkce** složky. Zaškrtněte příslušná políčka zkontrolujte, zda je nainstalován každý z následujících sad SDK:

   * Android 2.3 nebo vyšší
   * Revize Google úložiště 27 nebo vyšší
   * Služby Google Play 9.0.2 nebo vyšší

2. Vyberte **instalovat balíčky**. Potom počkejte na dokončení instalace.

Aktuální požadované knihovny jsou uvedeny v [phonegap-plugin nabízené instalace dokumentace][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Nabízená oznámení v aplikaci pro Android
Spuštěním aplikace a vložení položek v tabulce TodoItem můžete nyní nabízená oznámení. Ze stejného zařízení nebo z druhé zařízení, můžete otestovat tak dlouho, dokud používají stejný back-end. Testování aplikace Cordova na platformě Android v jednom z následujících způsobů:

* *Na fyzické zařízení:* přiřadit vývojovém počítači pomocí kabelu USB zařízení se systémem Android.  Místo **emulátor Google Android**, vyberte **zařízení**. Visual Studio nasadí aplikaci do zařízení a aplikaci spustí. Potom můžete pracovat s aplikací na zařízení.

  Obrazovka sdílení aplikací, jako [Mobizen] [ 20] vám může pomoci při vývoji aplikací pro Android. Mobizen projektů Android obrazovky na webový prohlížeč ve vašem počítači.

* *V emulátoru Androidu:* existují další konfigurační kroky, které jsou požadovány, pokud používáte emulátor.

    Ujistěte se, že nasazujete virtuální zařízení, která má rozhraní Google API nastavenou jako cíl, jak je vidět ve Správci virtuální zařízení Android (AVD).

    ![Android správce virtuálního zařízení](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Pokud chcete použít rychlejší x86 emulátoru, [nainstalujte ovladač HAXM][11]a pak nakonfigurujte emulátoru ji použít.

    Přidat účet Google do zařízení s Androidem výběrem **aplikace** > **nastavení** > **přidejte účet**. Potom postupujte podle pokynů.

    ![Přidat účet Google do zařízení s Androidem](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Spusťte aplikaci seznamu úkolů jako před a vložit novou položku úkolů. Tentokrát ikonu oznámení se zobrazí v oznamovací oblasti. Můžete otevřít panel oznámení k zobrazení textu v plném znění oznámení.

    ![zobrazení oznámení](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Volitelné) Nakonfigurujte a spusťte v systému iOS
Tato část se týká spuštění projektu Cordova na zařízení s iOS. Pokud nepracujete s zařízení s iOS, můžete tuto část přeskočit.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Nainstalujte a spusťte agenta vzdáleného sestavení iOS Mac nebo cloudové služby
Před spuštěním aplikace Cordova v iOS pomocí sady Visual Studio projít kroky v [iOS nastavení průvodce] [ 12] k instalaci a spuštění agenta vzdáleného sestavení.

Ujistěte se, že můžete vytvořit aplikaci pro iOS. Kroky v Průvodci instalací se vyžadují pro vytvoření aplikace pro iOS ze sady Visual Studio. Pokud nemáte algoritmu Mac, můžete vytvořit pro iOS pomocí agenta vzdáleného sestavení na službě, jako je MacInCloud. Další informace najdete v tématu [spuštění aplikace pro iOS v cloudu][21].

> [!NOTE]
> XCode 7 nebo novější, je potřeba použít modul plug-in nabízené v systému iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Najít ID, aby vám poskytla vaše ID aplikace
Najít před registrace aplikace pro nabízená oznámení, otevřete config.xml v aplikaci Cordova `id` atribut hodnota v elementu pomůcky a poté zkopírujte pro pozdější použití. V následující soubor XML, je ID `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Později použijte tento identifikátor při vytvoření ID aplikace na portálu pro vývojáře Apple. Pokud vytvoříte jiné ID aplikace na portálu pro vývojáře, vyžaduje několik kroků navíc později v tomto kurzu. ID v elementu pomůcky se musí shodovat s ID aplikace na portálu pro vývojáře.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registraci aplikace pro nabízená oznámení na portál pro vývojáře společnosti Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Podívejte se na video zobrazující podobný postup.](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure k odesílání nabízených oznámení
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Zkontrolujte, zda vaše ID aplikace odpovídá vaší aplikace Cordova
Pokud ID aplikace, kterou jste vytvořili v Apple vývojářský účet již odpovídá ID elementu pomůcka v souboru config.xml, můžete tento krok přeskočit. Pokud ID neshodují, proveďte následující kroky:

1. Odstraňte složku platformy ze svého projektu.
2. Odstraňte složku moduly plug-in z projektu.
3. Odstraňte složku node_modules ze svého projektu.
4. Aktualizujte atributu id elementu pomůcka v souboru config.xml můžete použít ID aplikace, kterou jste vytvořili ve vašem účtu vývojáře Apple.
5. Znovu sestavte projekt.

##### <a name="test-push-notifications-in-your-ios-app"></a>Nabízená oznámení v aplikaci s iOS
1. V sadě Visual Studio, ujistěte se, že **iOS** je vybrán jako cíl nasazení. Potom vyberte **zařízení** ke spuštění nabízená oznámení na vaše připojené zařízení s iOS.

    Nabízená oznámení můžete spustit na zařízení s iOS, která je připojena k vašemu počítači s iTunes. Simulátoru iOS nabízená oznámení nepodporuje.

2. Vyberte **spustit** tlačítko nebo **F5** v sadě Visual Studio pro sestavení projektu a spusťte aplikaci v zařízení se systémem iOS. Potom vyberte **OK** přijímat nabízená oznámení.

   > [!NOTE]
   > Aplikace požádá o potvrzení pro nabízená oznámení při prvním spuštění.

3. V aplikaci zadejte úlohu a potom vyberte plus **(+)** ikonu.
4. Ověřte, že bylo přijato oznámení. Potom vyberte **OK** k zavření oznámení.

## <a name="optional-configure-and-run-on-windows"></a>(Volitelné) Nakonfigurujte a spusťte v systému Windows
Tato část popisuje, jak spustit projekt aplikace Apache Cordova na zařízení s Windows 10 (modul plug-in nabízené PhoneGap podporuje se ve Windows 10). Pokud nepracujete s zařízení se systémem Windows, můžete tuto část přeskočit.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrace aplikace systému Windows pro nabízená oznámení s WNS
Pokud chcete používat možnosti úložiště v sadě Visual Studio, vyberte cíl Windows ze seznamu řešení platformy, jako **Windows x64** nebo **Windows x86**. (Vyhnout **Windows AnyCPU** pro nabízená oznámení.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Podívejte se na video s podobným způsobem][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurace aplikace Cordova pro podporu nabízených oznámení Windows
Otevřete návrháře konfigurace tak, že kliknete pravým tlačítkem na **config.xml**. Potom vyberte **Návrhář zobrazení**. Potom vyberte **Windows** a pak vyberte **Windows 10** pod **cílová verze Windows**.

Pro podporu nabízených oznámení ve vašich sestaveních výchozí (ladění), otevřete soubor build.json. Konfigurace ladění zkopírujte konfigurace "verze".

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Po aktualizaci build.json soubor by měl obsahovat následující kód:

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

Sestavte aplikaci a ověřte, že máte žádné chyby. Klientská aplikace by teď zaregistrovat pro oznámení z back-end mobilní aplikace. Tato část opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Nabízená oznámení v aplikaci Windows
V sadě Visual Studio, musí být vybrána, platforma Windows cíl nasazení, jako například **Windows x64** nebo **Windows x86**. Spusťte aplikaci v počítači s Windows 10, který je hostitelem Visual Studio, zvolte **místního počítače**.

1. Vyberte **spustit** tlačítko se projekt sestavil a spustil aplikaci.

2. V aplikaci, zadejte název nové todoitem a pak vyberte plus **(+)** ikonu ho přidejte.

Ověřte, že se při přidání položky přijato oznámení.

## <a name="next-steps"></a>Další kroky
* Přečtěte si informace o [Notification Hubs] [ 17] Další informace o nabízených oznámení.
* Pokud jste tak již neučinili, pokračovat v kurzu podle [přidání ověřování] [ 14] do vaší aplikace Apache Cordova.

Další informace o použití sady SDK následující:

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
