---
title: Postup použití Twilia pro hlasové hovory a SMS (Python) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v Pythonu.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f6d144daa165d350c6615f323b25f8860697f2c1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422489"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Postup použití Twilia pro hlasové hovory a SMS v Pythonu
Tato příručka ukazuje, jak k provádění běžných programovacích úloh pomocí služby Twilio API v Azure. Pokryté scénáře zahrnují vytváření telefonních hovorů a posílání zpráv služby krátké zprávy (SMS). Další informace o Twilio a používání hlasové hovory a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) oddílu.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je provozování budoucí komunikaci business, umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat veškerá infrastruktura, je potřeba v prostředí založené na cloudu a globální, bude vystavená prostřednictvím platformy Twilio komunikace rozhraní API. Snadné sestavení a škálovatelné jsou aplikace. Využijte flexibilitu s platbami jako můžete přejít, ceny a výhody cloudu spolehlivost.

**Twilio pro hlasové hovory** umožňuje vašim aplikacím, ujistěte se, a přijímat telefonní hovory.
**Twilio SMS** umožňuje vaší aplikaci odesílat a přijímat textové zprávy.
**Twilio klienta** umožňuje provádět volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníkům Azure poskytujeme [speciální nabídka] [ special_offer] 10 USD kreditu Twilio při upgradu vašeho účtu Twilio. Tento kredit Twilio můžete použít pro jakékoli využití Twilio (10 USD kreditu ekvivalentní k odesílání až 1 000 zpráv SMS nebo přijímání až 1 000 příchozí hlasové minut v závislosti na umístění cílové vaše telefonní číslo a zpráva nebo volání). Uplatnění to [Twilio kredit] [ special_offer] a můžete začít.

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a můžete kdykoli zavřít svůj účet. Můžete najít další podrobnosti najdete v [Twilio ceny][twilio_pricing].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilia je rozhraní RESTful API, která poskytuje hlasové hovory a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [knihovny rozhraní API Twilia][twilio_libraries].

Klíčové aspekty Twilio API jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazů například **&lt;Say&gt;** příkaz nastaví Twilio zvukově doručení zprávy na volání.

Následuje seznam příkazů Twilio. Další informace o ostatních příkazů a funkce prostřednictvím [Twilio Markup Language dokumentaci][twiml].

* **&lt;Volání&gt;**: volající se připojí k jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončení volání.
* **&lt;Pozastavit&gt;**: bezobslužná čeká na zadaný počet sekund.
* **&lt;Přehrát&gt;**: přehraje zvukový soubor.
* **&lt;Fronty&gt;**: přidejte do fronty pro volající.
* **&lt;Záznam&gt;**: zaznamenává hlasu volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* **&lt;Přesměrovat&gt;**: převede ovládací prvek hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: bez fakturace je odmítne příchozí volání na vaše číslo Twilio.
* **&lt;Řekněme, že&gt;**: převede text na řeč, který je k volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada instrukcí založený na formátu XML podle Twilio příkazy, které informují o Twilio, jak zpracovat hovor nebo SMS.

Například následující TwiML by převést text **Hello World** do mluvené řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Pokud vaše aplikace volá rozhraní API pro Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje můžete použít adresy URL zadané Twilio zodpovězení TwiML používají vaše aplikace. Může taky hostovat vlastní adresy URL pro vytvoření odpovědi TwiML a další možností je použít `TwiMLResponse` objektu.

