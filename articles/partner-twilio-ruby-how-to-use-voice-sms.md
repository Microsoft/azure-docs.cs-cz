---
title: Jak používat Twilio pro hlas a SMS (Ruby) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95542673"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Použití Twilio pro hlasové funkce a možnosti SMS v Ruby
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí služby Twilio API v Azure. Mezi zahrnuté scénáře patří telefonní hovor a odeslání zprávy o krátké službě zprávy (SMS). Další informace o Twilio a použití hlasu a SMS v aplikacích najdete v části [Další kroky](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio je telefonní rozhraní API pro webové služby, které umožňuje používat stávající webové jazyky a dovednosti k vytváření hlasových a SMS aplikací. Twilio je služba třetí strany (ne funkce Azure, nikoli produkt společnosti Microsoft).

**Twilio Voice** umožňuje vašim aplikacím vytvářet a přijímat telefonní hovory. **TWILIO SMS** umožňuje vašim aplikacím vytvářet a přijímat zprávy SMS. **Klient Twilio** umožňuje aplikacím povolit hlasovou komunikaci pomocí stávajících připojení k Internetu, včetně mobilních připojení.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio a speciální nabídky
Informace o cenách Twilio jsou dostupné v [Twilio cenách][twilio_pricing]. Zákazníci Azure obdrží [speciální nabídku][special_offer]: bezplatný kredit 1000ch textů nebo 1000 příchozích minut. Pokud se chcete přihlásit k této nabídce nebo získat další informace, navštivte prosím [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Rozhraní Twilio API je rozhraní RESTful API, které poskytuje funkce hlasu a SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích. seznam najdete v tématu [knihovny rozhraní API pro Twilio][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML je sada instrukcí založených na XML, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Například následující TwiML převede text **Hello World** na řeč.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Všechny dokumenty TwiML mají `<Response>` jako svůj kořenový element. Odtud použijete příkazy Twilio k definování chování aplikace.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Operace TwiML
Příkazy Twilio jsou značky XML, které oznamují Twilio, co **dělat**. Například příkaz **&lt; vyslovit &gt;** instruuje Twilio, aby audibly doručení zprávy na volání. 

Následuje seznam operací Twilio.

* **&lt; Vytočit &gt;**: připojí volajícího k jinému telefonu.
* **&lt; Shromáždit &gt;**: shromažďuje číselné číslice, které jsou zadány na klávesnici na telefonu.
* **&lt; Hangup &gt;**: ukončí volání.
* **&lt; Přehrát &gt;**: přehraje zvukový soubor.
* **&lt; Pozastavit &gt;**: netiché čekání po zadaný počet sekund.
* **&lt; Záznam &gt;**: zaznamenává hlas volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* **&lt; Přesměrování &gt;**: přenáší řízení volání nebo SMS na TwiML na jinou adresu URL.
* **&lt; Odmítnout &gt;**: odmítne příchozí volání na číslo Twilio bez fakturace.
* **&lt; Řekněme &gt;**: převede text na řeč, který se provádí na volání.
* **&lt; SMS &gt;**: pošle zprávu SMS.

Další informace o příkazech Twilio, jejich atributech a TwiML naleznete v tématu [TwiML][twiml]. Další informace o rozhraní Twilio API najdete v tématu [rozhraní Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se do [Twilio try][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Když se zaregistrujete k účtu Twilio, získáte bezplatné telefonní číslo vaší aplikace. Obdržíte také identifikátor SID účtu a ověřovací token. Pro volání rozhraní API Twilio budou potřeba obojí. Abyste zabránili neoprávněnému přístupu k účtu, udržujte svůj ověřovací token zabezpečený. Identifikátor SID účtu a ověřovací token se mohou zobrazit na [stránce účtu Twilio][twilio_account]v polích s názvem **SID účtu** a **ověřovacím tokenem** v uvedeném pořadí.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Ověřit telefonní čísla
Kromě čísla, které jste předali pomocí Twilio, můžete také ověřit čísla, která ovládáte (tj. váš mobilní telefon nebo telefonní číslo domů) pro použití ve svých aplikacích. 

Informace o tom, jak ověřit telefonní číslo, najdete v tématu [Správa čísel][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Vytvoření aplikace v Ruby
Aplikace v Ruby, která používá službu Twilio a je spuštěná v Azure, se neliší od jakékoli jiné aplikace v Ruby, která používá službu Twilio. I když jsou služby Twilio Services RESTful a je možné je volat z Ruby několika způsoby, Tento článek se zaměřuje na používání služeb Twilio Services s [podpůrnou knihovnou Twilio pro Ruby][twilio_ruby].

Nejdřív [nastavte nový virtuální počítač Azure Linux][azure_vm_setup] , který bude fungovat jako hostitel vaší nové webové aplikace v Ruby. Ignorujte kroky týkající se vytvoření aplikace s kolejnicemi, stačí nastavit virtuální počítač. Ujistěte se, že jste vytvořili koncový bod s externím portem 80 a interním portem 5000.

V příkladech níže budeme používat [Sinatra][sinatra], velmi jednoduchou webovou architekturu pro Ruby. Můžete ale použít pomocnou knihovnu Twilio pro Ruby s ostatními webovými rozhraními, včetně Ruby na železnici.

Připojte se k novému VIRTUÁLNÍmu počítači přes SSH a vytvořte adresář pro novou aplikaci. V tomto adresáři vytvořte soubor s názvem Gemfile a zkopírujte do něj následující kód:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

Na příkazovém řádku spusťte příkaz `bundle install` . Tím se nainstalují výše uvedené závislosti. Dále vytvořte soubor s názvem `web.rb` . To bude místo, kde je kód vaší webové aplikace život. Vložte do něj následující kód:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

V tomto okamžiku byste měli být moci spustit příkaz `ruby web.rb -p 5000` . Tím se vytočí malý webový server na portu 5000. Měli byste být schopni přejít do této aplikace v prohlížeči, a to návštěvou adresy URL, kterou jste nastavili pro virtuální počítač Azure. Až se dostanete k webové aplikaci v prohlížeči, můžete začít vytvářet aplikace v Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurace aplikace tak, aby používala Twilio
Webovou aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio, a to tak, že `Gemfile` se aktualizuje tak, aby zahrnovala tento řádek:

```bash
gem 'twilio-ruby'
```

Na příkazovém řádku spusťte příkaz `bundle install` . Nyní otevřete `web.rb` a včetně tohoto řádku v horní části:

```ruby
require 'twilio-ruby'
```

Teď jste vše nastavili pro použití pomocné knihovny Twilio pro Ruby ve vaší webové aplikaci.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postupy: provedení odchozího volání
Následující příklad ukazuje, jak provést odchozí volání. Klíčové koncepty zahrnují použití pomocné knihovny Twilio pro Ruby, která umožňuje REST API volání a vykreslování TwiML. Vysaďte hodnoty pro čísla **z** a **na** telefonní číslo a ujistěte se, že jste před spuštěním kódu ověřili předplatné **z** telefonního čísla účtu Twilio.

Přidat tuto funkci do `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

Pokud otevřete `http://yourdomain.cloudapp.net/make_call` v prohlížeči, který aktivuje volání rozhraní Twilio API, aby mohl telefonní hovor uskutečnit. První dva parametry v jsou poměrně vysvětlované `client.account.calls.create` : číslo volání `from` a číslo, které je volání `to` . 

Třetí parametr ( `url` ) je adresa URL, kterou Twilio požadavky na informace o tom, co se má provést po připojení volání. V tomto případě nastavíme adresu URL ( `http://yourdomain.cloudapp.net` ), která vrátí jednoduchý dokument TwiML a pomocí `<Say>` příkazu provede určitý převod textu na řeč a vysloví "Hello opice" osobě, která hovor přijme.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Postupy: přijetí zprávy SMS
V předchozím příkladu jsme iniciovali **odchozí** telefonní hovor. Tentokrát použijeme telefonní číslo, které vám Twilio při registraci pomohlo zpracovat **příchozí** zprávu SMS.

Nejdřív se přihlaste ke svému [řídicímu panelu Twilio][twilio_account]. V horním navigačním navigačním panelu klikněte na čísla a pak klikněte na Twilio číslo, které jste zadali. Zobrazí se dvě adresy URL, které můžete konfigurovat. Adresa URL požadavku hlasu a adresa URL požadavku serveru SMS. Jedná se o adresy URL, které Twilio volá při každém provedení telefonního hovoru, nebo se pošle SMS na vaše číslo. Adresy URL se označují také jako Webhooky.

Chceme zpracovat příchozí zprávy SMS, takže pojďme adresu URL aktualizovat na `http://yourdomain.cloudapp.net/sms_url` . Pokračujte a v dolní části stránky klikněte na Uložit změny. Teď se vraťte do `web.rb` programu, abychom tuto aplikaci zpracovali:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

Po provedení změny nezapomeňte znovu spustit svou webovou aplikaci. Nyní si vyžádejte svůj telefon a odešlete SMS k vašemu Twilio číslu. Měli byste se rychle zeptat na odpověď SMS, která říká "Hey", děkuji za testem. Twilio a Azure Rock! ".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Postupy: používání dalších služeb Twilio
Kromě zde uvedených příkladů Twilio nabízí webová rozhraní API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní Twilio API][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Teď, když jste se seznámili se základy služby Twilio, můžete získat další informace pomocí těchto odkazů:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio HowTos a ukázkový kód][twilio_howtos]
* [Kurzy rychlý Start pro Twilio][twilio_quickstarts] 
* [Twilio na GitHubu][twilio_on_github]
* [Kontaktujte podporu Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app