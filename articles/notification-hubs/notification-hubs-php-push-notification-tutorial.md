---
title: Použití Notification Hubs s PHP
description: Naučte se používat Azure Notification Hubs z back-endu PHP.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4df48475af4b140e4446dde9069eafcc95d9d3b2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213170"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Použití Notification Hubs z PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ke všem funkcím Notification Hubs můžete přistupovat z back-endu Java/PHP/Ruby pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [Notification HUBS REST API](https://msdn.microsoft.com/library/dn223264.aspx).

V tomto tématu si ukážeme, jak:

* Vytvoření klienta REST pro funkce Notification Hubs v PHP;
* Použijte úvodní [kurz](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro vaši mobilní platformu a implementujte část back-endu v php.

## <a name="client-interface"></a>Rozhraní klienta

Hlavní klientské rozhraní může poskytovat stejné metody, které jsou k dispozici v sadě [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx), což vám umožní přímo přeložit všechny kurzy a ukázky, které jsou aktuálně k dispozici na tomto webu, a přispívá komunitou na Internet.

Můžete najít veškerý kód dostupný v [Ukázka obálky PHP REST].

Například pro vytvoření klienta:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Odeslání nativního oznámení iOS:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementace

Pokud jste to ještě neučinili, postupujte podle [Kurz Začínáme] až do poslední části, kde je nutné implementovat back-end.
Také Pokud chcete použít kód z [Ukázka obálky PHP REST] a přejít přímo k části [dokončení kurzu](#complete-tutorial) .

Všechny podrobnosti o implementaci úplné obálky REST najdete na [webu MSDN](https://msdn.microsoft.com/library/dn530746.aspx). V této části popíšeme implementaci PHP hlavních kroků potřebných pro přístup k Notification Hubs koncovým bodům REST:

1. Analýza připojovacího řetězce
2. Generování autorizačního tokenu
3. Provedení volání HTTP

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce

Zde je hlavní třída implementující klienta, jehož konstruktor analyzuje připojovací řetězec:

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

### <a name="create-a-security-token"></a>Vytvoření tokenu zabezpečení

Informace o tom, jak [vytvořit token zabezpečení SAS](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token), najdete v dokumentaci k Azure.

Přidejte do `NotificationHub` třídy metoduprovytvořenítokenunazákladěidentifikátoruURIaktuálnížádostiapřihlašovacíchúdajůextrahovanýchzpřipojovacíhořetězce.`generateSasToken`

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

Nejdřív můžeme definovat třídu reprezentující oznámení.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Tato třída je kontejner pro nativní tělo oznámení nebo sadu vlastností pro případ oznámení šablony a sadu hlaviček, která obsahuje formát (nativní platforma nebo šablona) a vlastnosti specifické pro platformu (například vlastnost vypršení platnosti Apple a WNS). záhlaví).

Všechny dostupné možnosti najdete v [dokumentaci k rozhraním REST API pro Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) a ve formátech konkrétních platforem oznámení.

V ozbrojení této třídy teď můžeme zapsat metody odeslání oznámení uvnitř `NotificationHub` třídy:

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

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
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
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Výše uvedené metody odesílají požadavek HTTP POST do `/messages` koncového bodu centra oznámení se správným textem a hlavičkou pro odeslání oznámení.

## <a name="complete-tutorial"></a>Dokončení kurzu

Nyní můžete kurz Začínáme dokončit odesláním oznámení z back-endu PHP.

Inicializujte klienta Notification Hubs (nahraďte připojovací řetězec a název centra podle pokynů v [Kurz Začínáme]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Pak přidejte kód pro odeslání v závislosti na cílové mobilní platformě.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8,1 (ne Silverlight)

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
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 a 8,1 Silverlight

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

Spuštění kódu PHP by mělo nyní na cílovém zařízení zobrazovat oznámení.

## <a name="next-steps"></a>Další kroky

V tomto tématu jsme ukázali, jak vytvořit jednoduchého klienta Java REST pro Notification Hubs. Tady můžete:

* Stáhněte si úplnou [Ukázka obálky PHP REST], která obsahuje veškerý kód uvedený výše.
* Pokračovat v učení o funkci označování Notification Hubs v [kurzu pro průlomové zprávy]
* Přečtěte si o doručování oznámení jednotlivým uživatelům v [kurz pro oznamování uživatelů]

Další informace najdete v tématu také [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[Ukázka obálky PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Kurz Začínáme]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
