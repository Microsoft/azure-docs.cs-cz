---
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/14/2020
ms.author: sethm
ms.openlocfilehash: fb3c95b74128f1da7b29a290e17fefe21987dd76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019411"
---
## <a name="webapi-project"></a>Projekt WebAPI

1. V aplikaci Visual Studio otevřete projekt **projekt appbackend** , který jste vytvořili v kurzu pro **upozorňování uživatelů** .
2. V Notifications.cs nahraďte celou třídu **oznámení** následujícím kódem. Nezapomeňte nahradit zástupné symboly připojovacím řetězcem (s úplným přístupem) pro vaše centrum oznámení a názvem centra. Tyto hodnoty můžete získat z [Azure Portal](https://portal.azure.com). Tento modul teď představuje různá zabezpečená oznámení, která se odešlou. V úplné implementaci budou oznámení uložená v databázi. pro zjednodušení v tomto případě je ukládáme do paměti.

   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

3. V NotificationsController.cs nahraďte kód uvnitř definice třídy **NotificationsController** následujícím kódem. Tato součást implementuje způsob, jak zařízení správně načte oznámení, a poskytuje způsob (pro účely tohoto kurzu) pro aktivaci zabezpečeného nabízeného oznámení pro vaše zařízení. Všimněte si, že při odesílání oznámení do centra oznámení pošleme nezpracované oznámení s ID oznámení (bez skutečné zprávy):

   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Všimněte si, že `Post` Metoda teď neposílá oznámení informační zprávy. Pošle nezpracované oznámení, které obsahuje jenom ID oznámení, a ne žádný citlivý obsah. Nezapomeňte taky zadat komentář k operaci Odeslat pro platformy, pro které nemáte nakonfigurované přihlašovací údaje v centru oznámení, protože budou mít chyby.

1. Tuto aplikaci teď znovu nasadíme na web Azure, aby bylo možné ji zpřístupnit ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
2. Jako cíl publikování vyberte web Azure. Přihlaste se pomocí svého účtu Azure a vyberte existující nebo nový web a poznamenejte si vlastnost **cílová adresa URL** na kartě **připojení** . Na tuto adresu URL budeme odkazovat jako na *koncový bod back-end* později v tomto kurzu. Klikněte na **Publikovat**.
