---
title: Jak používat Twilio pro hlas a SMS (Java) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v jazyce Java.
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
ms.custom: devx-track-java
ms.openlocfilehash: 5e44ae3280d3ca0f8d5052be10d0955ba342352f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652870"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Použití Twilio pro hlasové funkce a možnosti SMS v Java
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí služby Twilio API v Azure. Mezi zahrnuté scénáře patří telefonní hovor a odeslání zprávy o krátké službě zprávy (SMS). Další informace o Twilio a použití hlasu a SMS v aplikacích najdete v části [Další kroky](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio je telefonní rozhraní API pro webové služby, které umožňuje používat stávající webové jazyky a dovednosti k vytváření hlasových a SMS aplikací. Twilio je služba třetí strany (ne funkce Azure, nikoli produkt společnosti Microsoft).

**Twilio Voice** umožňuje vašim aplikacím vytvářet a přijímat telefonní hovory. **TWILIO SMS** umožňuje vašim aplikacím vytvářet a přijímat zprávy SMS. **Klient Twilio** umožňuje aplikacím povolit hlasovou komunikaci pomocí stávajících připojení k Internetu, včetně mobilních připojení.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio a speciální nabídky
Informace o cenách Twilio jsou dostupné v [Twilio cenách][twilio_pricing]. Zákazníci Azure obdrží [speciální nabídku][special_offer]: bezplatný kredit 1000ch textů nebo 1000 příchozích minut. Pokud se chcete přihlásit k této nabídce nebo získat další informace, navštivte prosím [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Rozhraní Twilio API je rozhraní RESTful API, které poskytuje funkce hlasu a SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích. seznam najdete v tématu [knihovny rozhraní API pro Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou Twilio příkazy a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Operace Twilio
Rozhraní API využívá operace Twilio; Například příkaz **&lt; vyslovit &gt;** instruuje Twilio, aby audibly doručení zprávy na volání.

Následuje seznam operací Twilio.

* **&lt; Vytočit &gt;**: připojí volajícího k jinému telefonu.
* **&lt; Shromáždit &gt;**: shromažďuje číselné číslice, které jsou zadány na klávesnici na telefonu.
* **&lt; Hangup &gt;**: ukončí volání.
* **&lt; Přehrát &gt;**: přehraje zvukový soubor.
* **&lt; Queue &gt;**: přidejte do fronty volajících.
* **&lt; Pozastavit &gt;**: netiché čekání po zadaný počet sekund.
* **&lt; Záznam &gt;**: zaznamenává hlas volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* **&lt; Přesměrování &gt;**: přenáší řízení volání nebo SMS na TwiML na jinou adresu URL.
* **&lt; Odmítnout &gt;**: odmítne příchozí volání na číslo Twilio bez fakturace.
* **&lt; Řekněme &gt;**: převede text na řeč, který se provádí na volání.
* **&lt; SMS &gt;**: pošle zprávu SMS.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML je sada instrukcí založených na XML, která je založená na příkazech Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Například následující TwiML převede text **Hello World!** na řeč.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Když vaše aplikace volá rozhraní Twilio API, je jedním z parametrů rozhraní API adresa URL, která vrací TwiML odpověď. Pro účely vývoje můžete použít adresy URL poskytované Twilio a poskytnout tak odpovědi na TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL pro vytváření odpovědí TwiML a další možností je použít objekt **TwiMLResponse** .

Další informace o příkazech Twilio, jejich atributech a TwiML naleznete v tématu [TwiML][twiml]. Další informace o rozhraní Twilio API najdete v tématu [rozhraní Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se do [Twilio try][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio obdržíte ID účtu a ověřovací token. Pro volání rozhraní API Twilio budou potřeba obojí. Abyste zabránili neoprávněnému přístupu k účtu, udržujte svůj ověřovací token zabezpečený. ID účtu a ověřovací token se mohou zobrazit v [konzole Twilio][twilio_console]v polích s označením **SID účtu** a **ověřovacího tokenu** v uvedeném pořadí.

## <a name="create-a-java-application"></a><a id="create_app"></a>Vytvoření aplikace v Javě
1. Získejte SKLENICi Twilio a přidejte ji do cesty k sestavení Java a do sestavení pro nasazení WAR. Na [https://github.com/twilio/twilio-java][twilio_java] webu můžete stáhnout zdroje GitHubu a vytvořit vlastní sklenici nebo si stáhnout předem připravený jar (s závislostmi nebo bez nich).
2. Ujistěte se, že úložiště klíčů **cacerts** pro JDK obsahuje certifikát zabezpečené certifikační autority Equifax s OTISKem MD5.67:: 9d: C0:13:24:8a: 82:9b: B2:17:1e: D1:1b: ES: D4 (sériové číslo je 35: de: F4: CF a otisk SHA1, je D2:32:09: AD: 23: D3:14:23:21:74: E4:0d: 7f: 9d: 62:13:86:63:3A). Toto je certifikát certifikační autority (CA) pro [https://api.twilio.com][twilio_api_service] službu, která je volána při použití rozhraní Twilio API. Informace o tom, jak JDK úložiště klíčů **cacerts** obsahuje správný certifikát certifikační autority, najdete v tématu [Přidání certifikátu do úložiště certifikátů certifikační autority Java][add_ca_cert].

Podrobné pokyny pro použití klientské knihovny Twilio pro jazyk Java jsou k dispozici v článku [jak uskutečnit telefonní hovor pomocí Twilio v aplikaci Java v Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
V rámci kódu můžete přidat příkazy **Import** v horní části zdrojových souborů pro balíčky Twilio nebo třídy, které chcete použít ve své aplikaci.

Pro zdrojové soubory Java:

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

V závislosti na tom, které balíčky nebo třídy Twilio chcete použít, se vaše příkazy **Import** můžou lišit.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postupy: provedení odchozího volání
Následující příklad ukazuje, jak provést odchozí volání pomocí třídy **volání** . Tento kód také používá Twilio web k vrácení odpovědi TwiML (Twilio Markup Language). Vysaďte hodnoty pro čísla **z** a **na** telefonní číslo a ujistěte se, že jste před spuštěním kódu ověřili předplatné **z** telefonního čísla účtu Twilio.

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

Další informace o parametrech předaných metodě **Call. Creator** naleznete v tématu [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Jak bylo zmíněno, tento kód používá Twilio web k vrácení TwiML odpovědi. Místo toho můžete k poskytnutí odpovědi TwiML použít svůj vlastní web. Další informace najdete v tématu [jak poskytnout TwiML odpovědi v aplikaci Java v Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující příklad ukazuje, jak odeslat zprávu SMS pomocí třídy **Message** . Číslo **od** , **4155992671**, poskytuje Twilio pro zkušební účty pro odesílání zpráv SMS. Aby bylo možné účet Twilio spustit před spuštěním kódu, je nutné ověřit **jeho číslo.**

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

Další informace o parametrech předaných metodě **Message. Creator** naleznete v tématu [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postupy: poskytování odpovědí TwiML z vašeho vlastního webu
Když vaše aplikace zahájí volání rozhraní Twilio API, například pomocí metody **CallCreator. Create** , Twilio odešle požadavek na adresu URL, která by měla vrátit odpověď TwiML. Výše uvedený příklad používá adresu URL poskytnutou Twilio [https://twimlets.com/message][twimlet_message_url] . (I když je TwiML navržený pro použití webovými službami, můžete si Zobrazit TwiML v prohlížeči. Například kliknutím [https://twimlets.com/message][twimlet_message_url] zobrazíte prázdný element **&lt; odpovědi &gt;** . jako jiný příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] můžete kliknutím zobrazit element **&lt; odpovědi &gt;** , který obsahuje element **&lt; řekněte &gt;** .)

Místo toho, abyste museli spoléhat na adresu URL zadanou v Twilio, můžete vytvořit vlastní adresu URL, která bude vracet odpovědi HTTP. Web můžete vytvořit v jakémkoli jazyce, který vrací odpovědi HTTP; v tomto tématu se předpokládá, že budete hostovat adresu URL na stránce JSP.

Následující stránka JSP má za následek odpověď TwiML, která říká **Hello World!** na volání.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Následující stránka JSP má za následek odpověď TwiML, která říká část textu, má několik pozastavení a uvádí informace o verzi rozhraní Twilio API a názvu role Azure.

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

Parametr **ApiVersion** je k dispozici v žádostech hlasu Twilio (ne požadavků SMS). Informace o dostupných parametrech požadavků pro Twilio hlasové a SMS požadavky najdete v části <https://www.twilio.com/docs/api/twiml/twilio_request> a v <https://www.twilio.com/docs/api/twiml/sms/twilio_request> uvedeném pořadí. Proměnná prostředí **roleName** je k dispozici jako součást nasazení Azure. (Pokud chcete přidat vlastní proměnné prostředí, aby mohly být převzaty ze **System. getenv**, přečtěte si část proměnné prostředí v tématu [nastavení konfigurace různých rolí][misc_role_config_settings].)

Jakmile nastavíte stránku JSP tak, aby poskytovala odpovědi TwiML, použijte adresu URL stránky JSP jako adresu URL předanou metodě **Call. Creator** . Například pokud máte webovou aplikaci s názvem MyTwiML nasazenou do hostované služby Azure a název stránky JSP je mytwiml.jsp, adresa URL může být předána **volání. Creator** , jak je znázorněno v následujícím příkladu:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Další možností pro reagování na TwiML je prostřednictvím třídy **VoiceResponse** , která je k dispozici v balíčku **com. Twilio. TwiML** .

Další informace o používání Twilio v Azure s Java najdete v tématu [jak uskutečnit telefonní hovor pomocí Twilio v aplikaci Java v Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Postupy: používání dalších služeb Twilio
Kromě zde uvedených příkladů Twilio nabízí webová rozhraní API, která můžete použít k využití dalších funkcí Twilio z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní Twilio API][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Další kroky
Teď, když jste se seznámili se základy služby Twilio, můžete získat další informace pomocí těchto odkazů:

* [Pokyny pro zabezpečení Twilio][twilio_security_guidelines]
* [Twilio a ukázkový kód pro postupy][twilio_howtos]
* [Kurzy rychlý Start pro Twilio][twilio_quickstarts]
* [Twilio na GitHubu][twilio_on_github]
* [Kontaktujte podporu Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: /azure/developer/java/sdk/java-sdk-add-certificate-ca-store
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
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