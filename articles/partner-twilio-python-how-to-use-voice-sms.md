---
title: Jak používat Twilio pro hlas a SMS (Python) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v Pythonu.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242185"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak používat Twilio pro hlasové a SMS schopnosti v Pythonu
Tato příručka ukazuje, jak provádět běžné úlohy programování se službou Twilio API v Azure. Zahrnuté scénáře zahrnují telefonování a odeslání zprávy SMS (Short Message Service). Další informace o Twilio a používání hlasu a SMS ve vašich aplikacích naleznete v části [Další kroky.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio napájí budoucnost obchodní komunikace, což vývojářům umožňuje vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v globálním prostředí založeném na cloudu a vystavují ji prostřednictvím platformy Twilio communications API. Aplikace se snadno vytvářejí a lze škálovat. Využijte flexibilitu s průběžnými platbami a využijte spolehlivost cloudu.

**Twilio Voice** umožňuje vašim aplikacím uskutečňovat a přijímat telefonní hovory.
**Twilio SMS** umožňuje vaší aplikaci odesílat a přijímat textové zprávy.
**Twilio Client** umožňuje volat VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio a speciální nabídky
Zákazníci Azure obdrží při upgradu účtu Twilio [speciální nabídku][special_offer] kreditu Twilio. Tento kredit Twilio lze použít na jakékoli použití Twilio (kredit $ 10 ekvivalentní odesílání až 1 000 SMS zpráv nebo přijímání až 1000 příchozích minut hlasu v závislosti na umístění vašeho telefonního čísla a zprávy nebo cíle hovoru). Vykoupit tento [kredit Twilio][special_offer] a začít.

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a svůj účet můžete kdykoli uzavřít. Více informací najdete na [Twilio Pricing][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Twilio API je RESTful API, které poskytuje hlasové a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici ve více jazycích. Seznam naleznete v tématu [Knihovny rozhraní API Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou slovesa Twilio a twilio markup language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Slovesa
Rozhraní API využívá slovesa Twilio; Například ** &lt;Say&gt; ** sloveso pokyn Twilio slyšitelně doručit zprávu na volání.

Následuje seznam sloves Twilio. Další informace o dalších slovech a možnostech naleznete v [dokumentaci k twilio markup language][twiml].

* **Vytočit&gt;: Připojí volajícího k jinému &lt;** telefonu.
* **Sbírat&gt;: Shromažďuje číselné číslice zadané na klávesnici &lt;** telefonu.
* **Hangup&gt;: Ukončí &lt;** hovor.
* **Pauza&gt;: Tiše čeká na zadaný počet &lt;** sekund.
* **Přehrát&gt;: Přehraje zvukový &lt;** soubor.
* **Fronta&gt;: Přidat do fronty volajících. &lt;**
* Záznam : Zaznamená hlas volajícího a vrátí adresu URL souboru, který záznam obsahuje. ** &lt;&gt;**
* Přesměrování : Přenese řízení hovoru nebo SMS na TwiML na jinou adresu URL. ** &lt;&gt;**
* **Odmítnout&gt;: Odmítne příchozí hovor na vaše číslo Twilio, aniž by vám to fakturuje. &lt;**
* Say : Převede text na řeč, která je provedena na volání. ** &lt;&gt;**
* Sms : Odešle SMS zprávu. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>Twiml
TwiML je sada pokynů založených na XML založených na slovesa Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Jako příklad následující TwiML převede text **Hello World** na řeč.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Když vaše aplikace volá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, která vrací odpověď TwiML. Pro účely vývoje můžete pomocí adres URL poskytovaných twilio poskytnout odpovědi TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL k vytvoření odpovědí TwiML a `TwiMLResponse` další možností je použití objektu.

Další informace o slovesách Twilio, jejich atributech a TwiML naleznete v [tématu TwiML][twiml]. Další informace o rozhraní TWILIO API naleznete v tématu [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se na [Try Twilio][try_twilio]. Můžete začít s bezplatným účtem a později svůj účet upgradovat.

Když si zaregistrujete účet Twilio, obdržíte sid účtu a ověřovací token. Oba budou potřebné k volání rozhraní API Twilio. Chcete-li zabránit neoprávněnému přístupu k účtu, zabezpečte ověřovací token. Sid účtu a ověřovací token jsou viditelné v [konzoli Twilio][twilio_console], v polích označených **ACCOUNT SID** a **AUTH TOKEN**.

## <a name="create-a-python-application"></a><a id="create_app"></a>Vytvoření aplikace Pythonu
Aplikace Pythonu, která používá službu Twilio a běží v Azure, se nijak neliší od jakékoli jiné aplikace Pythonu, která používá službu Twilio. Zatímco služby Twilio jsou založené na REST a lze je volat z Pythonu několika způsoby, tento článek se zaměří na to, jak používat služby Twilio s [knihovnou Twilio pro Python z GitHubu][twilio_python]. Další informace o použití knihovny Twilio [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]pro Python naleznete v tématu .

Nejprve [nastavení nového virtuálního počítače Azure Linux][azure_vm_setup] tak, aby působil jako hostitel pro vaši novou webovou aplikaci Pythonu. Jakmile je virtuální počítač spuštěn, budete muset vystavit aplikaci na veřejném portu, jak je popsáno níže.

### <a name="add-an-incoming-rule"></a>Přidání příchozího pravidla
  1. Přejděte na stránku [Network Security Group][azure_nsg].
  2. Vyberte skupinu zabezpečení sítě, která odpovídá vašemu virtuálnímu počítači.
  3. Přidat a **odchozí pravidlo** pro **port 80**. Nezapomeňte povolit příchozí z libovolné adresy.

### <a name="set-the-dns-name-label"></a>Nastavení popisku názvu DNS
  1. Přejděte na stránku [Veřejné IP adresy][azure_ips].
  2. Vyberte veřejnou IP adresu, která odpovídá vašemu virtuálnímu počítači.
  3. V části **Konfigurace** nastavte **popisek názvu DNS.** V tomto příkladu to bude vypadat něco jako *vaše doména-label*.centralus.cloudapp.azure.com

Jakmile budete moci připojit přes SSH k virtuálnímu stroji, můžete nainstalovat webový rámec dle vašeho výběru (dva nejznámější v Pythonu jsou [Flask](http://flask.pocoo.org/) a [Django](https://www.djangoproject.com)). Můžete nainstalovat jeden z nich `pip install` pouze spuštěním příkazu.

Mějte na paměti, že jsme virtuální počítač nakonfigurovali tak, aby umožňoval provoz pouze na portu 80. Takže se ujistěte, že nakonfigurujete aplikaci pro použití tohoto portu.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio pro Python dvěma způsoby:

* Nainstalujte knihovnu Twilio pro Python jako balíček Pip. Může být instalován pomocí následujících příkazů:
   
        $ pip install twilio

    - nebo -

* Stáhněte si knihovnu Twilio[https://github.com/twilio/twilio-python][twilio_python]pro Python z GitHubu ( ) a nainstalujte ji takto:

        $ python setup.py install

Jakmile namontujete knihovnu Twilio `import` pro Python, můžete ji v souborech Pythonu:

        import twilio

Další informace naleznete [v tématu twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postup: Volání odchozího volání
Následující text ukazuje, jak uskutečnovat odchozí hovor. Tento kód také používá web poskytovaný twilio vrátit twilio markup jazyk (TwiML) odpověď. Nahraďte hodnoty **from_number** a **to_number** telefonních čísel a před spuštěním kódu ověřte **from_number** telefonní číslo svého účtu Twilio.

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

Jak již bylo zmíněno, tento kód používá web poskytnutý Twilio k vrácení odpovědi TwiML. Místo toho můžete použít vlastní web k poskytnutí odpovědi TwiML; Další informace naleznete [v tématu Jak poskytnout odpovědi TwiML z vlastního webu](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postup: Odeslání SMS zprávy
Následující ukazuje, jak odeslat sms `TwilioRestClient` zprávu pomocí třídy. **Číslo from_number** poskytuje Twilio pro zkušební účty pro odesílání SMS zpráv. Před spuštěním kódu musí být pro váš účet Twilio ověřeno **číslo to_number.**

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postup: Poskytněte odpovědi TwiML z vašich vlastních webových stránek
Když vaše aplikace zahájí volání rozhraní API Twilio, Twilio odešle váš požadavek na adresu URL, která se očekává, že vrátí odpověď TwiML. Výše uvedený příklad používá adresu URL [https://twimlets.com/message][twimlet_message_url]poskytnutou twilio . (Zatímco TwiML je určen pro použití Twilio, můžete jej zobrazit ve vašem prohlížeči. Klepnutím například [https://twimlets.com/message][twimlet_message_url] zobrazíte `<Response>` prázdný prvek. Jako další příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] klepnutím `<Response>` zobrazíte prvek, který obsahuje `<Say>` prvek.)

Místo spoléhání se na adresu URL poskytovanou twilio můžete vytvořit vlastní web, který vrací odpovědi HTTP. Web můžete vytvořit v libovolném jazyce, který vrací odpovědi XML. Toto téma předpokládá, že budete používat Python k vytvoření TwiML.

Následující příklady budou výstup twiml odpověď, která říká **Hello World** na volání.

S baňkou:

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

Jak můžete vidět z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro Python obsahuje třídy, které vám vygenerují TwiML. Následující příklad vytváří ekvivalentní odpověď, jak je `twiml` uvedeno výše, ale používá modul v knihovně Twilio pro Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Další informace o twiml [https://www.twilio.com/docs/api/twiml][twiml_reference]naleznete v tématu .

Jakmile máte aplikaci Python nastavenou tak, aby poskytovala odpovědi TwiML, použijte `client.calls.create` adresu URL aplikace jako adresu URL předanou metodě. Například pokud máte webovou aplikaci s názvem **MyTwiML** nasazenou do hostované služby Azure, můžete použít její adresu URL jako webhooku, jak je znázorněno v následujícím příkladu:

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

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Postup: Použití dalších služeb Twilio
Kromě příkladů uvedených zde nabízí Twilio webová řešení API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci k rozhraní TWILIO API][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Nyní, když jste se naučili základy služby Twilio, postupujte podle následujících odkazů, abyste se dozvěděli více:

* [Bezpečnostní pokyny Twilio][twilio_security_guidelines]
* [Twilio HowTo Vodítka a ukázkový kód][twilio_howtos]
* [Úvodní kurzy rychlého startu Twilio][twilio_quickstarts]
* [Twilio na GitHubu][twilio_on_github]
* [Promluvte si s podporou Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
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
