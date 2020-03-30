---
title: Jak telefonovat z Twilio (Java) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak volat z webové stránky pomocí Twilio v aplikaci Java v Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838559"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak provést telefonní hovor pomocí Twilio v aplikaci Java v Azure
Následující příklad ukazuje, jak můžete použít Twilio k volání z webové stránky hostované v Azure. Výsledná aplikace vyzve uživatele k zadání hodnot telefonního hovoru, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a Java][twilio_java]

Chcete-li použít kód v tomto tématu, budete muset provést následující kroky:

1. Získejte účet Twilio a ověřovací token. Chcete-li začít s Twilio, vyhodnoťte ceny na [https://www.twilio.com/pricing][twilio_pricing]. Můžete se zaregistrovat na adrese [https://www.twilio.com/try-twilio][try_twilio]. Informace o rozhraní API poskytované Twilio [https://www.twilio.com/api][twilio_api]naleznete v tématu .
2. Získejte Twilio JAR. Na [https://github.com/twilio/twilio-java][twilio_java_github], můžete stáhnout zdroje GitHub a vytvořit si vlastní JAR, nebo stáhnout předem sestavené JAR (s nebo bez závislostí).
   Kód v tomto tématu byl napsán pomocí pre-postavený TwilioJava-3.3.8-s-závislosti JAR.
3. Přidejte jar do cesty sestavení Java.
4. Pokud používáte Eclipse k vytvoření této aplikace Java, zahrňte Twilio JAR do souboru nasazení aplikace (WAR) pomocí funkce sestavení nasazení Eclipse. Pokud nepoužíváte Eclipse k vytvoření této aplikace Java, ujistěte se, že Twilio JAR je součástí stejné role Azure jako vaše aplikace Java a přidán do cesty třídy vaší aplikace.
5. Ujistěte se, že vaše cacerts keystore obsahuje certifikát Equifax Secure Certificate Authority s otiskem MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 35:DE:F4:CF a otisk prstu SHA1 je D2:32:09:AD:23:D 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Jedná se o certifikát certifikační autority [https://api.twilio.com][twilio_api_service] (CA) pro službu, který se nazývá při použití twilio api. Informace o přidání tohoto certifikátu certifikační autority do úložiště cacert sady JDK naleznete [v tématu Přidání certifikátu do úložiště certifikátů java ca][add_ca_cert].

Kromě toho znalost informací o [vytvoření aplikace Hello World pomocí Azure Toolkit pro Eclipse][azure_java_eclipse_hello_world]nebo s jinými technikami pro hostování java aplikací v Azure, pokud nepoužíváte Eclipse, je vysoce doporučeno.

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro volání. Pro účely tohoto příkladu byl vytvořen nový dynamický webový projekt s názvem **TwilioCloud**a **soubor callform.jsp** byl přidán jako soubor JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Vytvoření kódu pro volání
Následující kód, který je volán po dokončení formuláře zobrazeného pomocí callform.jsp, vytvoří zprávu o volání a vygeneruje volání. Pro účely tohoto příkladu je soubor JSP pojmenován **makecall.jsp** a byl přidán do projektu **TwilioCloud.** (Použijte svůj účet Twilio a ověřovací token namísto zástupných hodnot přiřazených **accountSID** a **authToken** v kódu níže.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Kromě volání makecall.jsp zobrazí koncový bod Twilio, verzi rozhraní API a stav volání. Příkladem je následující snímek obrazovky:

![Odpověď na volání Azure pomocí Twilio a Java][twilio_java_response]

## <a name="run-the-application"></a>Spuštění aplikace
Následují kroky vysoké úrovně pro spuštění aplikace; Podrobnosti o těchto krocích najdete na [webu Vytvoření aplikace Hello World pomocí sady nástrojů Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Exportujte twilioCloud WAR do složky **Azure approot.** 
2. Upravte **startup.cmd** rozbalit TwilioCloud WAR.
3. Zkompilujte aplikaci pro emulátor výpočetních prostředků.
4. Spusťte nasazení v emulátoru výpočetního prostředí.
5. Otevřete prohlížeč a `http://localhost:8080/TwilioCloud/callform.jsp`spusťte .
6. Zadejte hodnoty ve formuláři, klepněte na **tlačítko Provést toto volání**a pak zobcejte výsledky v souboru makecall.jsp.

Až budete připraveni k nasazení do Azure, překompilovat pro nasazení do cloudu, nasadit se do Azure a spustit http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp v prohlížeči (nahraďte svou hodnotu *za your_hosted_name).*

## <a name="next-steps"></a>Další kroky
Tento kód byl k dispozici ukázat základní funkce pomocí Twilio v Jazyce Java v Azure. Před nasazením do Azure v produkčním prostředí můžete přidat další zpracování chyb nebo jiné funkce. Například:

* Místo použití webového formuláře můžete k ukládání telefonních čísel a textu volání použít objekty BLOB úložiště Azure nebo databázi SQL. Informace o používání objektů BLOB úložiště Azure v jazyce Java najdete v [tématu Jak používat službu úložiště objektů blob z javy][howto_blob_storage_java]. 
* Pomocí **role Environment.getConfigurationSettings** můžete načíst ID účtu Twilio a ověřovací token z nastavení konfigurace nasazení namísto pevného kódování hodnot v souboru makecall.jsp. Informace o třídě **RoleEnvironment** najdete [v tématu Použití knihovny Runtime služby Azure v JSP][azure_runtime_jsp].
* Kód makecall.jsp přiřadí adrese URL zapředpokladu [https://twimlets.com/message][twimlet_message_url]Twilio , proměnné **URL.** Tato adresa URL poskytuje odpověď jazyka Twilio Markup Language (TwiML), která informuje Twilio, jak pokračovat ve volání. Například TwiML, který je vrácena může obsahovat ** &lt;Say&gt; ** sloveso, které má za následek text vyslovený příjemci volání. Namísto použití adresy URL poskytované twilio, můžete vytvořit vlastní službu reagovat na žádost Twilio; Další informace naleznete v [tématu Jak používat Twilio pro hlasové a SMS funkce v Javě][howto_twilio_voice_sms_java]. Více informací o TwiML [https://www.twilio.com/docs/api/twiml][twiml]naleznete na na ** &lt;&gt; ** . [https://www.twilio.com/docs/api/twiml/say][twilio_say]
* Přečtěte si bezpečnostní pokyny [https://www.twilio.com/docs/security][twilio_docs_security]Twilio na adrese .

Další informace o Twilio [https://www.twilio.com/docs][twilio_docs]naleznete v tématu .

## <a name="see-also"></a>Viz také
* [Jak používat Twilio pro hlasové a SMS schopnosti v Javě][howto_twilio_voice_sms_java]
* [Přidání certifikátu do úložiště certifikátů java ca][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
