---
title: Jak používat Notification Hubs s PHP
description: Zjistěte, jak používat Azure Notification Hubs z PHP back-endu.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: dd153558e8cf3dbe64bc2693a72d16934dfb23cb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446689"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak používat Notification Hubs z PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Dostanete všechny funkce Notification Hubs z Javy/PHP a Ruby back-endu pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [rozhraní REST API Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx).

V tomto tématu vám ukážeme, jak:

* Vytvoření klienta REST pro funkce Notification Hubs v jazyce PHP
* Postupujte podle [kurz Začínáme](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro mobilní platformy, implementace část back-end v jazyce PHP.

## <a name="client-interface"></a>Rozhraní klienta

Hlavní klientského rozhraní můžete zadat stejné metody, které jsou k dispozici v [sadu SDK serveru .NET Notification Hubs](https://msdn.microsoft.com/library/jj933431.aspx), umožňuje přímo přeložit těchto kurzů a ukázek aktuálně k dispozici na tomto webu a přispěla Komunita na Internetu.

K dispozici v kódu lze najít [Ukázka obálky PHP REST].

Chcete-li například vytvořit klienta:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

K odeslání zařízení s Iosem nativní oznámení:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementace

Pokud jste dosud provedli, postupujte [kurz Začínáme] až na poslední část, ve kterém bude nutné implementovat back-endu.
Navíc pokud chcete, můžete použít kód z [Ukázka obálky PHP REST] a přejít přímo na [absolvování tohoto kurzu](#complete-tutorial) části.

Všechny podrobnosti, které chcete implementovat úplné obálku REST můžete najít na [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). V této části popisujeme PHP provádění hlavní kroky potřebné pro přístup k koncové body Notification Hubs REST:

1. Analýza připojovacího řetězce
2. Vygenerování tokenu autorizace
3. Provádění volání HTTP

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce

Tady je hlavní třída implementace klienta, jejíž konstruktor, který analyzuje připojovací řetězec:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Vytvořit token zabezpečení

Přečtěte si dokumentaci k Azure informace o tom, jak [vytvořit Token SAS zabezpečení](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Přidat `generateSasToken` metodu `NotificationHub` třídy za účelem vytvoření tokenu podle identifikátoru URI aktuální žádosti a extrahovat z připojovacího řetězce přihlašovacích údajů.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Odeslání oznámení

Nejprve definujte dejte nám Třída reprezentující oznámení.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Tato třída slouží jako kontejner pro obsah nativních oznámení, nebo sadu vlastností v případě šablony oznámení a sadu hlaviček, který obsahuje formát (nativní platformy nebo šablony) a vlastnosti specifické pro platformu (např. vlastnost vypršení platnosti Apple a služby nabízených oznámení Windows záhlaví).

Odkazovat [dokumentace k rozhraní REST API Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) , který se naformátuje na platformách konkrétní oznámení pro všechny možnosti, které jsou k dispozici.

Ozbrojené s touto třídou, jsme se teď dá zapisovat odeslat oznámení metody uvnitř `NotificationHub` třídy:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Výše uvedené metody odeslat požadavek HTTP POST `/messages` koncového bodu vašeho centra oznámení, s textem správné a hlavičky k odesílání oznámení.

## <a name="complete-tutorial"></a>Dokončení tohoto kurzu

Nyní můžete dokončit kurz Začínáme zasláním oznámení z back-end PHP.

Inicializace klienta Notification Hubs (nahraďte název připojovacího řetězce a Centrum podle pokynů v tématu [kurz Začínáme]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Pak přidejte odeslat kód. v závislosti na vaše mobilní platformy.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8.1 (bez Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 a 8.1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Spouštění kódu PHP mohou být vráceny nyní oznámení uvedených na cílovém zařízení.

## <a name="next-steps"></a>Další kroky

V tomto tématu jsme vám ukázal, jak vytvořit jednoduchý klienta REST Javy pro Notification Hubs. Odsud můžete:

* Stáhněte si kompletní [Ukázka obálky PHP REST], která obsahuje všechny výše uvedený kód.
* Pokračujte ve čtení o Notification Hubs označení funkce v [novinkách kurzu]
* Další informace o odesílání nabízených oznámení pro jednotlivé uživatele v [oznamování uživatelům pomocí kurzu]

Další informace najdete v tématu taky [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[Ukázka obálky PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Kurz Začínáme]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
