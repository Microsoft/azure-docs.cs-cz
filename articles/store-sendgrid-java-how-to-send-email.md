---
title: Jak používat e-mailovou službu SendGrid (Java) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odesílat e-maily pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v jazyce Java.
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
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876512"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Jak odeslat e-mail pomocí SendGrid z Javy
Tato příručka ukazuje, jak provádět běžné úlohy programování s e-mailovou službou SendGrid v Azure. Ukázky jsou napsány v Javě. Zahrnuté scénáře zahrnují **vytváření e-mailů**, **odesílání e-mailů**, **přidávání příloh**, použití **filtrů**a **aktualizaci vlastností**. Další informace o sendgridu a odesílání e-mailů najdete v části [Další kroky.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [cloudová e-mailová služba,] která poskytuje spolehlivé [transakční doručování e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními api, která usnadňují vlastní integraci. Běžné scénáře použití SendGrid zahrnují:

* Automatické odesílání příjmů zákazníkům
* Správa distribučních seznamů pro zasílání měsíčních e-letců a speciálních nabídek
* Shromažďování metrik v reálném čase pro věci, jako je blokovaný e-mail a reakce zákazníků
* Generování sestav, které pomáhají identifikovat trendy
* Předávání dotazů zákazníků
* E-mailová oznámení z vaší aplikace

Další informace naleznete v tématu <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Postup: Použití knihoven javax.mail
Získejte javax.mail knihovny, <https://www.oracle.com/technetwork/java/javamail> například z a importovat je do kódu. Na vysoké úrovni, proces pro použití javax.mail knihovny k odesílání e-mailů pomocí SMTP je provést následující:

1. Zadejte hodnoty SMTP, včetně serveru SMTP, který je pro SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
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

1. Rozšiřte třídu *javax.mail.Authenticator* a při implementaci metody *getPasswordAuthentication* vraťte uživatelské jméno a heslo SendGrid.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Vytvořte ověřenou e-mailovou relaci prostřednictvím objektu *javax.mail.Session.*  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Vytvořte zprávu a **přiřaďte hodnotám**Komu , **Od** **, Předměta** a obsahu. To se zobrazí v části [Postup: Vytvoření e-mailu.](#how-to-create-an-email)
4. Odešlete zprávu prostřednictvím *javax.mail.Transport* objektu. To se zobrazí v části [How To: Send a Email][#how-to-send-an-email].

## <a name="how-to-create-an-email"></a>Postup: Vytvoření e-mailu
Následující text ukazuje, jak zadat hodnoty pro e-mail.

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

## <a name="how-to-send-an-email"></a>Postup: Odeslání e-mailu
Následující text ukazuje, jak odeslat e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Postup: Přidání přílohy
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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postup: Použití filtrů k povolení zápatí, sledování a analýzy
SendGrid poskytuje další e-mailové funkce pomocí *filtrů*. Jedná se o nastavení, která lze přidat do e-mailové zprávy, aby bylo možné povolit konkrétní funkce, jako je povolení sledování kliknutí, analýzy Google, sledování předplatného a tak dále. Úplný seznam filtrů naleznete v tématu [Nastavení filtrů][Filter Settings].

* Následující text ukazuje, jak vložit filtr zápatí, který má za následek zobrazení textu HTML v dolní části odesílaného e-mailu.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Dalším příkladem filtru je sledování kliknutí. Řekněme, že text e-mailu obsahuje hypertextový odkaz, například následující, a chcete sledovat rychlost kliknutí:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Chcete-li povolit sledování kliknutí, použijte následující kód:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Postup: Aktualizace vlastností e-mailu
Některé vlastnosti e-mailu lze přepsat pomocí **sady Vlastnost** nebo připojit pomocí **add Property**.

Chcete-li například zadat adresy **ReplyTo,** použijte následující:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Chcete-li přidat příjemce **kopie,** použijte následující:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Postup: Použití dalších služeb SendGrid
SendGrid nabízí webová řešení API, která můžete použít k využití dalších funkcí SendGrid z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci rozhraní API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili základy služby SendGrid Email, postupujte podle těchto odkazů, abyste se dozvěděli více.

* Ukázka, která ukazuje použití SendGrid v nasazení Azure: [Jak odesílat e-maily pomocí SendGrid z Javy v nasazení Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentace rozhraní API SendGrid:<https://sendgrid.com/docs/API_Reference/index.html>
* Speciální nabídka SendGrid pro zákazníky Azure:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[cloudová e-mailová služba]: https://sendgrid.com/email-solutions
[transakční doručování e-mailů]: https://sendgrid.com/transactional-email
