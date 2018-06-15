---
title: Použití centra oznámení s PHP
description: Naučte se používat Azure Notification Hubs z PHP back-end.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 930da7cca312ac6233b337dd7ddac478c3bbee7b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776035"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Jak používat centra oznámení z PHP
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Můžete ke všem funkcím centra oznámení z back-end Java, PHP nebo Ruby, pomocí rozhraní REST centra oznámení, jak je popsáno v tématu MSDN [rozhraní API REST centra oznámení](http://msdn.microsoft.com/library/dn223264.aspx).

V tomto tématu ukážeme postup:

* Vytvoření klienta REST pro funkce Notification Hubs v jazyce PHP;
* Postupujte podle [kurzu Začínáme Get](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro vaši mobilní platformu podle volby implementace části back-end v jazyce PHP.

## <a name="client-interface"></a>Rozhraní klienta
Rozhraní hlavní klienta může poskytovat stejné metody, které jsou k dispozici v [.NET SDK centra oznámení](http://msdn.microsoft.com/library/jj933431.aspx), který umožňuje přímo přeložit všechny výukové programy a ukázky aktuálně k dispozici na tomto webu a přispěli komunity na Internetu.

K dispozici v kódu lze najít [PHP REST obálku ukázka].

Chcete-li například vytvořit klienta:

    $hub = new NotificationHub("connection string", "hubname");    

K odeslání iOS nativní oznámení:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementace
Pokud jste ještě není, postupujte podle kroků [kurzu Začínáme Get] až na poslední část, kde je nutné implementovat back-end.
Navíc pokud chcete, můžete použít kód z [PHP REST obálku ukázka] a přejít přímo na [dokončit kurz](#complete-tutorial) části.

Všechny podrobnosti implementace úplné obálku REST naleznete na [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). V této části popisují jsme PHP implementace hlavní kroky potřebné pro přístup k koncové body REST centra oznámení:

1. Analýza připojovacího řetězce
2. Vygenerování tokenu autorizace
3. Provádění volání protokolu HTTP

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce
Tady je hlavní třída implementace klienta, jejichž konstruktor, který analyzuje připojovací řetězec:

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


### <a name="create-security-token"></a>Vytvoření tokenu zabezpečení
Podrobnosti o vytvoření tokenu zabezpečení jsou k dispozici [zde](http://msdn.microsoft.com/library/dn495627.aspx).
Následující metoda má být přidán do **NotificationHub** v identifikátoru URI aktuální žádosti a přihlašovací údaje extrahovat z připojovacího řetězce na základě třídy k vytvoření tohoto tokenu.

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

### <a name="send-a-notification"></a>Odeslat oznámení
Dejte nám nejdřív definice třídy představující oznámení.

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

Tato třída je kontejner pro nativní oznámení text, nebo sadu vlastností v případě šablony oznámení a sadu hlavičky, která obsahuje formátu (nativní platforma nebo šablony) a vlastnosti specifické pro platformu (např. vlastnost Apple vypršení platnosti a WNS hlavičky).

Odkazovat [dokumentaci rozhraní API REST centra oznámení](http://msdn.microsoft.com/library/dn495827.aspx) a na konkrétní oznámení platformách formátů pro všechny možnosti, které jsou k dispozici.

Díky této třídy, jsme nyní můžete napsat odesílání oznámení metody uvnitř **NotificationHub** třídy.

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

Výše uvedené metody odeslat požadavek HTTP POST koncovému bodu /messages centra oznámení, s správné textu a hlavičky k odesílání oznámení.

## <a name="complete-tutorial"></a>Dokončení tohoto kurzu
Nyní můžete dokončit kurz Začínáme zasláním oznámení z PHP back-end.

Inicializace vašeho centra oznámení klienta (nahraďte název připojovacího řetězce a centra podle pokynů v [kurzu Začínáme Get]):

    $hub = new NotificationHub("connection string", "hubname");    

Pak přidejte kód odeslat v závislosti na svou cílovou platformu mobilních.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store a Windows Phone 8.1 (bez Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 a 8.1 Silverlight
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


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Spuštěním kódu PHP by měl vytvořit nyní oznámení, které jsou na cílovém zařízení.

## <a name="next-steps"></a>Další kroky
V tomto tématu jsme vám ukázal, jak vytvořit jednoduché Java REST klienta pro centra oznámení. Odsud můžete:

* Stáhnout kompletní [PHP REST obálku ukázka], která obsahuje všechny výše uvedený kód.
* Pokračujte ve čtení o centrech oznámení označování funkce v [novinkách kurzu]
* Další informace o odesílání nabízených oznámení pro jednotlivé uživatele v [upozornění uživatelů kurzu]

Další informace naleznete také [středisku pro vývojáře PHP](/develop/php/).

[PHP REST obálku ukázka]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[kurzu Začínáme Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

