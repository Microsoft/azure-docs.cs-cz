---
title: Store-sendgrid-Java-How-to-send-email-example
description: Odesílání e-mailu pomocí SendGrid z Javy v nasazení Azure
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 400c8ac229e00e818e336f1c47b126d3e8a2d155
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253835"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Odesílání e-mailu pomocí SendGrid z Javy v nasazení Azure
Následující příklad ukazuje, jak můžete SendGrid k odesílání e-mailů z webové stránky hostované v Azure. Výsledné aplikace vyzve uživatele k e-mailu hodnot, jak je znázorněno na následujícím snímku obrazovky.

![E-mailu formuláře][emailform]

Výsledný e-mailu bude vypadat podobně jako na následujícím snímku obrazovky.

![E-mailová zpráva][emailsent]

Bude potřeba ji pomocí kódu v tomto tématu následujícím způsobem:

1. Třeba získat JAR javax.mail z <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Přidáte kromě souborů JAR vaše cesta sestavení Java.
3. Pokud používáte Eclipse k vytvoření této aplikace v Javě, můžete zahrnout knihovny SendGrid ve vašem nasazení souboru aplikace (WAR) pomocí funkce sestavení nasazení pro Eclipse. Pokud nepoužíváte Eclipse k vytvoření této aplikace v Javě, zkontrolujte knihoven jsou zahrnuté ve stejné roli Azure jako vaši aplikaci v Javě a přidány do cesty třídy vaší aplikace.

Musíte také mít vlastní SendGrid uživatelské jméno a heslo, abyste mohli odeslat e-mailu. Chcete-li začít se službou SendGrid, přečtěte si téma [odesílání e-mailu pomocí SendGrid z Javy](store-sendgrid-java-how-to-send-email.md).

Dále, známý s použitím informací v [vytvoření aplikace Hello World pro Azure v Eclipse](https://msdn.microsoft.com/library/windowsazure/hh690944), nebo s dalšími technikami, hostování aplikací Java v Azure, pokud nepoužíváte Eclipse, se důrazně doporučuje.

## <a name="create-a-web-form-for-sending-email"></a>Vytvoří webový formulář pro odeslání e-mailu
Následující kód ukazuje, jak vytvořit webový formulář data uživatele k odesílání e-mailu. Pro účely tohoto obsahu, je název souboru JSP **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Vytvoření kódu pro odesílání e-mailu
Následující kód, který se volá, když dokončíte formulář v nástrojích pro emailform.jsp, vytvoří e-mailovou zprávu a odešle ji. Pro účely tohoto obsahu, je název souboru JSP **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Kromě odeslání e-mailu, poskytuje emailform.jsp výsledek pro uživatele. Příkladem je na následujícím snímku obrazovky:

![Odeslat výsledky e-mailu][emailresult]

## <a name="next-steps"></a>Další postup
Nasazení aplikace na emulátor služby výpočty a v prohlížeči spustit emailform.jsp, zadejte hodnoty ve formuláři, klikněte na tlačítko **odešlete tento e-mail**a potom zobrazit výsledky v sendemail.jsp.

Tento kód byl poskytnut až vám ukážeme, jak pomocí služby SendGrid v jazyce Java v Azure. Před nasazením v produkčním prostředí do Azure, můžete přidat další zpracování chyb nebo jiné funkce. Příklad: 

* Objekty BLOB služby Azure storage nebo SQL Database může použít k uložení e-mailové adresy a e-mailové zprávy, namísto použití webového formuláře. Informace o použití objektů BLOB Azure storage v Javě, naleznete v tématu [použití služby Blob Storage z Javy](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Informace o použití SQL Database v jazyce Java najdete v tématu [pomocí SQL Database v jazyce Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Můžete použít `RoleEnvironment.getConfigurationSettings` načíst SendGrid uživatelského jména a hesla z nastavení konfigurace vašeho nasazení, namísto použití webového formuláře pro načtení těchto hodnot. Informace o tom, `RoleEnvironment` najdete v tématu [pomocí knihovny Runtime služeb Azure v JSP](https://msdn.microsoft.com/library/windowsazure/hh690948) a dokumentaci balíček modulu Runtime služby Azure na <http://dl.windowsazure.com/javadoc>.
* Další informace o používání služby SendGrid v jazyce Java najdete v tématu [odesílání e-mailu pomocí SendGrid z Javy](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
