---
title: Použití Notification Hubs s Pythonem
description: Naučte se používat Azure Notification Hubs z aplikace Python.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f964957b916c6841da097f93173b0306bb65c8a4
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576019"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Použití Notification Hubs z Pythonu

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ke všem funkcím Notification Hubs můžete přistupovat z back-endu Java/PHP/Python/Ruby pomocí rozhraní REST centra oznámení, jak je popsáno v článku na webu MSDN [Notification Hubs rozhraní REST API](/previous-versions/azure/reference/dn223264(v=azure.100)).

> [!NOTE]
> Toto je ukázková referenční implementace pro implementaci oznámení odesílá se v Pythonu a není oficiálně podporovaná pro Centrum oznámení SDK pro Python. Vzorek byl vytvořen pomocí Python 3,4.

V tomto článku se dozvíte, jak:

- Sestavte klienta REST pro funkce Notification Hubs v Pythonu.
- Odesílání oznámení pomocí rozhraní Pythonu do rozhraní REST API centra oznámení.
- Získejte výpis žádosti a odpovědi HTTP REST pro ladění/vzdělávací účely.

Můžete postupovat podle [kurzu Začínáme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) pro vaši mobilní platformu, která je implementací back-endové části v Pythonu.

> [!NOTE]
> Rozsah ukázky je omezený jenom pro posílání oznámení a neprovádí žádnou správu registrace.

## <a name="client-interface"></a>Rozhraní klienta

Hlavní klientské rozhraní může poskytovat stejné metody, které jsou k dispozici v sadě [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx). Toto rozhraní vám umožní přímo přeložit všechny kurzy a ukázky, které jsou aktuálně k dispozici na tomto webu, a přispívá komunitou na internetu.

Veškerý kód, který je k dispozici v [Pythonu], najdete v ukázce obálky s REST.

Například pro vytvoření klienta:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Odeslání informační zprávy systému Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementace

Pokud jste to ještě neučinili, postupujte podle [kurzu Začínáme] až do poslední části, kde je nutné implementovat back-end.

Všechny podrobnosti o implementaci úplné obálky REST najdete na [webu MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). Tato část popisuje implementaci Pythonu hlavních kroků potřebných pro přístup k Notification Hubs koncovým bodům REST a odesílání oznámení.

1. Analýza připojovacího řetězce
2. Generování autorizačního tokenu
3. Odeslání oznámení pomocí REST API HTTP

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce

Zde je hlavní třída implementující klienta, jehož konstruktor analyzuje připojovací řetězec:

```python
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
```

### <a name="create-security-token"></a>Vytvořit token zabezpečení

Podrobnosti o vytvoření tokenu zabezpečení jsou k dispozici [zde](/previous-versions/azure/reference/dn495627(v=azure.100)).
Přidejte následující metody do `NotificationHub` třídy pro vytvoření tokenu na základě identifikátoru URI aktuální žádosti a přihlašovacích údajů extrahovaných z připojovacího řetězce.

```python
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
```

### <a name="send-a-notification-using-http-rest-api"></a>Odeslání oznámení pomocí REST API HTTP

Nejdřív nechte použít definovat třídu reprezentující oznámení.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'gcm',
                         'windows', 'windowsphone', "adm", "baidu"]
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
```

Tato třída je kontejner pro nativní tělo oznámení nebo sadu vlastností oznámení šablony, sadu hlaviček, která obsahuje formát (nativní platforma nebo šablona) a vlastnosti specifické pro platformu (například vlastnost vypršení platnosti Apple a hlavičky WNS).

Všechny dostupné možnosti najdete v [dokumentaci k rozhraním REST API pro Notification Hubs](/previous-versions/azure/reference/dn495827(v=azure.100)) a ve formátech konkrétních platforem oznámení.

Nyní s touto třídou zapište metody odeslání oznámení uvnitř `NotificationHub` třídy.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
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


def send_google_notification(self, payload, tags=""):
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
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

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
```

Tyto metody odesílají požadavek HTTP POST do koncového bodu/Messages vašeho centra oznámení se správným textem a hlavičkou pro odeslání oznámení.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Použití vlastnosti Debug k povolení podrobného protokolování

