---
title: Jak používat Twilio pro hlas a SMS (Java) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v jazyce Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637209"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak používat Twilio pro hlasové a SMS schopnosti v Javě
Tato příručka ukazuje, jak provádět běžné úlohy programování se službou Twilio API v Azure. Zahrnuté scénáře zahrnují telefonování a odeslání zprávy SMS (Short Message Service). Další informace o Twilio a používání hlasu a SMS ve vašich aplikacích naleznete v části [Další kroky.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio je telefonní web-service API, které vám umožní používat stávající webové jazyky a dovednosti k vytváření hlasových a SMS aplikací. Twilio je služba třetí strany (není funkce Azure a ne produkt microsoftu).

**Twilio Voice** umožňuje vašim aplikacím uskutečňovat a přijímat telefonní hovory. **Twilio SMS** umožňuje vašim aplikacím přijímat a přijímat SMS zprávy. **Klient Twilio** umožňuje vašim aplikacím povolit hlasovou komunikaci pomocí stávajících připojení k Internetu, včetně mobilních připojení.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio a speciální nabídky
Informace o cenách Twilio jsou k dispozici na [twilio cenách][twilio_pricing]. Zákazníci Azure obdrží [speciální nabídku:][special_offer]bezplatný kredit 1000 textů nebo 1000 příchozích minut. Chcete-li se zaregistrovat k této [https://ahoy.twilio.com/azure][special_offer]nabídce nebo získat více informací, navštivte .

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Twilio API je RESTful API, které poskytuje hlasové a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici ve více jazycích. Seznam naleznete v tématu [Knihovny rozhraní API Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou slovesa Twilio a twilio markup language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Slovesa
Rozhraní API využívá slovesa Twilio; Například ** &lt;Say&gt; ** sloveso pokyn Twilio slyšitelně doručit zprávu na volání.

Následuje seznam sloves Twilio.

* **Vytočit&gt;: Připojí volajícího k jinému &lt;** telefonu.
* **Sbírat&gt;: Shromažďuje číselné číslice zadané na klávesnici &lt;** telefonu.
* **Hangup&gt;: Ukončí &lt;** hovor.
* **Přehrát&gt;: Přehraje zvukový &lt;** soubor.
* **Fronta&gt;: Přidat do fronty volajících. &lt;**
* **Pauza&gt;: Tiše čeká na zadaný počet &lt;** sekund.
* Záznam : Zaznamená hlas volajícího a vrátí adresu URL souboru, který záznam obsahuje. ** &lt;&gt;**
* Přesměrování : Přenese řízení hovoru nebo SMS na TwiML na jinou adresu URL. ** &lt;&gt;**
* **Odmítnout&gt;: Odmítne příchozí hovor na vaše číslo Twilio, aniž by vám to fakturuje. &lt;**
* Say : Převede text na řeč, která je provedena na volání. ** &lt;&gt;**
* Sms : Odešle SMS zprávu. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>Twiml
TwiML je sada pokynů založených na XML založených na slovesa Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Jako příklad, následující TwiML převést text **Hello World!** k řeči.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Když vaše aplikace volá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, která vrací odpověď TwiML. Pro účely vývoje můžete pomocí adres URL poskytovaných twilio poskytnout odpovědi TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o slovesách Twilio, jejich atributech a TwiML naleznete v [tématu TwiML][twiml]. Další informace o rozhraní TWILIO API naleznete v tématu [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se na [Try Twilio][try_twilio]. Můžete začít s bezplatným účtem a později svůj účet upgradovat.

Když si zaregistrujete účet Twilio, obdržíte ID účtu a ověřovací token. Oba budou potřebné k volání rozhraní API Twilio. Chcete-li zabránit neoprávněnému přístupu k účtu, zabezpečte ověřovací token. ID vašeho účtu a ověřovací token lze zobrazit v [konzole Twilio][twilio_console]console v polích označených **SID účtu** a **AUTH TOKEN**.

## <a name="create-a-java-application"></a><a id="create_app"></a>Vytvoření aplikace v Javě
1. Získejte Twilio JAR a přidejte jej do cesty sestavení Java a sestavení nasazení WAR. Na [https://github.com/twilio/twilio-java][twilio_java], můžete stáhnout zdroje GitHub a vytvořit si vlastní JAR, nebo stáhnout předem sestavené JAR (s nebo bez závislostí).
2. Ujistěte se, že vaše **jdk cacerts** keystore obsahuje certifikát Equifax Secure Certificate Authority s otisčivým otiskem MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 3 5:DE:F4:CF a otisk SHA1 je D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Jedná se o certifikát certifikační autority [https://api.twilio.com][twilio_api_service] (CA) pro službu, který se nazývá při použití twilio api. Informace o tom, že úložiště **klíčů cacerts** sady JDK obsahuje správný certifikát certifikační autority, naleznete [v tématu Přidání certifikátu do úložiště certifikátů java ca][add_ca_cert].

Podrobné pokyny pro použití klientské knihovny Twilio pro Jazyk Java jsou k dispozici na adrese [Jak provést telefonní hovor pomocí Twilio v aplikaci Java v Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
V rámci kódu můžete přidat **příkazy importu** v horní části zdrojových souborů pro balíčky Twilio nebo třídy, které chcete použít ve vaší aplikaci.

Pro zdrojové soubory Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Pro zdrojové soubory stránky Java Server Page (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
V závislosti na tom, které balíčky Twilio nebo třídy, které chcete použít, se vaše **příkazy importu** mohou lišit.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postup: Volání odchozího volání
Následující text ukazuje, jak provést odchozí volání pomocí **Call** třídy. Tento kód také používá web poskytovaný twilio vrátit twilio markup jazyk (TwiML) odpověď. Nahraďte své hodnoty pro **od** a **na** telefonní čísla, a ujistěte se, že jste ověřit **z** telefonního čísla pro váš účet Twilio před spuštěním kódu.

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

Další informace o parametrech předaných metodě **Call.creator** naleznete v tématu [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak již bylo zmíněno, tento kód používá web poskytnutý Twilio k vrácení odpovědi TwiML. Místo toho můžete použít vlastní web k poskytnutí odpovědi TwiML; Další informace najdete v tématu [Jak poskytnout odpovědi TwiML v aplikaci Java v Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postup: Odeslání SMS zprávy
Následující ukazuje, jak odeslat zprávu SMS pomocí **Message** třídy. Číslo **od** **4155992671**, je poskytováno Twilio pro zkušební účty pro odesílání SMS zpráv. Před **to** spuštěním kódu musí být číslo do ověřeno pro váš účet Twilio.

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

Další informace o parametrech předaných metodě **Message.creator** naleznete v tématu [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postup: Poskytněte odpovědi TwiML z vašich vlastních webových stránek
Když vaše aplikace zahájí volání rozhraní API Twilio, například prostřednictvím **callcreator.create** metody, Twilio odešle váš požadavek na adresu URL, která se očekává, že vrátí odpověď TwiML. Výše uvedený příklad používá adresu URL [https://twimlets.com/message][twimlet_message_url]poskytnutou twilio . (Zatímco TwiML je určen pro použití webovými službami, můžete zobrazit TwiML ve vašem prohlížeči. Klepnutím například [https://twimlets.com/message][twimlet_message_url] zobrazíte prázdný ** &lt;&gt; ** prvek odpovědi. Jako další příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] klepnutím zobrazíte prvek ** &lt;Response,&gt; ** který obsahuje prvek ** &lt;&gt; Say.)**

Místo spoléhání se na adresu URL poskytovanou twilio můžete vytvořit vlastní web URL, který vrací odpovědi HTTP. Web můžete vytvořit v libovolném jazyce, který vrací odpovědi HTTP. Toto téma předpokládá, že budete hostitelem adresy URL na stránce JSP.

Následující stránka JSP má za následek odpověď TwiML, která říká **Hello World!** na výzvu.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Následující stránka JSP má za následek odpověď TwiML, která říká, že nějaký text, má několik pozastaví a říká informace o verzi rozhraní API Twilio a název role Azure.

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

Parametr **ApiVersion** je k dispozici v hlasových požadavcích Twilio (nikoli sms požadavků). Dostupné parametry požadavku pro hlasové a SMS požadavky Twilio naleznete v tématu <https://www.twilio.com/docs/api/twiml/twilio_request> a <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, resp. Proměnná prostředí **RoleName** je dostupná jako součást nasazení Azure. (Pokud chcete přidat vlastní proměnné prostředí, aby [mohly][misc_role_config_settings]být vyzvednuty z **System.getenv**, viz část proměnné prostředí v různých nastavení konfigurace rolí .)

Jakmile máte stránku JSP nastavenou tak, aby poskytovala odpovědi TwiML, použijte adresu URL stránky JSP jako adresu URL předanou metodě **Call.creator.** Pokud máte například webovou aplikaci s názvem MyTwiML nasazenou do hostované služby Azure a název stránky JSP je mytwiml.jsp, může být adresa URL předána **souboru Call.creator,** jak je znázorněno v následujícím textu:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další možností pro odpověď pomocí TwiML je třída **VoiceResponse,** která je k dispozici v balíčku **com.twilio.twiml.**

Další informace o používání Twilio v Azure s Javou najdete v [tématu Jak provést telefonní hovor pomocí Twilio v aplikaci Java v Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Postup: Použití dalších služeb Twilio
Kromě příkladů uvedených zde nabízí Twilio webová řešení API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci k rozhraní TWILIO API][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Nyní, když jste se naučili základy služby Twilio, postupujte podle následujících odkazů a dozvíte se více:

* [Bezpečnostní pokyny Twilio][twilio_security_guidelines]
* [Twilio HowTo a příklad kódu][twilio_howtos]
* [Úvodní kurzy rychlého startu Twilio][twilio_quickstarts]
* [Twilio na GitHubu][twilio_on_github]
* [Promluvte si s podporou Twilio][twilio_support]

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
