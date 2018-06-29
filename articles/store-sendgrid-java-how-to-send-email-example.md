---
title: Store-sendgrid-Java-How-to-send-email-example
description: Postup odesílání e-mailu pomocí sendgrid vám umožňuje z prostředí Java v Azure nasazení
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
ms.openlocfilehash: fab76467025518bc455295da681d0b0127a9cbfe
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098725"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Postup odesílání e-mailu pomocí sendgrid vám umožňuje z prostředí Java v Azure nasazení
Následující příklad ukazuje, jak můžete sendgrid vám umožňuje použít k odesílání e-mailů z webové stránky hostované v Azure. Výsledné aplikace vyzve uživatele, hodnoty e-mailu, jak je znázorněno na následujícím snímku obrazovky.

![E-mailu formuláře][emailform]

Výsledný e-mailu, bude vypadat podobně jako na následujícím snímku obrazovky.

![E-mailová zpráva][emailsent]

Budete muset následujícím postupem použít kód v tomto tématu:

1. Získat javax.mail JAR, například z <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Přidáte JAR do vaší cesta sestavení Java.
3. Pokud používáte Eclipse k vytvoření této aplikace Java, můžete zahrnout knihovny sendgrid vám umožňuje v souboru nasazení vaší aplikace (WAR), pomocí funkce sestavení Eclipse na nasazení. Pokud nepoužíváte Eclipse k vytvoření této aplikace Java, zkontrolujte v knihovnách jsou zahrnuty v rámci stejné Azure role jako aplikace v jazyce Java a přidat do třídy cestu aplikace.

Musí také mít vlastní sendgrid vám umožňuje uživatelské jméno a heslo, abyste mohli odeslat e-mailu. Začínáme s Sendgridu, najdete v tématu [postup odesílání e-mailu pomocí sendgrid vám umožňuje z prostředí Java](store-sendgrid-java-how-to-send-email.md).

Kromě toho znalost na informace na [vytvoření aplikace Hello World služby Azure v prostředí Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944), nebo s jinými technikami, pro hostování aplikací Java v Azure, pokud nepoužíváte Eclipse, důrazně doporučujeme.

## <a name="create-a-web-form-for-sending-email"></a>Vytvoření webového formuláře pro odesílání e-mailu
Následující kód ukazuje, jak vytvořit webového formuláře pro načtení dat uživatele pro odesílání e-mailu. Pro účely tohoto obsahu, má název souboru JSP **emailform.jsp**.

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

## <a name="create-the-code-to-send-the-email"></a>Vytvoření kódu pro odeslání e-mailu
Následující kód, který je volána po vyplnění formuláře v emailform.jsp, vytvoří e-mailové zprávy a odešle ji. Pro účely tohoto obsahu, má název souboru JSP **sendemail.jsp**.

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

Kromě odesílání e-mailu, poskytuje emailform.jsp výsledku pro uživatele. na následujícím snímku obrazovky je například:

![Odesílání e-mailu výsledek][emailresult]

## <a name="next-steps"></a>Další postup
Nasazení aplikace emulátoru služby výpočty a prohlížeče, spusťte emailform.jsp, zadejte hodnoty ve formátu, klikněte na tlačítko **odeslání této e-mailu**a pak zobrazte výsledky v sendemail.jsp.

Tento kód byl poskytnut návod, jak používat sendgrid vám umožňuje v jazyce Java v Azure. Před nasazením do Azure v produkčním prostředí, můžete přidat další zpracování chyb a další funkce. Příklad: 

* Můžete použít objekty BLOB úložiště Azure nebo databáze SQL pro ukládání e-mailové adresy a e-mailové zprávy, místo použití webového formuláře. Informace o použití objektů BLOB služby Azure storage v jazyce Java najdete v tématu [jak používat služby úložiště objektů Blob z Javy](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Informace o používání databáze SQL v jazyce Java najdete v tématu [pomocí SQL Database v jazyce Java](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-java).
* Můžete použít `RoleEnvironment.getConfigurationSettings` načíst sendgrid vám umožňuje uživatelské jméno a heslo z nastavení konfigurace vašeho nasazení, místo použití webového formuláře pro načtení těchto hodnot. Informace o `RoleEnvironment` třídy najdete v tématu [pomocí běhové knihovny služby Azure v JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) a v dokumentaci k balíčku běh služby Azure na <http://dl.windowsazure.com/javadoc>.
* Další informace o používání sendgrid vám umožňuje v jazyce Java najdete v tématu [postup odesílání e-mailu pomocí sendgrid vám umožňuje z prostředí Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
