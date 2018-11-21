---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269655"
---
## <a name="webapi-project"></a>Projektem WebAPI
1. V sadě Visual Studio, otevřete **AppBackend** projekt, který jste vytvořili v **oznamování uživatelům pomocí** kurzu.
2. Notifications.cs nahradit celý **oznámení** třídy následujícím kódem. Nezapomeňte nahradit zástupné symboly vaším připojovacím řetězcem (s úplným přístupem) pro vaše Centrum oznámení a název centra. Tyto hodnoty z můžete získat [webu Azure portal](http://portal.azure.com). Tento modul teď představuje různé zabezpečených oznámení se odešlou. Ve zcela implementován oznámení uloží v databázi. pro jednoduchost v tomto případě jsme uložit je do paměti.
   
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

1. V souboru NotificationsController.cs nahraďte kód uvnitř **NotificationsController** třídy definice s následujícím kódem. Tato komponenta implementuje způsob, aby zařízení načetlo oznámení bezpečně a také nabízí možnost (pro účely tohoto kurzu) Chcete-li aktivovat zabezpečené nabízení do zařízení. Všimněte si, že při odesílání oznámení do centra oznámení, pouze pošleme nezpracovaná oznámení s ID oznámení (a žádný skutečný zprávy):
   
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


Všimněte si, `Post` metoda nyní neodešle oznámení s informační zprávou. Odešle nezpracovaná oznámení, která obsahuje pouze ID oznámení a nikoliv citlivého obsahu. Také ujistěte se, že chcete přidat komentář operace odeslání pro platformy, pro které nemáte přihlašovací údaje nakonfigurované v centru oznámení, jako jsou způsobí chyby.

1. Nyní jsme se znovu nasadit tuto aplikaci na web Azure aby byla přístupná ze všech zařízení. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.
2. Vyberte web Azure jako váš cíl publikování. Přihlaste se pomocí svého účtu Azure a vyberte stávající nebo novou webovou stránku a poznamenejte si **cílovou adresu URL** vlastnost **připojení** kartu. Na tuto adresu URL budeme odkazovat jako na *koncový bod back-endu* později v tomto kurzu. Klikněte na **Publikovat**.

