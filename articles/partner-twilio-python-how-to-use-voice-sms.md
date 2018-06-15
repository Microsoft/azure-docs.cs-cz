---
title: Jak používat Twilio pro hlasový a serveru SMS (Python) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu jsou vytvořené v Pythonu.
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
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866139"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak používat Twilio pro hlasový a možnosti serveru SMS v Pythonu
Tato příručka ukazuje, jak provádět běžné úkoly programování službou Twilio rozhraní API v Azure. Pokryté scénáře zahrnují uskutečnění telefonního hovoru a odesílání zprávy služby krátké zprávy (SMS). Další informace o Twilio a používání hlasového a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) části.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je pohánějící budoucí obchodní komunikaci, umožňuje vývojářům pro vložení hlasové, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat všechny infrastrukturu potřebnou v prostředí cloudu, globální vystavení prostřednictvím platformy komunikace rozhraní API Twilio. Aplikace jsou jednoduché k sestavení a škálovatelné. Získejte flexibilitu s platím jako vámi přejděte ceny a těžit z cloudu spolehlivost.

**Hlasové Twilio** umožňuje aplikacím zkontrolujte a příjem telefonních hovorů.
**Twilio SMS** umožňuje aplikaci posílat a přijímat textové zprávy.
**Klient Twilio** umožňuje provádět volání VoIP z jakékoli telefon, tablet nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciálních nabídek
Přijímat Azure zákazníků [speciální nabídka] [ special_offer] 10 Twilio kreditu při upgradu vašeho účtu Twilio. Tato Twilio platební je použít pro všechny Twilio využití (10 platební ekvivalentní až 1 000 zpráv serveru SMS odesílání nebo přijímání až 1 000 příchozí hlasové minut, v závislosti na umístění telefonní číslo a zpráva nebo volání cíl). To uplatnit [Twilio platební] [ special_offer] a začít.

Twilio je služba, průběžnými platbami. Neexistují žádné poplatky nastavení a kdykoli můžete zavřít svůj účet. Můžete najít další podrobnosti o [Twilio ceny][twilio_pricing].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilio je rozhraní RESTful API, která poskytuje hlas a funkce serveru SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [Twilio rozhraní API knihovny][twilio_libraries].

Klíčové aspekty rozhraní API Twilio jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazy; například  **&lt;indikované&gt;**  příkaz dá pokyn Twilio zpráva zvukově dodržujeme volání.

Následuje seznam operací Twilio. Další informace o jiné příkazy a možnosti prostřednictvím [Twilio Markup Language dokumentaci][twiml].

* **&lt;Vytočit&gt;**: připojí volající na jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončí volání.
* **&lt;Pozastavení&gt;**: bezobslužná čeká na zadaném počtu sekund.
* **&lt;Přehrání&gt;**: přehraje zvukový soubor.
* **&lt;Fronty&gt;**: přidejte do fronty volající.
* **&lt;Záznam&gt;**: zaznamenává hlasu volajícího a vrátí adresu URL souboru, který obsahuje záznamu.
* **&lt;Přesměrování&gt;**: předá řízení hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: odmítne příchozí volání na vaše číslo Twilio bez fakturace můžete.
* **&lt;Řekněme&gt;**: Převod převede textu na řeč, že z volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada založený na jazyce XML pokyny podle Twilio příkazy, které informují Twilio postup zpracování hovoru nebo SMS.

Jako příklad následující TwiML by převést text **Hello, World** k rozpoznávání řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Pokud aplikace zavolá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje URL můžete použít zadaný Twilio zajistit TwiML odpovědi použít v aplikacích. Může taky hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít `TwiMLResponse` objektu.

Další informace o Twilio příkazy, jejich atributů a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilio najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte si v [zkuste Twilio][try_twilio]. Můžete začít s bezplatný účet a upgradujte si účet později.

