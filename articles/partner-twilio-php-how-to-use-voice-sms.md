---
title: Jak používat Twilio pro hlas a SMS (PHP) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 34057f1962338927a252011dccc56ed6a77bec47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636025"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak používat Twilio pro hlasové a SMS schopnosti v PHP
Tato příručka ukazuje, jak provádět běžné úlohy programování se službou Twilio API v Azure. Zahrnuté scénáře zahrnují telefonování a odeslání zprávy SMS (Short Message Service). Další informace o Twilio a používání hlasu a SMS ve vašich aplikacích naleznete v části [Další kroky.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio napájí budoucnost obchodní komunikace, což vývojářům umožňuje vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v globálním prostředí založeném na cloudu a vystavují ji prostřednictvím platformy Twilio communications API. Aplikace se snadno vytvářejí a lze škálovat. Využijte flexibilitu s průběžnými platbami a využijte spolehlivost cloudu.

**Twilio Voice** umožňuje vašim aplikacím uskutečňovat a přijímat telefonní hovory. **Twilio SMS** umožňuje vaší aplikaci odesílat a přijímat textové zprávy. **Twilio Client** umožňuje volat VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio a speciální nabídky
Zákazníci Azure obdrží [speciální nabídku](https://www.twilio.com/azure): bezplatný kredit Twilio kredit u twilio kreditu zdarma při upgradu účtu Twilio. Tento kredit Twilio lze použít na jakékoli použití Twilio (kredit $ 10 ekvivalentní odesílání až 1 000 SMS zpráv nebo přijímání až 1000 příchozích minut hlasu v závislosti na umístění vašeho telefonního čísla a zprávy nebo cíle hovoru). Uplatnit tento kredit Twilio a [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)začít na adrese: .

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a svůj účet můžete kdykoli uzavřít. Více informací najdete na [Twilio Pricing][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Twilio API je RESTful API, které poskytuje hlasové a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici ve více jazycích. Seznam naleznete v tématu [Knihovny rozhraní API Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou slovesa Twilio a twilio markup language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Slovesa
Rozhraní API využívá slovesa Twilio; Například ** &lt;Say&gt; ** sloveso pokyn Twilio slyšitelně doručit zprávu na volání.

Následuje seznam sloves Twilio. Další informace o dalších slovech a možnostech naleznete v [dokumentaci k twilio markup language](https://www.twilio.com/docs/api/twiml).

* **Vytočit&gt;: Připojí volajícího k jinému &lt;** telefonu.
* **Sbírat&gt;: Shromažďuje číselné číslice zadané na klávesnici &lt;** telefonu.
* **Hangup&gt;: Ukončí &lt;** hovor.
* **Přehrát&gt;: Přehraje zvukový &lt;** soubor.
* **Pauza&gt;: Tiše čeká na zadaný počet &lt;** sekund.
* Záznam : Zaznamená hlas volajícího a vrátí adresu URL souboru, který záznam obsahuje. ** &lt;&gt;**
* Přesměrování : Přenese řízení hovoru nebo SMS na TwiML na jinou adresu URL. ** &lt;&gt;**
* **Odmítnout&gt;: Odmítne příchozí hovor na vaše číslo Twilio, aniž by vám to vyfakturuje &lt;**
* Say : Převede text na řeč, která je provedena na volání. ** &lt;&gt;**
* Sms : Odešle SMS zprávu. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>Twiml
TwiML je sada pokynů založených na XML založených na slovesa Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Jako příklad následující TwiML převede text **Hello World** na řeč.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Když vaše aplikace volá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, která vrací odpověď TwiML. Pro účely vývoje můžete pomocí adres URL poskytovaných twilio poskytnout odpovědi TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o slovesách Twilio, jejich atributech a TwiML naleznete v [tématu TwiML][twiml]. Další informace o rozhraní TWILIO API naleznete v tématu [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se na [Try Twilio][try_twilio]. Můžete začít s bezplatným účtem a později svůj účet upgradovat.

Když si zaregistrujete účet Twilio, obdržíte ID účtu a ověřovací token. Oba budou potřebné k volání rozhraní API Twilio. Chcete-li zabránit neoprávněnému přístupu k účtu, zabezpečte ověřovací token. ID vašeho účtu a ověřovací token lze zobrazit na [stránce účtu Twilio][twilio_account]v polích označených **ACCOUNT SID** a **AUTH TOKEN**.

## <a name="create-a-php-application"></a><a id="create_app"></a>Vytvoření aplikace PHP
Aplikace PHP, která používá službu Twilio a běží v Azure se nijak neliší od jakékoli jiné aplikace PHP, která používá službu Twilio. Zatímco služby Twilio jsou založeny na REST a mohou být volány z PHP několika způsoby, tento článek se zaměří na to, jak používat služby Twilio s [knihovnou Twilio pro PHP z GitHubu][twilio_php]. Další informace o používání knihovny Twilio [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]pro PHP naleznete v tématu .

Podrobné pokyny pro vytváření a nasazování aplikace Twilio/PHP do Azure jsou k dispozici na adrese [How to make a Phone Call Using Twilio in a PHP Application on Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio pro PHP dvěma způsoby:

1. Stáhněte si knihovnu Twilio[https://github.com/twilio/twilio-php][twilio_php]pro PHP z GitHubu ( ) a přidejte adresář **Services** do vaší aplikace.
   
    - nebo -
2. Nainstalujte knihovnu Twilio pro PHP jako balíček PEAR. Může být instalován pomocí následujících příkazů:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Jakmile jste nainstalovali knihovnu Twilio pro PHP, můžete přidat **require_once** prohlášení v horní části souborů PHP odkazovat na knihovnu:

        require_once 'Services/Twilio.php';

Další informace naleznete [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]v tématu .

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postup: Volání odchozího volání
Následující ukazuje, jak provést odchozí volání pomocí **Services_Twilio** třídy. Tento kód také používá web poskytovaný twilio vrátit twilio markup jazyk (TwiML) odpověď. Nahraďte své hodnoty telefonními čísly **Od** a **Do** a před spuštěním kódu ověřte telefonní číslo **Od** vašeho účtu Twilio.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Jak již bylo zmíněno, tento kód používá web poskytnutý Twilio k vrácení odpovědi TwiML. Místo toho můžete použít vlastní web k poskytnutí odpovědi TwiML; Další informace naleznete [v tématu Jak poskytnout odpovědi TwiML z vlastního webu](#howto_provide_twiml_responses).

* **Poznámka:** Řešení potíží s chybami ověření certifikátu SSL naleznete v tématu[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postup: Odeslání SMS zprávy
Následující ukazuje, jak odeslat zprávu SMS pomocí **Services_Twilio** třídy. Číslo **Od** je poskytováno Twilio pro zkušební účty pro odesílání SMS zpráv. Před **spuštěním** kódu musí být pro váš účet Twilio ověřeno číslo To.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postup: Poskytněte odpovědi TwiML z vašich vlastních webových stránek
Když vaše aplikace zahájí volání rozhraní API Twilio, Twilio odešle váš požadavek na adresu URL, která se očekává, že vrátí odpověď TwiML. Výše uvedený příklad používá adresu URL [https://twimlets.com/message][twimlet_message_url]poskytnutou twilio . (Zatímco TwiML je určen pro použití Twilio, můžete jej zobrazit ve vašem prohlížeči. Klepnutím například [https://twimlets.com/message][twimlet_message_url] zobrazíte `<Response>` prázdný prvek. Jako další příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] klepnutím `<Response>` zobrazíte prvek, který obsahuje `<Say>` prvek.)

Místo spoléhání se na adresu URL poskytovanou twilio můžete vytvořit vlastní web, který vrací odpovědi HTTP. Web můžete vytvořit v libovolném jazyce, který vrací odpovědi XML. Toto téma předpokládá, že budete používat PHP k vytvoření TwiML.

Následující stránka PHP má za následek odpověď TwiML, která říká **Hello World** na volání.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak můžete vidět z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro PHP obsahuje třídy, které vám vygenerují TwiML. Následující příklad vytváří ekvivalentní odpověď, jak je uvedeno výše, ale používá třídu **Services\_Twilio\_Twiml** v knihovně Twilio pro PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Další informace o twiml [https://www.twilio.com/docs/api/twiml][twiml_reference]naleznete v tématu . 

Jakmile budete mít stránku PHP nastavenou tak, aby poskytovala odpovědi TwiML, `Services_Twilio->account->calls->create` použijte adresu URL stránky PHP jako adresu URL předanou metodě. Pokud máte například webovou aplikaci s názvem **MyTwiML** nasazenou do hostované služby Azure a název stránky PHP je **mytwiml.php**, může být adresa URL předána **Services_Twilio >účet >volání >vytvořit,** jak je znázorněno v následujícím příkladu:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Další informace o používání Twilio v Azure s PHP najdete v [tématu Jak pomocí telefonního hovoru pomocí Twilio v aplikaci PHP v Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Postup: Použití dalších služeb Twilio
Kromě příkladů uvedených zde nabízí Twilio webová řešení API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci k rozhraní TWILIO API][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Nyní, když jste se naučili základy služby Twilio, postupujte podle následujících odkazů a dozvíte se více:

* [Bezpečnostní pokyny Twilio][twilio_security_guidelines]
* [Twilio HowTo a příklad kódu][twilio_howtos]
* [Úvodní kurzy rychlého startu Twilio][twilio_quickstarts] 
* [Twilio na GitHubu][twilio_on_github]
* [Promluvte si s podporou Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
