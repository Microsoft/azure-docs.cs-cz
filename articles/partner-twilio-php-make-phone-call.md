---
title: Jak uskutečnit telefonní hovor z Twilio (PHP) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky jsou pro aplikaci PHP.
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
ms.openlocfilehash: e75b5a265ba85813581e39a548d897ea57a33da6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080554"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Jak uskutečnit telefonní hovor pomocí Twilio v aplikaci PHP v Azure
Následující příklad ukazuje, jak můžete pomocí Twilio uskutečnit volání z webové stránky PHP hostované v Azure. Výsledná aplikace zobrazí uživateli výzvu k zadání hodnot telefonních hovorů, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a PHP][twilio_php]

Chcete-li použít kód v tomto tématu, musíte provést následující:

1. Získejte účet Twilio a ověřovací token z [konzoly Twilio][twilio_console]. Pokud chcete začít s Twilio, vyhodnoťte ceny na adrese [https://www.twilio.com/pricing][twilio_pricing] . Zkušební účet si můžete zaregistrovat na adrese [https://www.twilio.com/try-twilio][try_twilio] .
2. Získejte [knihovnu Twilio pro php](https://github.com/twilio/twilio-php) nebo ji nainstalujte jako balíček pro hrušky. Další informace najdete v [souboru Readme](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Nainstalujte sadu Azure SDK pro PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód HTML ukazuje, jak vytvořit webovou stránku (**callform.html**), která načte uživatelská data pro volání:

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
Následující kód ukazuje, jak sestavit **MakeCall. php**, který se volá, když uživatel odešle formulář zobrazený pomocí **callform.html**. Kód zobrazený níže vytvoří zprávu volání a vygeneruje volání. Také se ujistěte, že používáte účet Twilio a ověřovací token z [konzoly Twilio][twilio_console] místo zástupných hodnot přiřazených **$SID** a **$token** v následujícím kódu.

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

Kromě toho, že **MakeCall. php** , zobrazuje některá metadata volání, jak je znázorněno na obrázku níže. Další informace o metadatech volání naleznete v tématu [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties] .

![Odpověď volání Azure pomocí Twilio a PHP][twilio_php_response]

## <a name="run-the-application"></a>Spuštění aplikace
Dalším krokem je [nasazení vaší aplikace do Azure Web Apps v Gitu](app-service/quickstart-php.md) (ale ne všechny informace, které jsou relevantní). 

## <a name="next-steps"></a>Další kroky
Tento kód vám poskytne základní funkce využívající Twilio v PHP v Azure. Před nasazením do Azure v produkčním prostředí budete možná chtít přidat další zpracování chyb nebo jiné funkce. Například:

* Místo používání webového formuláře můžete použít objekty blob služby Azure Storage nebo SQL Database k ukládání telefonních čísel a volání textu. Informace o použití objektů BLOB služby Azure Storage v PHP najdete v tématu [použití Azure Storage s aplikacemi php][howto_blob_storage_php]. Informace o použití SQL Database v PHP najdete v tématu [použití SQL Database s aplikacemi php][howto_sql_azure_php].
* Kód **MakeCall. php** používá Twilio adresu URL ( [https://twimlets.com/message][twimlet_message_url] ) k poskytnutí odpovědi Twilio Markup Language (TwiML), která informuje Twilio, jak pokračovat v volání. Například vrácený TwiML může obsahovat `<Say>` příkaz, který je výsledkem mluveného textu pro příjemce volání. Místo použití adresy URL poskytnuté v Twilio můžete vytvořit vlastní službu, která bude reagovat na žádost Twilio. Další informace najdete v tématu [použití Twilio pro hlasové a SMS funkce v php][howto_twilio_voice_sms_php]. Další informace o TwiML najdete na adrese a [https://www.twilio.com/docs/api/twiml][twiml] Další informace o `<Say>` a dalších příkazech Twilio najdete na adrese [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Přečtěte si pokyny pro zabezpečení Twilio na adrese [https://www.twilio.com/docs/security][twilio_docs_security] .

Další informace o Twilio najdete v tématu [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Viz také
* [Použití Twilio pro hlasové funkce a možnosti SMS v PHP](partner-twilio-php-how-to-use-voice-sms.md)

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
