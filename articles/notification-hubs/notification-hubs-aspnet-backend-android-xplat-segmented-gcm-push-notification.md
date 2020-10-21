---
title: Posílání oznámení na konkrétní zařízení s Androidem pomocí Azure Notification Hubs a Google Cloud Messaging | Microsoft Docs
description: Přečtěte si, jak používat Notification Hubs k zasílání nabízených oznámení určitým zařízením s Androidem službami Azure Notification Hubs a Google Cloud Messaging.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c0c0018ac3007f77da820b9b0cecbb69c68bef31
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308297"
---
# <a name="tutorial-send-push-notifications-to-specific-android-devices-using-google-cloud-messaging-deprecated"></a>Kurz: odeslání nabízených oznámení na konkrétní zařízení s Androidem pomocí Google Cloud Messaging (zastaralé)

> [!WARNING]
> Od 10. dubna 2018 má Google zastaralé Google Cloud Messaging (GCM). GCM Server a klientská rozhraní API jsou zastaralá a budou se odebírat hned jako 29. května 2019. Další informace najdete v článku [Nejčastější dotazy k GCM a FCM](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se naučíte používat službu Azure Notification Hubs k vysílání důležitých oznámení do aplikací pro Android. Po dokončení kurzu si budete moct zaregistrovat kategorie důležitých oznámení, které vás zajímají, abyste dostávali nabízená oznámení, která se týkají jenom těchto kategorií. Tento scénář se běžně používá v řadě aplikací, které posílají oznámení skupinám uživatelům, kteří o ně projevili zájem. Může jít třeba o čtečku RSS, aplikaci pro hudební fanoušky atd.

Scénáře vysílání povolíte tak, že při registraci v centru oznámení přidáte jednu nebo více *značek*. Pokud se oznámení posílají značce, přijdou všem zařízením, která si značku zaregistrovala. Značky jsou jednoduše řetězce, které se nemusejí vytvářet předem. Další informace o značkách najdete v článku [Směrování a výrazy značek ve službě Notification Hubs](notification-hubs-tags-segment-push-message.md).

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Přidáte do mobilní aplikace výběr kategorií.
> * Registrováno pro oznámení pomocí značek.
> * Odešlete označená oznámení.
> * Otestování aplikace

## <a name="prerequisites"></a>Předpoklady

Tento kurz vychází z aplikace, kterou jste vytvořili v [kurzu Zasílání nabízených oznámení zařízením s Androidem službami Azure Notification Hubs a Google Cloud Messaging][get-started]. Než začnete tento kurz, dokončete [kurz Zasílání nabízených oznámení zařízením s Androidem službami Azure Notification Hubs a Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

První krok spočívá v přidání prvků uživatelského rozhraní do stávající třídy MainActivity, aby si uživatel mohl vybrat kategorie, které si zaregistruje. Kategorie, které uživatel vybere, jsou uložené v zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi ve formě značek.

1. Otevřete `res/layout/activity_main.xml file` a nahraďte obsah následujícím:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Otevřete `res/values/strings.xml` soubor a přidejte následující řádky:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    Vaše `main_activity.xml` grafické rozložení by mělo vypadat jako na následujícím obrázku:

    ![Snímek obrazovky vývojového prostředí s viditelnou obrazovkou aplikace Aplikace obsahuje seznam kategorií zpráv, které byly přidány do kódu.][A1]
3. Vytvořte třídu `Notifications` ve stejném balíčku jako svou `MainActivity` třídu.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Tato třída uloží kategorie novinek, které bude zařízení dostávat, do místního úložiště. Obsahuje také metody registrace kategorií.
4. Ve `MainActivity` třídě odeberte soukromá pole pro a a `NotificationHub` `GoogleCloudMessaging` přidejte pole pro `Notifications` :

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Pak v `onCreate` metodě odstraňte inicializaci `hub` pole a `registerWithNotificationHubs` metody. Pak přidejte následující řádky, které inicializují instanci `Notifications` třídy.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Potvrďte správné nastavení názvu centra a připojovacího řetězce ve třídě NotificationSettings.

    > [!NOTE]
    > Obecně platí, že přihlašovací údaje distribuované klientskou aplikací nejsou příliš bezpečné, a proto byste měli s klientskou aplikací distribuovat jenom přístupový klíč pro naslouchání. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale nedovolí měnit stávající registrace ani odesílat oznámení. Plný přístupový klíč se používá v zabezpečené back-endové službě k posílání oznámení a změně stávajících registrací.

6. Pak přidejte následující importy:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Přidejte následující metodu `subscribe`, která zpracuje událost kliknutí na tlačítko Přihlášení k odběru:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Tato metoda vytvoří seznam kategorií a pomocí `Notifications` třídy uloží seznam do místního úložiště a zaregistruje odpovídající značky do vašeho centra oznámení. Při změně kategorií se vytvoří registrace s novými kategoriemi.

Aplikace teď dokáže do místního úložiště v zařízení uložit sadu kategorií a zaregistrovat ji v centru oznámení pokaždé, když uživatel změní vybrané kategorie.

## <a name="register-for-notifications"></a>Registrace oznámení

Tento postup provede při spuštění registraci v centru oznámení. Použije k tomu kategorie uložené v místním úložišti.

> [!NOTE]
> Hodnota registraionId přiřazená službou GCM (Google Cloud Messaging) se může kdykoli změnit, a proto byste měli oznámení často registrovat, abyste se vyhnuli chybám. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pokud se aplikace spouštějí často, třeba častěji než jednou denně, pravděpodobně můžete registraci přeskočit kvůli úspoře šířky pásma, protože od předchozí registrace neuplynul ani den.

1. Na konec `onCreate` metody ve třídě přidejte následující kód `MainActivity` :

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Tento kód zajistí, aby aplikace při každém spuštění načetla kategorie z místního úložiště a vyžadovala registraci těchto kategorií.
2. Potom aktualizujte metodu `onStart()` třídy `MainActivity`:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Tento kód aktualizuje třídu MainActivity na základě stavu dříve uložených kategorií.

Hotová aplikace teď do místního úložiště v zařízení uloží sadu kategorií. Tato sada se použije k registraci v centru oznámení pokaždé, když uživatel změní vybrané kategorie. V dalším kroku definujte back-end, který aplikaci posílá oznámení týkající se kategorií.

## <a name="send-tagged-notifications"></a>Posílání označených oznámení

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Otestování aplikace

1. V Android Studiu spusťte aplikaci buď na zařízení s Androidem, nebo v emulátoru. Uživatelské rozhraní aplikace nabízí sadu přepínačů, kterými můžete vybrat odebírané kategorie.
2. Zapněte jeden nebo více přepínačů kategorií a klikněte na **Přihlásit k odběru**. Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení. Zaregistrované kategorie se vrátí a zobrazí se v informační zprávě.

    ![Přihlášení k odběru kategorií](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Spusťte konzolovou aplikaci .NET, která zasílá oznámení o každé kategorii. Oznámení pro vybrané kategorie se zobrazí jako informační zprávy.

    ![Oznámení o technologických novinkách](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste odeslali nabízená oznámení určitým zařízením s Androidem, která si zaregistrovala kategorie. Pokud se chcete naučit zasílat nabízená oznámení určitým uživatelům, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním uživatelům](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)