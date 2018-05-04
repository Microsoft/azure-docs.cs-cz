Funkce Mobile Apps služby Azure App Service používá [Azure Notification Hubs] k odesílání nabízených oznámení, tak se konfigurace centra oznámení pro mobilní aplikace.

1. V [portál Azure], přejděte na **App Services**a potom vyberte vaší aplikace back-end. V části **nastavení**, vyberte **Push**.
2. Chcete-li přidat prostředek centra oznámení do aplikace, vyberte **Connect**. Můžete buď vytvořit rozbočovač nebo připojení k existující.

    ![Konfigurace rozbočovače](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nyní jste se připojili centra oznámení projektu back-end mobilní aplikace. Později nakonfigurujete toto centrum oznámení pro připojení k systému oznámení platformy (PNS) k zařízení.

[portál Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
