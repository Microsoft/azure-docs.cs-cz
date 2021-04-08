---
title: Jak uskutečnit telefonní hovor z Twilio (Java) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor z webové stránky pomocí Twilio v aplikaci Java v Azure.
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
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548756"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak uskutečnit telefonní hovor pomocí Twilio v aplikaci Java v Azure
Následující příklad ukazuje, jak můžete pomocí Twilio uskutečnit volání z webové stránky hostované v Azure. Výsledná aplikace zobrazí uživateli výzvu k zadání hodnot telefonních hovorů, jak je znázorněno na následujícím snímku obrazovky.

![Formulář volání Azure pomocí Twilio a Java][twilio_java]

Chcete-li použít kód v tomto tématu, musíte provést následující:

1. Získejte účet Twilio a ověřovací token. Pokud chcete začít s Twilio, vyhodnoťte ceny na adrese [https://www.twilio.com/pricing][twilio_pricing] . Můžete se přihlásit [https://www.twilio.com/try-twilio][try_twilio] . Informace o rozhraní API, které poskytuje Twilio, najdete v tématu [https://www.twilio.com/api][twilio_api] .
2. Získejte JAR Twilio. Na [https://github.com/twilio/twilio-java][twilio_java_github] webu můžete stáhnout zdroje GitHubu a vytvořit vlastní sklenici nebo si stáhnout předem připravený jar (s závislostmi nebo bez nich).
   Kód v tomto tématu byl vytvořen pomocí předem vytvořeného JAR TwilioJava-3.3.8-with-závislosti.
3. Přidejte JAR do cesty k sestavení Java.
4. Pokud k vytvoření této aplikace v jazyce Java používáte zatmění, zahrňte Twilio JAR do souboru nasazení aplikace (WAR) pomocí funkce sestavení nasazení v zatmění. Pokud k vytvoření této aplikace v jazyce Java nepoužíváte zatmění, zajistěte, aby byl JAR Twilio součástí stejné role Azure jako aplikace Java a přidal se do cesty třídy vaší aplikace.
5. Ujistěte se, že váš cacerts úložiště klíčů obsahuje certifikát Equifax zabezpečené certifikační autority s otiskem MD5.67:: 9D: C0:13:24:8A: 82:9B: B2:17:1E: D1:1B: ES: D4 (sériové číslo je 35: DE: F4: CF a otisk SHA1, je D2:32:09: AD: 23: D3:14:23:21:74: E4:0D: 7F: 9D: 62:13:86:63:3A). Toto je certifikát certifikační autority (CA) pro [https://api.twilio.com][twilio_api_service] službu, která je volána při použití rozhraní Twilio API.

Je také známo, že informace týkající se [vytváření Hello World aplikace pomocí Azure Toolkit for Eclipse][azure_java_eclipse_hello_world]nebo jiné techniky pro hostování aplikací Java v Azure, pokud nepoužíváte zatmění, se důrazně doporučuje.

## <a name="create-a-web-form-for-making-a-call"></a>Vytvoření webového formuláře pro volání
Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro volání. Pro účely tohoto příkladu byl vytvořen nový dynamický webový projekt s názvem **TwilioCloud** a **callform.jsp** byl přidán jako soubor JSP.

```jsp
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
```

## <a name="create-the-code-to-make-the-call"></a>Vytvoření kódu pro volání
Následující kód, který je volána, když uživatel dokončí formulář zobrazený pomocí callform.jsp, vytvoří zprávu volání a vygeneruje volání. Pro účely tohoto příkladu se soubor JSP jmenuje **makecall.jsp** a přidal se do projektu **TwilioCloud** . (Použijte svůj účet Twilio a ověřovací token místo zástupných hodnot přiřazených ke **accountSID** a **authToken** v následujícím kódu.)

```jsp
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
```

Kromě volání makecall.jsp zobrazí koncový bod Twilio, verzi rozhraní API a stav volání. Příkladem je následující snímek obrazovky:

![Odpověď volání Azure pomocí Twilio a Java][twilio_java_response]

## <a name="run-the-application"></a>Spuštění aplikace
Níže jsou uvedené kroky vysoké úrovně pro spuštění vaší aplikace. Podrobnosti o těchto krocích najdete v [části vytvoření Hello World aplikace pomocí Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Exportujte TwilioCloud WAR do složky Azure **AppRoot** . 
2. Úpravou **Startup. cmd** rozbalte TwilioCloud War.
3. Zkompilujte aplikaci pro emulátor služby Compute.
4. Spusťte nasazení v emulátoru služby Compute.
5. Otevřete prohlížeč a spusťte příkaz `http://localhost:8080/TwilioCloud/callform.jsp` .
6. Zadejte hodnoty do formuláře, klikněte na tlačítko **vytvořit toto volání** a potom se podívejte na výsledky v makecall.jsp.

Až budete připraveni na nasazení do Azure, rekompilovat pro nasazení do cloudu, nasadit do Azure a spustit http://*your_hosted_name*. cloudapp.NET/TwilioCloud/callform.jsp v prohlížeči (nahraďte hodnotu *your_hosted_name*).

## <a name="next-steps"></a>Další kroky
Tento kód je k dispozici pro zobrazení základních funkcí pomocí Twilio v jazyce Java v systému Azure. Před nasazením do Azure v produkčním prostředí budete možná chtít přidat další zpracování chyb nebo jiné funkce. Například:

* Místo používání webového formuláře můžete použít objekty blob služby Azure Storage nebo SQL Database k ukládání telefonních čísel a volání textu. Informace o použití objektů BLOB služby Azure Storage v jazyce Java najdete v tématu [How to use the BLOB Storage Service from Java][howto_blob_storage_java]. 
* Pomocí **RoleEnvironment. getConfigurationSettings** můžete načíst ID účtu Twilio a ověřovací token z nastavení konfigurace nasazení, místo hardwarového kódování hodnot v makecall.jsp. Informace o třídě **RoleEnvironment** najdete [v tématu použití knihovny modulu runtime Azure Service v JSP][azure_runtime_jsp].
* Kód makecall.jsp přiřadí [https://twimlets.com/message][twimlet_message_url] k proměnné **URL** adresu URL zadanou v Twilio. Tato adresa URL poskytuje odpověď TwiML (Twilio Markup Language), která informuje Twilio, jak pokračovat v volání. Například vrácený TwiML může **&lt; &gt; obsahovat sloveso** , které je výsledkem mluveného textu na příjemce volání. Místo použití adresy URL poskytnuté v Twilio můžete vytvořit vlastní službu, která bude reagovat na žádost Twilio. Další informace najdete v tématu [použití Twilio pro hlasové funkce a možnosti SMS v jazyce Java][howto_twilio_voice_sms_java]. Další informace o TwiML najdete na adrese [https://www.twilio.com/docs/api/twiml][twiml] a další informace o **&lt; &gt; slovnících** a dalších Twilio operacích najdete na adrese [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Přečtěte si pokyny pro zabezpečení Twilio na adrese [https://www.twilio.com/docs/security][twilio_docs_security] .

Další informace o Twilio najdete v tématu [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Viz také
* [Použití Twilio pro hlasové funkce a možnosti SMS v Java][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
