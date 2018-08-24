---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 654bc3745768fccea41d7c3991142bf7183b54be
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811587"
---
1. Ve vaší **aplikace** projektu, otevřete soubor `AndroidManifest.xml`. Přidejte následující kód za `application` počáteční značku:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Otevřete soubor `ToDoActivity.java`a proveďte následující změny:

    - Přidáte příkaz importu:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Změnit definici `MobileServiceClient` z **privátní** k **soukromé statické**, takže ho teď vypadá takto:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Přidat `registerPush` metody:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Aktualizace **onCreate** metodu `ToDoActivity` třídy. Ujistěte se, že tento kód po přidání `MobileServiceClient` je vytvořena instance.

        ```java
        registerPush();
        ```

3. Přidání nové třídy pro zpracování oznámení. V prohlížeči Project Explorer, otevřete **aplikace** > **java** > **your názvů projektu** uzly a klikněte pravým tlačítkem na uzel název balíčku. Klikněte na tlačítko **nový**a potom klikněte na tlačítko **třídy Java**. Do pole Název zadejte `ToDoMessagingService`a potom klikněte na tlačítko OK. Potom nahraďte deklaraci třídy s:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Přidáte jiné třídy pro zpracování tokenu aktualizací. Vytvoření `ToDoInstanceIdService` java třídy a nahraďte deklaraci třídy s:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Vaše aplikace je nyní aktualizována o podporu nabízených oznámení.