Další informace o Twilio příkazů, jejich atributy a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilia najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte se na [zkuste Twilio][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio, obdržíte identifikátor SID účtu a ověřovací token. Obojí je potřeba volat rozhraní API Twilia. Pokud chcete zabránit neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. SID účtu a ověřovací token je možné zobrazit v [Twilio konzoly][twilio_console], v polích s popiskem **SID účtu** a **Ověřovacím TOKENEM**v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace v Pythonu
Aplikace v Pythonu, který používá služba Twilio a běží v Azure se nijak neliší od jakékoli jiné aplikace v Pythonu, který používá služba Twilio. Zatímco služeb Twilio jsou založené na protokolu REST a může být volána z Pythonu několika způsoby, tento článek se zaměří na použití služby Twilio [Twilio knihovny pro Python z Githubu][twilio_python]. Další informace o použití knihovny Twilio pro Python najdete v tématu [ https://readthedocs.org/docs/twilio-python/en/latest/index.html ] [ twilio_lib_docs].

První s názvem [nastavení nového virtuálního počítače Linux Azure] [azure_vm_setup] tak, aby fungoval jako hostitel pro Python vaší nové webové aplikace. Jakmile je virtuální počítač spuštěn, je potřeba zveřejněte svou aplikaci na veřejném portu, jak je popsáno níže.

### <a name="add-an-incoming-rule"></a>Přidat příchozí pravidlo
  1. Přejděte na stránku [Skupina zabezpečení sítě] [azure_nsg].
  2. Vyberte skupiny zabezpečení sítě, která odpovídá s virtuálním počítačem.
  3. Přidat a **odchozí pravidlo** pro **port 80**. Je potřeba povolit příchozí z libovolné adresy.

### <a name="set-the-dns-name-label"></a>Nastavení popisku názvu DNS
  1. Přejděte na stránku [veřejné IP adresy] [azure_ips].
  2. Vyberte veřejnou IP adresu tohoto correspends s virtuálním počítačem.
  3. Nastavte **popisku názvu DNS** v **konfigurace** oddílu. V tomto příkladu to bude vypadat asi takhle nějak *váš popisek domény*. centralus.cloudapp.azure.com

Jakmile budete moct připojit přes SSH k virtuálnímu počítači můžete nainstalovat webové platformy podle vašeho výběru (nejvíce dobře známá v Pythonu se dvěma [Flask](http://flask.pocoo.org/) a [Django](https://www.djangoproject.com)). Můžete nainstalovat buď z nich stačí spuštěním `pip install` příkazu.

Uvědomte si, že jsme konfiguraci virtuálního počítače pro povolení provozu pouze na portu 80. Proto ujistěte se, že konfigurace aplikace pro použití tohoto portu.

## <a id="configure_app"></a>Konfigurace aplikace pro používání knihovny platformy Twilio
Můžete nakonfigurovat aplikaci použít knihovnu Twilio pro Python dvěma způsoby:

* Jako balíček Pip nainstalujte Twilio knihovny pro Python. Nainstalujete ho pomocí následujících příkazů:
   
        $ pip install twilio

    - nebo -

* Stáhněte si knihovny Twilio pro Pythonu z Githubu ([https://github.com/twilio/twilio-python][twilio_python]) a nainstalujte ho následujícím způsobem:

        $ python setup.py install

Po instalaci Twilio knihovny pro Python, pak můžete `import` ho ve vašich souborech Pythonu:

        import twilio

Další informace najdete v tématu [ https://github.com/twilio/twilio-python/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provést odchozích volání. Tento kód také používá k vrácení odpovědi Twilio Markup Language (TwiML) poskytované Twilio lokality. Dosaďte svoje hodnoty **from_number** a **to_number** telefonní čísla a ujistěte se, že jste ověřili **from_number** telefonní číslo pro svůj účet Twilio Před spuštěním kódu.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Jak už bylo zmíněno, tento kód používá poskytované Twilio webu vrátit TwiML odpovědi. Můžete místo toho použít svůj vlastní web k poskytování odezvy TwiML; Další informace najdete v tématu [jak poskytnout TwiML odpovědi z vašeho vlastního webu](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání SMS zprávy
Následující ukazuje, jak odeslat zprávu SMS pomocí `TwilioRestClient` třídy. **From_number** poskytuje číslo Twilio pro účty posílat SMS zprávy. **To_number** číslo musí být ověřena svého účtu Twilio, před spuštěním kódu.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odezvy z vlastního webu
Pokud aplikace zahájí volání rozhraní API pro Twilio, Twilio pošle vaši žádost na adresu URL, která se očekává navrácení TwiML odpovědi. Výše uvedený příklad používá adresu URL poskytnutou Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (TwiML je určen pro Twilio, ale když zobrazíte ji v prohlížeči. Klikněte například na [ https://twimlets.com/message ] [ twimlet_message_url] zobrazíte prázdná `<Response>` element; další příklad, klikněte na tlačítko [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]zobrazíte `<Response>` element, který obsahuje `<Say>` elementu.)

Aniž byste museli spoléhat na URL poskytnutou Twilio, můžete vytvořit vlastní web, který vrátí odpovědi protokolu HTTP. Můžete vytvořit web v jakémkoli jazyce, který vrací odpovědí ve formátu XML; Toto téma předpokládá, že se pomocí Pythonu vytvořit TwiML.

V následujících příkladech se výstup TwiML odpovědi s upozorněním **Hello World** při volání.

Pomocí Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

S Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak je vidět z výše uvedeného příkladu TwiML odpověď je jednoduše dokumentu XML. Twilio knihovny pro Python obsahuje třídy, které budou generovat TwiML za vás. Následující příklad vytvoří odpovídající odpověď, jak je uvedeno výše, ale používá `twiml` modulu v knihovně Twilio pro Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Další informace o TwiML najdete v tématu [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference].

Jakmile budete mít aplikaci v Pythonu nastavit na poskytování TwiML odezvy, použijte adresu URL aplikace jako adresa URL předaná do `client.calls.create` metody. Například, pokud máte webovou aplikaci s názvem **MyTwiML** nasadit do Azure hostované služby, můžete její adresu url webhooku, jak je znázorněno v následujícím příkladu:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Postupy: použití služby Twilio další
Kromě příkladů uvedených v tomto poli Twilio nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce platformy Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API Twilia][twilio_api].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služba Twilio, použijte tyto odkazy na další informace:

* [Pokyny pro zabezpečení platformy Twilio][twilio_security_guidelines]
* [Průvodce postupy Twilio a příklady kódu][twilio_howtos]
* [Kurzy rychlý start Twilio][twilio_quickstarts]
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