Povolení vlastnosti ladění při inicializaci centra oznámení zapisuje podrobné informace o protokolování požadavků HTTP a výpisu odpovědí a také podrobný výsledek odeslání zprávy s oznámením.
[Vlastnost Notification Hubs TestSend](/previous-versions/azure/reference/dn495827(v=azure.100)) vrací podrobné informace o výsledku odeslání oznámení.
Pokud ho chcete použít, inicializujte ho pomocí následujícího kódu:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Adresa URL žádosti o odeslání centra oznámení se jako výsledek připojí pomocí řetězce dotazu "test".

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Dokončení kurzu

Nyní můžete kurz Začínáme dokončit odesláním oznámení z back-endu Pythonu.

Inicializujte klienta Notification Hubs (nahraďte připojovací řetězec a název centra podle pokynů v [kurzu Začínáme]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Pak přidejte kód pro odeslání v závislosti na cílové mobilní platformě. Tato ukázka také přidá metody vyšší úrovně, aby bylo možné odesílat oznámení na základě platformy, například send_windows_notification pro systém Windows. send_apple_notification (pro Apple) atd.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8,1 (ne Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 a 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
gcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_google_notification(gcm_payload)
```

### <a name="kindle-fire"></a>Kindle požár

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Spuštění kódu Pythonu by mělo mít na cílovém zařízení zobrazeno oznámení.

## <a name="examples"></a>Příklady

### <a name="enabling-the-debug-property"></a>Povolení `debug` vlastnosti

Pokud povolíte příznak ladění při inicializaci NotificationHub, zobrazí se detailní požadavek HTTP a výpis odpovědí a také NotificationOutcome podobný následujícímu, kde můžete zjistit, jaké hlavičky protokolu HTTP se v požadavku předávají a jaká odpověď HTTP byla přijata z centra oznámení:

![Snímek obrazovky konzoly s podrobnostmi o požadavku H T T P a výpisu odpovědí a zpráv o výsledku oznámení, které jsou červeně označeny.][1]

Vidíte například podrobný výsledek centra oznámení.

- Po úspěšném odeslání zprávy do služby nabízených oznámení.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Pokud nebyly pro žádné nabízené oznámení nalezeny žádné cíle, pravděpodobně se zobrazí následující výstup jako odpověď (což znamená, že nebyly nalezeny žádné registrace pro doručení oznámení pravděpodobně proto, že registrace obsahovala neodpovídající značky).
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Oznámení informačních zpráv všesměrového vysílání ve Windows

Všimněte si hlaviček, které se odesílají při odesílání oznámení informační zprávy všesměrového vysílání klientovi Windows.

```python
hub.send_windows_notification(wns_payload)
```

![Snímek obrazovky konzoly s podrobnostmi o požadavku H T T P a formátem Service Bus oznámení a hodnotami typu X W N S červeně.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Odeslat oznámení s určením značky (nebo výrazu značky)

Všimněte si hlavičky značek HTTP, které se přidají do požadavku protokolu HTTP (v níže uvedeném příkladu je oznámení odesíláno pouze do registrací s "sportovní" částkou.

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Snímek obrazovky konzoly s podrobnostmi o požadavku H T T P a Service Busovém formátu oznámení, Service Bus značka oznámení a hodnoty typu X W N S uvedené červeně.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Odeslat oznámení s určením více značek

Všimněte si, jak se při posílání více značek změní záhlaví HTTP značek.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Snímek obrazovky konzoly s podrobnostmi o požadavku H T T P a formátu oznámení Service Bus, více značek Service Bus oznámení a hodnot typu X W N S popsaných červeně.][4]

### <a name="templated-notification"></a>Oznámení v šabloně

Všimněte si, že se změní hlavička HTTP formátu a tělo datové části se odešle jako součást textu požadavku HTTP:

**Šablona registrovaná na straně klienta:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serverová strana – posílá datovou část:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Snímek obrazovky konzoly s podrobnostmi o požadavku H T T P a typem obsahu a hodnotami formátu oznámení Service Bus červeně.][5]

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak vytvořit klienta Python REST pro Notification Hubs. Odsud můžete:

- Stáhněte si kompletní [ukázku obálky REST v Pythonu], která obsahuje veškerý kód v tomto článku.
- Pokračovat v učení o funkci označování Notification Hubs v [kurzu průlomové novinky]
- Pokračovat v učení o funkci šablon Notification Hubs v [kurzu lokalizace zpráv]

<!-- URLs -->
[Ukázka obálky Python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Úvodní kurz]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Kurz pro průlomové novinky]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Kurz lokalizace zpráv]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
