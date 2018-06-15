---
title: Jak používat Twilio pro hlasový a serveru SMS (Java) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu napsanou v jazyce Java.
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
ms.openlocfilehash: 5a1b2ffa160a31b639605242b651dc8d14e7a01b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866153"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak používat Twilio pro hlasový a možnosti serveru SMS v jazyce Java
Tato příručka ukazuje, jak provádět běžné úkoly programování službou Twilio rozhraní API v Azure. Pokryté scénáře zahrnují uskutečnění telefonního hovoru a odesílání zprávy služby krátké zprávy (SMS). Další informace o Twilio a používání hlasového a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) části.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je telefonickou rozhraní API webové služby, který vám umožní používat existující webové jazyky a dovednosti pro sestavení hlasu a aplikace serveru SMS. Twilio je služba třetích stran (ne funkcí platformy Azure a produkt společnosti Microsoft).

**Hlasové Twilio** umožňuje aplikacím zkontrolujte a příjem telefonních hovorů. **Twilio SMS** umožňuje aplikacím zkontrolujte a přijímat zprávy SMS. **Klient Twilio** umožňuje aplikace tak, aby hlasové komunikace pomocí existujícího připojení k Internetu, včetně mobilních připojení.

## <a id="Pricing"></a>Ceny Twilio a speciálních nabídek
Informace o cenách Twilio je k dispozici na [Twilio ceny][twilio_pricing]. Přijímat Azure zákazníků [speciální nabídka][special_offer]: volné platební 1000 textů nebo 1000 příchozí minut. Pokud chcete zaregistrovat v rámci této nabídky nebo získat další informace, navštivte [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilio je rozhraní RESTful API, která poskytuje hlas a funkce serveru SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [Twilio rozhraní API knihovny][twilio_libraries].

Klíčové aspekty rozhraní API Twilio jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazy; například  **&lt;indikované&gt;**  příkaz dá pokyn Twilio zpráva zvukově dodržujeme volání.

Následuje seznam operací Twilio.

* **&lt;Vytočit&gt;**: připojí volající na jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončí volání.
* **&lt;Přehrání&gt;**: přehraje zvukový soubor.
* **&lt;Fronty&gt;**: přidejte do fronty volající.
* **&lt;Pozastavení&gt;**: bezobslužná čeká na zadaném počtu sekund.
* **&lt;Záznam&gt;**: zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznamu.
* **&lt;Přesměrování&gt;**: předá řízení hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: odmítne příchozí volání na vaše číslo Twilio bez fakturace můžete.
* **&lt;Řekněme&gt;**: Převod převede textu na řeč, že z volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada založený na jazyce XML pokyny podle Twilio příkazy, které informují Twilio postup zpracování hovoru nebo SMS.

Jako příklad následující TwiML by převést text **Hello, World!** k rozpoznávání řeči.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Pokud aplikace zavolá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje URL můžete použít zadaný Twilio zajistit TwiML odpovědi použít v aplikacích. Může taky hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazy, jejich atributů a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilio najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte si v [zkuste Twilio][try_twilio]. Můžete začít s bezplatný účet a upgradujte si účet později.

Když se zaregistrujete k účtu Twilio, obdržíte ID účtu a ověřovací token. Jak bude potřeba k volání rozhraní API Twilio. Aby se zabránilo neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování tokenu je možné zobrazit na [Twilio konzoly][twilio_console], v polích s názvem bez přípony **SID účtu** a **ověřování TOKENU**, v uvedeném pořadí.

## <a id="create_app"></a>Vytvořit aplikaci Java
1. Získat Twilio JAR a přidat jej do vaší cesta sestavení Java a vaše WAR nasazení sestavení. V [https://github.com/twilio/twilio-java][twilio_java], můžete stáhnout zdroje Githubu a vytvořit vlastní JAR nebo stáhnout předdefinovaných JAR (s nebo bez závislosti).
2. Zkontrolujte vaše JDK **cacerts** úložiště klíčů obsahuje certifikát Equifax zabezpečení certifikační autority s MD5 otisk prstu 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 35:DE:F4:CF a otisk prstu SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Toto je certifikát certifikační autority certifikátu pro [https://api.twilio.com] [ twilio_api_service] služba, která je volána, když používáte rozhraní API Twilio. Informace o zajištění vaší JDK **cacerts** úložiště klíčů obsahuje správný certifikát certifikační Autority najdete v tématu [přidání certifikátu do úložiště certifikátů certifikační Autority Java][add_ca_cert].

Podrobné pokyny pro používání Twilio klientské knihovny pro jazyk Java jsou k dispozici v [postup proveďte v aplikaci Java v Azure Twilio pomocí telefonního hovoru][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurace aplikace k používání Twilio knihovny
V kódu, můžete přidat **importovat** příkazy v horní části vaší zdrojové soubory pro Twilio balíčky nebo třídy, kterou chcete použít v aplikaci.

Pro zdrojové soubory Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Pro zdrojové soubory Java serveru stránky (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
V závislosti na tom, které balíčky Twilio nebo třídy, kterou chcete použít, vaše **importovat** příkazy se můžou lišit.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak zajistit odchozí volání pomocí **volání** třídy. Tento kód také používá zadaný Twilio lokality k vrácení odpovědi Twilio Markup Language (TwiML). Dosaďte svoje hodnoty **z** a **k** telefonních čísel a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("http://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další informace o parametry předané do **Call.creator** metodu, najdete v části [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak je uvedeno, tento kód používá k vrácení odpovědi TwiML zadaný Twilio lokality. Můžete místo toho použít vlastní funkční web zajistit TwiML odpovědi; Další informace najdete v tématu [jak poskytnout TwiML odpovědi v aplikaci Java v Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující ukazuje, jak odeslat zprávu SMS pomocí **zpráva** třídy. **z** číslo, **4155992671**, zajišťuje Twilio pro zkušebními účty k odesílání zpráv serveru SMS. **k** číslo musí být ověřena pro váš účet Twilio před spuštěním kódu.

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

Další informace o parametry předané do **Message.creator** metodu, najdete v části [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odpovědí z vlastního webu
Pokud vaše aplikace zahájí volání rozhraní API Twilio například prostřednictvím **CallCreator.create** metody Twilio odešlete žádost na adresu URL, kterou je očekáván vrátí odpověď TwiML. V předchozím příkladu používá adresu URL poskytnutou Twilio [http://twimlets.com/message][twimlet_message_url]. (Při TwiML je určen k použití pomocí webové služby, můžete zobrazit TwiML v prohlížeči. For example, klikněte na tlačítko [http://twimlets.com/message] [ twimlet_message_url] zobrazíte prázdnou  **&lt;odpovědi&gt;**  element; například klikněte na tlačítko [http://twimlets.com/message?Message%5B0%5D=Hello%20World%21] [ twimlet_message_url_hello_world] zobrazíte  **&lt;odpovědi&gt;**  elementu, který obsahuje  **&lt;indikované&gt;**  elementu.)

Aniž byste museli spoléhat na adresu URL pro zadaný Twilio, můžete vytvořit vlastního webu adresu URL, která vrací odpovědi HTTP. Můžete vytvořit web v libovolném jazyce, který vrací odpovědi HTTP; Toto téma předpokládá, že budete hostování adresu URL na stránce JSP.

Na následující stránce JSP výsledkem TwiML odpovědi, která uvádí, že **Hello, World!** Při volání.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Na následující stránce JSP výsledkem TwiML odpověď, který je uveden text, má několik pozastaví a uvádí informace o verzi Twilio API a název Azure role.

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

**ApiVersion** parametr je k dispozici v žádostech o hlasové Twilio (ne požadavků SMS). Parametry žádosti k dispozici pro hlasové Twilio a žádosti o služby SMS najdete v sekci <https://www.twilio.com/docs/api/twiml/twilio_request> a <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, v uvedeném pořadí. **RoleName** proměnná prostředí je k dispozici jako součást nasazení služby Azure. (Pokud chcete přidat vlastní proměnné prostředí, aby se může být zachyceny z **System.getenv**, naleznete v části proměnných prostředí v [ostatní nastavení konfigurace Role][misc_role_config_settings].)

Jakmile máte stránku JSP nastavit na, zadejte TwiML odpovědi, použijte adresu URL stránky JSP jako adresu URL předaný do **Call.creator** metoda. Například pokud máte webovou aplikaci s názvem MyTwiML nasadit do Azure hostované služby a název stránky JSP je mytwiml.jsp, adresa URL se dá předat do **Call.creator** jak je znázorněno v následujícím:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další možnost reagovat s TwiML je prostřednictvím **VoiceResponse** třída, která je k dispozici v **com.twilio.twiml** balíčku.

Další informace o používání Twilio v Azure s Java najdete v tématu [postup proveďte v aplikaci Java v Azure Twilio pomocí telefonního hovoru][howto_phonecall_java].

## <a id="AdditionalServices"></a>Postupy: použití další Twilio služeb
Kromě příkladů tady uvedené nabízí Twilio rozhraní API založené na webu, který můžete využít další funkce Twilio vaše aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Další kroky
Teď, když jste se naučili základy služby Twilio, postupujte podle následujících odkazech na další informace:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio postupy a příklady kódu][twilio_howtos]
* [Kurzy Twilio rychlý start][twilio_quickstarts]
* [Twilio na Githubu][twilio_on_github]
* [Obraťte se na podporu Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/docs
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
