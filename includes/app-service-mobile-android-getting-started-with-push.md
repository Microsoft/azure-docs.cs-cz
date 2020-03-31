---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175521"
---
1. V projektu **aplikace** otevřete `AndroidManifest.xml`soubor . Za `application` otevírací značku přidejte následující kód:

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

2. Otevřete `ToDoActivity.java`soubor a proveďte následující změny:

    - Přidejte příkaz importu:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Změňte definici ze soukromé na `MobileServiceClient` **soukromou** **statickou**, takže teď vypadá takto:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Přidat `registerPush` metodu:

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

    - Aktualizujte **metodu onCreate** třídy. `ToDoActivity` Ujistěte se, že `MobileServiceClient` přidáte tento kód po vytvoření instance.

        ```java
        registerPush();
        ```

3. Přidejte novou třídu pro zpracování oznámení. V Průzkumníkovi projektu **app** > otevřete uzly oboru názvů**v jazyce Java** > **a** klikněte pravým tlačítkem myši na uzel názvu balíčku. Klepněte na tlačítko **Nový**a potom klepněte na **položku Třída Java**. Do pole `ToDoMessagingService`Název zadejte a klikněte na OK. Potom nahraďte deklaraci třídy:

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

4. Přidejte další třídu pro zpracování aktualizací tokenů. Vytvořte `ToDoInstanceIdService` třídu java a nahraďte deklaraci třídy:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Vaše aplikace je teď aktualizována, aby podporovala nabízená oznámení.
