---
title: "Odesílání nabízených oznámení do aplikací pro Chrome přes Azure Notification Hubs | Dokumentace Microsoftu"
description: "Dozvíte se, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace pro Chrome."
services: notification-hubs
keywords: "mobilní nabízená oznámení,nabízená oznámení,nabízené oznámení,nabízená oznámení chrome"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 376348c3af0ff97162f1b2ed865a5edd47f52546
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Odesílání nabízených oznámení do aplikací pro Chrome přes Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto článku se dozvíte, jak pomocí Azure Notification Hubs odesílat do aplikace pro Chrome nabízená oznámení, která se zobrazí v kontextu prohlížeče Google Chrome. V tomto kurzu vytvoříte aplikaci pro Chrome, která bude přijímat nabízená oznámení pomocí služby [GCM (Google Cloud Messaging)](https://developers.google.com/cloud-messaging/). 

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

Tento kurz vás provede těmito základními kroky ke zprovoznění nabízených oznámení:

* [Povolení služby GCM (Google Cloud Messaging)](#register)
* [Konfigurace centra oznámení](#configure-hub)
* [Propojení aplikace pro Chrome s centrem oznámení](#connect-app)
* [Odeslání nabízeného oznámení do aplikace pro Chrome](#send)
* [Další funkce a schopnosti](#next-steps)

> [!NOTE]
> Nabízená oznámení v aplikaci pro Chrome nepředstavují obecná oznámení v prohlížeči – jsou specifické pro model rozšiřitelnosti prohlížeče (podrobnosti najdete v [Přehled aplikací pro Chrome]). Kromě desktopového prohlížeče běží aplikace pro Chrome na mobilních zařízeních (s Androidem a iOS) prostřednictvím Apache Cordovy. Další informace najdete v tématu [Aplikace pro Chrome na mobilních zařízeních].
> 
> 

Konfigurace služby GCM a Azure Notification Hubs je totožná s konfigurací pro Android, protože [služba GCM (Google Cloud Messaging) pro Chrome] je zastaralá a tatáž služba GCM nyní podporuje jak zařízení Android, tak instance Chromu.

## <a id="register"></a>Povolení služby GCM (Google Cloud Messaging)
1. Přejděte na web [Google Cloud Console], přihlaste se přihlašovacími údaji ke svému účtu Google a klikněte na tlačítko **Create Project** (Vytvořit projekt). Do pole **Project Name** (Název projektu) zadejte odpovídající název a klikněte na tlačítko **Create** (Vytvořit).
   
    ![Google Cloud Console – Vytvoření projektu][1]
2. Na stránce **Projects** (Projekty) si poznamenejte číslo **Project Number** (Číslo projektu) pro projekt, který jste vytvořili. Číslo projektu použijete v aplikaci pro Chrome jako **ID odesílatele GCM** k registraci do služby GCM.
   
    ![Google Cloud Console – Číslo projektu][2]
3. V levém podokně klikněte na **APIs & auth** (Rozhraní API a ověřování), posuňte se dolů a klikněte na přepínač, který povolí službu **Google Cloud Messaging (GCM) pro Android**. Není nutné povolovat službu **služba GCM (Google Cloud Messaging) pro Chrome**.
   
    ![Google Cloud Console – Klíč serveru][3]
4. V levém podokně klikněte na **Credentials** (Přihlašovací údaje) > **Create New Key** (Vytvořit nový klíč) > **Server Key** (Klíč serveru) > **Create** (Vytvořit).
   
    ![Google Cloud Console – Přihlašovací údaje][4]
5. Poznamenejte si **API Key** (Klíč rozhraní API) serveru. Tuto hodnotu použijete v další části ke konfiguraci centra oznámení, aby mohlo odesílat nabízená oznámení do služby GCM.
   
    ![Google Cloud Console – Klíč rozhraní API][5]

## <a id="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6.   Na stránce **Nastavení** vyberte **Notification Services** a pak **Google (GCM)**. Zadejte klíč rozhraní API a uložte jej.

        ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>Propojení aplikace pro Chrome s centrem oznámení
Vaše centrum oznámení je nyní nakonfigurováno pro práci se službou GCM. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro příjem a odesílání nabízených oznámení. LK

### <a name="create-a-new-chrome-app"></a>Vytvoření nové aplikace pro Chrome
Následující ukázka je založena na [ukázce GCM aplikace pro Chrome] a k vytvoření aplikace pro Chrome používá doporučený postup. Tato část ukazuje kroky specifické pro službu Azure Notification Hubs. 

> [!NOTE]
> Doporučujeme stáhnout si zdrojové kódy pro tuto aplikaci pro Chrome z [ukázky využití Notification Hubs v aplikaci pro Chrome]. 

Aplikace pro Chrome se vytváří prostřednictvím JavaScriptu a můžete k tomu použít libovolný textový editor. Na následujícím obrázku je příklad aplikace pro Chrome:

![Aplikace pro Google Chrome][15]

1. Vytvořte složku a pojmenujte ji `ChromePushApp`. Název může být libovolný – pokud ji pojmenujete jinak, bude nutné změnit cestu v příslušných segmentech kódu.
2. Do složky, kterou jste vytvořili v druhém kroku, stáhněte [knihovnu crypto-js]. Tato složka knihovny obsahuje dvě podsložky: `components` a `rollups`.
3. Vytvořte soubor `manifest.json`. Všechny aplikace pro Chrome používají soubor manifestu, který obsahuje metadata aplikace, a hlavně všechna oprávnění udělená aplikaci ve chvíli, kdy ji uživatel nainstaluje.
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    Všimněte si elementu `permissions`, který určuje, že tato aplikace pro Chrome může přijímat nabízená oznámení ze služby GCM. Kromě toho musí určovat identifikátor URI služby Azure Notification Hubs, kde aplikace pro Chrome provede volání REST pro registraci.
    Ukázková aplikace také používá soubor ikony, `gcm_128.png`, který najdete ve zdrojovém kódu, který již byl použit v původní ukázce GCM. Můžete jej nahradit libovolným obrázkem, který splňuje [kritéria pro ikonu](https://developer.chrome.com/apps/manifest/icons).
4. Vytvořte soubor s názvem `background.js` a následujícím kódem:
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    Tento soubor otevře HTML okna aplikace pro Chrome (**register.html**) a také definuje obslužnou rutinu **messageReceived**, která zpracuje příchozí nabízené oznámení.
5. Vytvořte soubor s názvem `register.html`, který definuje uživatelské rozhraní aplikace pro Chrome. 
   
   > [!NOTE]
   > V této ukázce se používá **CryptoJS v3.1.2**. Pokud jste si stáhli jinou verzi knihovny, je nezbytné v cestě `src` správně nahradit verzi.
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. Vytvořte soubor s názvem `register.js` a kódem uvedeným v tomto kroku. Tento soubor obsahuje skript na pozadí `register.html`. Aplikace pro Chrome neumožňují vložené spouštění, proto pro uživatelské rozhraní musíte vytvořit samostatný skript na pozadí.
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    Tento skript má následující klíčové parametry:
   
   * **window.onload** definuje události kliknutí na tlačítko pro dvě tlačítka uživatelského rozhraní. První obslužná rutina události kliknutí na tlačítko provádí registraci ke službě GCM, druhé používá ID registrace vrácené po registraci ke službě GCM, pomocí kterého se provede registrace do Azure Notification Hubs.
   * **updateLog** je funkce, která kódu umožňuje protokolovat informace. 
   * **registerWithGCM** je první obslužná rutina kliknutí na tlačítko, která prostřednictvím volání `chrome.gcm.register` do služby GCM registruje aktuální instanci aplikace pro Chrome.
   * **registerCallback** je funkce zpětného volání, která je volána v případě, že skončí volání registrace služby GCM.
   * **registerWithNH** je druhá obslužná rutina kliknutí na tlačítko, která provádí registraci do Notification Hubs. Získává `hubName` a `connectionString` (které zadal uživatel) a sestavuje volání REST API pro registraci do Notification Hubs.
   * **splitConnectionString** a **generateSaSToken** jsou pomocné funkce, které představují javascriptovou implementaci procesu vytvoření tokenu SaS, jenž se musí použít ve všech voláních REST API. Další informace najdete v tématu [Běžné koncepty](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** je funkce, která provádí volání HTTP REST do Azure Notification Hubs.
   * **registrationPayload** definuje datovou část registrace XML. Další informace najdete v tématu o [vytvoření NH REST API registrace]. V ní musíte aktualizovat ID registrace na hodnotu získanou ze služby GCM.
   * **client** je instance **XMLHttpRequest**, kterou aplikace používá k odeslání požadavku HTTP POST. Hlavičku `Authorization` aktualizujte na `sasToken`. Pokud se toto volání dokončí úspěšně, bude instance aplikace pro Chrome zaregistrována do Azure Notification Hubs.

Celková struktura složek tohoto projektu by měla vypadat přibližně takto: ![Aplikace pro Google Chrome – Struktura složek][21]

### <a name="set-up-and-test-your-chrome-app"></a>Nastavení a testování aplikace pro Chrome
1. Otevřete prohlížeč Chrome. Otevřete **Rozšíření Chromu** a povolte **Režim pro vývojáře**.
   
    ![Google Chrome – Povolení režimu pro vývojáře][16]
2. Klikněte na **Načíst rozbalené rozšíření** a přejděte na složku, kde jste vytvořili soubory. Volitelně také můžete použít **Chrome Apps & Extensions Developer Tool**. Tento nástroj je sám aplikací pro Chrome (nainstalovanou z Internetového obchodu Chrome) a poskytuje pokročilé schopnosti ladění pro vývoj aplikací pro Chrome.
   
    ![Google Chrome – Načíst rozbalené rozšíření][17]
3. Pokud se aplikace pro Chrome vytvoří bez chyb, zobrazí se.
   
    ![Google Chrome – Zobrazení aplikace pro Chrome][18]
4. Jako ID odesílatele zadejte **číslo projektu**, které jste předtím získali z **Google Cloud Console** a klikněte na **Register with GCM** (Zaregistrovat do služby GCM). Musí se zobrazit zpráva **Registration with GCM succeeded** (Registrace do služby GCM byla úspěšná).
   
    ![Google Chrome – Přizpůsobení aplikace pro Chrome][19]
5. Zadejte **Název centra oznámení** a **DefaultListenSharedAccessSignature**, který jste předtím získali na portálu, a klikněte na **Zaregistrovat do Azure Notification Hubs**. Musí se zobrazit zpráva **Registrace do Notification Hubs byla úspěšná!** a podrobnosti o odpovědi registrace, která obsahuje ID registrace do Azure Notification Hubs.
   
    ![Google Chrome – Zadání podrobností centra oznámení][20]  

## <a name="send"></a>Odeslání oznámení aplikaci pro Chrome
Pro účely testování pošlete nabízené oznámení Chrome pomocí konzolové aplikace .NET. 

> [!NOTE]
> Pomocí veřejného <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">rozhraní REST</a> je možné pomocí Notification Hubs posílat nabízená oznámení z jakéhokoli back-endu. Další ukázky pro více platforem najdete na [portálu dokumentace](https://azure.microsoft.com/documentation/services/notification-hubs/).
> 
> 

1. V nástroji Visual Studio v nabídce **Soubor** vyberte **Nový** a **Projekt**. V části **Visual C#** klikněte na **Windows** a **Konzolová aplikace** a pak na **OK**.  Tímto krokem se vytvoří nový projekt konzolové aplikace.
2. V nabídce **Nástroje** klikněte na **Správce balíčků NuGet** a pak na **Konzola správce balíčků**. V dolním okně se zobrazí konzola správce balíčků.
3. V okně konzoly spusťte následující příkaz:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Otevřete `Program.cs` a přidejte následující příkaz `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Do třídy `Program` přidejte následující metodu:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > Ujistěte se, že používáte připojovací řetězec s **úplným** přístupem, nikoli s přístupem **Naslouchat**. Připojovací řetězec s přístupem **Naslouchat** neposkytuje oprávnění posílat nabízená oznámení.
   > 
   > 
6. Do metody `Main` přidejte následující volání:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Ujistěte se, že je spuštěn Chrome, a spusťte konzolovou aplikaci.
8. Na ploše by se vám mělo zobrazit následující místní oznámení.
   
    ![Google Chrome – Oznámení][13]
9. Když je Chrome spuštěn, můžete pomocí okna Oznámení Chrome na hlavním panelu vidět všechna oznámení.
   
    ![Google Chrome – Seznam oznámení](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> Aplikaci pro Chrome není nutné mít spuštěnou nebo otevřenou v prohlížeči (ale prohlížeč samotný běžet musí). V okně Oznámení pro Chrome získáte celkový pohled na všechna oznámení.
> 
> 

## <a name="next-steps"></a>Další kroky
Další informace o Notification Hubs najdete v [přehledu této služby].

Pokud chcete cílit na konkrétní uživatele, přečtěte si kurz [Nastavení oznámení pro uživatele v Azure Notification Hubs]. 

Pokud chcete své uživatele rozdělit podle specifických skupin, můžete postupovat podle kurzu [Nejnovější zprávy přes Azure Notification Hubs].

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[ukázky využití Notification Hubs v aplikaci pro Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud Console]: http://cloud.google.com/console
[přehledu této služby]: notification-hubs-push-notification-overview.md
[Přehled aplikací pro Chrome]: https://developer.chrome.com/apps/about_apps
[ukázce GCM aplikace pro Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplikace pro Chrome na mobilních zařízeních]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[vytvoření NH REST API registrace]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[knihovnu crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[služba GCM (Google Cloud Messaging) pro Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Nastavení oznámení pro uživatele v Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Nejnovější zprávy přes Azure Notification Hubs]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
