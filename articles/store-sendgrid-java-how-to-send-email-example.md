---
title: store-sendgrid-java-how-to-send-email-example
description: Jak odesílat e-maily pomocí SendGrid z Javy v nasazení Azure
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
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876485"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Jak odeslat e-mail pomocí SendGrid z Javy v nasazení Azure
Následující příklad ukazuje, jak můžete použít SendGrid k odesílání e-mailů z webové stránky hostované v Azure. Výsledná aplikace vyzve uživatele k zadání e-mailových hodnot, jak je znázorněno na následujícím snímku obrazovky.

![E-mailový formulář][emailform]

Výsledný e-mail bude vypadat podobně jako následující snímek obrazovky.

![E-mailová zpráva][emailsent]

Chcete-li použít kód v tomto tématu, budete muset provést následující kroky:

1. Získejte javax.mail JARs, <https://www.oracle.com/technetwork/java/javamail/index.html>například z .
2. Přidejte jars do cesty sestavení Java.
3. Pokud používáte Eclipse k vytvoření této aplikace Java, můžete zahrnout knihovny SendGrid do souboru nasazení aplikace (WAR) pomocí funkce sestavení nasazení Eclipse. Pokud nepoužíváte Eclipse k vytvoření této aplikace Java, ujistěte se, že knihovny jsou zahrnuty do stejné role Azure jako vaše aplikace Java a přidány do cesty třídy vaší aplikace.

Musíte mít také vlastní SendGrid uživatelské jméno a heslo, aby bylo možné odeslat e-mail. Pokud chcete začít s SendGrid, přečtěte si informace [o tom, jak odesílat e-maily pomocí sendgridu z Javy](store-sendgrid-java-how-to-send-email.md).

Kromě toho znalost informací na [vytvoření Hello World aplikace pro Azure v Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)nebo s jinými technikami pro hostování java aplikací v Azure, pokud nepoužíváte Eclipse, je vysoce doporučeno.

## <a name="create-a-web-form-for-sending-email"></a>Vytvoření webového formuláře pro odesílání e-mailů
Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro odesílání e-mailů. Pro účely tohoto obsahu je soubor JSP pojmenován **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-send-the-email"></a>Vytvoření kódu pro odeslání e-mailu
Následující kód, který se volá po dokončení formuláře v emailform.jsp, vytvoří e-mailovou zprávu a odešle ji. Pro účely tohoto obsahu je soubor JSP pojmenován **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
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

Kromě odeslání e-mailu poskytuje emailform.jsp pro uživatele výsledek. příkladem je následující snímek obrazovky:

![Odeslat výsledek odeslání pošty][emailresult]

## <a name="next-steps"></a>Další kroky
Nasazení aplikace do emulátoru výpočetního prostředí a v prohlížeči spustit emailform.jsp, zadejte hodnoty ve formuláři, klikněte na **Odeslat tento e-mail**a pak zobrazit výsledky v sendemail.jsp.

Tento kód byl k dispozici, aby vám ukázal, jak používat SendGrid v Jazyce Java v Azure. Před nasazením do Azure v produkčním prostředí můžete přidat další zpracování chyb nebo jiné funkce. Například: 

* K ukládání e-mailových adres a e-mailových zpráv můžete místo webového formuláře použít objekty BLOB úložiště Azure nebo databázi SQL. Informace o používání objektů BLOB úložiště Azure v jazyce Java najdete v [tématu Jak používat službu úložiště objektů blob z javy](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Informace o používání databáze SQL v jazyce Java naleznete v [tématu Použití databáze SQL v jazyce Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Další informace o používání SendGrid v Jazyce Java najdete v [tématu Jak odesílat e-maily pomocí SendGrid z Javy](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
