---
title: Jak používat e-mailovou službu SendGrid (Java) | Microsoft Docs
description: Přečtěte si, jak odeslat e-mail pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v jazyce Java.
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
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015447"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Odeslání e-mailu pomocí SendGrid z Java
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí e-mailové služby SendGrid v Azure. Ukázky jsou napsané v jazyce Java. Mezi zahrnuté scénáře patří **vytváření e-mailů**, **posílání e-mailů**, **přidávání příloh**, **používání filtrů** a **aktualizace vlastností**. Další informace o SendGrid a odesílání e-mailů najdete v části [Další kroky](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [Cloudová e-mailová služba] , která poskytuje spolehlivé zasílání [transakčních e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními rozhraními API, která usnadňují vlastní integraci. Mezi běžné scénáře použití SendGrid patří:

* Automatické odesílání účtenek zákazníkům
* Správa distribučních seznamů pro posílání zákazníků měsíčně e-letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro věci, jako je Blokovaný e-mail a reakce zákazníků
* Generování sestav, které vám pomůžou identifikovat trendy
* Předávání dotazů zákazníkům
* E-mailová oznámení z vaší aplikace

Další informace naleznete v tématu <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Postupy: použití knihoven javax. mail
Získejte knihovny javax. mail, například z <https://www.oracle.com/technetwork/java/javamail> a importujte je do kódu. K posílání e-mailů pomocí protokolu SMTP na nejvyšší úrovni je potřeba provést tyto kroky:

1. Zadejte hodnoty SMTP, včetně serveru SMTP, který pro SendGrid je smtp.sendgrid.net.

    ```java
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

1. Rozšíříte třídu *javax. mail. Authenticator* a v implementaci metody *getPasswordAuthentication* vrátíte uživatelské jméno a heslo SendGrid.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Pomocí objektu *javax. mail. Session* vytvořte ověřenou e-mailovou relaci.  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Vytvořte zprávu a přiřaďte **k** nim hodnoty, **od**, **Předmět** a obsah. To se zobrazuje v části [Postupy: vytvoření e-mailu](#how-to-create-an-email) .
4. Odešle zprávu prostřednictvím objektu *javax. mail. Transport* . Zobrazuje se v části [postupy: odeslání e-mailu] [#how-to-Send-e-mail].

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Následující příklad ukazuje, jak zadat hodnoty pro e-mail.

```java
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
```

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailů
Následující příklad ukazuje, jak odeslat e-mail.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Následující kód ukazuje, jak přidat přílohu.

```java
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
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Postupy: použití filtrů k povolení zápatí, sledování a analýz
SendGrid poskytuje další funkce e-mailu prostřednictvím použití *filtrů*. Jedná se o nastavení, která se dají přidat do e-mailové zprávy, aby se povolily konkrétní funkce, jako je povolení sledování kliknutí, Google Analytics, sledování předplatného atd. Úplný seznam filtrů najdete v tématu [nastavení filtru][Filter Settings].

* Následující příklad ukazuje, jak vložit filtr zápatí, který má za následek zobrazení textu HTML v dolní části odesílaného e-mailu.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Dalším příkladem filtru je kliknutí na sledování. Řekněme, že váš e-mailový text obsahuje hypertextový odkaz, například následující a chcete sledovat rychlost kliknutí:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Chcete-li povolit sledování Click, použijte následující kód:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Postupy: aktualizace vlastností e-mailu
Některé vlastnosti e-mailu lze přepsat pomocí **vlastnosti set** nebo pomocí možnosti **Přidat vlastnost**.

Pokud například chcete zadat adresy **ReplyTo** , použijte následující:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Chcete-li přidat příjemce **CC** , použijte následující:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: používání dalších služeb SendGrid
SendGrid nabízí webová rozhraní API, která můžete použít k využití dalších funkcí SendGrid z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy e-mailové služby SendGrid, získáte další informace na následujících odkazech.

* Ukázka, která ukazuje použití SendGrid v nasazení Azure: [odeslání e-mailu pomocí SendGrid z Java v nasazení Azure](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentace k rozhraní SendGrid API: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid speciální nabídka pro zákazníky Azure: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[cloudové e-mailové služby]: https://sendgrid.com/email-solutions
[doručení transakčního e-mailu]: https://sendgrid.com/transactional-email
