---
title: Jak používat centra oznámení s Pythonem
description: Naučte se používat Azure Notification Hubs z Python back-end.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 32953bacb8fdb135d5f3e0e9324218d2a71b0818
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777014"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak používat centra oznámení z Pythonu
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Můžete ke všem funkcím centra oznámení z Java/PHP nebo Python nebo Ruby back-end pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [rozhraní API REST centra oznámení](http://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> To je ukázka odkazu implementace pro implementaci odešle oznámení v Pythonu a není oficiálně podporované Python SDK centra oznámení.
> 
> Tato ukázka je napsané v Pythonu 3.4.
> 
> 

Tento článek ukazuje, jak na:

* Vytvoření klienta REST centra oznámení funkcí v Pythonu.
* Odesílání oznámení pomocí rozhraní Python rozhraní API REST centra oznámení. 
* Získá výpis požadavků a odpovědí HTTP REST pro ladění vzdělávací účely. 

Můžete provést [kurzu Začínáme Get](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) pro vaši mobilní platformu podle volby implementace části back-end v Pythonu.

> [!NOTE]
> Obor vzorku je omezena pouze k odesílání oznámení a neprovádí žádné registrace správy.
> 
> 

## <a name="client-interface"></a>Rozhraní klienta
Rozhraní hlavní klienta může poskytovat stejné metody, které jsou k dispozici v [.NET SDK centra oznámení](http://msdn.microsoft.com/library/jj933431.aspx). Toto rozhraní umožňuje přímo přeložit všechny výukové programy a ukázky aktuálně k dispozici na tomto webu a přispěli komunity na Internetu.

K dispozici v kódu lze najít [Python REST obálku ukázka].

Chcete-li například vytvořit klienta:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

K odeslání oznámení s informační zprávou Windows:

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

## <a name="implementation"></a>Implementace
Pokud jste ještě není, postupujte podle kroků [kurzu Začínáme Get] až na poslední část, kde je nutné implementovat back-end.

Všechny podrobnosti implementace úplné obálku REST naleznete na [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Tato část popisuje Python implementace hlavní kroky potřebné pro přístup k koncové body REST centra oznámení a odesílání oznámení

1. Analýza připojovacího řetězce
2. Vygenerování tokenu autorizace
3. Odeslání oznámení pomocí rozhraní HTTP REST API

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce
Tady je hlavní třída implementace klienta, jejichž konstruktor analyzuje připojovací řetězec:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"

        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug

            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")

            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Vytvoření tokenu zabezpečení
Podrobnosti o vytvoření tokenu zabezpečení jsou k dispozici [zde](http://msdn.microsoft.com/library/dn495627.aspx).
Přidejte následující metody **NotificationHub** v identifikátoru URI aktuální žádosti a přihlašovací údaje extrahovat z připojovacího řetězce na základě třídy k vytvoření tohoto tokenu.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Odeslání oznámení pomocí rozhraní HTTP REST API
První, umožňují použít definice třídy představující oznámení.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

            self.format = notification_format
            self.payload = payload

            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Tato třída je kontejner pro nativní oznámení textu nebo sadu vlastností šablony oznámení, sadu hlavičky, která obsahuje formátu (nativní platforma nebo šablony) a vlastnosti specifické pro platformu (např. vlastnost Apple vypršení platnosti a hlavičky WNS).

Odkazovat [dokumentaci rozhraní API REST centra oznámení](http://msdn.microsoft.com/library/dn495827.aspx) a na konkrétní oznámení platformách formátů pro všechny možnosti, které jsou k dispozici.

Teď s touto třídou zapsat odesílání oznámení metody uvnitř **NotificationHub** třídy.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Tyto metody odeslat požadavek HTTP POST koncovému bodu /messages centra oznámení, s správné textu a hlavičky k odesílání oznámení.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Pomocí vlastnosti ladění povolit podrobné protokolování
Povolení ladění vlastnost při inicializaci centra oznámení zapíše podrobné protokolování informace o požadavku HTTP a odpovědi výpisu, jakož i podrobné zprávy oznámení odeslat výsledek. [TestSend centra oznámení vlastnost](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) vrací podrobné informace o výsledek odeslání oznámení. Použít-inicializovat pomocí následujícího kódu:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Žádost o odeslání oznámení centra adresy URL protokolu HTTP získá spolu s "test" querystring v důsledku. 

## <a name="complete-tutorial"></a>Dokončení tohoto kurzu
Nyní můžete dokončit kurz Začínáme zasláním oznámení z back-end Python.

Inicializace vašeho centra oznámení klienta (nahraďte název připojovacího řetězce a centra podle pokynů v [kurzu Začínáme Get]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Pak přidejte kód odeslat v závislosti na svou cílovou platformu mobilních. Tato ukázka přidá také vyšší úrovně metody k povolení odesílání oznámení podle platformy, například send_windows_notification pro systém windows. send_apple_notification (pro apple) atd. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8.1 (bez Silverlight)
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 a 8.1 Silverlight
    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS
    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Spuštěním kódu Python by měl vytvořit oznámení, které jsou na cílovém zařízení.

## <a name="examples"></a>Příklady:
### <a name="enabling-debug-property"></a>Povolení ladění vlastnost
Když povolíte příznak ladění při inicializaci NotificationHub, uvidíte podrobné požadavku HTTP a odpovědi výpisu, jakož i NotificationOutcome takto kde pochopit, jaké hlavičky protokolu HTTP se předávají v požadavku a byl co odpovědi HTTP přijaté z centra oznámení: ![][1]

Uvidíte, například podrobný výsledek centra oznámení. 

* Když zprávu úspěšně odeslat na službu nabízených oznámení. 
  
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
* Pokud neexistují žádné cíle najít pro všechny nabízené oznámení, pak pravděpodobně chcete zobrazit následující výstup jako odpověď (což znamená, že neexistují žádné registrace nalezen pravděpodobně doručit oznámení, protože registrace měl některé neshoda značky)
  
        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Vysílání informační zpráva do systému Windows
Všimněte si seznam hlaviček, které získat odeslaná při odesílání oznámení s informační zprávou všesměrového vysílání pro klienta systému Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Odeslat oznámení o určení značky (nebo výraz označení)
Všimněte si hlavičku HTTP značky, který získá přidán do požadavku HTTP (v příkladu níže, odešle se oznámení pouze na registrací s odebranou datovou částí "sports")

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Odeslat oznámení o určení více značek
Všimněte si, jak hlavičku HTTP značky změní, když jsou odesílány více značek. 

    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Použitím šablon oznámení
Všimněte si, že změní hlavičku formátu HTTP a text datové části je odeslána jako část obsahu žádosti HTTP:

**Na straně klienta - registrované šablony**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";

**Na straně serveru – odesílání datové části**

        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]

## <a name="next-steps"></a>Další kroky
Tento článek vám ukázal, jak vytvořit klienta Python REST centra oznámení. Odsud můžete:

* Stáhnout kompletní [Python REST obálku ukázka], která obsahuje všechny kód v tomto článku.
* Pokračujte ve čtení o označování funkce v centrech oznámení [novinkách kurzu]
* Pokračujte ve čtení o funkce šablon centra oznámení v [kurzu lokalizace zprávy]

<!-- URLs -->
[Python REST obálku ukázka]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[kurzu Začínáme Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[novinkách kurzu]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[kurzu lokalizace zprávy]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png

