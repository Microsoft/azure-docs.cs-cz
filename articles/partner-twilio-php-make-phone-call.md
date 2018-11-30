---
title: Jak volat z Twilia (PHP) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky jsou pro aplikaci PHP.
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
ms.openlocfilehash: 6d31cd66138bd20cf09b3dc569d0ac83addaaa95
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426770"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak telefonování pomocí Twilio aplikace PHP v Azure
Následující příklad ukazuje, jak Twilio můžete uskutečnit volání z webové stránky PHP hostované v Azure. Výsledné aplikace se zobrazí výzva pro hodnoty telefonního hovoru, jak je znázorněno na následujícím snímku obrazovky.

![Azure volání formulář s využitím platformy Twilio a PHP][twilio_php]

Bude potřeba ji pomocí kódu v tomto tématu následujícím způsobem:

1. Získání účtu Twilio a ověřování tokenů z vaší [Twilio konzoly][twilio_console]. Abyste mohli začít s platformou Twilio, vyhodnocení ceny na [ https://www.twilio.com/pricing ] [ twilio_pricing]. Si můžete zaregistrovat zkušební účet za [ https://www.twilio.com/try-twilio ] [ try_twilio].
2. Získat [Twilio knihovny pro PHP](https://github.com/twilio/twilio-php) nebo ji nainstalovat jako HRUŠKOVÝ balíček. Další informace najdete v tématu [souboru readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instalace sady Azure SDK pro PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoří webový formulář pro volání
Následující kód HTML ukazuje, jak vytvořit webovou stránku (**callform.html**), která načte data uživatele pro volání:

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
Následující kód ukazuje, jak vytvořit **makecall.php**, která je volána, když uživatel odešle formulář zobrazí **callform.html**. Kód níže vytvoří zprávu volání a vygeneruje volání. Také, nezapomeňte použít účtu Twilio a ověřování tokenů z [Twilio konzoly] [ twilio_console] místo zástupné hodnoty přiřazené **$sid** a  **$token** v následujícím kódu.

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

Kromě toho, volání **makecall.php** některá metadata volání se zobrazí, jak je znázorněno na následujícím obrázku. Další informace o metadatech volání najdete v tématu [ https://www.twilio.com/docs/api/rest/call#instance-properties ] [ twilio_call_properties].

![Azure volání odpovědi HTTP pomocí platformy Twilio a PHP][twilio_php_response]

## <a name="run-the-application"></a>Spuštění aplikace
Dalším krokem je [nasazení aplikace do Azure Web Apps pomocí Git](app-service/app-service-web-get-started-php.md) (v případě, že ne všechny informace je relevantní). 

## <a name="next-steps"></a>Další postup
Tento kód byl poskytnut zobrazit základní funkce s použitím Twilio v PHP v Azure. Před nasazením v produkčním prostředí do Azure, můžete přidat další zpracování chyb nebo jiné funkce. Příklad:

* Místo použití webového formuláře, můžete použít objekty BLOB služby Azure storage nebo SQL Database k ukládání telefonní čísla a volání text. Informace o použití objektů BLOB Azure storage v jazyce PHP najdete v tématu [pomocí služby Azure Storage s aplikací PHP][howto_blob_storage_php]. Informace o použití SQL Database v jazyce PHP najdete v tématu [pomocí SQL Database pomocí aplikace v jazyce PHP][howto_sql_azure_php].
* **Makecall.php** kód používá URL poskytnutou Twilio ([https://twimlets.com/message][twimlet_message_url]) k poskytování odpověď Twilio Markup Language (TwiML), která informuje Twilio, jak postupovat u volání. Například může obsahovat TwiML, který je vrácen `<Say>` příkaz, který má za následek text, se kterým se mluví příjemce volání. Namísto použití URL poskytnutou Twilio, může sestavení vlastní služby reagovat na žádosti pro Twilio; Další informace najdete v tématu [postupy použití Twilia pro hlasové hovory a SMS v jazyce PHP][howto_twilio_voice_sms_php]. Další informace o TwiML najdete [ https://www.twilio.com/docs/api/twiml ] [ twiml]a další informace o `<Say>` a ostatní operace Twilio lze nalézt v [ https://www.twilio.com/docs/api/twiml/say ][twilio_say].
* Přečtěte si pokyny pro zabezpečení platformy Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Další informace o Twilio, naleznete v tématu [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Viz také
* [Postup použití Twilia pro hlasové hovory a SMS v jazyce PHP.](partner-twilio-php-how-to-use-voice-sms.md)

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
