---
title: Store-SendGrid-Java-How-to-Send-e-mail-example
description: Posílání e-mailů pomocí SendGrid z Java v nasazení Azure. Výsledná aplikace vyzve uživatele k zadání hodnoty e-mailu.
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
ms.custom: devx-track-java
ms.openlocfilehash: 3957d7501634be01a978481787885ae13ad3273b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225950"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Odeslání e-mailu pomocí SendGrid z Java v nasazení Azure
Následující příklad ukazuje, jak můžete použít SendGrid k posílání e-mailů z webové stránky hostované v Azure. Výsledná aplikace zobrazí uživateli výzvu k zadání hodnot e-mailu, jak je znázorněno na následujícím snímku obrazovky.

![E-mailový formulář][emailform]

Výsledný e-mail bude vypadat podobně jako na následujícím snímku obrazovky.

![E-mailová zpráva][emailsent]

Chcete-li použít kód v tomto tématu, musíte provést následující:

1. Získejte javax. mail jar, například z <https://www.oracle.com/technetwork/java/javamail/index.html> .
2. Přidejte jar do cesty k sestavení Java.
3. Pokud k vytvoření této aplikace v jazyce Java používáte zatmění, můžete do souboru nasazení aplikace (WAR) zahrnout knihovny SendGrid pomocí funkce sestavení nasazení v zatmění. Pokud k vytvoření této aplikace v jazyce Java nepoužíváte zatmění, zajistěte, aby byly knihovny zahrnuty v rámci stejné role Azure jako aplikace Java a přidaly se do cesty třídy vaší aplikace.

K odeslání e-mailu musíte mít také vlastní uživatelské jméno a heslo pro SendGrid. Pokud chcete začít pracovat s SendGrid, přečtěte si téma [odeslání e-mailu pomocí SendGrid z Java](store-sendgrid-java-how-to-send-email.md).

Kromě toho se seznamte s informacemi při [vytváření Hello World aplikace pro Azure v zatmění](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)nebo s jinými postupy pro hostování aplikací Java v Azure, pokud nepoužíváte zatmění, důrazně se doporučuje.

## <a name="create-a-web-form-for-sending-email"></a>Vytvoření webového formuláře pro odesílání e-mailů
Následující kód ukazuje, jak vytvořit webový formulář pro načtení uživatelských dat pro odesílání e-mailů. Pro účely tohoto obsahu se soubor JSP jmenuje **emailform.jsp**.

```html
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
```

## <a name="create-the-code-to-send-the-email"></a>Vytvoření kódu k odeslání e-mailu
Následující kód, který je volán při dokončování formuláře v emailform.jsp, vytvoří e-mailovou zprávu a odešle ji. Pro účely tohoto obsahu se soubor JSP jmenuje **sendemail.jsp**.

```java
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
```

Kromě posílání e-mailů poskytuje emailform.jsp výsledek pro uživatele. Příkladem je následující snímek obrazovky:

![Odeslat výsledek pošty][emailresult]

## <a name="next-steps"></a>Další kroky
Nasaďte aplikaci do emulátoru služby COMPUTE a v rámci prohlížeče spusťte emailform.jsp, zadejte hodnoty do formuláře, klikněte na **Odeslat tento e-mail**a potom se podívejte na výsledky v sendemail.jsp.

Tento kód vám ukáže, jak používat SendGrid v jazyce Java v Azure. Před nasazením do Azure v produkčním prostředí budete možná chtít přidat další zpracování chyb nebo jiné funkce. Například: 

* Pomocí objektů BLOB služby Azure Storage nebo SQL Database můžete ukládat e-mailové adresy a e-mailové zprávy místo používání webového formuláře. Informace o použití objektů BLOB služby Azure Storage v jazyce Java najdete v tématu [How to use the BLOB Storage Service from Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Informace o použití SQL Database v jazyce Java najdete v tématu [použití SQL Database v jazyce Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Další informace o používání SendGrid v jazyce Java najdete v tématu [odeslání e-mailu pomocí SendGrid z Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
