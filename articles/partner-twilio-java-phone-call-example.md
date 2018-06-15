---
title: Postup telefonní hovor z Twilio (Java) | Microsoft Docs
description: Naučte se telefonní hovor z webové stránky v aplikaci Java v Azure pomocí Twilio.
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
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865936"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Postup telefonní hovor pomocí Twilio v aplikaci Java v Azure
Následující příklad ukazuje, jak Twilio můžete využít volání z webové stránky hostované v Azure. Výsledné aplikace vyzve uživatele, hodnoty telefonní hovor, jak je znázorněno na následujícím snímku obrazovky.

![Azure volání formulář s využitím Twilio a Java][twilio_java]

Budete muset následujícím postupem použít kód v tomto tématu:

1. Získání účtu Twilio a ověření tokenu. Začít s Twilio vyhodnotit ceny v [http://www.twilio.com/pricing][twilio_pricing]. Můžete si zaregistrovat v [https://www.twilio.com/try-twilio][try_twilio]. Informace o rozhraní API poskytované Twilio najdete v tématu [http://www.twilio.com/api][twilio_api].
2. Získáte Twilio JAR. V [https://github.com/twilio/twilio-java][twilio_java_github], můžete stáhnout zdroje Githubu a vytvořit vlastní JAR nebo stáhnout předdefinovaných JAR (s nebo bez závislosti).
   Kód v tomto tématu, byla zapsána pomocí předdefinovaných JAR TwilioJava 3.3.8 s závislosti.
3. Přidáte JAR do vaší cesta sestavení Java.
4. Pokud používáte Eclipse k vytvoření této aplikace Java, zahrňte do souboru nasazení vaší aplikace (WAR), pomocí funkce sestavení nasazení na Eclipse Twilio JAR. Pokud nepoužíváte Eclipse k vytvoření této aplikace Java, ujistěte se, Twilio JAR je uvedený v rámci stejné Azure role jako aplikace v jazyce Java a přidat do třídy cestu aplikace.
5. Ujistěte se, úložiště klíčů vaší cacerts obsahuje certifikát Equifax zabezpečení certifikační autority s MD5 otisk prstu 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (sériové číslo je 35:DE:F4:CF a otisk prstu SHA1 je D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Toto je certifikát certifikační autority certifikátu pro [https://api.twilio.com] [ twilio_api_service] služba, která je volána, když používáte rozhraní API Twilio. Informace o přidání tento certifikát certifikační Autority vaší JDK cacert úložiště najdete v tématu [přidání certifikátu do úložiště certifikátů certifikační Autority Java][add_ca_cert].

Kromě toho znalost na informace na [vytváření Hello World aplikace pomocí nástrojů Azure pro Eclipse][azure_java_eclipse_hello_world], nebo s jinými technikami pro hostování aplikací Java v Azure, pokud jste nepoužíváte Eclipse, důrazně doporučujeme.

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód ukazuje, jak vytvořit webového formuláře pro načtení dat uživatele pro volání. Pro účely tohoto příkladu nového dynamického webového projektu, s názvem **TwilioCloud**, byl vytvořen, a **callform.jsp** byl přidán jako soubor JSP.

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
Následující kód, který je volána, když uživatel dokončí formulář zobrazuje callform.jsp, vytvoří zprávu volání a generuje volání. Pro účely tohoto příkladu je název souboru JSP **makecall.jsp** a byl přidán do **TwilioCloud** projektu. (Použití vašeho účtu Twilio a ověřování tokenu místo zástupné hodnoty přiřazené **accountSID** a **ověřovacího tokenu** v následujícím kódu.)

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

Kromě vytváření volání, zobrazí makecall.jsp Twilio koncový bod, verze rozhraní API a stav volání. Na následujícím snímku obrazovky je například:

![Azure volání odpovědi pomocí Twilio a Java][twilio_java_response]

## <a name="run-the-application"></a>Spuštění aplikace
Tady jsou hlavní kroky při spuštění aplikace; Podrobnosti o těchto krocích naleznete v [vytváření Hello World aplikace pomocí nástrojů Azure pro Eclipse][azure_java_eclipse_hello_world].

1. Export vašeho TwilioCloud WAR do Azure **approot** složky. 
2. Upravit **startup.cmd** dekomprimovat vaší TwilioCloud WAR.
3. Zkompilujte aplikaci pro emulátoru služby výpočty v.
4. Spusťte v emulátoru služby výpočty v nasazení.
5. Otevřete prohlížeč a spusťte **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Zadejte hodnoty ve formátu, klikněte na tlačítko **toto volání**a pak zobrazte výsledky v makecall.jsp.

Až budete připraveni k nasazení do Azure, a pak ji znovu zkompilovat pro nasazení do cloudu a nasazení do Azure a spusťte http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp v prohlížeči (nahraďte hodnota pro  *your_hosted_name*).

## <a name="next-steps"></a>Další kroky
Tento kód byl poskytnut tak, aby zobrazovalo základních funkcí pomocí Twilio v jazyce Java v Azure. Před nasazením do Azure v produkčním prostředí, můžete přidat další zpracování chyb a další funkce. Například:

* Místo použití webového formuláře, můžete použít objekty BLOB úložiště Azure nebo databázi SQL pro ukládání telefonních čísel a volání text. Informace o použití objektů BLOB služby Azure storage v jazyce Java najdete v tématu [jak používat služby úložiště objektů Blob z Javy][howto_blob_storage_java]. Informace o používání databáze SQL v jazyce Java najdete v tématu [pomocí SQL Database v jazyce Java][howto_sql_azure_java].
* Můžete použít **RoleEnvironment.getConfigurationSettings** načíst Twilio ID účtu a ověřování tokenu z nastavení konfigurace vašeho nasazení, místo pevně kódováno hodnoty v makecall.jsp. Informace o **RoleEnvironment** třídy najdete v tématu [pomocí běhové knihovny služby Azure v JSP] [ azure_runtime_jsp] a v dokumentaci k balíčku běh služby Azure na [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Kód makecall.jsp přiřadí URL poskytnutou Twilio, [http://twimlets.com/message][twimlet_message_url]do **Url** proměnné. Tato adresa URL obsahuje odpovědi Twilio Markup Language (TwiML), která informuje Twilio postup při volání. Například může obsahovat TwiML, která je vrácena  **&lt;indikované&gt;**  akci, která je výsledkem text použitém k příjemce volání. Místo použití URL poskytnutou Twilio, může vytvořit vlastní služba neodpoví na požadavek na Twilio; Další informace najdete v tématu [jak Twilio použijte pro hlasový a možnosti serveru SMS v jazyce Java][howto_twilio_voice_sms_java]. Další informace o TwiML lze najít na [http://www.twilio.com/docs/api/twiml][twiml]a další informace o  **&lt;indikované&gt;**  a ostatní operace Twilio naleznete na adrese [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Přečtěte si pokyny zabezpečení Twilio v [https://www.twilio.com/docs/security][twilio_docs_security].

Další informace o Twilio najdete v tématu [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Viz také
* [Jak používat Twilio pro hlasový a možnosti serveru SMS v jazyce Java][howto_twilio_voice_sms_java]
* [Přidání certifikátu do úložiště certifikátů certifikační Autority Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
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
