---
title: Postup telefonní hovor z Twilio (PHP) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky jsou pro aplikace PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865754"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Postup telefonní hovor pomocí Twilio v aplikaci PHP v Azure
Následující příklad ukazuje, jak Twilio můžete využít volání z webové stránky PHP hostované v Azure. Výsledné aplikace vyzve uživatele, hodnoty telefonní hovor, jak je znázorněno na následujícím snímku obrazovky.

![Azure volání formulář s využitím Twilio a PHP][twilio_php]

Budete muset následujícím postupem použít kód v tomto tématu:

1. Získání účtu Twilio a ověřování tokenu z vaší [Twilio konzoly][twilio_console]. Začít s Twilio vyhodnotit ceny v [http://www.twilio.com/pricing][twilio_pricing]. Můžete si zaregistrovat zkušební účet v [https://www.twilio.com/try-twilio][try_twilio].
2. Získat [Twilio knihovny pro jazyk PHP](https://github.com/twilio/twilio-php) nebo ji nainstalovat jako balíček HRUŠKAMI. Další informace najdete v tématu [souboru readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Nainstalujte si Azure SDK pro jazyk PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód HTML ukazuje, jak vytvořit webovou stránku (**callform.html**), načte data uživatele pro volání:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Vytvoření kódu pro volání
Následující kód ukazuje, jak sestavit **makecall.php**, která je volána, když uživatel odešle formulář zobrazuje **callform.html**. Následující kód vytvoří zprávu volání a vygeneruje volání. Navíc je nutné používat svůj účet Twilio a ověřování tokenu z [Twilio konzoly] [ twilio_console] místo zástupné hodnoty přiřazené **$sid** a **$token** v následujícím kódu.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Kromě vytváření volání **makecall.php** zobrazí některá metadata volání tak, jak je znázorněno na obrázku níže. Další informace o metadatech volání najdete v tématu [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure volání odpovědi pomocí Twilio a PHP][twilio_php_response]

## <a name="run-the-application"></a>Spuštění aplikace
Dalším krokem je [nasazení aplikace Azure Web Apps s Gitem](app-service/app-service-web-get-started-php.md) (v případě, že ne všechny informace je relevantní). 

## <a name="next-steps"></a>Další kroky
Tento kód byl poskytnut tak, aby zobrazovalo základních funkcí pomocí Twilio v jazyce PHP v Azure. Před nasazením do Azure v produkčním prostředí, můžete přidat další zpracování chyb a další funkce. Například:

* Místo použití webového formuláře, můžete použít objekty BLOB úložiště Azure nebo databázi SQL pro ukládání telefonních čísel a volání text. Informace o použití objektů BLOB služby Azure storage v jazyce PHP najdete v tématu [pomocí Azure Storage s aplikací PHP][howto_blob_storage_php]. Informace o používání databáze SQL v jazyce PHP najdete v tématu [pomocí SQL Database pomocí aplikace PHP][howto_sql_azure_php].
* **Makecall.php** kód používá URL poskytnutou Twilio ([http://twimlets.com/message][twimlet_message_url]) můžete zadat odpověď Twilio Markup Language (TwiML), které informují Twilio postup při volání. Například může obsahovat TwiML, která je vrácena `<Say>` akci, která je výsledkem text použitém k příjemce volání. Místo použití URL poskytnutou Twilio, může vytvořit vlastní služba neodpoví na požadavek na Twilio; Další informace najdete v tématu [jak Twilio použijte pro hlasový a možnosti serveru SMS v jazyce PHP][howto_twilio_voice_sms_php]. Další informace o TwiML lze najít na [http://www.twilio.com/docs/api/twiml][twiml]a další informace o `<Say>` a ostatní operace Twilio naleznete na adrese [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Přečtěte si pokyny zabezpečení Twilio v [https://www.twilio.com/docs/security][twilio_docs_security].

Další informace o Twilio najdete v tématu [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Viz také
* [Jak používat Twilio pro hlasový a možnosti serveru SMS v jazyce PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
