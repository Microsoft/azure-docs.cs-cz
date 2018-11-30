---
title: Postup použití Twilia pro hlasové hovory a SMS (Java) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v jazyce Java.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423360"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Postup použití Twilia pro hlasové hovory a SMS v jazyce Java
Tato příručka ukazuje, jak k provádění běžných programovacích úloh pomocí služby Twilio API v Azure. Pokryté scénáře zahrnují vytváření telefonních hovorů a posílání zpráv služby krátké zprávy (SMS). Další informace o Twilio a používání hlasové hovory a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) oddílu.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je rozhraní API webové služby telefonního subsystému, která umožňuje využívat vaše existující webových jazyků a dovednosti k sestavení hlasové hovory a SMS aplikace. Twilio je služba třetí strany (ne funkcí platformy Azure a produkt společnosti Microsoft).

**Twilio pro hlasové hovory** umožňuje vašim aplikacím, ujistěte se, a přijímat telefonní hovory. **Twilio SMS** umožňuje vašim aplikacím, ujistěte se, a přijímat zprávy SMS. **Twilio klienta** umožňuje vašim aplikacím, chcete-li umožnit hlasovou komunikaci prostřednictvím existujících připojení k Internetu včetně mobilních.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Informace o cenách služby Twilio je k dispozici na [Twilio ceny][twilio_pricing]. Zákazníkům Azure poskytujeme [speciální nabídka][special_offer]: bezplatný kredit ve výši 1 000 textů nebo 1000 příchozí minut. Pokud chcete zaregistrovat v rámci této nabídky nebo získat další informace, navštivte prosím [ https://ahoy.twilio.com/azure ] [ special_offer].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilia je rozhraní RESTful API, která poskytuje hlasové hovory a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [knihovny rozhraní API Twilia][twilio_libraries].

Klíčové aspekty Twilio API jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazů například **&lt;Say&gt;** příkaz nastaví Twilio zvukově doručení zprávy na volání.

Následuje seznam příkazů Twilio.

* **&lt;Volání&gt;**: volající se připojí k jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončení volání.
* **&lt;Přehrát&gt;**: přehraje zvukový soubor.
* **&lt;Fronty&gt;**: přidejte do fronty pro volající.
* **&lt;Pozastavit&gt;**: bezobslužná čeká na zadaný počet sekund.
* **&lt;Záznam&gt;**: zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* **&lt;Přesměrovat&gt;**: převede ovládací prvek hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: bez fakturace je odmítne příchozí volání na vaše číslo Twilio.
* **&lt;Řekněme, že&gt;**: převede text na řeč, který je k volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada instrukcí založený na formátu XML podle Twilio příkazy, které informují o Twilio, jak zpracovat hovor nebo SMS.

Například následující TwiML by převést text **Hello World!** do mluvené řeči.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Pokud vaše aplikace volá rozhraní API pro Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje můžete použít adresy URL zadané Twilio zodpovězení TwiML používají vaše aplikace. Může taky hostovat vlastní adresy URL pro vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazů, jejich atributy a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilia najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni začít účtu Twilio, zaregistrujte se na [zkuste Twilio][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio, obdržíte ID účtu a ověřovací token. Obojí je potřeba volat rozhraní API Twilia. Pokud chcete zabránit neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování jsou zobrazené v tokenu [Twilio konzoly][twilio_console], v polích s popiskem **SID účtu** a **Ověřovacím TOKENEM**v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace v Javě
1. Získat Twilio JAR a přidejte ho a vaše cesta sestavení Java WAR nasazení sestavení. Na [ https://github.com/twilio/twilio-java ] [ twilio_java], můžete stáhnout kódů Githubu a vytvořit svůj vlastní soubor JAR nebo stažení předem vytvořených JAR (s nebo bez závislosti).
2. Zkontrolujte vaše JDK **cacerts** úložiště klíčů obsahuje Equifax zabezpečení certifikační autority certifikát s otiskem prstu 67:CB:9 MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 35:DE:F4:CF a SHA1 otisk prstu se D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Toto je certifikát certifikační autority pro [ https://api.twilio.com ] [ twilio_api_service] službu, která je volána, když používáte API twilia můžete volat. Informace o zajištění vašeho JDK **cacerts** úložiště klíčů obsahuje správný certifikát certifikační Autority, najdete v článku [přidání certifikátu do Store certifikát certifikační Autority Java][add_ca_cert].

Podrobné pokyny k používání Twilio klientské knihovny pro Javu najdete na adrese [jak provádět v aplikaci Java v Azure Twilio pomocí telefonního hovoru][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurace aplikace pro používání knihovny platformy Twilio
V rámci kódu, můžete přidat **importovat** příkazů v horní části zdrojové soubory pro Twilio balíčky nebo třídy, kterou chcete použít v aplikaci.

Pro zdrojové soubory Javy:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Pro zdrojové soubory stránky serveru Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
V závislosti na tom, které balíčky Twilio nebo třídy, kterou chcete použít, vaše **importovat** příkazy se můžou lišit.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provádět volání pomocí odchozí **volání** třídy. Tento kód také používá k vrácení odpovědi Twilio Markup Language (TwiML) poskytované Twilio lokality. Dosaďte svoje hodnoty **z** a **k** telefonní čísla a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další informace o parametrech předaná **Call.creator** metodu, najdete v článku [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Jak už bylo zmíněno, tento kód používá poskytované Twilio webu vrátit TwiML odpovědi. Můžete místo toho použít svůj vlastní web k poskytování odezvy TwiML; Další informace najdete v tématu [jak poskytnout TwiML odpovědi v aplikace v Javě v Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání SMS zprávy
Následující ukazuje, jak odeslat zprávu SMS pomocí **zpráva** třídy. **z** číslo, **4155992671**, poskytuje Twilio pro účty posílat SMS zprávy. **k** číslo musí být ověřený pro váš účet Twilio před spuštěním kódu.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Další informace o parametrech předaná **Message.creator** metodu, najdete v článku [ https://www.twilio.com/docs/api/rest/sending-sms ] [ twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odezvy z vlastního webu
Pokud aplikace zahájí volání rozhraní API Twilia například prostřednictvím **CallCreator.create** metoda, Twilio odešlete žádost na adresu URL, která se očekává navrácení TwiML odpovědi. Výše uvedený příklad používá adresu URL poskytnutou Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (TwiML je určen k použití webových služeb, ale když zobrazíte TwiML v prohlížeči. Klikněte například na [ https://twimlets.com/message ] [ twimlet_message_url] zobrazíte prázdná **&lt;odpovědi&gt;** element; další příklad, klikněte na tlačítko [ https://twimlets.com/message?Message%5B0%5D=Hello%20World%21 ] [ twimlet_message_url_hello_world] zobrazíte **&lt;odpovědi&gt;** element, který obsahuje **&lt;Say&gt;** elementu.)

Aniž byste museli spoléhat na URL poskytnutou Twilio, můžete vytvořit vlastní adresu URL webu, který vrací odpovědi protokolu HTTP. Můžete vytvořit web v jakémkoli jazyce, který vrací odpovědi HTTP; Toto téma předpokládá, že budete hostovat na adresu URL v JSP stránky.

Výsledky následující stránky JSP v TwiML odpovědi s upozorněním **Hello World!** Při volání.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Následující stránky JSP výsledkem TwiML odpověď, která uvádí, že nějaký text, má několik pozastaví a uvádí, že informace o verzi rozhraní API platformy Twilio a názvu Azure role.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

**ApiVersion** parametr je k dispozici v Twiliu hlasové požadavky (ne požadavků SMS). Parametry žádosti k dispozici pro Twilio pro hlasové hovory a SMS požadavky najdete v tématu <https://www.twilio.com/docs/api/twiml/twilio_request> a <https://www.twilio.com/docs/api/twiml/sms/twilio_request>v uvedeném pořadí. **RoleName** proměnná prostředí je k dispozici jako součást nasazení Azure. (Pokud chcete přidat vlastní proměnné prostředí, takže se může být zachyceny z **System.getenv**, najdete v části proměnných prostředí [různá nastavení konfigurace Role] [ misc_role_config_settings].)

Jakmile máte stránku JSP nastavit na poskytování TwiML odezvy, použijte adresu URL stránky JSP jako adresa URL předaná do **Call.creator** metody. Například pokud máte webovou aplikaci s názvem MyTwiML nasadit do Azure hostované služby a název stránky JSP je mytwiml.jsp, adresa URL může být předán **Call.creator** jak je znázorněno v následujícím:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další možností pro odpovědi s TwiML je prostřednictvím **VoiceResponse** třída, která je k dispozici v **com.twilio.twiml** balíčku.

Další informace o používání Twilio v Azure pomocí Javy najdete v tématu [jak provádět v aplikaci Java v Azure Twilio pomocí telefonního hovoru][howto_phonecall_java].

## <a id="AdditionalServices"></a>Postupy: použití služby Twilio další
Kromě příkladů uvedených v tomto poli Twilio nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce platformy Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API Twilia][twilio_api_documentation].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služba Twilio, použijte tyto odkazy na další informace:

* [Pokyny pro zabezpečení platformy Twilio][twilio_security_guidelines]
* [Twilio postupy a příklady kódu][twilio_howtos]
* [Kurzy rychlý start Twilio][twilio_quickstarts]
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
