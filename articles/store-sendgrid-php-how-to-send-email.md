---
title: Jak používat e-mailové služby SendGrid (PHP) | Dokumentace Microsoftu
description: Zjistěte, jak poslat e-mailu pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v jazyce PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: db3333aa52782ceb949ef3f46a903b618f6e3f2f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693020"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak používat službu e-mailu Sendgridu z PHP

Tato příručka ukazuje, jak k provádění běžných programovacích úloh s e-mailové služby SendGrid v Azure. Ukázky jsou napsané v jazyce PHP.
Mezi popsané scénáře patří **vytváření e-mailu**, **odesílání e-mailů**, a **přidávání příloh**. Další informace o SendGrid a odesílání e-mailu, najdete v článku [další kroky](#next-steps) oddílu.

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailové služby SendGrid?
SendGrid je [založené na cloudu e-mailové služby] , která poskytuje spolehlivé [doručování transakční e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilní API, která usnadňují vlastní integraci. Obvyklé scénáře použití SendGrid patří:

* Automatické odesílání oznámení pro zákazníky
* Správa distribuce jsou uvedené pro odesílání zákazníkům měsíčních e letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro takové věci, jako jsou e-mailu blokovaný a odezvy zákazníků
* Generování sestav vám pomůže identifikovat trendy
* Předávání dotazy zákazníků
* E-mailových oznámení z aplikace

Další informace najdete v tématu [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Pomocí služby SendGrid z vaší aplikace PHP

V aplikaci PHP v Azure pomocí Sendgridu vyžaduje žádná zvláštní konfigurace nebo psaní kódu. Protože SendGrid je služba, mohou být přístupné přesně stejným způsobem z cloudové aplikace, jako se můžete z místní aplikace.

## <a name="how-to-send-an-email"></a>Postup: Odeslání e-mailu

Můžete odeslat e-mailu přes SMTP nebo webové rozhraní API, které SendGrid poskytuje.

### <a name="smtp-api"></a>SMTP API

Chcete-li odeslat e-mailu pomocí rozhraní API SendGrid SMTP, použijte *Swift poštovní*, knihovnu založených na komponentách odesílání e-mailů z aplikací PHP. Si můžete stáhnout [Swift poštovní knihovny](https://swiftmailer.symfony.com/) v5.3.0 (použijte [Composer] instalace poštovní Swift). Odesílání e-mailů s knihovnou zahrnuje vytvoření instance `Swift\_SmtpTransport`, `Swift\_Mailer`, a `Swift\_Message` tříd, nastavení příslušné vlastnosti a volání `Swift\_Mailer::send` metody.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Web API
Použití PHP na [curl funkce] [ curl function] k odesílání e-mailu pomocí webového rozhraní API SendGrid.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

Webové rozhraní API Sendgridu je velmi podobný rozhraní REST API, když není skutečně rozhraní RESTful API, protože ve většině volání, jak získat a operací POST zaměnitelné.

## <a name="how-to-add-an-attachment"></a>Postup: Přidat přílohu

### <a name="smtp-api"></a>SMTP API

Odeslání přílohy pomocí rozhraní SMTP API zahrnuje jeden další řádek kódu na ukázkový skript pro odeslání e-mailu s poštovní Swift.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

Další řádek kódu vypadá takto:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Tento řádek kódu volá metodu připojení na `Swift\_Message` objektu a používá statickou metodu `fromPath` na `Swift\_Attachment` třídy k získání a připojit soubor na zprávu.

### <a name="web-api"></a>Web API

Odeslání přílohy pomocí rozhraní Web API je velmi podobný jako poslání e-mailu pomocí rozhraní Web API. Mějte však na paměti, že v následujícím příkladu pole parametrů musí obsahovat tento element:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Příklad:

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postup: Použití filtrů k povolení zápatí, sledování a analýza

SendGrid umožňuje zajistit další e-mailové funkce prostřednictvím *filtry*. Toto jsou nastavení, které lze přidat do e-mailovou zprávu povolit konkrétní funkce, jako například povolení sledování kliknutí, Google analytics, předplatné, sledování a tak dále.

Pomocí vlastnosti filtry můžete na zprávu použity filtry. Každý filtr je určená hodnotu hash, který obsahuje nastavení specifická pro filtr. Následující příklad povolí filtr zápatí a určuje textovou zprávu, která se připojí k dolnímu okraji e-mailové zprávy. V tomto příkladu budeme používat [Knihovna sendgrid php].

Použití [Composer] k instalaci knihovny:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Příklad:  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy služby e-mailu Sendgridu, použijte tyto odkazy na další informace.

* SendGrid dokumentace: <https://sendgrid.com/docs>
* Knihovna SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
* SendGrid speciální nabídka pro zákazníky Azure: <https://sendgrid.com/windowsazure.html>

Další informace najdete v tématu taky [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[založené na cloudu e-mailové služby]: https://sendgrid.com/email-solutions
[doručování transakční e-mailů]: https://sendgrid.com/transactional-email
[Knihovna sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
