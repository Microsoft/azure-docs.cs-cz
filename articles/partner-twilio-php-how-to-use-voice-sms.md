---
title: Jak používat Twilio pro hlas a SMS (PHP) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v PHP.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636025"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Použití Twilio pro hlasové funkce a možnosti SMS v PHP
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí služby Twilio API v Azure. Mezi zahrnuté scénáře patří telefonní hovor a odeslání zprávy o krátké službě zprávy (SMS). Další informace o Twilio a použití hlasu a SMS v aplikacích najdete v části [Další kroky](#NextSteps) .

## <a id="WhatIs"></a>Co je Twilio?
Twilio vypíná budoucí obchodní komunikaci a umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v cloudovém globálním prostředí a zpřístupňuje je prostřednictvím platformy rozhraní API pro komunikaci Twilio. Aplikace jsou jednoduché pro sestavování a škálovatelnost. Využijte flexibilitu při průběžných platbách a využijte výhod spolehlivosti cloudu.

**Twilio Voice** umožňuje vašim aplikacím vytvářet a přijímat telefonní hovory. **TWILIO SMS** umožňuje aplikaci posílat a přijímat textové zprávy. **Klient Twilio** umožňuje volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníci Azure obdrží [zvláštní nabídku](https://www.twilio.com/azure): bezplatný $10 kredit Twilio při upgradu účtu Twilio. Tento Twilio kredit se dá použít pro jakékoli použití Twilio ($10 kreditový ekvivalent pro odeslání až 1 000 zpráv SMS nebo přijetí až 1000 zpráv o telefonním čísle v závislosti na umístění telefonního čísla a zprávy nebo cíli volání). Uplatněte tento Twilio kredit a začněte na: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a účet můžete kdykoli zavřít. Další podrobnosti najdete v [Twilio cenách][twilio_pricing].

## <a id="Concepts"></a>Charakteristiky
Rozhraní Twilio API je rozhraní RESTful API, které poskytuje funkce hlasu a SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích. seznam najdete v tématu [knihovny rozhraní API pro Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou Twilio příkazy a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Operace Twilio
Rozhraní API využívá operace Twilio; **například&lt;příkazvyslovit&gt;** instruuje Twilio, aby audibly doručení zprávy na volání.

Následuje seznam operací Twilio. Přečtěte si o dalších příkazech a funkcích prostřednictvím [dokumentace jazyka Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* Vytočit:  **&lt;&gt;** Připojí volajícího k jinému telefonu.
* Shromáždit:  **&lt;&gt;** Shromažďuje číselné číslice zadané na telefonní klávesnici.
* Hangup:  **&lt;&gt;** Ukončí volání.
* Přehrát:  **&lt;&gt;** Přehraje zvukový soubor.
* Pozastavit:  **&lt;&gt;** V tichém režimu počká po zadaný počet sekund.
* Záznam:  **&lt;&gt;** Zaznamenává hlas volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* Přesměrování:  **&lt;&gt;** Přenáší řízení volání nebo SMS na TwiML na jinou adresu URL.
* Odmítnout:  **&lt;&gt;** Odmítne příchozí volání na číslo Twilio bez fakturace.
* Řekněme:  **&lt;&gt;** Převede text na řeč, který je proveden na volání.
* SMS:  **&lt;&gt;** Odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada instrukcí založených na XML, která je založená na příkazech Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Například následující TwiML převede text **Hello World** na řeč.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Když vaše aplikace volá rozhraní Twilio API, je jedním z parametrů rozhraní API adresa URL, která vrací TwiML odpověď. Pro účely vývoje můžete použít adresy URL poskytované Twilio a poskytnout tak odpovědi na TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL pro vytváření odpovědí TwiML a další možností je použít objekt **TwiMLResponse** .

Další informace o příkazech Twilio, jejich atributech a TwiML naleznete v tématu [TwiML][twiml]. Další informace o rozhraní Twilio API najdete v tématu [rozhraní Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se do [Twilio try][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio obdržíte ID účtu a ověřovací token. Pro volání rozhraní API Twilio budou potřeba obojí. Abyste zabránili neoprávněnému přístupu k účtu, udržujte svůj ověřovací token zabezpečený. ID účtu a ověřovací token se mohou zobrazit na [stránce účet Twilio][twilio_account]v polích s popiskem **SID účtu** a ověřovacím **tokenem**v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace PHP
Aplikace PHP, která používá službu Twilio a je spuštěná v Azure, se neliší od jakékoli jiné aplikace PHP, která používá službu Twilio. I když jsou služby Twilio založené na REST a můžou se volat z PHP několika způsoby, Tento článek se zaměří na to, jak používat služby Twilio s [Twilio knihovnou pro php z GitHubu][twilio_php]. Další informace o použití knihovny Twilio pro PHP naleznete v tématu [https://www.twilio.com/docs/libraries/php][twilio_lib_docs].

Podrobné pokyny pro sestavování a nasazování aplikace Twilio/PHP do Azure jsou k dispozici v článku [jak uskutečnit telefonní hovor pomocí Twilio v aplikaci php v Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio pro PHP dvěma způsoby:

1. Stáhněte si knihovnu Twilio pro php z GitHubu[https://github.com/twilio/twilio-php][twilio_php]() a přidejte do své aplikace adresář **služby** .
   
    -OR-
2. Nainstalujte knihovnu Twilio pro PHP jako balíček pro HRUŠKy. Dá se nainstalovat pomocí následujících příkazů:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po instalaci knihovny Twilio pro PHP můžete přidat příkaz **require_once** v horní části souborů php, abyste odkazovali na knihovnu:

        require_once 'Services/Twilio.php';

Další informace najdete na webu [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Jak: Uskutečnit odchozí volání
Následující příklad ukazuje, jak provést odchozí volání pomocí třídy **Services_Twilio** . Tento kód také používá Twilio web k vrácení odpovědi TwiML (Twilio Markup Language). Vysaďte hodnoty pro čísla **z** a **na** telefonní číslo a ujistěte se, že jste před spuštěním kódu ověřili předplatné **z** telefonního čísla účtu Twilio.

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

Jak bylo zmíněno, tento kód používá Twilio web k vrácení TwiML odpovědi. Místo toho můžete k poskytnutí odpovědi TwiML použít svůj vlastní web. Další informace najdete v tématu [jak poskytnout TwiML odpovědi z vašeho vlastního](#howto_provide_twiml_responses)webu.

* **Poznámka:** Informace o řešení chyb ověřování certifikátů SSL najdete v tématu.[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Jak: Odeslat zprávu SMS
Následující příklad ukazuje, jak odeslat zprávu SMS pomocí třídy **Services_Twilio** . Číslo **od** je poskytované Twilio pro zkušební účty k odeslání zpráv SMS. Aby bylo možné účet Twilio spustit před spuštěním kódu, je nutné ověřit jeho číslo.

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

## <a id="howto_provide_twiml_responses"></a>Jak: Poskytování TwiMLch odpovědí z vašeho vlastního webu
Když vaše aplikace zahájí volání rozhraní Twilio API, Twilio odešle požadavek na adresu URL, která by měla vrátit odpověď TwiML. Výše uvedený příklad používá adresu URL [https://twimlets.com/message][twimlet_message_url]poskytnutou Twilio. (I když je TwiML navržený pro použití v Twilio, můžete ho zobrazit v prohlížeči. Například [https://twimlets.com/message][twimlet_message_url] kliknutím zobrazíte prázdný [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] `<Response>` prvek. jako jiný příklad můžete kliknutím zobrazit `<Response>` prvek, který obsahuje `<Say>` prvek.)

Místo toho, abyste museli spoléhat na adresu URL zadanou v Twilio, můžete vytvořit vlastní web, který bude vracet odpovědi HTTP. Web můžete vytvořit v jakémkoli jazyce, který vrací odpovědi XML; v tomto tématu se předpokládá, že k vytvoření TwiML použijete PHP.

Následující stránka PHP má za následek odpověď TwiML, která říká **Hello World** volání.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak vidíte z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro PHP obsahuje třídy, které budou generovat TwiML za vás. Následující příklad vytvoří ekvivalentní odpověď, jak je uvedeno výše, ale používá třídu **Services\_Twilio\_Twiml** v knihovně Twilio Library pro PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Jakmile nastavíte stránku PHP tak, aby poskytovala odpovědi TwiML, použijte adresu URL stránky PHP jako adresu URL předanou `Services_Twilio->account->calls->create` metodě. Například pokud máte webovou aplikaci s názvem **MyTwiML** nasazenou do hostované služby Azure a název stránky PHP je **MyTwiML. php**, adresa URL může být předána **Services_Twilio-> účtu-> volání > vytvořit** , jak je znázorněno na Následující příklad:

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

Další informace o používání Twilio v Azure s PHP najdete v tématu [jak uskutečnit telefonní hovor pomocí Twilio v aplikaci php v Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Jak: Použití dalších služeb Twilio
Kromě zde uvedených příkladů Twilio nabízí webová rozhraní API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní Twilio API][twilio_api_documentation].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se seznámili se základy služby Twilio, můžete získat další informace pomocí těchto odkazů:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio a ukázkový kód pro postupy][twilio_howtos]
* [Kurzy rychlý Start pro Twilio][twilio_quickstarts] 
* [Twilio na GitHubu][twilio_on_github]
* [Kontaktujte podporu Twilio][twilio_support]

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
