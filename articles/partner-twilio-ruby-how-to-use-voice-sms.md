---
title: Jak používat Twilio pro hlasový a serveru SMS (Ruby) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu jsou vytvořeny v Ruby.
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
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866076"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Jak používat Twilio pro hlasový a možnosti serveru SMS v Ruby
Tato příručka ukazuje, jak provádět běžné úkoly programování službou Twilio rozhraní API v Azure. Pokryté scénáře zahrnují uskutečnění telefonního hovoru a odesílání zprávy služby krátké zprávy (SMS). Další informace o Twilio a používání hlasového a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) části.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je telefonickou rozhraní API webové služby, který vám umožní používat existující webové jazyky a dovednosti pro sestavení hlasu a aplikace serveru SMS. Twilio je služba třetích stran (ne funkcí platformy Azure a produkt společnosti Microsoft).

**Hlasové Twilio** umožňuje aplikacím zkontrolujte a příjem telefonních hovorů. **Twilio SMS** umožňuje aplikacím zkontrolujte a přijímat zprávy SMS. **Klient Twilio** umožňuje aplikace tak, aby hlasové komunikace pomocí existujícího připojení k Internetu, včetně mobilních připojení.

## <a id="Pricing"></a>Ceny Twilio a speciálních nabídek
Informace o cenách Twilio je k dispozici na [Twilio ceny][twilio_pricing]. Přijímat Azure zákazníků [speciální nabídka][special_offer]: volné platební 1000 textů nebo 1000 příchozí minut. Pokud chcete zaregistrovat v rámci této nabídky nebo získat další informace, navštivte [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilio je rozhraní RESTful API, která poskytuje hlas a funkce serveru SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [Twilio rozhraní API knihovny][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML je sada pokynů formátu XML, které informovat Twilio postup zpracování hovoru nebo SMS.

Jako příklad následující TwiML by převést text **Hello, World** k rozpoznávání řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Mají všechny dokumenty TwiML `<Response>` jako kořenový element. Z tohoto místa používáte Twilio příkazy k definování chování vaší aplikace.

### <a id="Verbs"></a>Příkazy TwiML
Příkazy Twilio jsou značky XML, které informují Twilio, co **provést**. Například  **&lt;indikované&gt;**  příkaz dá pokyn Twilio zpráva zvukově dodržujeme volání. 

Následuje seznam operací Twilio.

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

Další informace o Twilio příkazy, jejich atributů a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilio najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte si v [zkuste Twilio][try_twilio]. Můžete začít s bezplatný účet a upgradujte si účet později.

Když zaregistrujete k účtu Twilio, získáte bezplatné telefonní číslo pro vaši aplikaci. Také budete dostávat účet SID a tokenu ověřování. Jak bude potřeba k volání rozhraní API Twilio. Aby se zabránilo neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. SID účtu a ověření tokenu jsou viditelná na [stránku účtu Twilio][twilio_account], v polích s názvem bez přípony **SID účtu** a **ověřování TOKENU**, v uvedeném pořadí.

### <a id="VerifyPhoneNumbers"></a>Zkontrolujte telefonní čísla
Kromě číslo platí při Twilio, můžete si taky ověřit čísla (tj. vaše mobilní telefon nebo Domovská telefonní číslo) řízení pro použití v aplikacích. 

Informace o tom, jak ověření telefonního čísla najdete v tématu [spravovat čísla][verify_phone].

## <a id="create_app"></a>Vytvoření Ruby aplikace
Ruby aplikace, která používá službu Twilio a běží v Azure je nejsou jiné než jiná Ruby aplikace, která používá službu Twilio. Při Twilio služby jsou dosáhl standardu RESTful a lze volat z Ruby několika způsoby, v tomto článku se soustředí na použití služeb Twilio s [Twilio pomocné knihovny pro Ruby][twilio_ruby].

První, [nastavení nového virtuálního počítače Azure Linux] [ azure_vm_setup] tak, aby fungoval jako hostitele pro nový Ruby webové aplikace. Kroky týkající se vytváření aplikace, které právě vytvořeny virtuální počítač ignorujte. Ujistěte se, že vytvoříte koncový bod s externí port 80 a interní port 5000.

V následujících příkladech použijeme [Sinatra][sinatra], velmi jednoduché webové rozhraní pro Ruby. Ale určitě můžete Twilio pomocné knihovny pro Ruby s jakékoli jiné webové rozhraní, včetně Ruby, na které.

SSH do nového virtuálního počítače a vytvořte adresář pro nové aplikace. V tomto adresáři vytvořte soubor s názvem Gemfile a zkopírujte do ní následující kód:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na příkazovém řádku spusťte `bundle install`. Dojde k instalaci závislostí uvedených výše. Dále vytvořte soubor s názvem `web.rb`. Bude jím, kde je umístěn kódu pro webovou aplikaci. Vložte do něj následující kód:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

V tomto okamžiku byste měli mít možnost spustit příkaz `ruby web.rb -p 5000`. To bude roztočení malé webový server na portu 5000. Nyní byste měli mít vyhledejte tuto aplikaci v prohlížeči návštěvou URL jste nastavení pro virtuální počítač Azure. Jakmile se můžete dostat vaší webové aplikace v prohlížeči, jste připravení začít vytvářet aplikace Twilio.

## <a id="configure_app"></a>Konfigurace aplikace k používání Twilio
Můžete nakonfigurovat webové aplikace na používání knihovny, Twilio aktualizací vaší `Gemfile` zahrnout tento řádek:

    gem 'twilio-ruby'

Na příkazovém řádku spusťte `bundle install`. Nyní otevřete `web.rb` a včetně tento řádek v horní části:

    require 'twilio-ruby'

Jste nyní vše připraveno na používání knihovny Twilio pomocné rutiny pro Ruby ve vaší webové aplikaci.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provést odchozí volání. Klíčové koncepty zahrnují používat Twilio pomocné knihovny pro Ruby pro volání rozhraní REST API a vykreslování TwiML. Dosaďte svoje hodnoty **z** a **k** telefonních čísel a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

Přidejte tuto funkci k `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
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

Pokud otevřete až `http://yourdomain.cloudapp.net/make_call` v prohlížeči, který aktivuje volání rozhraní API Twilio telefonní hovor. První dva parametry v `client.account.calls.create` poměrně není potřeba vysvětlovat: počet volání je `from` a počet volání je `to`. 

Třetí parametr (`url`) je adresu URL, kterou chcete-li získat pokyny k tomu, co udělat po připojení volání požadavky Twilio. V tomto případě jsme nastavení a adresy URL (`http://yourdomain.cloudapp.net`), vrátí jednoduché TwiML dokument a používá `<Say>` příkaz dělat některé převod textu na řeč a uvést "Hello opic", aby osoba, která přijímá volání.

## <a id="howto_recieve_sms"></a>Postupy: Receive zpráva SMS
V předchozím příkladu jsme spustili **odchozí** telefonního hovoru. Tento čas, použijeme telefonní číslo, které jste nám během Twilio dali procesu registrace **příchozí** zprávy SMS.

První, přihlaste se k vaší [řídicí panel Twilio][twilio_account]. Klikněte na "Čísla" v horním navigaci a potom klikněte na číslo Twilio, byla zadaná. Zobrazí se dvou adres URL, která se dají konfigurovat. Adresa URL požadavku na adrese URL žádosti hlasu a serveru služby SMS. Tyto jsou adresy URL, které volá Twilio pokaždé, když se provádí telefonní hovor nebo serveru služby SMS je odeslaný na vaše číslo. Adresy URL se také označuje jako "web háky".

Rádi bychom se zpracovává příchozí zprávy SMS, tak umožňuje aktualizovat adresu URL `http://yourdomain.cloudapp.net/sms_url`. Pokračujte a klikněte na tlačítko Uložit změny v dolní části stránky. Nyní, zpět na `web.rb` umožňuje programu naše aplikace pro zpracování toto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po provedení změny, zajistěte, aby na restartování webové aplikace. Teď vyjměte telefonu a odeslat zprávu SMS na vaše číslo Twilio. Měli byste rychle získat odpověď serveru SMS, která říká "blogu Hey, Děkujeme za příkazem ping! Twilio a Azure rock! ".

## <a id="additional_services"></a>Postupy: použití další Twilio služeb
Kromě příkladů tady uvedené nabízí Twilio rozhraní API založené na webu, který můžete využít další funkce Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služby Twilio, postupujte podle následujících odkazech na další informace:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio HowTos a ukázkový kód][twilio_howtos]
* [Kurzy Twilio rychlý start][twilio_quickstarts] 
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
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
