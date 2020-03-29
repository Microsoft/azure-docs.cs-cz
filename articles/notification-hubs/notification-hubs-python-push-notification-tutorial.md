---
title: Jak používat centra oznámení v Pythonu
description: Zjistěte, jak používat Centra oznámení Azure z aplikace Pythonu.
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
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313823"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Jak používat centra oznámení z Pythonu

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ke všem funkcím centra oznámení můžete přistupovat z back-endu Java/PHP/Python/Ruby pomocí rozhraní REST centra oznámení, jak je popsáno v článku MSDN [Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Toto je ukázková referenční implementace pro implementaci oznámení odesílá v Pythonu a není oficiálně podporované Oznámení Hub Python SDK. Ukázka byla vytvořena pomocí Pythonu 3.4.

V tomto článku se dozvíte, jak:

- Vytvořte klienta REST pro funkce centra oznámení v Pythonu.
- Odesílejte oznámení pomocí rozhraní Pythonu do rozhraní REST centra oznámení.
- Získejte výpis požadavku/odpovědi HTTP REST pro účely ladění/vzdělávání.

Můžete sledovat [kurz Začínáme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) pro vaši mobilní platformu, kterou si vyberete, a implementovat back-endovou část v Pythonu.

> [!NOTE]
> Rozsah ukázky je omezen pouze na odesílání oznámení a neprovádí žádnou správu registrace.

## <a name="client-interface"></a>Klientské rozhraní

Hlavní klientské rozhraní může poskytovat stejné metody, které jsou k dispozici v sada [SDK centra oznámení .NET](https://msdn.microsoft.com/library/jj933431.aspx). Toto rozhraní umožňuje přímo přeložit všechny výukové programy a ukázky v současné době k dispozici na této stránce, a přispěl komunitou na internetu.

Veškerý kód dostupný najdete v [ukázce obálky Pythonu REST].

Chcete-li například vytvořit klienta:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Odeslání informačnízprávy systému Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementace

Pokud jste tak ještě neučinili, postupujte podle [kurzu Začínáme] až do poslední části, kde je nutné implementovat back-end.

Všechny podrobnosti k implementaci úplného obalu REST naleznete na [msdn](https://msdn.microsoft.com/library/dn530746.aspx). Tato část popisuje implementaci Pythonu hlavních kroků potřebných pro přístup ke koncovým bodům REST v oznamovacích centrech a odesílání oznámení

1. Analýza připojovacího řetězce
2. Generovat autorizační token
3. Odeslání oznámení pomocí rozhraní HTTP REST API

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

### <a name="create-security-token"></a>Vytvoření tokenu zabezpečení

Podrobnosti o vytvoření tokenu zabezpečení jsou k dispozici [zde](https://msdn.microsoft.com/library/dn495627.aspx).
Přidejte následující `NotificationHub` metody do třídy k vytvoření tokenu na základě identifikátoru URI aktuálního požadavku a pověření extrahovaných z připojovacího řetězce.

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

### <a name="send-a-notification-using-http-rest-api"></a>Odeslání oznámení pomocí rozhraní HTTP REST API

Nejprve nechte použít definovat třídu představující oznámení.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Tato třída je kontejner pro tělo nativní oznámení nebo sadu vlastností oznámení šablony, sadu hlaviček, která obsahuje formát (nativní platformu nebo šablonu) a vlastnosti specifické pro platformu (jako je vlastnost vypršení platnosti Apple a hlavičky WNS).

Všechny dostupné možnosti naleznete v [dokumentaci k rozhraním REST API centra oznámení](https://msdn.microsoft.com/library/dn495827.aspx) a k formátům konkrétních platforem pro oznámení.

Nyní s touto třídou napište metody `NotificationHub` oznámení odeslat uvnitř třídy.

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

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

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


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
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

Tyto metody odeslat požadavek HTTP POST do koncového bodu /messages centra oznámení se správným tělem a záhlavími pro odeslání oznámení.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Použití vlastnosti ladění k povolení podrobného protokolování

Povolení vlastnosti ladění při inicializaci centra oznámení zapíše podrobné informace o protokolování o výpisu požadavku HTTP a výpisu odpovědí a také podrobný výsledek odeslání zprávy oznámení.
Vlastnost [TestSend centra oznámení](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) vrátí podrobné informace o výsledku odeslání oznámení.
Chcete-li jej použít - inicializovat pomocí následujícího kódu:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Adresa URL požadavku na odeslání oznámení HTTP se v důsledku toho připojí k řetězci dotazu "test".

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Dokončení kurzu

Nyní můžete dokončit kurz Začínáme odesláním oznámení z back-endu Pythonu.

Inicializovat klienta Notification Hubs (nahraďte připojovací řetězec a název centra podle pokynů v [kurzu Začínáme]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Pak přidejte kód pro odeslání v závislosti na cílové mobilní platformě. Tato ukázka také přidává metody vyšší úrovně pro povolení odesílání oznámení na základě platformy, například send_windows_notification pro windows; send_apple_notification (pro jablko) atd.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8.1 (jiné než Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 a 8.1 Silverlight

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
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Oheň

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

Spuštění kódu Pythonu by mělo vytvořit oznámení, které se zobrazí na cílovém zařízení.

## <a name="examples"></a>Příklady

### <a name="enabling-the-debug-property"></a>Povolení `debug` vlastnosti

Když povolíte příznak ladění při inicializaci NotificationHub, zobrazí se podrobný výpis požadavků a odpovědí HTTP, stejně jako NotificationOutcome, jako je následující, kde můžete pochopit, jaké hlavičky PROTOKOLU HTTP jsou předány v požadavku a jaká odpověď HTTP byla obdržel od centra pro oznamování:

![][1]

Zobrazí se například podrobný výsledek centra oznámení.

- pokud je zpráva úspěšně odeslána službě nabízených oznámení.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Pokud nebyly nalezeny žádné cíle pro jakékoli nabízené oznámení, pak se pravděpodobně zobrazí následující výstup jako odpověď (což znamená, že nebyly nalezeny žádné registrace pro doručení oznámení pravděpodobně proto, že registrace měly některé neshody tagy)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Vysílání informačních zpráv do Windows

Všimněte si záhlaví, která se odešlou při odesílání oznámení informační zprávy všesměrového vysílání klientovi systému Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Odeslat oznámení určující značku (nebo výraz značky)

Všimněte si hlavičky HTTP značky, která se přidá do požadavku HTTP (v níže uvedeném příkladu je oznámení odesláno pouze na registrace s datovou zátěží "sportovní")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Odeslat oznámení určující více značek

Všimněte si, jak se při odeslání více značek změní hlavička HTTP značky.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Oznámení s šablonami

Všimněte si, že formát HTTP záhlaví změny a datové části těla je odeslána jako součást těla požadavku HTTP:

**Klientská strana - registrovaná šablona:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Strana serveru - odeslání datové části:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak vytvořit klienta PythonREST pro centra oznámení. Odsud můžete:

- Stáhněte si celou [ukázku obálky Pythonu REST], která obsahuje veškerý kód v tomto článku.
- Pokračovat ve studiu o funkci označování center oznámení v [kurzu Nejnovější zprávy]
- Pokračovat ve sušárování o funkci Šablony center oznámení v [kurzu Lokalizace zpráv]

<!-- URLs -->
[Ukázka obálky pythonu REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Kurz Začínáme]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Nejnovější zprávy tutorial]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Kurz lokalizace novinek]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
