---
title: Postup použití Twilia pro hlasové hovory a SMS (Ruby) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v Ruby.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 41b5383dd319f2cb6fad4316e963f86dd7a4bc61
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036604"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Postup použití Twilia pro hlasové hovory a SMS v Ruby
Tato příručka ukazuje, jak k provádění běžných programovacích úloh pomocí služby Twilio API v Azure. Pokryté scénáře zahrnují vytváření telefonních hovorů a posílání zpráv služby krátké zprávy (SMS). Další informace o Twilio a používání hlasové hovory a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) oddílu.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je rozhraní API webové služby telefonního subsystému, která umožňuje využívat vaše existující webových jazyků a dovednosti k sestavení hlasové hovory a SMS aplikace. Twilio je služba třetí strany (ne funkcí platformy Azure a produkt společnosti Microsoft).

**Twilio pro hlasové hovory** umožňuje vašim aplikacím, ujistěte se, a přijímat telefonní hovory. **Twilio SMS** umožňuje vašim aplikacím, ujistěte se, a přijímat zprávy SMS. **Twilio klienta** umožňuje vašim aplikacím, chcete-li umožnit hlasovou komunikaci prostřednictvím existujících připojení k Internetu včetně mobilních.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Informace o cenách služby Twilio je k dispozici na [Twilio ceny][twilio_pricing]. Zákazníkům Azure poskytujeme [speciální nabídka][special_offer]: bezplatný kredit ve výši 1 000 textů nebo 1000 příchozí minut. Pokud chcete zaregistrovat v rámci této nabídky nebo získat další informace, navštivte prosím [ http://ahoy.twilio.com/azure ] [ special_offer].  

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilia je rozhraní RESTful API, která poskytuje hlasové hovory a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [knihovny rozhraní API Twilia][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML je sada instrukcí se založený na formátu XML, které informují o Twilio, jak zpracovat hovor nebo SMS.

Například následující TwiML by převést text **Hello World** do mluvené řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Všechny dokumenty TwiML mají `<Response>` jako jeho kořenový element. Odtud použít Twilio příkazy definují chování aplikace.

### <a id="Verbs"></a>Příkazy TwiML
Twilio příkazy jsou značky XML, které informují Twilio, jak **proveďte**. Například **&lt;Say&gt;** příkaz nastaví Twilio zvukově doručení zprávy na volání. 

Následuje seznam příkazů Twilio.

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

Další informace o Twilio příkazů, jejich atributy a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilia najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni začít účtu Twilio, zaregistrujte se na [zkuste Twilio][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Po registraci účtu Twilio, získáte bezplatné telefonní číslo pro vaši aplikaci. Budou se zobrazovat také účet SID a ověřovací token. Obojí je potřeba volat rozhraní API Twilia. Pokud chcete zabránit neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. SID účtu a ověřovací token, jsou zobrazené na [stránka účtu Twilio][twilio_account], v polích s popiskem **SID účtu** a **Ověřovacím TOKENEM**, v uvedeném pořadí.

### <a id="VerifyPhoneNumbers"></a>Ověřit telefonní čísla
Kromě číslo jsou dána Twilio, můžete si taky ověřit čísla, kterou řídíte (například mobilní telefon nebo Domovská stránka telefonní číslo) pro použití ve vašich aplikacích. 

Informace o tom, jak ověřit telefonní číslo, naleznete v tématu [spravovat čísla][verify_phone].

## <a id="create_app"></a>Vytvoření aplikace v Ruby
Aplikace Ruby, který používá služba Twilio a běží v Azure se nijak neliší od jakékoli jiné aplikace v Ruby, který používá služba Twilio. I když Twilio služby RESTful a může být volána z Ruby několika způsoby, tento článek se zaměří na použití služby Twilio [Twilio pomocné knihovny pro Ruby][twilio_ruby].

Nejprve je potřeba [nastavení nového virtuálního počítače Linux Azure] [ azure_vm_setup] tak, aby fungoval jako hostitel pro nové Ruby webové aplikace. Ignorujte kroky týkající se vytvoření aplikace Rails, jenom nastavení virtuálního počítače. Ujistěte se, že vytvoříte koncový bod s externí port 80 a interní port 5000.

V následujících příkladech použijeme [Sinatra][sinatra], velmi jednoduchá webová architektura pro Ruby. Ale určitě můžete použít Twilio pomocné knihovny pro Ruby pomocí libovolné jiné webové architektury, včetně Ruby on Rails.

Připojte se přes SSH nový virtuální počítač a vytvořte adresář pro novou aplikaci. V tomto adresáři vytvořte soubor s názvem Gemfile a zkopírujte do něj následující kód:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na příkazovém řádku spusťte `bundle install`. Tím se nainstaluje výše uvedených závislostí. Dále vytvořte soubor s názvem `web.rb`. To bude, kde se nachází kód vaší webové aplikace. Vložte do něj následující kód:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

V tomto okamžiku by měla být možné spustit příkaz `ruby web.rb -p 5000`. To bude typu číselník – až malé webového serveru na portu 5000. Je třeba přejít k této aplikaci v prohlížeči návštěvou adresu URL můžete nastavení pro váš virtuální počítač Azure. Jakmile dosáhnete vaší webové aplikace v prohlížeči, jste připraveni začít vytvářet aplikace Twilio.

## <a id="configure_app"></a>Konfigurace aplikace pro použití Twilia
Můžete nakonfigurovat webovou aplikaci pro použití knihovny Twilio prostřednictvím aktualizace vašeho `Gemfile` zahrnout tento řádek:

    gem 'twilio-ruby'

Na příkazovém řádku spusťte `bundle install`. Nyní otevřete `web.rb` a tento řádek v horní části, včetně:

    require 'twilio-ruby'

Všechno teď je nastavené na použití pomocné knihovny Twilio pro Ruby ve vaší webové aplikaci.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provést odchozích volání. Klíčové koncepty zahrnout pomocí Twilio pomocné knihovny pro Ruby k volání rozhraní REST API a vykreslování TwiML. Dosaďte svoje hodnoty **z** a **k** telefonní čísla a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

Přidejte tuto funkci, která `web.md`:

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

Pokud otevřete nahoru `http://yourdomain.cloudapp.net/make_call` v prohlížeči, která aktivuje volání rozhraní API Twilia telefonní hovor. První dva parametry v `client.account.calls.create` není potřeba vysvětlovat poměrně: počet volání je `from` a počet volání je `to`. 

Třetí parametr (`url`) je adresa URL, která se zobrazí pokyny o tom, jak udělat po připojení volání vyžaduje Twilio. V tomto případě můžeme nastavit adresu URL (`http://yourdomain.cloudapp.net`), který vrací jednoduchý TwiML dokumentu a používá `<Say>` příkaz některé převod textu na řeč a říct "Dobrý den opic" příjemce volání.

## <a id="howto_recieve_sms"></a>Postupy: přijmout zprávu SMS
V předchozím příkladu jsme spustili **odchozí** telefonního hovoru. Tento čas, použijeme zadané telefonní číslo Twilio nemusíme během procesu registrace **příchozí** SMS zprávy.

První, přihlaste se k vaší [řídicí panel Twilio][twilio_account]. Klikněte na "Čísla" v horním navigačním podokně a potom klikněte na číslo Twilio, které bylo zadáno. Uvidíte dvě adresy URL, které můžete nakonfigurovat. Adresa URL požadavku na adrese URL žádosti hlasu a zprávu SMS. Toto jsou adresy URL, které Twilio volá vždy, když proběhne telefonický hovor nebo zprávu SMS je odeslána na vaše číslo. Adresy URL jsou také označovány jako "web hooks".

Rádi bychom se zpracování příchozích zpráv SMS, můžeme aktualizovat adresu URL tak `http://yourdomain.cloudapp.net/sms_url`. Pokračujte a klikněte na Uložit změny v dolní části stránky. Nyní, zase `web.rb` Pojďme naši aplikaci ke zvládnutí této programu:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po provedení změn, ujistěte se, že znovu spustit webové aplikace. Nyní vyjměte telefonu a odeslat zprávu SMS na vaše číslo Twilio. O tom bezodkladně informuje byste měli získat odpověď serveru SMS, že "Hey, Děkujeme, že příkaz ping! Twilio a Azure rock! ".

## <a id="additional_services"></a>Postupy: použití služby Twilio další
Kromě příkladů uvedených v tomto poli Twilio nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce platformy Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API Twilia][twilio_api_documentation].

### <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služba Twilio, použijte tyto odkazy na další informace:

* [Pokyny pro zabezpečení platformy Twilio][twilio_security_guidelines]
* [Twilio HowTos a příklady kódu][twilio_howtos]
* [Kurzy rychlý start Twilio][twilio_quickstarts] 
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
