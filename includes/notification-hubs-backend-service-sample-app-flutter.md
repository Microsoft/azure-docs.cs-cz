---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147790"
---
### <a name="create-the-flutter-solution"></a>Vytvoření řešení flutter

1. Otevřete novou instanci **Visual Studio Code**.

1. Otevřete **paletu příkazů** (**SHIFT**  +  **Command**  +  **P**).

1. Vyberte příkaz **flutter: nový projekt** a potom stiskněte klávesu **ENTER**.

1. Jako **název projektu** zadejte *push_demo* a pak vyberte **umístění projektu**.

1. Po zobrazení výzvy vyberte možnost **získat balíčky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **Kotlin** (v části **App**  >  **Src**  >  **Main**) a pak zvolte **Zobrazit ve Finderu**. Potom přejmenujte podřízené složky (ve složce **Kotlin** ) na ```com``` , ```<your_organization>``` a ```pushdemo``` v uvedeném pořadí.

    > [!NOTE]
    > Při použití šablony **Visual Studio Code** jsou tyto složky standardně nastavené na **com**, **například** **<PROJECT_NAME>**. Za předpokladu, že se pro **organizaci**používá **mobcat** , struktura složky by se měla zobrazit jako:
    >
    > - kotlin
    >     - Doplňky
    >         - mobcat
    >             - pushdemo

1. Zpátky v **Visual Studio Code**aktualizujte hodnotu **ApplicationId** v **android**  >  **app**  >  **buildu** aplikace pro Android. Gradle na `com.<your_organization>.pushdemo` .

    > [!NOTE]
    > Pro *<your_organization>* zástupný symbol byste měli použít vlastní název organizace. Například použití **mobcat** jako organizace bude mít za následek hodnotu **názvu balíčku** *com. mobcat. pushdemo*.

1. Aktualizujte atribut **Package** v souborech **AndroidManifest.xml** v části **Src**  >  **Debug**, **Src**  >  **Main**a **Src**  >  **Profile** . Zajistěte, aby hodnoty odpovídaly hodnotě **ApplicationId** , kterou jste použili v předchozím kroku.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Aktualizujte ```android:label``` atribut v souboru **AndroidManifest.xml** pod položkou **Src**  >  **Main** to *PushDemo*. Pak přidejte ```android:allowBackup``` atribut přímo pod ```android:label``` a nastavte jeho hodnotu na **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Otevřete soubor **Build. Gradle** na úrovni**aplikace (**  >  **app**  >  **Build App Build. Gradle**) a pak aktualizujte *compileSdkVersion* (z oddílu **Android** ) tak, aby používal rozhraní API **29**. Pak aktualizujte hodnoty *hodnotu minsdkversion* a *targetSdkVersion* (z oddílu **defaultConfig** ) na **26** a **29** .

    > [!NOTE]
    > Pro účely tohoto kurzu se podporují jenom ta zařízení s **rozhraním API, která používají verzi 26 a vyšší** . můžete je ale zvětšit tak, aby podporovala zařízení, na kterých běží starší verze.

1. **Ovládací prvek**  +  **Klikněte** na složku **iOS** a pak zvolte **otevřít v Xcode**.

1. V **Xcode**klikněte na **Runner** ( **XCODEPROJ** v horní části, ne na složku). Pak vyberte cíl **spouštěče** a vyberte kartu **Obecné** . Když je vybraná možnost **všechny** konfigurace sestavení, aktualizujte **identifikátor sady prostředků** na `com.<your_organization>.PushDemo` .

    > [!NOTE]
    > Pro *<your_organization>* zástupný symbol byste měli použít vlastní název organizace. Například použití **mobcat** jako organizace bude mít za následek hodnotu **identifikátoru sady prostředků** *com. mobcat. PushDemo*.

1. Klikněte na **info. plist** a pak aktualizujte hodnotu **název sady prostředků** na **PushDemo** .

1. Zavřete **Xcode** a vraťte se k **Visual Studio Code**.

