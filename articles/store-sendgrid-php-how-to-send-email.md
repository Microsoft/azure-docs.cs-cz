---
title: Jak používat e-mailovou službu SendGrid (PHP) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odesílat e-maily pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v PHP.
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
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67870914"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak používat sendgrid e-mailovou službu z PHP

Tato příručka ukazuje, jak provádět běžné úlohy programování s e-mailovou službou SendGrid v Azure. Ukázky jsou napsány v PHP.
Zahrnuté scénáře zahrnují **vytváření e-mailů**, **odesílání e-mailů**a **přidávání příloh**. Další informace o sendgridu a odesílání e-mailů najdete v části [Další kroky.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [cloudová e-mailová služba,] která poskytuje spolehlivé [transakční doručování e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními api, která usnadňují vlastní integraci. Běžné scénáře použití SendGrid zahrnují:

* Automatické odesílání příjmů zákazníkům
* Správa distribučních seznamů pro zasílání měsíčních e-letců a speciálních nabídek
* Shromažďování metrik v reálném čase pro věci, jako je blokovaný e-mail a reakce zákazníků
* Generování sestav, které pomáhají identifikovat trendy
* Předávání dotazů zákazníků
* E-mailová oznámení z vaší aplikace

Další informace naleznete [https://sendgrid.com][https://sendgrid.com]v tématu .

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Použití SendGrid z vaší PHP aplikace

Použití SendGrid v aplikaci Azure PHP nevyžaduje žádnou speciální konfiguraci nebo kódování. Protože SendGrid je služba, lze k ní přistupovat přesně stejným způsobem z cloudové aplikace, jak je to možné z místní aplikace.

## <a name="how-to-send-an-email"></a>Postup: Odeslání e-mailu

E-mail můžete odesílat pomocí smtp nebo webového rozhraní API poskytovaného sendgridem.

### <a name="smtp-api"></a>SMTP API

Chcete-li odeslat e-mail pomocí rozhraní SendGrid SMTP API, použijte *Swift Mailer*, knihovnu založenou na komponentách pro odesílání e-mailů z aplikací PHP. Zde si můžete stáhnout [Swift Mailer knihovna](https://swiftmailer.symfony.com/) v5.3.0 (použijte [Composer] nainstalovat Swift Mailer). Odesílání e-mailů s knihovnou `Swift\_SmtpTransport`zahrnuje `Swift\_Mailer`vytváření `Swift\_Message` instancí , a tříd, nastavení příslušných vlastností a volání `Swift\_Mailer::send` metody.

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
Pomocí funkce [curl][curl function] php můžete odesílat e-maily pomocí webového rozhraní SendGrid.

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

Webové rozhraní API SendGrid je velmi podobné rozhraní REST API, i když není skutečně RESTful API, protože ve většině volání lze zaměnitelně použít slovesa GET i POST.

## <a name="how-to-add-an-attachment"></a>Postup: Přidání přílohy

### <a name="smtp-api"></a>SMTP API

Odeslání přílohy pomocí rozhraní SMTP API zahrnuje jeden další řádek kódu do ukázkového skriptu pro odesílání e-mailu pomocí Swift Mailer.

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

Další řádek kódu je následující:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Tento řádek kódu volá metodu `Swift\_Message` attach na `fromPath` objektu `Swift\_Attachment` a používá statickou metodu ve třídě k získání a připojení souboru ke zprávě.

### <a name="web-api"></a>Web API

Odeslání přílohy pomocí webového rozhraní API je velmi podobné odeslání e-mailu pomocí webového rozhraní API. Všimněte si však, že v následujícím příkladu pole parametrů musí obsahovat tento prvek:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Příklad

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postup: Povolení zápatí, sledování a analýzy pomocí filtrů

SendGrid poskytuje další e-mailové funkce pomocí *filtrů*. Jedná se o nastavení, která lze přidat do e-mailové zprávy, aby bylo možné povolit konkrétní funkce, jako je povolení sledování kliknutí, analýzy Google, sledování předplatného a tak dále.

Filtry lze použít na zprávu pomocí vlastnosti filters. Každý filtr je určen hash obsahující nastavení specifická pro filtr. Následující příklad povolí filtr zápatí a určí textovou zprávu, která bude připojena k dolní části e-mailové zprávy. Pro tento příklad použijeme [sendgrid-php knihovnu].

K instalaci knihovny použijte [composer:]

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Příklad  

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

Nyní, když jste se naučili základy služby SendGrid Email, postupujte podle těchto odkazů, abyste se dozvěděli více.

* Dokumentace SendGrid:<https://sendgrid.com/docs>
* Knihovna SendGrid PHP:<https://github.com/sendgrid/sendgrid-php>
* Speciální nabídka SendGrid pro zákazníky Azure:<https://sendgrid.com/windowsazure.html>

Další informace naleznete také v [CENTRU pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[cloudová e-mailová služba]: https://sendgrid.com/email-solutions
[transakční doručování e-mailů]: https://sendgrid.com/transactional-email
[sendgrid-php knihovna]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
