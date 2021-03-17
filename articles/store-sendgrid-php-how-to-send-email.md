---
title: Jak používat e-mailovou službu SendGrid (PHP) | Microsoft Docs
description: Přečtěte si, jak odeslat e-mail pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v PHP.
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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023784"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Jak používat e-mailovou službu SendGrid z PHP

Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí e-mailové služby SendGrid v Azure. Ukázky jsou napsány v PHP.
Mezi zahrnuté scénáře patří **vytváření e-mailů**, **posílání e-mailů** a **přidávání příloh**. Další informace o SendGrid a odesílání e-mailů najdete v části [Další kroky](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [Cloudová e-mailová služba] , která poskytuje spolehlivé zasílání [transakčních e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními rozhraními API, která usnadňují vlastní integraci. Mezi běžné scénáře použití SendGrid patří:

* Automatické odesílání účtenek zákazníkům
* Správa distribučních seznamů pro posílání zákazníků měsíčně e-letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro věci, jako je Blokovaný e-mail a reakce zákazníků
* Generování sestav, které vám pomůžou identifikovat trendy
* Předávání dotazů zákazníkům
* E-mailová oznámení z vaší aplikace

Další informace najdete na webu [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Použití SendGrid z aplikace PHP

Použití SendGrid v aplikaci Azure PHP nevyžaduje žádnou zvláštní konfiguraci ani kódování. Vzhledem k tomu, že SendGrid je služba, je k ní možné přistupovat přesně stejným způsobem než z cloudové aplikace, protože se může nacházet z místní aplikace.

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailů

Můžete posílat e-maily buď pomocí protokolu SMTP, nebo webového rozhraní API, které poskytuje SendGrid.

### <a name="smtp-api"></a>API SMTP

Pokud chcete odesílat e-maily pomocí rozhraní API služby SendGrid SMTP, použijte k posílání e-mailů z aplikací PHP knihovnu založenou na komponentě *SWIFT*. Můžete si stáhnout [poštovní knihovnu SWIFT](https://swiftmailer.symfony.com/) v 5.3.0 (použijte [skladatele] k instalaci poštovního pole SWIFT). Odesílání e-mailů s knihovnou zahrnuje vytváření `Swift\_SmtpTransport` instancí `Swift\_Mailer` tříd,, a `Swift\_Message` nastavení odpovídajících vlastností a volání `Swift\_Mailer::send` metody.

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

### <a name="web-api"></a>Webové rozhraní API
K posílání e-mailů pomocí webového rozhraní API SendGrid použijte [funkci složené][curl function] z php.

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

Webové rozhraní API SendGrid je velmi podobné jako REST API, přestože není skutečně rozhraní RESTful API, protože ve většině volání se dají i příkazy GET a POST použít zaměnitelné.

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy

### <a name="smtp-api"></a>API SMTP

Odeslání přílohy pomocí protokolu SMTP API zahrnuje do ukázkového skriptu pro odeslání e-mailu s poštou SWIFT jeden další řádek kódu.

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

Tento řádek kódu volá metodu Attach `Swift\_Message` objektu a používá statickou metodu pro `fromPath` `Swift\_Attachment` třídu k získání a připojení souboru ke zprávě.

### <a name="web-api"></a>Webové rozhraní API

Odeslání přílohy pomocí webového rozhraní API se velmi podobá posílání e-mailů pomocí webového rozhraní API. Všimněte si však, že v následujícím příkladu musí pole parametrů obsahovat tento prvek:

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postupy: použití filtrů k povolení zápatí, sledování a analýz

SendGrid poskytuje další funkce e-mailu prostřednictvím použití *filtrů*. Jedná se o nastavení, která se dají přidat do e-mailové zprávy, aby se povolily konkrétní funkce, jako je povolení sledování kliknutí, Google Analytics, sledování předplatného atd.

Filtry lze použít pro zprávu pomocí vlastnosti filters. Každý filtr je určen hodnotou hash obsahující nastavení specifické pro filtr. Následující příklad povoluje filtr zápatí a určuje textovou zprávu, která bude připojena k dolnímu okraji e-mailové zprávy. V tomto příkladu použijeme [knihovnu SendGrid-php].

Použijte [skladatele] k instalaci knihovny:

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

Teď, když jste se seznámili se základy e-mailové služby SendGrid, získáte další informace na následujících odkazech.

* Dokumentace k SendGrid: <https://sendgrid.com/docs>
* Knihovna PHP SendGrid: <https://github.com/sendgrid/sendgrid-php>
* SendGrid speciální nabídka pro zákazníky Azure: <https://sendgrid.com/windowsazure.html>

Další informace najdete v tématu také [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[cloudové e-mailové služby]: https://sendgrid.com/email-solutions
[doručení transakčního e-mailu]: https://sendgrid.com/transactional-email
[SendGrid – knihovna php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
