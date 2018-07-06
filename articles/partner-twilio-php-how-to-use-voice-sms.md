---
title: Postup použití Twilia pro hlasové hovory a SMS (PHP) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v jazyce PHP.
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
ms.openlocfilehash: 3026436238975b764f09b4a2f370eeb460473a45
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865705"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Postup použití Twilia pro hlasové hovory a SMS v jazyce PHP.
Tato příručka ukazuje, jak k provádění běžných programovacích úloh pomocí služby Twilio API v Azure. Pokryté scénáře zahrnují vytváření telefonních hovorů a posílání zpráv služby krátké zprávy (SMS). Další informace o Twilio a používání hlasové hovory a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) oddílu.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je provozování budoucí komunikaci business, umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat veškerá infrastruktura, je potřeba v prostředí založené na cloudu a globální, bude vystavená prostřednictvím platformy Twilio komunikace rozhraní API. Snadné sestavení a škálovatelné jsou aplikace. Využijte flexibilitu s platbami jako můžete přejít, ceny a výhody cloudu spolehlivost.

**Twilio pro hlasové hovory** umožňuje vašim aplikacím, ujistěte se, a přijímat telefonní hovory. **Twilio SMS** umožňuje vaší aplikaci odesílat a přijímat textové zprávy. **Twilio klienta** umožňuje provádět volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníkům Azure poskytujeme [speciální nabídka](http://www.twilio.com/azure): praktického 10 USD kreditu Twilio při upgradu vašeho účtu Twilio. Tento kredit Twilio můžete použít pro jakékoli využití Twilio (10 USD kreditu ekvivalentní k odesílání až 1 000 zpráv SMS nebo přijímání až 1 000 příchozí hlasové minut v závislosti na umístění cílové vaše telefonní číslo a zpráva nebo volání). Uplatnění tento kredit Twilio a začněte s: [ http://ahoy.twilio.com/azure ](http://ahoy.twilio.com/azure).

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a můžete kdykoli zavřít svůj účet. Můžete najít další podrobnosti najdete v [Twilio ceny][twilio_pricing].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilia je rozhraní RESTful API, která poskytuje hlasové hovory a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [knihovny rozhraní API Twilia][twilio_libraries].

Klíčové aspekty Twilio API jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazů například **&lt;Say&gt;** příkaz nastaví Twilio zvukově doručení zprávy na volání.

Následuje seznam příkazů Twilio. Další informace o ostatních příkazů a funkce prostřednictvím [dokumentaci Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Volání&gt;**: volající se připojí k jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončení volání.
* **&lt;Přehrát&gt;**: přehraje zvukový soubor.
* **&lt;Pozastavit&gt;**: bezobslužná čeká na zadaný počet sekund.
* **&lt;Záznam&gt;**: zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* **&lt;Přesměrovat&gt;**: převede ovládací prvek hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: bez fakturace je odmítne příchozí volání na vaše číslo Twilio
* **&lt;Řekněme, že&gt;**: převede text na řeč, který je k volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada instrukcí založený na formátu XML podle Twilio příkazy, které informují o Twilio, jak zpracovat hovor nebo SMS.

Například následující TwiML by převést text **Hello World** do mluvené řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Pokud vaše aplikace volá rozhraní API pro Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje můžete použít adresy URL zadané Twilio zodpovězení TwiML používají vaše aplikace. Může taky hostovat vlastní adresy URL pro vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazů, jejich atributy a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilia najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni začít účtu Twilio, zaregistrujte se na [zkuste Twilio][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio, obdržíte ID účtu a ověřovací token. Obojí je potřeba volat rozhraní API Twilia. Pokud chcete zabránit neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování jsou zobrazené v tokenu [stránka účtu Twilio][twilio_account], v polích s popiskem **SID účtu** a **Ověřovacím TOKENEM**v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření PHP aplikace
Aplikace v jazyce PHP, který používá služba Twilio a běží v Azure se nijak neliší od jiné PHP aplikace, které používá služba Twilio. Zatímco služby Twilio jsou založené na protokolu REST a může být volána z PHP několika způsoby, tento článek se zaměří na použití služby Twilio s [Twilio knihovny pro PHP z Githubu][twilio_php]. Další informace o použití knihovny Twilio pro jazyk PHP najdete v tématu [ http://readthedocs.org/docs/twilio-php/en/latest/index.html ] [ twilio_lib_docs].

Podrobné pokyny pro vytváření a nasazování aplikací Twilio/PHP do Azure najdete na adrese [jak provádět v aplikaci PHP v Azure Twilio pomocí telefonního hovoru][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurace aplikace pro používání knihovny platformy Twilio
Můžete nakonfigurovat aplikace pro používání knihovny Twilio pro jazyk PHP dvěma způsoby:

1. Stažení knihovny Twilio pro jazyk PHP z Githubu ([https://github.com/twilio/twilio-php][twilio_php]) a přidejte **služby** adresáře do vaší aplikace.
   
    - nebo -
2. Nainstalujte knihovnu Twilio pro jazyk PHP jako HRUŠKOVÝ balíček. Nainstalujete ho pomocí následujících příkazů:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po instalaci Twilio knihovny pro PHP, poté můžete přidat **require_once** příkazu v horní části stránky PHP souborů tak, aby odkazovaly knihovny:

        require_once 'Services/Twilio.php';

Další informace najdete v tématu [ https://github.com/twilio/twilio-php/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provádět volání pomocí odchozí **Services_Twilio** třídy. Tento kód také používá k vrácení odpovědi Twilio Markup Language (TwiML) poskytované Twilio lokality. Dosaďte svoje hodnoty **z** a **k** telefonní čísla a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

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

Jak už bylo zmíněno, tento kód používá poskytované Twilio webu vrátit TwiML odpovědi. Můžete místo toho použít svůj vlastní web k poskytování odezvy TwiML; Další informace najdete v tématu [jak poskytnout TwiML odpovědi z vašeho vlastního webu](#howto_provide_twiml_responses).

* **Poznámka:**: řešení potíží s chyb ověření certifikátu SSL, najdete v tématu [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Postupy: odeslání SMS zprávy
Následující ukazuje, jak odeslat zprávu SMS pomocí **Services_Twilio** třídy. **z** poskytuje číslo Twilio pro účty posílat SMS zprávy. **k** číslo musí být ověřený pro váš účet Twilio před spuštěním kódu.

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

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odezvy z vlastního webu
Pokud aplikace zahájí volání rozhraní API pro Twilio, Twilio pošle vaši žádost na adresu URL, která se očekává navrácení TwiML odpovědi. Výše uvedený příklad používá adresu URL poskytnutou Twilio [ http://twimlets.com/message ] [ twimlet_message_url]. (TwiML je určen pro Twilio, ale když zobrazíte it v prohlížeči. Klikněte například na [ http://twimlets.com/message ] [ twimlet_message_url] zobrazíte prázdná `<Response>` element; další příklad, klikněte na tlačítko [ http://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]zobrazíte `<Response>` element, který obsahuje `<Say>` elementu.)

Aniž byste museli spoléhat na URL poskytnutou Twilio, můžete vytvořit vlastní web, který vrátí odpovědi protokolu HTTP. Můžete vytvořit web v jakémkoli jazyce, který vrací odpovědí ve formátu XML; Toto téma předpokládá, že budete použití PHP k vytvoření TwiML.

Na následující stránce PHP výsledkem TwiML odpovědi s upozorněním **Hello World** při volání.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak je vidět z výše uvedeného příkladu TwiML odpověď je jednoduše dokumentu XML. Twilio knihovny pro PHP obsahuje třídy, které budou generovat TwiML za vás. Následující příklad vytvoří odpovídající odpověď, jak je uvedeno výše, ale používá **služby\_Twilio\_Twiml** tříd v knihovně Twilio pro PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Další informace o TwiML najdete v tématu [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference]. 

Jakmile máte stránku PHP nastavit na poskytování odezvy TwiML, použijte adresu URL stránky PHP jako adresa URL předaná do `Services_Twilio->account->calls->create` metody. Například, pokud máte webovou aplikaci s názvem **MyTwiML** nasadit do Azure hostovaná služba a název stránky PHP je **mytwiml.php**, adresa URL může být předán **Services_Twilio -> účet -> volání -> vytvořit** jak je znázorněno v následujícím příkladu:

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

Další informace o používání Twilio v Azure pomocí PHP najdete v tématu [jak provádět v aplikaci PHP v Azure Twilio pomocí telefonního hovoru][howto_phonecall_php].

## <a id="AdditionalServices"></a>Postupy: použití služby Twilio další
Kromě příkladů uvedených v tomto poli Twilio nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce platformy Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API Twilia][twilio_api_documentation].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služba Twilio, použijte tyto odkazy na další informace:

* [Pokyny pro zabezpečení platformy Twilio][twilio_security_guidelines]
* [Twilio postupy a příklady kódu][twilio_howtos]
* [Kurzy rychlý start Twilio][twilio_quickstarts] 
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
