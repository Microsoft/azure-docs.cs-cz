---
title: zahrnout soubor
description: Soubor k zahrnutí, který obsahuje kód pro vytvoření projektu back-end ASP .NET WebAPI
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/04/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9a94f76e2d1fe930d2d6d43e81a756f0cb15d23d
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453065"
---
## <a name="create-the-webapi-project"></a>Vytvoření projektu WebAPI

Následující části popisují vytvoření nového back-endu ASP.NET WebAPI. Tento proces má tři hlavní účely:

- **Ověřování klientů**: Přidáte obslužné rutiny zpráv pro ověření žádostí klienta a přidružit uživatele k žádosti.
- **Registrace k oznámením pomocí back-endu WebAPI**: Můžete přidat kontroler zpracovávat nové registrace klientských zařízení k přijímání oznámení. Ověřené uživatelské jméno se automaticky přidá do registrace jako [značka](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Odesílání oznámení klientům**: Přidání kontroleru poskytují způsob, jak uživatelům aktivovat zabezpečené nabízení do zařízení a klientů přidružených značky.

Vytvořte nový back-end ASP.NET WebAPI provedením následujících akcí:

> [!IMPORTANT]
> Pokud používáte sadu Visual Studio 2015 nebo starší, před zahájením tohoto kurzu se ujistěte, že máte nainstalovanou nejnovější verzi Správce balíčků NuGet pro Visual Studio.
>
>Pokud to chcete zkontrolovat, spusťte sadu Visual Studio. V nabídce **Nástroje** vyberte **Rozšíření a aktualizace**. Vyhledejte **Správce balíčků NuGet** ve vaší verzi sady Visual Studio a ujistěte se, že máte nejnovější verzi. Pokud vaše verze není nejnovější verzí, odinstalujte ji a pak znovu nainstalujte Správce balíčků NuGet.

![][B4]

> [!NOTE]
> Ujistěte se, že máte nainstalovanou sadu [Azure SDK](https://azure.microsoft.com/downloads/) pro vývoj pro web.

1. Spusťte sadu Visual Studio nebo Visual Studio Express.

2. Vyberte **Průzkumník serveru** a přihlaste se ke svému účtu Azure. Abyste mohli vytvářet prostředky webu, musíte být přihlášení.

3. V sadě Visual Studio klikněte pravým tlačítkem na řešení sady Visual Studio, přejděte na **Přidat** a klikněte na **Nový projekt**.
4. Rozbalte položku **Visual C#**, vyberte **Web** a klikněte na **Webová aplikace ASP.NET**.

5. Do pole **Název** zadejte **AppBackend** a pak vyberte **OK**.

    ![Okno Nový projekt][B1]

6. V okně **Nový projekt ASP.NET** zaškrtněte políčko **Webové rozhraní API** a pak vyberte **OK**.

    ![Okno Nový projekt ASP.NET][B2]

7. V okně **Konfigurovat webovou aplikaci Microsoft Azure** vyberte předplatné a pak v seznamu **Plán služby App Service** proveďte jednu z následujících akcí:

    * Vyberte plán služby App Service, který jste už vytvořili.
    * Vyberte možnost **Vytvořit nový plán App Service** a pak tento plán vytvořte.

  Pro účely tohoto kurzu nepotřebujete databázi. Jakmile vyberete plán služby App Service, výběrem **OK** vytvořte projekt.

    ![Okno Konfigurovat webovou aplikaci Microsoft Azure][B5]

## <a name="authenticate-clients-to-the-webapi-backend"></a>Ověřování klientů v back-endu WebAPI

V této části vytvoříte pro nový back-end novou třídu popisovače zprávy **AuthenticationTestHandler**. Tato třída je odvozená od třídy [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) a přidaná jako popisovač zprávy, aby mohla zpracovávat všechny požadavky přicházející na back-end.

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **AppBackend**, vyberte **Přidat** a pak vyberte **Třída**.
2. Pojmenujte novou třídu **AuthenticationTestHandler.cs** a pak ji výběrem **Přidat** vygenerujte. Tato třída bude pro zjednodušení ověřovat uživatele pomocí *základního ověřování*. Vaše aplikace může používat jakékoli schéma ověřování.
3. V souboru AuthenticationTestHandler.cs přidejte následující příkazy `using`:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. V souboru AuthenticationTestHandler.cs nahraďte definici třídy `AuthenticationTestHandler` následujícím kódem:

    Tato obslužná rutina ověří požadavek při splnění těchto tří podmínek:

   * Požadavek obsahuje *autorizační* hlavičku.
   * Požadavek používá *základní* ověřování.
   * Řetězce uživatelského jména a hesla jsou stejné.

  Jinak bude požadavek zamítnut. Při tomto ověření se nepoužívá správný přístup k ověřování a autorizaci. Je to jenom jednoduchý příklad pro účely tohoto kurzu.

  Pokud třída `AuthenticationTestHandler` ověří a autorizuje zprávu požadavku, uživatel základního ověřování se připojí k aktuálnímu požadavku v objektu [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Informace o uživateli v objektu HttpContext později použije jiný kontroler (RegisterController) pro přidání [značky](https://msdn.microsoft.com/library/azure/dn530749.aspx) do požadavku na registraci oznámení.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (verifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool verifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Poznámka k zabezpečení: `AuthenticationTestHandler` Třída neposkytuje skutečné ověřování. Používá se pouze k napodobení základního ověřování a není bezpečná. Ve svých produkčních aplikacích a službách musíte implementovat mechanismus zabezpečeného ověřování.
5. Pro registraci popisovače zprávy přidejte následující kód na konec metody `Register` ve třídě **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Uložte provedené změny.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Registrace oznámení pomocí back-endu WebAPI

V této části přidáte do back-endu WebAPI nový kontroler, který bude zpracovávat požadavky na registraci uživatele a zařízení k oznámením pomocí klientské knihovny pro centra oznámení. Kontroler přidá značku uživatele pro uživatele, který byl ověřen a připojen k objektu HttpContext třídou `AuthenticationTestHandler`. Značka bude mít formát řetězce `"username:<actual username>"`.

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt **AppBackend** a pak vyberte **Spravovat balíčky NuGet**.

2. V levém podokně vyberte **Online** a pak do pole **Hledat** zadejte **Microsoft.Azure.NotificationHubs**.

3. V seznamu výsledků vyberte **Microsoft Azure Notification Hubs** a pak vyberte **Nainstalovat**. Dokončete instalaci a pak zavřete okno Správce balíčků NuGet.

    Tato akce přidá referenci na sadu SDK služby Azure Notification Hubs pomocí [balíčku NuGet Microsoft.Azure.Notification Hubs](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Vytvořte nový soubor třídy, která představuje propojení s centrem událostí sloužícím k odesílání oznámení. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Modely**, vyberte **Přidat** a pak vyberte **Třída**. Pojmenujte novou třídu **Notifications.cs** a pak ji výběrem **Přidat** vygenerujte.

    ![Okno Přidat novou položku][B6]

5. Na začátek souboru Notifications.cs přidejte následující příkaz `using`:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Nahraďte definici třídy `Notifications` následujícím kódem a nahraďte dva zástupné symboly připojovacím řetězcem (pro úplný přístup) vašeho centra událostí a názvem centra (najdete ho na webu [Azure Portal](http://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
7. Dále vytvořte nový kontroler **RegisterController**. V Průzkumníku řešení klikněte pravým tlačítkem na složku **Kontrolery**, vyberte **Přidat** a pak vyberte **Kontroler**.

8. Vyberte **Kontroler Web API 2 – prázdný** a pak vyberte **Přidat**.

    ![Okno Přidat vygenerované uživatelské rozhraní][B7]

9. Do pole **Název kontroleru** zadejte **RegisterController** a pojmenujte tak novou třídu, pak vyberte **Přidat**.

    ![Okno Přidat kontroler][B8]

10. V souboru RegisterController.cs přidejte následující příkazy `using`:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Do definice třídy `RegisterController` přidejte následující kód. V tomto kódu přidáváte značku uživatele pro uživatele, který je připojený k objektu HttpContext. Tento uživatel byl ověřen a připojen k objektu HttpContext filtrem zpráv `AuthenticationTestHandler`, který jste vytvořili. Můžete také přidat volitelné kontroly pro ověření, že uživatel má práva pro registraci k požadovaným značkám.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "gcm":
                registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Uložte provedené změny.

## <a name="send-notifications-from-the-webapi-backend"></a>Odesílání oznámení z back-endu WebAPI

V této části přidáte nový kontroler, který zveřejňuje způsob odesílání oznámení klientskými zařízeními. Oznámení je založené na značce uživatelského jména, kterou používá knihovna .NET Notification Hubs v back-endu ASP.NET WebAPI.

1. Vytvořte další nový kontroler **NotificationsController** stejným způsobem, kterým jste v předchozí části vytvořili **RegisterController**.

2. V souboru NotificationsController.cs přidejte následující příkazy `using`:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Do třídy **NotificationsController** přidejte následující metodu:

    Tento kód odesílá typ oznámení na základě parametru `pns` systému oznámení platformy. Hodnota `to_tag` slouží k nastavení značky *username* (uživatelské jméno) pro zprávu. Tato značka musí odpovídat značce uživatelského jména aktivní registrace k centru událostí. Zpráva oznámení se přetáhne z textu požadavku POST a naformátuje se pro cílový systém oznámení platformy.

    V závislosti na systému oznámení platformy, který vaše zařízení používá k přijímání oznámení, jsou podporována oznámení v různých formátech. Například na zařízeních s Windows byste mohli použít [informační zprávu pomocí Služby nabízených oznámení Windows](https://msdn.microsoft.com/library/windows/apps/br230849.aspx), kterou ostatní systémy oznámení platformy přímo nepodporují. V takovém případě musí váš back-end formátovat oznámení na podporované oznámení pro systémy oznámení platformy zařízení, která chcete podporovat. Ve [třídě NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx) pak použijte vhodné rozhraní API pro odesílání.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "gcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Stisknutím klávesy **F5** aplikaci spusťte a ověřte, že jste zatím postupovali správně. Aplikace otevře webový prohlížeč a zobrazí se na domovské stránce ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Publikování nového back-endu WebAPI

Dále tuto aplikaci nasadíte na web Azure, aby byla přístupná ze všech zařízení.

1. Klikněte pravým tlačítkem na projekt **AppBackend** a vyberte **Publikovat**.

2. Jako cíl publikování vyberte **Microsoft Azure App Service** a pak vyberte \*\*Publikovat. Otevře se okno Vytvořit plán App Service. Tady můžete vytvořit všechny prostředky Azure, které jsou potřeba ke spuštění webové aplikace ASP.NET v Azure.

    ![Dlaždice Microsoft Azure App Service][B15]

3. V okně **Vytvořit plán App Service** vyberte váš účet Azure. Vyberte **Změnit typ** > **Webová aplikace**. Ponechejte výchozí **Název webové aplikace** a vyberte **Předplatné**, **Skupinu prostředků** a **Plán služby App Service**.

4. Vyberte **Vytvořit**.

5. Poznamenejte si vlastnost **Adresa URL webu** v části **Souhrn**. Tato adresa URL je váš *koncový bod back-endu*, který použijete později v tomto kurzu.

6. Vyberte **Publikovat**.

Průvodce po dokončení publikuje webovou aplikaci ASP.NET do Azure a pak tuto aplikaci otevře ve výchozím prohlížeči.  Vaši aplikaci bude možné zobrazit ve službě Azure App Service.

Adresa URL používá dříve zadaný název webové aplikace ve formátu http://<název_aplikace>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
