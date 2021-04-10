---
title: Posílání nabízených oznámení do systému Android pomocí Azure Notification Hubs a sady Firebase SDK verze 1.0.0-preview1
description: V tomto kurzu se naučíte používat Azure Notification Hubs a službu Google Firebase Cloud Messaging k odesílání nabízených oznámení do zařízení se systémem Android (verze 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 091ada7f099aca17152fc806e61dce6ab979852e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055157"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Kurz: odeslání nabízených oznámení na zařízení s Androidem pomocí sady Firebase SDK verze 1.0.0-preview1

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs a aktualizovanou verzi sady SDK FCM (Firebase Cloud Messaging) (verze 1.0.0-preview1) k odesílání nabízených oznámení do aplikace pro Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

Dokončený kód pro tento kurz si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Tento kurz se zabývá následujícími kroky:

- Vytvoříte projekt v Android Studiu.
- Vytvoříte projekt Firebase, který podporuje Firebase Cloud Messaging.
- Vytvoříte centrum oznámení.
- Připojte aplikaci k centru.
- Otestujete aplikaci.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

Potřebujete také následující položky:

- Doporučuje se nejnovější verze [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) .
- Minimální podpora je API Level 19.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu Android Studio

Prvním krokem je vytvoření projektu v Android Studio:

1. Spusťte Android Studio.

2. Vyberte **soubor**, vyberte **Nový** a pak **Nový projekt**.

3. Na stránce **Zvolte projekt** vyberte možnost **prázdná aktivita** a potom vyberte možnost **Další**.

4. Na stránce **Konfigurovat projekt** postupujte takto:
   1. Zadejte název aplikace.
   2. Zadejte umístění, do kterého chcete uložit soubory projektu.
   3. Vyberte **Dokončit**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Konfigurovat projekt":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Vytvoření projektu Firebase, který podporuje FCM

1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.

2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android).

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Přidat Firebase":::

3. Na stránce **Přidat Firebase na svou aplikaci pro Android** udělejte toto:

   1. V případě **názvu balíčku Android** Zkopírujte hodnotu **ApplicationId** do souboru **Build. Gradle** vaší aplikace. V tomto příkladu je to `com.fabrikam.fcmtutorial1app` .

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Zadat název balíčku":::

   2. Vyberte **Registrovat aplikaci**.

4. Vyberte **stáhnout google-services.jsna**, uložte soubor do složky **aplikace** projektu a pak vyberte **Další**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Stáhnout službu Google":::

5. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Nastavení projektu":::

6. Pokud jste nestáhli **google-services.js** do složky **aplikace** projektu Android Studio, můžete to udělat na této stránce.

7. Přepněte na kartu **Cloud Messaging** .

8. Zkopírujte a uložte **klíč serveru** pro pozdější použití. Tuto hodnotu použijete ke konfiguraci svého rozbočovače.

## <a name="configure-a-notification-hub"></a>Konfigurace centra oznámení

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby** a v části **mobilní zařízení** vyberte **Notification Hubs** . Výběrem ikony hvězdičky vedle názvu služby přidáte službu do oddílu **Oblíbené položky** v levé nabídce. Po přidání **Notification Hubs** k **oblíbeným položkám** ji vyberte v nabídce vlevo.

3. Na stránce **Notification Hubs** vyberte **Přidat** na panelu nástrojů.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Přidat centrum":::

4. Na stránce **Notification Hubs** postupujte takto:

   1. Zadejte název do **centra oznámení**.

   2. Zadejte název do v **vytvořit nový obor názvů**. Obor názvů obsahuje nejméně jedno rozbočovače.

   3. Vyberte hodnotu z rozevíracího seznamu **umístění** . Tato hodnota určuje umístění, ve kterém chcete vytvořit centrum.

   4. Vyberte existující skupinu prostředků ve **skupině prostředků** nebo vytvořte novou.

   5. Vyberte **Vytvořit**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Vytvořit centrum":::

5. Vyberte **oznámení** (ikona zvonku) a pak vyberte **Přejít k prostředku**. Můžete také aktualizovat seznam na stránce **Notification Hubs** a vybrat své centrum.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Vybrat centrum":::

6. Vyberte ze seznamu **Zásady přístupu**. Všimněte si, že jsou k dispozici dva připojovací řetězce. Budete je potřebovat později pro zpracování nabízených oznámení.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Zásady přístupu":::

   > [!IMPORTANT]
   > V aplikaci nepoužívejte zásady **DefaultFullSharedAccessSignature** . Tato zásada se použije jenom v back-endu aplikace.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurace nastavení služby Firebase Cloud Messaging pro centrum

1. V levém podokně v části **Nastavení** vyberte **Google (GCM/FCM)**.

2. Zadejte **klíč serveru** pro projekt FCM, který jste předtím uložili.

3. Na panelu nástrojů vyberte **Uložit**.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Klíč serveru":::

4. Azure Portal zobrazí zprávu, že centrum bylo úspěšně aktualizováno. Tlačítko **Uložit** je zakázané.

Vaše centrum oznámení je teď nakonfigurované tak, aby fungovalo s Firebasem cloudovým zasíláním zpráv. Máte také připojovací řetězce, které jsou nezbytné k odesílání oznámení do zařízení a k registraci aplikace pro příjem oznámení.

