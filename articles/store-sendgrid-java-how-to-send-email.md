---
title: Jak používat e-mailové služby SendGrid (Java) | Dokumentace Microsoftu
description: Zjistěte, jak poslat e-mailu pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v jazyce Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: cadd771a516270faf23f18a36f54fa9a1541e6d5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698666"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Odesílání e-mailu pomocí SendGrid z Javy
Tato příručka ukazuje, jak k provádění běžných programovacích úloh s e-mailové služby SendGrid v Azure. Ukázky jsou napsané v jazyce Java. Mezi popsané scénáře patří **vytváření e-mailu**, **odesílání e-mailů**, **přidávání příloh**, **pomocí filtrů**a **aktualizují se vlastnosti**. Další informace o SendGrid a odesílání e-mailu, najdete v článku [další kroky](#next-steps) oddílu.

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailové služby SendGrid?
SendGrid je [založené na cloudu e-mailové služby] , která poskytuje spolehlivé [doručování transakční e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilní API, která usnadňují vlastní integraci. Obvyklé scénáře použití SendGrid patří:

* Automatické odesílání oznámení pro zákazníky
* Správa distribuce jsou uvedené pro odesílání zákazníkům měsíčních e letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro takové věci, jako jsou e-mailu blokovaný a odezvy zákazníků
* Generování sestav vám pomůže identifikovat trendy
* Předávání dotazy zákazníků
* E-mailových oznámení z aplikace

Další informace najdete v tématu <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Postupy: použití knihoven javax.mail
Získat javax.mail knihovny, například z <http://www.oracle.com/technetwork/java/javamail> a importujte je do vašeho kódu. Na hlavní proces pomocí knihovny javax.mail k odesílání e-mailu pomocí protokolu SMTP je provést následující kroky:

1. Zadejte hodnoty SMTP, včetně serveru SMTP, který je pro SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Rozšíření *javax.mail.Authenticator* třídy a ve vaší implementaci *getPasswordAuthentication* metody, vrátí SendGrid uživatelské jméno a heslo.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Vytvoření relace ověřený e-mailu prostřednictvím *javax.mail.Session* objektu.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Vytvořte zprávu a přiřadit **k**, **z**, **předmět** a obsah hodnoty. To je ukázáno [postupy: vytvoření e-mailu](#how-to-create-an-email) části.
4. Odeslat zprávu přes *javax.mail.Transport* objektu. To je ukázáno [postupy: odeslání e-mailu] [# postupy-k-odeslání-e-mailu] části.

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Následující znázorňuje způsob zadávání hodnot pro e-mailu.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailu
Následující ukazuje, jak odeslat e-mailu.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Následující kód ukazuje, jak přidat přílohu.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postupy: použití filtrů k povolení zápatí, sledování a analýza
SendGrid umožňuje zajistit další e-mailové funkce prostřednictvím *filtry*. Toto jsou nastavení, které lze přidat do e-mailovou zprávu povolit konkrétní funkce, jako například povolení sledování kliknutí, Google analytics, předplatné, sledování a tak dále. Úplný seznam filtrů, naleznete v tématu [nastavení filtru][Filter Settings].

* Následující ukazuje, jak vložit zápatí filtr, který má za následek text HTML, které povolí, v dolní části odeslání e-mailu.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Dalším příkladem filtru je sledování kliknutí. Řekněme, že text e-mailu obsahuje hypertextový odkaz, jako je například následující příkaz, a chcete sledovat rychlost, klikněte na tlačítko:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Pokud chcete povolit sledování kliknutí, použijte následující kód:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Postupy: vlastnosti e-mailové aktualizace
Některé vlastnosti e-mailu můžete přepsat pomocí **nastavit vlastnost** nebo připojených pomocí **přidat vlastnost**.

Například, chcete-li určit **ReplyTo** adresy, použijte následující:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Chcete-li přidat **kopie** příjemce, použijte následující:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití další služby SendGrid
SendGrid nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce SendGrid z aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby e-mailu Sendgridu, použijte tyto odkazy na další informace.

* Ukázka, která ukazuje, jak pomocí služby SendGrid v nasazení Azure: [odesílání e-mailu pomocí SendGrid z Javy v nasazení Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentace k rozhraní API SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid speciální nabídka pro zákazníky Azure: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[založené na cloudu e-mailové služby]: https://sendgrid.com/email-solutions
[doručování transakční e-mailů]: https://sendgrid.com/transactional-email
