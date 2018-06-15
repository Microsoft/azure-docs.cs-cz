---
title: Jak používat Twilio pro hlasový a serveru SMS (PHP) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu jsou vytvořeny v jazyce PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: bd50eac7390e8639f77894689388e6926cdb619c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866174"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak používat Twilio pro hlasový a možnosti serveru SMS v jazyce PHP
Tato příručka ukazuje, jak provádět běžné úkoly programování službou Twilio rozhraní API v Azure. Pokryté scénáře zahrnují uskutečnění telefonního hovoru a odesílání zprávy služby krátké zprávy (SMS). Další informace o Twilio a používání hlasového a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) části.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je pohánějící budoucí obchodní komunikaci, umožňuje vývojářům pro vložení hlasové, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat všechny infrastrukturu potřebnou v prostředí cloudu, globální vystavení prostřednictvím platformy komunikace rozhraní API Twilio. Aplikace jsou jednoduché k sestavení a škálovatelné. Získejte flexibilitu s platím jako vámi přejděte ceny a těžit z cloudu spolehlivost.

**Hlasové Twilio** umožňuje aplikacím zkontrolujte a příjem telefonních hovorů. **Twilio SMS** umožňuje aplikaci posílat a přijímat textové zprávy. **Klient Twilio** umožňuje provádět volání VoIP z jakékoli telefon, tablet nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciálních nabídek
Přijímat Azure zákazníků [speciální nabídka](http://www.twilio.com/azure): bezplatnou 10 Twilio kreditu při upgradu vašeho účtu Twilio. Tato Twilio platební je použít pro všechny Twilio využití (10 platební ekvivalentní až 1 000 zpráv serveru SMS odesílání nebo přijímání až 1 000 příchozí hlasové minut, v závislosti na umístění telefonní číslo a zpráva nebo volání cíl). Uplatnit tento platební Twilio a začít v: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio je služba, průběžnými platbami. Neexistují žádné poplatky nastavení a kdykoli můžete zavřít svůj účet. Můžete najít další podrobnosti o [Twilio ceny][twilio_pricing].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilio je rozhraní RESTful API, která poskytuje hlas a funkce serveru SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [Twilio rozhraní API knihovny][twilio_libraries].

Klíčové aspekty rozhraní API Twilio jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazy; například  **&lt;indikované&gt;**  příkaz dá pokyn Twilio zpráva zvukově dodržujeme volání.

Následuje seznam operací Twilio. Další informace o jiné příkazy a možnosti prostřednictvím [Twilio Markup Language dokumentaci](http://www.twilio.com/docs/api/twiml).

* **&lt;Vytočit&gt;**: připojí volající na jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončí volání.
* **&lt;Přehrání&gt;**: přehraje zvukový soubor.
* **&lt;Pozastavení&gt;**: bezobslužná čeká na zadaném počtu sekund.
* **&lt;Záznam&gt;**: zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznamu.
* **&lt;Přesměrování&gt;**: předá řízení hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: odmítne příchozí volání na vaše číslo Twilio bez fakturace můžete
* **&lt;Řekněme&gt;**: Převod převede textu na řeč, že z volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada založený na jazyce XML pokyny podle Twilio příkazy, které informují Twilio postup zpracování hovoru nebo SMS.

Jako příklad následující TwiML by převést text **Hello, World** k rozpoznávání řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Pokud aplikace zavolá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje URL můžete použít zadaný Twilio zajistit TwiML odpovědi použít v aplikacích. Může taky hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazy, jejich atributů a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilio najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte si v [zkuste Twilio][try_twilio]. Můžete začít s bezplatný účet a upgradujte si účet později.

Když se zaregistrujete k účtu Twilio, obdržíte ID účtu a ověřovací token. Jak bude potřeba k volání rozhraní API Twilio. Aby se zabránilo neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování tokenu je možné zobrazit na [stránku účtu Twilio][twilio_account], v polích s názvem bez přípony **SID účtu** a **ověřování TOKENU**, v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace PHP
Aplikace PHP, která používá službu Twilio a běží v Azure je nejsou jiné než jiná aplikace PHP, která používá službu Twilio. Při Twilio services jsou založené na REST a lze volat z PHP několika způsoby, v tomto článku se soustředí na použití služeb Twilio s [Twilio knihovny pro jazyk PHP z Githubu][twilio_php]. Další informace o používání knihovny Twilio pro jazyk PHP najdete v tématu [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Podrobné pokyny pro vytváření a nasazování aplikace Twilio nebo PHP do Azure jsou k dispozici na [postup proveďte Twilio pomocí telefonního hovoru v aplikaci PHP v Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurace aplikace k používání Twilio knihovny
Můžete nakonfigurovat aplikace na používání knihovny, Twilio pro jazyk PHP dvěma způsoby:

1. Stáhnout Twilio knihovny pro jazyk PHP z webu GitHub ([https://github.com/twilio/twilio-php][twilio_php]) a přidejte **služby** adresář do vaší aplikace.
   
    - nebo -
2. Nainstalujte knihovnu Twilio pro jazyk PHP jako balíček HRUŠKAMI. Může se nainstalovat pomocí následujících příkazů:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po instalaci Twilio knihovny pro jazyk PHP, poté můžete přidat **require_once** příkaz v horní části PHP soubory k odkazování knihovny:

        require_once 'Services/Twilio.php';

Další informace najdete v tématu [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak zajistit odchozí volání pomocí **Services_Twilio** třídy. Tento kód také používá zadaný Twilio lokality k vrácení odpovědi Twilio Markup Language (TwiML). Dosaďte svoje hodnoty **z** a **k** telefonních čísel a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

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

Jak je uvedeno, tento kód používá k vrácení odpovědi TwiML zadaný Twilio lokality. Můžete místo toho použít vlastní funkční web zajistit TwiML odpovědi; Další informace najdete v tématu [jak poskytnout TwiML odpovědí z vaše vlastní webové stránky](#howto_provide_twiml_responses).

* **Poznámka:**: Chcete-li vyřešit chyby ověření certifikátu SSL, najdete v části [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující ukazuje, jak odeslat zprávu SMS pomocí **Services_Twilio** třídy. **z** číslo poskytne Twilio pro zkušebními účty k odesílání zpráv serveru SMS. **k** číslo musí být ověřena pro váš účet Twilio před spuštěním kódu.

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

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odpovědí z vlastního webu
Pokud vaše aplikace zahájí volání rozhraní API Twilio, odešle Twilio vaši žádost o adresu URL, která zpět TwiML odpověď. V předchozím příkladu používá adresu URL poskytnutou Twilio [http://twimlets.com/message][twimlet_message_url]. (Při TwiML je určen pro Twilio, můžete zobrazit it v prohlížeči. For example, klikněte na tlačítko [http://twimlets.com/message] [ twimlet_message_url] zobrazíte prázdnou `<Response>` element; například klikněte na tlačítko [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] zobrazíte `<Response>` elementu, který obsahuje `<Say>` elementu.)

Aniž byste museli spoléhat na adresu URL pro zadaný Twilio, můžete vytvořit vlastního webu, který vrací odpovědi HTTP. Můžete vytvořit web v libovolném jazyce, který vrací odpovědí XML; Toto téma předpokládá, že budete používat PHP k vytvoření TwiML.

Na následující stránce PHP výsledkem TwiML odpovědi, která uvádí, že **Hello, World** při volání.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak je vidět na výše uvedeném příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro jazyk PHP obsahuje třídy, které způsobí vygenerování TwiML za vás. Následující příklad vytvoří ekvivalentní odpovědi jako v příkladu nahoře, ale používá **služby\_Twilio\_Twiml** – třída v knihovně Twilio pro jazyk PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Jakmile máte stránku PHP nastavit na, zadejte TwiML odpovědi, použijte adresu URL stránky PHP jako adresu URL předaný do `Services_Twilio->account->calls->create` metoda. Například, pokud máte webovou aplikaci s názvem **MyTwiML** nasadit do Azure hostovaná služba a název stránky PHP je **mytwiml.php**, adresa URL se dá předat do **Services_Twilio -> účet -> volání -> vytvořit** jak je znázorněno v následujícím příkladu:

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

Další informace o používání Twilio v Azure s PHP najdete v tématu [postup proveďte Twilio pomocí telefonního hovoru v aplikaci PHP v Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Postupy: použití další Twilio služeb
Kromě příkladů tady uvedené nabízí Twilio rozhraní API založené na webu, který můžete využít další funkce Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služby Twilio, postupujte podle následujících odkazech na další informace:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio postupy a příklady kódu][twilio_howtos]
* [Kurzy Twilio rychlý start][twilio_quickstarts] 
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
