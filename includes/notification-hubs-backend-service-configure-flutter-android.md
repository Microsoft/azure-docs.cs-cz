---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147789"
---
### <a name="add-the-google-services-json-file"></a>Přidat soubor JSON služeb Google

1. **Ovládací prvek**  +  **Klikněte** na složku **Android** a pak zvolte **otevřít v Android Studio**. Pak přepněte do zobrazení **projektu** (Pokud ještě není).

1. Vyhledejte *google-services.jsv* souboru, který jste stáhli dříve při nastavení projektu **PushDemo** v [konzole Firebase](https://console.firebase.google.com). Pak ho přetáhněte do kořenového adresáře modulu **aplikace** (aplikace**Android**  >  **Android**  >  **app**).

### <a name="configure-build-settings-and-permissions"></a>Konfigurovat nastavení a oprávnění sestavení

1. Přepněte zobrazení **projektu** na **Android**.

1. Otevřete **AndroidManifest.xml**a pak přidejte oprávnění **Internet** a **READ_PHONE_STATE** za prvek **aplikace** před uzavírací **</manifest>** značku.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Přidat sady SDK Firebase

1. V **Android Studio**otevřete soubor **Build. Gradle** na úrovni projektu (**Gradle Scripts**  >  **Build. Gradle (Project: Android)**). a ujistěte se, že máte v ``buildscript``  >  uzlu **závislosti** cestu k cestě typu com. Google. gms: Google-Services.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Ujistěte se, že při vytváření **projektu pro Android**odkazujete na nejnovější verzi podle pokynů uvedených v [konzole Firebase](https://console.firebase.google.com) .

1. V souboru **Build. Gradle** na úrovni aplikace (**Gradle Scripts**  >  **Build. Gradle (Module: App)**) použijte **modul plug-in Google Services Gradle**. Použijte modul plug-in hned nad uzlem **Android** .

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. Ve stejném souboru přidejte do uzlu **závislosti** závislost knihovny **Cloud Messaging** pro Android.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Ujistěte se, že jste v [dokumentaci ke cloudu pro zasílání zpráv pro Android](https://firebase.google.com/docs/cloud-messaging/android/client)odkazovali na nejnovější verzi.

1. Uložte změny a pak klikněte na tlačítko **synchronizovat nyní** (z příkazového řádku na panelu nástrojů) nebo **synchronizujte projekt se soubory Gradle**.

### <a name="handle-push-notifications-for-android"></a>Zpracování nabízených oznámení pro Android

1. V **Android Studio** **řízení**  +  **klikněte** na složku **com. <your_organization> složky balíčku. pushdemo** (**App**  >  **Src**  >  **Main**  >  **Kotlin**), v nabídce **Nový** vyberte možnost **balíček** . Jako název zadejte **služby** a stiskněte tlačítko **vrátit**.

1. **Ovládací prvek**  +  **Klikněte** na složku **služby** , v nabídce **Nový** vyberte **soubor/třída Kotlin** . Jako název zadejte **DeviceInstallationService** a stiskněte **ENTER.**

1. Implementujte **DeviceInstallationService** pomocí následujícího kódu.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/deviceinstallation` . To bylo definováno v části flutter aplikace v rámci **DeviceInstallationService. DART**. V tomto případě jsou volání provedena ze společného kódu do nativního hostitele. Nezapomeňte nahradit **<your_organization>** s vlastní organizací bez ohledu na to, kde se používá.
    >
    > Tato třída poskytuje jedinečné ID (pomocí [Secure. AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) jako součást datové části registrace centra oznámení.

1. Přidejte další **soubor nebo třídu Kotlin** do složky **Services** s názvem *NotificationRegistrationService*a poté přidejte následující kód.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/notificationregistration` . To bylo definováno v části flutter aplikace v rámci **NotificationRegistrationService. DART**. V tomto případě jsou volání uskutečněna z nativního hostitele do společného kódu. Bez ohledu na to, co se používá, je potřeba, abyste nahradili **<your_organization>** s vlastní organizací.

1. Přidejte další **soubor nebo třídu Kotlin** do složky **Services** s názvem *NotificationActionService*a poté přidejte následující kód.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/notificationaction` . To bylo definováno v části flutter aplikace v rámci **NotificationActionService. DART**. V tomto případě lze v obou směrech provést volání. Nezapomeňte nahradit **<your_organization>** s vlastní organizací bez ohledu na to, kde se používá.

1. Přidejte nový **soubor nebo třídu Kotlin** do balíčku **com. <your_organization>. Pushdemo** s názvem *PushNotificationsFirebaseMessagingService*a potom implementujte pomocí následujícího kódu.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída zodpovídá za zpracování oznámení v případě, že aplikace běží v popředí. Pokud je akce obsažena v datové části oznámení, která je přijata v **onMessageReceived**, bude v podmíněném volání **triggerAction** na **NotificationActionService** . Tím se také zavolá **refreshRegistration** na **NotificationRegistrationService** , když se token **Firebase** znovu vygeneruje přepsáním funkce **onNewToken** .
    >
    > Znovu se postará o nahrazení **<your_organization>** s vlastní organizací bez ohledu na to, kde se používá.

1. V **AndroidManifest.xml** (**App**  >  **Src**  >  **Main**) přidejte **PushNotificationsFirebaseMessagingService** do dolní části prvku **aplikace** pomocí `com.google.firebase.MESSAGING_EVENT` filtru záměrů.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. V **DeviceInstallationService**se ujistěte, že jsou v horní části souboru k dispozici následující importy.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Nahraďte **<your_organization>** hodnotou vaší organizace.

1. Aktualizujte zástupný text *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* , aby se získala hodnota tokenu z **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. V **MainActivity**se ujistěte, že jsou v horní části souboru k dispozici následující importy.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Nahraďte **<your_organization>** hodnotou vaší organizace.

1. Přidejte proměnnou, do které se uloží odkaz na **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Přidejte funkci nazvanou **processNotificationActions** a ověřte, zda má **záměr** hodnotu navíc s názvem **Action**. Podmíněně aktivuje tuto akci nebo ji uloží pro pozdější použití, pokud se akce zpracovává během spuštění aplikace.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Přepište funkci **onNewIntent** pro volání **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Vzhledem k tomu, že **LaunchMode** pro **MainActivity** je nastaven na hodnotu **jedna nahoře**, se **záměr** do existující instance **aktivity** pošle prostřednictvím funkce **onNewIntent** místo funkce **Create** , takže je potřeba zpracovat příchozí **záměr** v rutinách **Create** i **onNewIntent** .

1. Přepište funkci **Create** , nastavte **deviceInstallationService** na novou instanci **deviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Nastavte vlastnosti **notificationActionService** a **notificationRegistrationService** na **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Ve stejné funkci podmíněně volejte **FirebaseInstanceId. GetInstance (). InstanceId**. Implementujte **OnCompleteListener** pro nastavení výsledné hodnoty **tokenu** na **PushNotificationFirebaseMessagingService** před voláním **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Pořád ve funkci **Create**volejte na konci funkce volání **processNotificationActions** . Pokud chcete určit, že se tato akce zpracovává během spuštění aplikace, použijte *hodnotu true* pro argument *launchAction* .

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Aplikaci musíte znovu zaregistrovat pokaždé, když ji spustíte, a pokud ji zastavíte z relace ladění, budete moct dál přijímat nabízená oznámení.