## <a name="connect-your-app-to-the-notification-hub"></a>Připojte aplikaci k centru oznámení

### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play

1. V Android Studio v nabídce vyberte **nástroje** a pak vyberte **správce SDK**.

2. Vyberte cílovou verzi Android SDK, která se používá ve vašem projektu. Pak vyberte **Zobrazit podrobnosti balíčku**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="Správce sady SDK":::

3. Vyberte **Google API**, pokud ještě není nainstalovaná.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="Rozhraní API":::

4. Přepněte na kartu **SDK Tools** . Pokud jste ještě nenainstalovali Služby Google Play, vyberte **služby Google Play** , jak je znázorněno na následujícím obrázku. Pak vyberte **použít** k instalaci. Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Služby hraní":::

5. Pokud se zobrazí dialogové okno **Potvrdit změnu** , vyberte **OK**. Instalační program součásti nainstaluje požadované součásti. Po instalaci součástí vyberte **Dokončit** .

6. Kliknutím na **tlačítko OK** zavřete dialogové okno **nastavení pro nové projekty** .

### <a name="add-azure-notification-hubs-libraries"></a>Přidání knihoven Azure Notification Hubs

1. Do souboru **Build. Gradle** pro aplikaci přidejte následující řádky v části závislosti:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Přidejte následující úložiště za část závislosti:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Přidání podpory Google Firebase

1. Na konec souboru přidejte následující modul plug-in, pokud tam ještě není.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Vyberte **synchronizovat hned** na panelu nástrojů.

### <a name="add-code"></a>Přidat kód

1. Vytvoří objekt **NotificationHubListener** , který zpracovává zachycení zpráv z Azure Notification Hubs.

   ```java
   public class CustomNotificationListener implements NotificationListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. V `OnCreate` metodě `MainActivity` třídy přidejte následující kód pro spuštění procesu inicializace Notification Hubs při vytvoření aktivity:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.start(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. V Android Studio na panelu nabídek vyberte **sestavení** a pak vyberte znovu **sestavit projekt** , abyste se ujistili, že ve vašem kódu nejsou žádné chyby. Pokud se zobrazí chyba s ikonou **ic_launcher** , odeberte z AndroidManifest.xml souboru následující příkaz:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Ujistěte se, že máte k dispozici virtuální zařízení pro spuštění aplikace. Pokud žádný nemáte, přidejte ho následujícím způsobem:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Správce zařízení":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Virtuální zařízení":::
   3. Spusťte aplikaci na vybraném zařízení a ověřte, že se do centra úspěšně zaregistruje.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Registrace zařízení":::

      > [!NOTE]
      > Při počátečním spuštění může být registrace neúspěšná, dokud nebude `onTokenRefresh()` volána metoda služby ID instance. Aktualizace by měla zahájit úspěšnou registraci v centru oznámení.

## <a name="send-a-test-notification"></a>Odešlete zkušební oznámení

Nabízená oznámení můžete odesílat do centra oznámení z [Azure Portal](https://portal.azure.com/)následujícím způsobem:

1. V Azure Portal na stránce Centra oznámení pro vaše centrum vyberte v části **Poradce při potížích** možnost **test Send** .

2. V přepínačích **platformy** vyberte **Android**.

3. Vyberte **Odeslat**. Na zařízení s Androidem se ještě nezobrazuje oznámení, protože jste na něm nespouštěli mobilní aplikaci. Po spuštění mobilní aplikace znovu vyberte tlačítko **Odeslat** a zobrazí se zpráva s oznámením.

4. Podívejte se na výsledek operace v seznamu v dolní části stránky portálu.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Poslat zkušební oznámení":::

5. Na zařízení se zobrazí zpráva s oznámením.

Nabízená oznámení se normálně odesílají do back-endové služby, jako je Mobile Apps nebo ASP.NET, pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k posílání oznámení použít také REST API přímo.

## <a name="run-the-mobile-app-on-emulator"></a>Spustit mobilní aplikaci na emulátoru

Než otestujete nabízená oznámení v emulátoru, ujistěte se, že vaše image emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, může se zobrazit **SERVICE_NOT_AVAILABLE** výjimka.

Také se ujistěte, že jste do svého spuštěného emulátoru v části **Nastavení**  >  **účty** přidali účet Google. V opačném případě se můžou vaše pokusy o registraci v FCM způsobit výjimku **AUTHENTICATION_FAILED** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Firebase Cloud Messaging pro vysílání oznámení na všechna zařízení s Androidem, která byla zaregistrovaná ve službě. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Kurz: odesílání oznámení konkrétním uživatelům](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Níže je seznam některých dalších kurzů pro posílání oznámení:

- Azure Mobile Apps: Příklad odesílání oznámení z back-endu Mobile Apps, který je integrovaný do Notification Hubs, najdete v tématu [Přidání nabízených oznámení do vaší aplikace pro iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [použijte Notification Hubs k odesílání nabízených oznámení uživatelům](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Sada Azure Notification Hubs Java SDK: Informace o odesílání oznámení z Javy najdete v článku [Jak používat Notification Hubs z Javy](notification-hubs-java-push-notification-tutorial.md). Tato metoda prošla pro potřeby vývoje pro Android testováním v Eclipse.

- PHP: [Jak používat Notification Hubs z PHP](notification-hubs-php-push-notification-tutorial.md).
