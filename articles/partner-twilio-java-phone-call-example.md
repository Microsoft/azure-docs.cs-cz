---
title: Jak volat z Twilia (Java) | Dokumentace Microsoftu
description: Zjistěte, jak volat z webové stránky v aplikaci Java v Azure pomocí Twilio.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9ef754e9952bcbd47d668331e906b19ad582b90c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818594"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak telefonování pomocí Twilio v aplikace v Javě v Azure
Následující příklad ukazuje, jak Twilio můžete uskutečnit volání z webové stránky hostované v Azure. Výsledné aplikace se zobrazí výzva pro hodnoty telefonního hovoru, jak je znázorněno na následujícím snímku obrazovky.

![Azure volání formulář s využitím platformy Twilio a Java][twilio_java]

Bude potřeba ji pomocí kódu v tomto tématu následujícím způsobem:

1. Získání účtu Twilio a ověřovací token. Abyste mohli začít s platformou Twilio, vyhodnocení ceny na [ http://www.twilio.com/pricing ] [ twilio_pricing]. Můžete se zaregistrovat v [ https://www.twilio.com/try-twilio ] [ try_twilio]. Informace o rozhraní API poskytovaných Twilio, naleznete v tématu [ http://www.twilio.com/api ] [ twilio_api].
2. Získáte Twilio soubor JAR. Na [ https://github.com/twilio/twilio-java ] [ twilio_java_github], můžete stáhnout kódů Githubu a vytvořit svůj vlastní soubor JAR nebo stažení předem vytvořených JAR (s nebo bez závislosti).
   Kód v tomto tématu se napsané s využitím předem připravených JAR TwilioJava 3.3.8 s závislosti.
3. Přidáte soubor JAR pro vaše cesta sestavení Java.
4. Pokud používáte Eclipse k vytvoření této aplikace v Javě, zahrnout do nasazení souboru aplikace (WAR) pomocí Eclipse od nasazení sestavení funkce Twilio JAR. Pokud nepoužíváte Eclipse k vytvoření této aplikace v Javě, ujistěte se, Twilio JAR je už v rámci Azure jako stejný atribut role vaši aplikaci v Javě a přidány do cesty třídy vaší aplikace.
5. Ujistěte se, úložiště klíčů cacerts obsahuje Equifax zabezpečení certifikační autority certifikát s otiskem prstu 67:CB:9 MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 35:DE:F4:CF a SHA1 otisků prstů je D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Toto je certifikát certifikační autority pro [ https://api.twilio.com ] [ twilio_api_service] službu, která je volána, když používáte API twilia můžete volat. Informace o přidání tento certifikát certifikační Autority vaší JDK cacert úložiště najdete v tématu [přidání certifikátu pro certifikát certifikační Autority Java Store][add_ca_cert].

Dále, známý s použitím informací v [vytvořit Hello World aplikace pomocí sady Azure Toolkit pro Eclipse][azure_java_eclipse_hello_world], nebo s dalšími technikami, hostování aplikací Java v Azure, pokud jste bez použití Eclipse, důrazně doporučujeme.

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoří webový formulář pro volání
Následující kód ukazuje, jak vytvořit webový formulář data uživatele pro volání. Pro účely tohoto příkladu, nový dynamický webový projekt s názvem **TwilioCloud**, byla vytvořena, a **callform.jsp** byl přidán jako soubor JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
Následující kód, který se volá, když uživatel vykoná formuláře zobrazí callform.jsp, vytvoří zprávu volání a vygeneruje volání. Pro účely tohoto příkladu je soubor JSP s názvem **makecall.jsp** a přidal do **TwilioCloud** projektu. (Použití vašeho účtu Twilio a ověřování tokenem místo zástupné hodnoty přiřazené **accountSID** a **ověřovacího tokenu** v následujícím kódu.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
         String Url="http://twimlets.com/message";
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

Kromě toho volání, zobrazí makecall.jsp koncový bod Twilio, verze rozhraní API a stav volání. Příkladem je na následujícím snímku obrazovky:

![Azure volání odpovědi HTTP pomocí platformy Twilio a Java][twilio_java_response]

## <a name="run-the-application"></a>Spuštění aplikace
Toto jsou hlavní kroky při spuštění aplikace; Podrobnosti pro tyto kroky můžete najít v [vytvořit Hello World aplikace pomocí sady Azure Toolkit pro Eclipse][azure_java_eclipse_hello_world].

1. Export vašeho TwilioCloud WAR do Azure **approot** složky. 
2. Upravit **startup.cmd** k rozbalení vaše TwilioCloud WAR.
3. Zkompilujte aplikaci pro emulátor služby výpočty.
4. Zahájení nasazení v emulátoru služby compute.
5. Otevřete prohlížeč a spusťte **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Zadejte hodnoty ve formuláři, klikněte na tlačítko **toto volání**a potom zobrazit výsledky v makecall.jsp.

Až budete připravení nasadit do Azure a překompilujte pro nasazení do cloudu a nasadit do Azure a spustit http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp v prohlížeči (nahraďte svoji hodnotu pro  *your_hosted_name*).

## <a name="next-steps"></a>Další postup
Tento kód byl poskytnut zobrazit základní funkce s použitím Twilio v jazyce Java v Azure. Před nasazením v produkčním prostředí do Azure, můžete přidat další zpracování chyb nebo jiné funkce. Příklad:

* Místo použití webového formuláře, můžete použít objekty BLOB služby Azure storage nebo SQL Database k ukládání telefonní čísla a volání text. Informace o použití objektů BLOB Azure storage v Javě, naleznete v tématu [použití služby Blob Storage z Javy][howto_blob_storage_java]. 
* Můžete použít **RoleEnvironment.getConfigurationSettings** načíst Twilio ID účtu a ověřovací token z nastavení konfigurace vašeho nasazení, namísto pevně kódováno pomocí hodnot v makecall.jsp. Informace o tom, **RoleEnvironment** najdete v tématu [pomocí knihovny Runtime služeb Azure v JSP] [ azure_runtime_jsp] a balíček modulu Runtime služby Azure documentation [ http://dl.windowsazure.com/javadoc][azure_javadoc].
* Kód makecall.jsp přiřadí URL poskytnutou Twilio, [ http://twimlets.com/message ] [ twimlet_message_url]do **Url** proměnné. Tato adresa URL obsahuje odpověď Twilio Markup Language (TwiML), která informuje Twilio, jak postupovat u volání. Například může obsahovat TwiML, který je vrácen **&lt;Say&gt;** příkaz, který má za následek text, se kterým se mluví příjemce volání. Namísto použití URL poskytnutou Twilio, může sestavení vlastní služby reagovat na žádosti pro Twilio; Další informace najdete v tématu [postupy použití Twilia pro hlasové hovory a SMS v jazyce Java][howto_twilio_voice_sms_java]. Další informace o TwiML najdete [ http://www.twilio.com/docs/api/twiml ] [ twiml]a další informace o **&lt;Say&gt;** a ostatní operace Twilio můžete najít v [ http://www.twilio.com/docs/api/twiml/say ] [ twilio_say].
* Přečtěte si pokyny pro zabezpečení platformy Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Další informace o Twilio, naleznete v tématu [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Viz také
* [Postup použití Twilia pro hlasové hovory a SMS v jazyce Java][howto_twilio_voice_sms_java]
* [Přidání certifikátu do Store certifikát certifikační Autority Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
