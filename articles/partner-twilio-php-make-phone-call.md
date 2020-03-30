---
title: Jak telefonovat z Twilio (PHP) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky jsou určeny pro aplikaci PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637325"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak provést telefonní hovor pomocí Twilio v php aplikaci v Azure
Následující příklad ukazuje, jak můžete pomocí Twilio volat z webové stránky PHP hostované v Azure. Výsledná aplikace vyzve uživatele k zadání hodnot telefonního hovoru, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a PHP][twilio_php]

Chcete-li použít kód v tomto tématu, budete muset provést následující kroky:

1. Získejte účet Twilio a ověřovací token z [konzoly Twilio][twilio_console]. Chcete-li začít s Twilio, vyhodnoťte ceny na [https://www.twilio.com/pricing][twilio_pricing]. Zkušební účet si můžete zaregistrovat na adrese [https://www.twilio.com/try-twilio][try_twilio].
2. Získejte [knihovnu Twilio pro PHP](https://github.com/twilio/twilio-php) nebo ji nainstalujte jako balíček PEAR. Další informace naleznete v [souboru readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Nainstalujte Azure SDK pro PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód HTML ukazuje, jak vytvořit webovou stránku **(callform.html),** která načítá uživatelská data pro volání:

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
Následující kód ukazuje, jak vytvořit **makecall.php**, který se nazývá, když uživatel odešle formulář zobrazený **callform.html**. Níže uvedený kód vytvoří zprávu o volání a vygeneruje volání. Také nezapomeňte použít účet Twilio a ověřovací token z [konzoly Twilio][twilio_console] namísto zástupných hodnot přiřazených **k $sid** a **$token** v níže uvedeném kódu.

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
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Kromě volání, **makecall.php** zobrazí některá metadata volání, jak je znázorněno na obrázku níže. Další informace o metadatech [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]volání naleznete v tématu .

![Odpověď na volání Azure pomocí Twilio a PHP][twilio_php_response]

## <a name="run-the-application"></a>Spuštění aplikace
Dalším krokem je [nasazení aplikace do Azure Web Apps s Gitem](app-service/app-service-web-get-started-php.md) (i když ne všechny informace, které jsou relevantní). 

## <a name="next-steps"></a>Další kroky
Tento kód byl poskytnut tak, aby vám ukázal základní funkce pomocí Twilio v PHP v Azure. Před nasazením do Azure v produkčním prostředí můžete přidat další zpracování chyb nebo jiné funkce. Například:

* Místo použití webového formuláře můžete k ukládání telefonních čísel a textu volání použít objekty BLOB úložiště Azure nebo databázi SQL. Informace o používání objektů BLOB úložiště Azure v PHP najdete v [tématu Používání Azure Storage s aplikacemi PHP][howto_blob_storage_php]. Informace o používání sql databasev PHP najdete v [tématu Použití databáze SQL s aplikacemi PHP][howto_sql_azure_php].
* Kód **makecall.php** používá adresu URL poskytnutou[https://twimlets.com/message][twimlet_message_url]twilio ( ) k poskytnutí odpovědi twilio markup language (TwiML), která informuje Twilio, jak pokračovat v hovoru. Například TwiML, který je vrácena může obsahovat `<Say>` sloveso, které má za následek text vyslovený příjemci volání. Namísto použití adresy URL poskytované twilio, můžete vytvořit vlastní službu reagovat na žádost Twilio; Další informace naleznete v tématu [Jak používat Twilio pro hlasové a SMS schopnosti v PHP][howto_twilio_voice_sms_php]. Více informací o TwiML [https://www.twilio.com/docs/api/twiml][twiml]naleznete na na `<Say>` . [https://www.twilio.com/docs/api/twiml/say][twilio_say]
* Přečtěte si bezpečnostní pokyny [https://www.twilio.com/docs/security][twilio_docs_security]Twilio na adrese .

Další informace o Twilio [https://www.twilio.com/docs][twilio_docs]naleznete v tématu .

## <a name="see-also"></a>Viz také
* [Jak používat Twilio pro hlasové a SMS schopnosti v PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