Když zaregistrujete k účtu Twilio, zobrazí se účet SID a ověřovací token. Jak bude potřeba k volání rozhraní API Twilio. Aby se zabránilo neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. SID účtu a ověřovací token lze zobrazit v [Twilio konzoly][twilio_console], v polích s názvem bez přípony **SID účtu** a **ověřování TOKENU**, v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace Python
Aplikace Python, která používá službu Twilio a běží v Azure je nejsou jiné než jiná aplikace Python, která používá službu Twilio. Při Twilio services jsou založené na REST a lze volat z Pythonu několika způsoby, v tomto článku se soustředí na použití služeb Twilio s [Twilio knihovny pro jazyk Python z webu GitHub][twilio_python]. Další informace o používání knihovny Twilio pro jazyk Python najdete v tématu [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

První s názvem [nastavení nového virtuálního počítače Azure Linux] [azure_vm_setup] tak, aby fungoval jako hostitel pro Python vaší nové webové aplikace. Jakmile je virtuální počítač spuštěn, musíte se ke zveřejnění aplikace na veřejném portu, jak je popsáno níže.

### <a name="add-an-incoming-rule"></a>Přidat příchozí pravidlo
  1. Přejděte na stránku [skupinu zabezpečení sítě] [azure_nsg].
  2. Vyberte skupinu zabezpečení sítě odpovídající s virtuálním počítačem.
  3. Přidat a **odchozí pravidlo** pro **port 80**. Ujistěte se, zda povolit příchozí z libovolné adresy.

### <a name="set-the-dns-name-label"></a>Nastavte Popisek názvu DNS
  1. Přejděte na stránku [The veřejné IP adresy] [azure_ips].
  2. Vyberte veřejnou IP adresu tohoto correspends s virtuálním počítačem.
  3. Nastavte **Popisek názvu DNS** v **konfigurace** části. V případě tohoto příkladu bude vypadat přibližně takto *vaše popisek domény*. centralus.cloudapp.azure.com

Jakmile budete moci připojit přes SSH k virtuálnímu počítači můžete nainstalovat architektury webů podle vašeho výběru (většina známých v Pythonu ve dvou [Flask](http://flask.pocoo.org/) a [Django](https://www.djangoproject.com)). Můžete nainstalovat buď z nich právě spuštěním `pip install` příkaz.

Uvědomte si, že jsme nakonfigurovali virtuální počítač povolit přenos jenom na portu 80. Proto nezapomeňte nakonfigurovat aplikaci, aby tento port použít.

## <a id="configure_app"></a>Konfigurace aplikace k používání Twilio knihovny
Můžete nakonfigurovat aplikace na používání knihovny, Twilio pro jazyk Python dvěma způsoby:

* Nainstalujte knihovnu Twilio pro jazyk Python jako balíček Pip. Může se nainstalovat pomocí následujících příkazů:
   
        $ pip install twilio

    - nebo -

* Stáhnout Twilio knihovny pro jazyk Python z webu GitHub ([https://github.com/twilio/twilio-python][twilio_python]) a nainstalujte ho takto:

        $ python setup.py install

Po instalaci Twilio knihovny pro jazyk Python, můžete pak `import` ho v souborech Python:

        import twilio

Další informace najdete v tématu [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provést odchozí volání. Tento kód také používá zadaný Twilio lokality k vrácení odpovědi Twilio Markup Language (TwiML). Dosaďte svoje hodnoty **from_number** a **to_number** telefonních čísel a ujistěte se, že ověříte **from_number** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Jak je uvedeno, tento kód používá k vrácení odpovědi TwiML zadaný Twilio lokality. Můžete místo toho použít vlastní funkční web zajistit TwiML odpovědi; Další informace najdete v tématu [jak poskytnout TwiML odpovědí z vaše vlastní webové stránky](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující ukazuje, jak odeslat zprávu SMS pomocí `TwilioRestClient` třídy. **From_number** číslo poskytne Twilio pro zkušebními účty k odesílání zpráv serveru SMS. **To_number** číslo musí ověřit účtu Twilio před spuštěním kódu.

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

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odpovědí z vlastního webu
Pokud vaše aplikace zahájí volání rozhraní API Twilio, odešle Twilio vaši žádost o adresu URL, která zpět TwiML odpověď. V předchozím příkladu používá adresu URL poskytnutou Twilio [http://twimlets.com/message][twimlet_message_url]. (Při TwiML je určen pro Twilio, můžete ji zobrazit v prohlížeči. For example, klikněte na tlačítko [http://twimlets.com/message] [ twimlet_message_url] zobrazíte prázdnou `<Response>` element; například klikněte na tlačítko [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] zobrazíte `<Response>` elementu, který obsahuje `<Say>` elementu.)

Aniž byste museli spoléhat na adresu URL pro zadaný Twilio, můžete vytvořit vlastního webu, který vrací odpovědi HTTP. Můžete vytvořit web v libovolném jazyce, který vrací odpovědí XML; Toto téma předpokládá, že se používá Python vytvořit TwiML.

Následující příklady výstup TwiML odpovědi, která uvádí, že **Hello, World** při volání.

S Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Pomocí rozhraní Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak je vidět na výše uvedeném příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro jazyk Python obsahuje třídy, které způsobí vygenerování TwiML za vás. Následující příklad vytvoří ekvivalentní odpovědi jako v příkladu nahoře, ale používá `twiml` modulu v knihovně Twilio pro jazyk Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml][twiml_reference].

Až budete mít aplikaci Python nastavit na, zadejte TwiML odpovědi, použijte adresu URL aplikace, jako adresa URL předaný do `client.calls.create` metoda. Například, pokud máte webovou aplikaci s názvem **MyTwiML** nasadit do Azure hostované služby, můžete použít jeho adresa url jako webhooku, jak je znázorněno v následujícím příkladu:

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

## <a id="AdditionalServices"></a>Postupy: použití další Twilio služeb
Kromě příkladů tady uvedené nabízí Twilio rozhraní API založené na webu, který můžete využít další funkce Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API Twilio][twilio_api].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služby Twilio, postupujte podle následujících odkazech na další informace:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Příručky Twilio postupy a příklady kódu][twilio_howtos]
* [Kurzy Twilio rychlý start][twilio_quickstarts]
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
