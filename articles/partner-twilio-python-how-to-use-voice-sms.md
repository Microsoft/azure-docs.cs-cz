---
title: Jak používat Twilio pro hlas a SMS (Python) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v Pythonu
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
ms.custom: devx-track-python
ms.openlocfilehash: ba93591ade730c4e9c9bdb6a42232e71e10d6469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850147"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Použití Twilio pro hlasové funkce a možnosti SMS v Pythonu
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí služby Twilio API v Azure. Mezi zahrnuté scénáře patří telefonní hovor a odeslání zprávy o krátké službě zprávy (SMS). Další informace o Twilio a použití hlasu a SMS v aplikacích najdete v části [Další kroky](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio vypíná budoucí obchodní komunikaci a umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v cloudovém globálním prostředí a zpřístupňuje je prostřednictvím platformy rozhraní API pro komunikaci Twilio. Aplikace jsou jednoduché pro sestavování a škálovatelnost. Využijte flexibilitu při průběžných platbách a využijte výhod spolehlivosti cloudu.

**Twilio Voice** umožňuje vašim aplikacím vytvářet a přijímat telefonní hovory.
**TWILIO SMS** umožňuje aplikaci posílat a přijímat textové zprávy.
**Klient Twilio** umožňuje volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníci s Azure obdrží při upgradu účtu Twilio [speciální nabídku][special_offer] $10 kreditu Twilio. Tento Twilio kredit se dá použít pro jakékoli použití Twilio ($10 kreditový ekvivalent pro odeslání až 1 000 zpráv SMS nebo přijetí až 1000 zpráv o telefonním čísle v závislosti na umístění telefonního čísla a zprávy nebo cíli volání). Uplatněte tento [Twilio kredit][special_offer] a začněte.

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a účet můžete kdykoli zavřít. Další podrobnosti najdete v [Twilio cenách][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Rozhraní Twilio API je rozhraní RESTful API, které poskytuje funkce hlasu a SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích. seznam najdete v tématu [knihovny rozhraní API pro Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou Twilio příkazy a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Operace Twilio
Rozhraní API využívá operace Twilio; Například příkaz ** &lt; vyslovit &gt; ** instruuje Twilio, aby audibly doručení zprávy na volání.

Následuje seznam operací Twilio. Přečtěte si o dalších příkazech a funkcích prostřednictvím [dokumentace jazyka Twilio Markup Language][twiml].

* ** &lt; Vytočit &gt; **: připojí volajícího k jinému telefonu.
* ** &lt; Shromáždit &gt; **: shromažďuje číselné číslice, které jsou zadány na klávesnici na telefonu.
* ** &lt; Hangup &gt; **: ukončí volání.
* ** &lt; Pozastavit &gt; **: netiché čekání po zadaný počet sekund.
* ** &lt; Přehrát &gt; **: přehraje zvukový soubor.
* ** &lt; Queue &gt; **: přidejte do fronty volajících.
* ** &lt; Záznam &gt; **: zaznamenává hlas volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* ** &lt; Přesměrování &gt; **: přenáší řízení volání nebo SMS na TwiML na jinou adresu URL.
* ** &lt; Odmítnout &gt; **: odmítne příchozí volání na číslo Twilio bez fakturace.
* ** &lt; Řekněme &gt; **: převede text na řeč, který se provádí na volání.
* ** &lt; SMS &gt; **: pošle zprávu SMS.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML je sada instrukcí založených na XML, která je založená na příkazech Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Například následující TwiML převede text **Hello World** na řeč.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

Když vaše aplikace volá rozhraní Twilio API, je jedním z parametrů rozhraní API adresa URL, která vrací TwiML odpověď. Pro účely vývoje můžete použít adresy URL poskytované Twilio a poskytnout tak odpovědi na TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL pro vytváření odpovědí TwiML a další možnost je použít `TwiMLResponse` objekt.

Další informace o příkazech Twilio, jejich atributech a TwiML naleznete v tématu [TwiML][twiml]. Další informace o rozhraní Twilio API najdete v tématu [rozhraní Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se do [Twilio try][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Když se přihlásíte k účtu Twilio, obdržíte identifikátor SID účtu a ověřovací token. Pro volání rozhraní API Twilio budou potřeba obojí. Abyste zabránili neoprávněnému přístupu k účtu, udržujte svůj ověřovací token zabezpečený. Identifikátor SID a ověřovací token vašeho účtu se v [konzole Twilio][twilio_console]zobrazit v polích s označením **SID účtu** a ověřovacího **tokenu**v uvedeném pořadí.

## <a name="create-a-python-application"></a><a id="create_app"></a>Vytvoření aplikace v Pythonu
Aplikace v Pythonu, která používá službu Twilio a je spuštěná v Azure, se neliší od jakékoli jiné aplikace v Pythonu, která používá službu Twilio. I když jsou služby Twilio založené na REST a můžou se volat z Pythonu několika způsoby, Tento článek se zaměřuje na použití služeb Twilio s [Twilio knihovnou pro Python z GitHubu][twilio_python]. Další informace o použití knihovny Twilio pro Python najdete v tématu [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] .

Nejprve [nastavte nový virtuální počítač Azure Linux] [azure_vm_setup], který bude sloužit jako hostitel pro novou webovou aplikaci v Pythonu. Jakmile je virtuální počítač spuštěný, budete muset svou aplikaci zveřejnit na veřejném portu, jak je popsáno níže.

### <a name="add-an-incoming-rule"></a>Přidat příchozí pravidlo
  1. Přejít na stránku [skupina zabezpečení sítě] [azure_nsg].
  2. Vyberte skupinu zabezpečení sítě, která odpovídá vašemu virtuálnímu počítači.
  3. Přidat a **odchozí pravidlo** pro **port 80**. Ujistěte se, že povolíte příchozí přenos z libovolné adresy.

### <a name="set-the-dns-name-label"></a>Nastavit popisek názvu DNS
  1. Přejít na stránku [Veřejné IP adresy] [azure_ips].
  2. Vyberte veřejnou IP adresu, která odpovídá vašemu virtuálnímu počítači.
  3. V **konfiguračním** oddílu nastavte **popisek název DNS** . V případě tohoto příkladu bude vypadat nějak takto: *-Domain-Label*. centralus.cloudapp.Azure.com

Až budete schopni připojit se přes SSH k virtuálnímu počítači, můžete nainstalovat webovou architekturu podle vlastního výběru (dvě nejužitečnější v Pythonu a [Flask](http://flask.pocoo.org/) [Django](https://www.djangoproject.com)). Jednu z nich můžete nainstalovat pouhým spuštěním `pip install` příkazu.

Mějte na paměti, že jsme nakonfigurovali virtuální počítač tak, aby povoloval provoz jenom na portu 80. Nezapomeňte ale nakonfigurovat aplikaci tak, aby používala tento port.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio pro Python dvěma způsoby:

* Nainstalujte knihovnu Twilio pro Python jako balíček PIP. Dá se nainstalovat pomocí následujících příkazů:
   
  `$ pip install twilio`

    - nebo -

* Stáhněte si knihovnu Twilio pro Python z GitHubu ( [https://github.com/twilio/twilio-python][twilio_python] ) a nainstalujte ji takto:

  `$ python setup.py install`

Po instalaci knihovny Twilio pro Python `import` ji můžete v souborech Pythonu:

  `import twilio`

Další informace najdete v tématu [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postupy: provedení odchozího volání
Následující příklad ukazuje, jak provést odchozí volání. Tento kód také používá Twilio web k vrácení odpovědi TwiML (Twilio Markup Language). Před spuštěním kódu nahraďte hodnoty pro **from_number** a telefonní čísla **TO_NUMBER** a ujistěte se, že jste ověřili **from_number** telefonní číslo účtu Twilio.

```python
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
```

Jak bylo zmíněno, tento kód používá Twilio web k vrácení TwiML odpovědi. Místo toho můžete k poskytnutí odpovědi TwiML použít svůj vlastní web. Další informace najdete v tématu [jak poskytnout TwiML odpovědi z vašeho vlastního](#howto_provide_twiml_responses)webu.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující příklad ukazuje, jak odeslat zprávu SMS pomocí `TwilioRestClient` třídy. **From_number** číslo poskytuje zkušební účty pro odeslání zpráv SMS. Před spuštěním kódu musí být pro účet Twilio ověřené číslo **TO_NUMBER** .

```python
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
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postupy: poskytování odpovědí TwiML z vašeho vlastního webu
Když vaše aplikace zahájí volání rozhraní Twilio API, Twilio odešle požadavek na adresu URL, která by měla vrátit odpověď TwiML. Výše uvedený příklad používá adresu URL poskytnutou Twilio [https://twimlets.com/message][twimlet_message_url] . (I když je TwiML navržený pro použití v Twilio, můžete ho zobrazit v prohlížeči. Například kliknutím [https://twimlets.com/message][twimlet_message_url] zobrazíte prázdný `<Response>` prvek. jako jiný příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] můžete kliknutím zobrazit `<Response>` prvek, který obsahuje `<Say>` prvek.)

Místo toho, abyste museli spoléhat na adresu URL zadanou v Twilio, můžete vytvořit vlastní web, který bude vracet odpovědi HTTP. Web můžete vytvořit v jakémkoli jazyce, který vrací odpovědi XML; v tomto tématu se předpokládá, že k vytvoření TwiML použijete Python.

V následujících příkladech je výstup odpovědi TwiML, která říká **Hello World** volání.

S baňkou:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

S Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Jak vidíte z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio pro Python obsahuje třídy, které budou generovat TwiML za vás. Následující příklad vytvoří ekvivalentní odpověď, jak je uvedeno výše, ale používá `twiml` modul v knihovně Twilio pro Python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml][twiml_reference] .

Jakmile budete mít aplikaci Python nastavenou tak, aby poskytovala odpovědi TwiML, použijte adresu URL aplikace jako adresu URL předanou `client.calls.create`  metodě. Například pokud máte webovou aplikaci s názvem **MyTwiML** nasazenou do hostované služby Azure, můžete použít její adresu URL jako Webhook, jak je znázorněno v následujícím příkladu:

```python
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
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Postupy: používání dalších služeb Twilio
Kromě zde uvedených příkladů Twilio nabízí webová rozhraní API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní Twilio API][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Teď, když jste se seznámili se základy služby Twilio, můžete získat další informace pomocí těchto odkazů:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio příručky pro postupy a příklady kódu][twilio_howtos]
* [Kurzy rychlý Start pro Twilio][twilio_quickstarts]
* [Twilio na GitHubu][twilio_on_github]
* [Kontaktujte podporu Twilio][twilio_support]

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
