

1. Přihlaste se ke [konzole Firebase](https://firebase.google.com/console/). Vytvořte nový projekt Firebase, pokud jej ještě nemáte.
2. Po vytvoření projektu vyberte **Add Firebase to your Android app** (Přidat Firebase do aplikace pro Android). Postupujte podle zobrazených pokynů.

    ![Přidání Firebase do aplikace pro Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. V konzole Firebase vyberte kolečko pro váš projekt. Potom vyberte **Project Settings** (Nastavení projektu).

    ![Výběr nastavení projektu](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. V nastavení projektu vyberte kartu **General** (Obecné). Potom si stáhněte soubor **google-services.json**, který obsahuje klíč rozhraní Server API a ID klienta.
5. Klikněte na kartu **Cloud Messaging** v nastavení projektu a potom zkopírujte hodnotu **Legacy server key** (Klíč serveru starší verze). Tuto hodnotu použijete ke konfiguraci zásad přístupu centra oznámení.