1. Zpět v **Visual Studio Code**otevřete **pubspec. yaml**, přidejte balíčky [http](https://pub.dev/packages/http) a [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **DART** jako závislosti. Pak soubor uložte a po zobrazení výzvy klikněte na **získat balíčky** .

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. V **terminálu**změňte adresář na složku **iOS** (pro projekt flutter). Potom spuštěním příkazu [**pod instalací**](https://guides.cocoapods.org/using/getting-started.html#installation) nainstalujte nové lusky (vyžadované balíčkem [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) ).

1. **Ovládací prvek**  +  **Klikněte** na složku **lib** a v nabídce vyberte příkaz **nový soubor** s názvem *main_page. DART* . Pak přidejte následující kód.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. V **Main. DART**nahraďte kód s kódem následujícím.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. V **terminálu**Sestavte a spusťte aplikaci na každé cílové platformě, abyste mohli otestovat aplikaci, která je spuštěná na vašich zařízeních. Zajistěte, aby byla podporovaná zařízení připojená.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementace komponent pro různé platformy

1. **Ovládací prvek**  +  **Klikněte** na složku **lib** a v nabídce vyberte možnost **Nová složka** *s* **názvem složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **modely** a v nabídce vyberte příkaz **nový soubor** s názvem *device_installation. DART* . Pak přidejte následující kód.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Přidejte nový soubor do složky **modely** s názvem *push_demo_action. DART* definující výčet akcí, které jsou v tomto příkladu podporovány.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Přidejte do projektu novou složku s názvem **služby** a přidejte do ní nový soubor s názvem *device_installation_service. DART* s následující implementací.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Pro *<your_organization>* zástupný symbol byste měli použít vlastní název organizace. Například použití **mobcat** jako organizace bude mít za následek [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) názvu *com. mobcat. pushdemo/deviceinstallation*.
    >
    > Tato třída zapouzdřuje práci se základní nativní platformou, aby získala informace o požadavcích na instalaci zařízení. [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) usnadňuje obousměrnou asynchronní komunikaci se základními nativními platformami. Protějšek konkrétní platformy pro tento kanál bude vytvořen v pozdějších krocích.

1. Přidejte do této složky další soubor s názvem *notification_action_service. DART* s následující implementací.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Slouží jako jednoduchý mechanismus pro centralizaci manipulace s akcemi oznámení, aby je bylo možné zpracovat ve formě pro různé platformy pomocí výčtu silného typu. Služba umožňuje základní nativní platformě aktivovat akci, když je zadána v datové části oznámení. Také umožňuje společnému kódu vrátit se zpět, zda byla akce zadána během spuštění aplikace, jakmile je flutter připraven na jeho zpracování. Například při spuštění aplikace klepnutím na oznámení z centra oznámení.

1. Přidejte nový soubor do složky **Services** s názvem *notification_registration_service. DART* s následující implementací.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Tato třída zapouzdřuje použití **DeviceInstallationService** a požadavků do back-endové služby k provedení požadavků na registraci, zrušení registrace a aktualizaci registračních akcí. Argument **apiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .

1. Přidejte nový soubor do složky **lib** s názvem *config. DART* s následující implementací.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Slouží jako jednoduchý způsob, jak definovat tajné klíče aplikace. Zástupné hodnoty nahraďte vlastními. Měli byste si je poznamenat, když jste vytvořili back-end službu. **Adresa URL aplikace API** by měla být `https://<api_app_name>.azurewebsites.net/` . Člen **apiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .
    >
    > Nezapomeňte ho přidat do souboru gitignore, abyste se vyhnuli potvrzování těchto tajných kódů do správy zdrojového kódu.

### <a name="implement-the-cross-platform-ui"></a>Implementace uživatelského rozhraní pro různé platformy

1. V **main_page. DART**, nahraďte funkci **Build** následujícím.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Do horní části souboru **main_page. DART** přidejte požadované importy.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Přidejte pole do třídy **_MainPageState** , aby se uložil odkaz na **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Na **_MainPageState** třídu Implementujte obslužné rutiny **události pro** tlačítka **Registrovat** a zrušit **registraci** . Zavolejte odpovídající metody **registrace registru** / **Deregister** a potom zobrazte výstrahu, která indikuje výsledek.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Nyní v **Main. DART**ověřte, že jsou v horní části souboru k dispozici následující importy.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Deklarujte proměnnou pro uložení odkazu na instanci **NotificationActionService** a její inicializaci.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Přidejte funkce pro zpracování zobrazení výstrahy, když se aktivuje akce.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Aktualizujte **Hlavní** funkci tak, aby sledovala **NotificationActionService** Stream **actionTriggered** a kontrolovala všechny akce zaznamenané během spuštění aplikace.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > To je jednoduché k předvedení příjmu a šíření akcí nabízených oznámení. Obvykle by se tyto chyby zpracovaly v tichém režimu, například přejít k určitému zobrazení nebo aktualizovat některá data namísto zobrazení výstrahy v tomto případě.
