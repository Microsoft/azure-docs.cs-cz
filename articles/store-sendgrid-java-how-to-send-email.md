---
title: Jak používat služby sendgrid vám umožňuje e-mailů (Java) | Microsoft Docs
description: Zjistěte, jak odeslat e-mailu pomocí e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky kódu napsanou v jazyce Java.
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
ms.openlocfilehash: c9f7d4841846e41eacb66125bcdeeb29466bb0b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Postup odesílání e-mailu pomocí sendgrid vám umožňuje z Javy
Tato příručka ukazuje, jak provádět běžné úkoly programování s e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky jsou napsané v jazyce Java. Pokryté scénáře zahrnují **vytváření e-mailu**, **odesílání e-mailu**, **přidávání příloh**, **pomocí filtrů**, a **aktualizace vlastností**. Další informace o sendgrid vám umožňuje a odesílání e-mailu, najdete v článku [další kroky](#next-steps) části.

## <a name="what-is-the-sendgrid-email-service"></a>Co je služba sendgrid vám umožňuje e-mailu?
Je sendgrid vám umožňuje [cloudový e-mailovou službu] poskytuje spolehlivé [doručování e-mailem transakční], škálovatelnost a analýzu v reálném čase společně s flexibilní rozhraní API, které umožňují snadnou vlastní integrace. Obvyklé scénáře použití sendgrid vám umožňuje patří:

* Automatické odesílání oznámení zákazníkům
* Správa distribučních seznamů pro odesílání zákazníkům měsíční e letáků a speciálních nabídek
* Shromažďování metriky v reálném čase pro takové věci, jako e-mailu blokovaný a odezvy zákazníka
* Generování sestav k identifikaci trendů
* Předávání dotazy zákazníků
* E-mailových oznámení z vaší aplikace

Další informace najdete v tématu <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu sendgrid vám umožňuje
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Postupy: použití knihoven javax.mail
Získat javax.mail knihovny, například z <http://www.oracle.com/technetwork/java/javamail> a importovat je do vašeho kódu. Na hlavní proces pro použití knihovny javax.mail k odesílání e-mailu pomocí protokolu SMTP je provést následující akce:

1. Zadejte hodnoty SMTP, včetně serveru SMTP, který je pro sendgrid vám umožňuje smtp.sendgrid.net.

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

1. Rozšíření *javax.mail.Authenticator* třída a ve vaší implementace nástroje *getPasswordAuthentication* metody vracet sendgrid vám umožňuje uživatelské jméno a heslo.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Vytvoření relace ověřené e-mailu prostřednictvím *javax.mail.Session* objektu.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Vytvořte zprávu a přiřadit **k**, **z**, **subjektu** a obsahu hodnoty. To je ukázáno v [postupy: vytvoření e-mailu](#how-to-create-an-email) části.
4. Odeslat zprávu prostřednictvím *javax.mail.Transport* objektu. To je ukázáno v [postupy: e-mailovou zprávu] [#how-k-send-] části e-mail.

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Následující ukazuje, jak zadat hodnoty pro e-mailu.

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

## <a name="how-to-send-an-email"></a>Postupy: odeslání e-mailu
Následující ukazuje, jak odeslat e-mail.

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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postupy: použití filtrů k povolení zápatí stránky, sledování a analýzy
Sendgrid vám umožňuje poskytuje další e-mailové funkce prostřednictvím *filtry*. Jsou to nastavení, které mohou být přidány do e-mailovou zprávu povolit specifické funkce, například povolení sledování klikněte na tlačítko, Google analytics, předplatné, sledování a tak dále. Úplný seznam filtrů, najdete v části [nastavení filtru][Filter Settings].

* Následující ukazuje, jak vložit zápatí filtr, který je výsledkem text HTML zobrazovaných v dolní části odesílány e-mailu.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Další příklad filtr je kliknutím na tlačítko sledování. Řekněme, že vaše text e-mailu obsahuje hypertextový odkaz, jako je například následující příkaz, a chcete sledovat rychlost klikněte na:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Pokud chcete povolit, klikněte na tlačítko sledování, použijte následující kód:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Postupy: aktualizovat vlastnosti e-mailu
Některé vlastnosti e-mailu můžete přepsat pomocí **nastavit * vlastnost*** nebo připojených pomocí **přidat*vlastnost ***.

Například zadejte **ReplyTo** adresy, použijte tento příkaz:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Chcete-li přidat **kopie** příjemce, použijte tento příkaz:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití další služby sendgrid vám umožňuje
Sendgrid vám umožňuje nabízí rozhraní API založené na webu, který můžete využít další funkce sendgrid vám umožňuje aplikaci Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API sendgrid vám umožňuje][SendGrid API documentation].

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby sendgrid vám umožňuje e-mailu, postupujte podle následujících odkazech na další informace.

* Ukázka, která demonstruje použití sendgrid vám umožňuje v nasazení služby Azure: [postup odesílání e-mailu pomocí sendgrid vám umožňuje z prostředí Java v Azure nasazení](store-sendgrid-java-how-to-send-email-example.md)
* Sendgrid vám umožňuje Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentaci k rozhraní API sendgrid vám umožňuje: <https://sendgrid.com/docs/API_Reference/index.html>
* Sendgrid vám umožňuje speciální nabídka pro zákazníky, Azure: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[cloudový e-mailovou službu]: https://sendgrid.com/email-solutions
[doručování e-mailem transakční]: https://sendgrid.com/transactional-email
