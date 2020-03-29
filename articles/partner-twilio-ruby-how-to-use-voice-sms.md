---
title: Jak používat Twilio pro hlas a SMS (Ruby) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v Ruby.
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
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637203"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Jak používat Twilio pro hlasové a SMS schopnosti v Ruby
Tato příručka ukazuje, jak provádět běžné úlohy programování se službou Twilio API v Azure. Zahrnuté scénáře zahrnují telefonování a odeslání zprávy SMS (Short Message Service). Další informace o Twilio a používání hlasu a SMS ve vašich aplikacích naleznete v části [Další kroky.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio je telefonní web-service API, které vám umožní používat stávající webové jazyky a dovednosti k vytváření hlasových a SMS aplikací. Twilio je služba třetí strany (není funkce Azure a ne produkt microsoftu).

**Twilio Voice** umožňuje vašim aplikacím uskutečňovat a přijímat telefonní hovory. **Twilio SMS** umožňuje vašim aplikacím přijímat a přijímat SMS zprávy. **Klient Twilio** umožňuje vašim aplikacím povolit hlasovou komunikaci pomocí stávajících připojení k Internetu, včetně mobilních připojení.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio a speciální nabídky
Informace o cenách Twilio jsou k dispozici na [twilio cenách][twilio_pricing]. Zákazníci Azure obdrží [speciální nabídku:][special_offer]bezplatný kredit 1000 textů nebo 1000 příchozích minut. Chcete-li se zaregistrovat k této [https://ahoy.twilio.com/azure][special_offer]nabídce nebo získat více informací, navštivte .  

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Twilio API je RESTful API, které poskytuje hlasové a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici ve více jazycích. Seznam naleznete v tématu [Knihovny rozhraní API Twilio][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>Twiml
TwiML je sada pokynů založených na jazyce XML, které informují Twilio o tom, jak zpracovat hovor nebo SMS.

Jako příklad následující TwiML převede text **Hello World** na řeč.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Všechny dokumenty TwiML mají `<Response>` jako svůj kořenový prvek. Odtud pomocí Twilio slovesa definovat chování vaší aplikace.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Slovesa TwiML
Twilio Slovesa jsou XML značky, které říkají Twilio, co **má dělat**. Například ** &lt;Say&gt; ** sloveso pokyn Twilio slyšitelně doručit zprávu na volání. 

Následuje seznam sloves Twilio.

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

Další informace o slovesách Twilio, jejich atributech a TwiML naleznete v [tématu TwiML][twiml]. Další informace o rozhraní TWILIO API naleznete v tématu [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se na [Try Twilio][try_twilio]. Můžete začít s bezplatným účtem a později svůj účet upgradovat.

Když si zaregistrujete účet Twilio, získáte bezplatné telefonní číslo pro vaši aplikaci. Obdržíte také sid účtu a auth token. Oba budou potřebné k volání rozhraní API Twilio. Chcete-li zabránit neoprávněnému přístupu k účtu, zabezpečte ověřovací token. Váš účet SID a auth token jsou viditelné na [stránce účtu Twilio][twilio_account], v polích označených **ACCOUNT SID** a **AUTH TOKEN**.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Ověření telefonních čísel
Kromě čísla, které vám společnost Twilio zadá, můžete také ověřit čísla, která ovládáte (tj. číslo mobilního telefonu nebo domácího telefonu) pro použití ve vašich aplikacích. 

Informace o ověření telefonního čísla naleznete v tématu [Správa čísel][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Vytvoření aplikace Ruby
Aplikace Ruby, která používá službu Twilio a běží v Azure se nijak neliší od jakékoli jiné aplikace Ruby, která používá službu Twilio. Zatímco Twilio služby jsou RESTful a mohou být volány z Ruby několika způsoby, tento článek se zaměří na to, jak používat služby Twilio s [Twilio pomocné knihovny pro Ruby][twilio_ruby].

Nejprve [nastavte nový virtuální počítač Azure Linux,][azure_vm_setup] který bude fungovat jako hostitel pro vaši novou webovou aplikaci Ruby. Ignorovat kroky zahrnující vytvoření aplikace Rails, stačí nastavit virtuální ho. Ujistěte se, že vytvoříte koncový bod s externím portem 80 a vnitřním portem 5000.

V níže uvedených příkladech budeme používat [Sinatru][sinatra], velmi jednoduchý webový rámec pro Ruby. Ale určitě můžete použít pomocnou knihovnu Twilio pro Ruby s jakýmkoli jiným webovým rámcem, včetně Ruby on Rails.

SSH do nového virtuálního počítače a vytvořte adresář pro novou aplikaci. Uvnitř tohoto adresáře vytvořte soubor s názvem Gemfile a zkopírujte do něj následující kód:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na příkazovém `bundle install`řádku spustit . Tím nainstalujete výše uvedené závislosti. Dále vytvořte `web.rb`soubor s názvem . To bude místo, kde kód pro vaši webovou aplikaci žije. Vložte do něj následující kód:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

V tomto okamžiku byste měli být `ruby web.rb -p 5000`schopni spustit příkaz . To bude spin-up malý webový server na portu 5000. Měli byste být schopni přejít na tuto aplikaci ve vašem prohlížeči pomocí adresy URL, kterou jste nastavili pro virtuální počítač Azure. Jakmile se v prohlížeči dostanete do webové aplikace, můžete začít vytvářet aplikaci Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurace aplikace pro použití twilio
Webovou aplikaci můžete nakonfigurovat tak, aby používala knihovnu Twilio, a to tak, že aktualizujete takto, aby `Gemfile` zahrnovala tento řádek:

    gem 'twilio-ruby'

Na příkazovém `bundle install`řádku spusťte . Nyní `web.rb` otevřít a včetně tohoto řádku v horní části:

    require 'twilio-ruby'

Nyní máte vše nastaveno na používání pomocné knihovny Twilio pro Ruby ve vaší webové aplikaci.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postup: Volání odchozího volání
Následující text ukazuje, jak uskutečnovat odchozí hovor. Mezi klíčové koncepty patří použití pomocné knihovny Twilio pro Ruby k volání rozhraní REST API a vykreslování TwiML. Nahraďte své hodnoty telefonními čísly **Od** a **Do** a před spuštěním kódu ověřte telefonní číslo **Od** vašeho účtu Twilio.

Přidat tuto `web.md`funkci do :

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

Pokud otevřete `http://yourdomain.cloudapp.net/make_call` v prohlížeči, spustí se volání rozhraní API Twilio, aby se telefonní hovor. První dva parametry `client.account.calls.create` v jsou poměrně samozřejmé: číslo volání `from` je a číslo `to`volání je . 

Třetí parametr`url`( ) je adresa URL, kterou Twilio požaduje, aby získal pokyny k tomu, co dělat po připojení hovoru. V tomto případě nastavíme`http://yourdomain.cloudapp.net`adresu URL ( ), která vrací `<Say>` jednoduchý dokument TwiML a používá sloveso k tomu, aby někdo řekl text na řeč a řekl "Hello Monkey" osobě, která přijímá hovor.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Postup: Příjem SMS zprávy
V předchozím příkladu jsme zahájili **odchozí** telefonní hovor. Tentokrát použijeme telefonní číslo, které nám Twilio dal během registrace, abychom zpracovali **příchozí** SMS zprávu.

Nejprve se přihlaste k [řídicímu panelu Twilio][twilio_account]. Klikněte na "Čísla" v horní nánosy a pak klikněte na číslo Twilio, které byly poskytnuty. Zobrazí se dvě adresy URL, které můžete nakonfigurovat. Adresa URL hlasové žádosti a adresa URL požadavku SMS. Jedná se o adresy URL, které Twilio volá vždy, když je uskutečněn telefonní hovor nebo sms je odeslána na vaše číslo. Adresy URL jsou také známé jako "webové háčky".

Rádi bychom zpracovali příchozí SMS zprávy, takže aktualizujte adresu URL na `http://yourdomain.cloudapp.net/sms_url`. Jděte do toho a v dolní části stránky klikněte na Uložit změny. Nyní, zpět `web.rb` do pojďme program naší aplikace zvládnout toto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po provedení změny nezapomeňte webovou aplikaci znovu spustit. Nyní vynesete telefon a pošlete SMS na číslo Twilio. Měli byste okamžitě dostat SMS odpověď, která říká: "Hej, díky za ping! Twilio a Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Postup: Použití dalších služeb Twilio
Kromě příkladů uvedených zde nabízí Twilio webová řešení API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci k rozhraní TWILIO API][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Nyní, když jste se naučili základy služby Twilio, postupujte podle následujících odkazů a dozvíte se více:

* [Bezpečnostní pokyny Twilio][twilio_security_guidelines]
* [Twilio HowTos a příklad kódu][twilio_howtos]
* [Úvodní kurzy rychlého startu Twilio][twilio_quickstarts] 
* [Twilio na GitHubu][twilio_on_github]
* [Promluvte si s podporou Twilio][twilio_support]

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
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
