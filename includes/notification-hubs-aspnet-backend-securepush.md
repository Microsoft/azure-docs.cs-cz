---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74260695"
---
## <a name="webapi-project"></a>Projekt WebAPI

1. V sadě Visual Studio otevřete projekt **AppBackend,** který jste vytvořili v kurzu **Upozornit uživatele.**
2. V Notifications.cs nahraďte celou třídu **Oznámení** následujícím kódem. Nezapomeňte nahradit zástupné symboly připojovacím řetězcem (s úplným přístupem) pro centrum oznámení a název centra. Tyto hodnoty můžete získat z [webu Azure Portal](https://portal.azure.com). Tento modul nyní představuje různé zabezpečené oznámení, které budou odeslány. V úplné implementaci budou oznámení uložena v databázi. pro jednoduchost, v tomto případě je ukládáme do paměti.
   
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

1. V NotificationsController.cs nahraďte kód uvnitř definice **třídy NotificationsController** následujícím kódem. Tato součást implementuje způsob, jakým zařízení bezpečně načíst oznámení a také poskytuje způsob (pro účely tohoto kurzu) pro aktivaci zabezpečené hodování do vašich zařízení. Všimněte si, že při odesílání oznámení do centra oznámení, pošleme pouze nezpracované oznámení s ID oznámení (a žádná skutečná zpráva):
   
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

Všimněte `Post` si, že metoda nyní neodesílá informační zprávu. Odešle nezpracovaná oznámení, která obsahuje pouze ID oznámení a žádný citlivý obsah. Také nezapomeňte komentovat operaci odeslání pro platformy, pro které nemáte pověření nakonfigurovaná v centru oznámení, protože budou mít za následek chyby.

1. Teď tuto aplikaci znovu nasadíme na web Azure, aby byla přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
2. Jako cíl publikování vyberte web Azure. Přihlaste se pomocí svého účtu Azure a vyberte existující nebo nový web a poznamenejte si **vlastnost cílové adresy URL** na kartě **Připojení.** Budeme odkazovat na tuto adresu URL jako *koncový bod back-endu* dále v tomto kurzu. Klikněte na **Publikovat**.
