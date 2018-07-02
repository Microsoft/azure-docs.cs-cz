---
title: Jak používat služby sendgrid vám umožňuje e-mailů (Node.js) | Microsoft Docs
description: Zjistěte, jak odeslat e-mailu pomocí e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky kódu jsou vytvořené pomocí rozhraní API Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "23873986"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Postup odesílání e-mailu pomocí sendgrid vám umožňuje z Node.js
Tato příručka ukazuje, jak provádět běžné úkoly programování s e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky jsou zapsány pomocí rozhraní API Node.js. Pokryté scénáře zahrnují **vytváření e-mailu**, **odesílání e-mailu**, **přidávání příloh**, **pomocí filtrů**a **aktualizace vlastností**. Další informace o sendgrid vám umožňuje a odesílání e-mailu, najdete v článku [další kroky](#next-steps) části.

## <a name="what-is-the-sendgrid-email-service"></a>Co je služba sendgrid vám umožňuje e-mailu?
Je sendgrid vám umožňuje [cloudový e-mailovou službu] poskytuje spolehlivé [doručování e-mailem transakční], škálovatelnost a analýzu v reálném čase společně s flexibilní rozhraní API, které umožňují snadnou vlastní integrace. Obvyklé scénáře použití sendgrid vám umožňuje patří:

* Automatické odesílání oznámení zákazníkům
* Správa distribučních seznamů pro odesílání zákazníkům měsíční e letáků a speciálních nabídek
* Shromažďování metriky v reálném čase pro takové věci, jako e-mailu blokovaný a odezvy zákazníka
* Generování sestav k identifikaci trendů
* Předávání dotazy zákazníků
* E-mailových oznámení z vaší aplikace

Další informace najdete na adrese [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu sendgrid vám umožňuje
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odkazovat na modul Node.js sendgrid vám umožňuje
Modul sendgrid vám umožňuje pro Node.js dají nainstalovat přes uzel Správce balíčků (npm) pomocí následujícího příkazu:

    npm install sendgrid

Po instalaci můžete požadovat modulu ve vaší aplikaci pomocí následujícího kódu:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Exportuje modul sendgrid vám umožňuje **sendgrid vám umožňuje** a **e-mailu** funkce.
**Sendgrid vám umožňuje** je odpovědná za zasílání e-mailu pomocí webového rozhraní API, zatímco **e-mailu** zapouzdří e-mailovou zprávu.

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Vytvoření e-mailovou zprávu pomocí modulu sendgrid vám umožňuje zahrnuje nejdříve vytvořením e-mailovou zprávu pomocí funkce e-mailu a pak jej pomocí funkce sendgrid vám umožňuje odeslat. Následuje příklad vytvoření nové zprávy pomocí funkce e-mailu:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Můžete také určit zprávu HTML pro klienty, kteří ho podporují nastavením vlastnosti html. Příklad:

    html: This is a sample <b>HTML<b> email message.

Nastavení vlastností text a html poskytuje bezproblémové nouzového řešení ověření pomocí textového obsahu pro klienty, které nemohou podporovat zprávy ve formátu HTML.

Další informace o všech vlastnostech podporovaných zprostředkovatelem funkce e-mailu, najdete v části [sendgrid vám umožňuje nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Postupy: odeslání e-mailu
Po vytvoření e-mailovou zprávu pomocí funkce e-mailu, můžete ho pomocí webového rozhraní API poskytované sendgrid vám umožňuje odeslat. 

### <a name="web-api"></a>Web API
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Při výše uvedené příklady ukazují předávání v e-mailu funkci objekt a zpětného volání, můžete také přímo vyvolat funkce pro odeslání přímo zadáním vlastnosti e-mailu. Příklad:  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Přílohy lze přidat na zprávu zadáním názvy souboru a cesty ke v **soubory** vlastnost. Následující příklad ukazuje, odesílání přílohy:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> Při použití **soubory** vlastnost soubor musí být přístupné prostřednictvím [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Pokud chcete připojit soubor hostovaný ve službě Azure Storage, například v kontejneru objektů Blob, je nutné nejprve zkopírovat soubor do místního úložiště nebo k Azure jednotce před odesláním jako k připojení pomocí **soubory** vlastnost.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Postupy: použití filtrů k povolení zápatí a sledování
Sendgrid vám umožňuje poskytuje další e-mailové funkce prostřednictvím filtry. Jsou to nastavení, které mohou být přidány do e-mailovou zprávu povolit specifické funkce, například povolení sledování klikněte na tlačítko, Google analytics, předplatné, sledování a tak dále. Úplný seznam filtrů, najdete v části [nastavení filtru][Filter Settings].

Filtry můžete použít na zprávy pomocí **filtry** vlastnost.
Každý filtr je zadána hodnota hash obsahující nastavení pro konkrétní filtru.
Následující příklady ukazují zápatí a klikněte na tlačítko Sledování filtry:

### <a name="footer"></a>Zápatí
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>Klikněte na tlačítko Sledování
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>Postupy: aktualizovat vlastnosti e-mailu
Některé vlastnosti e-mailu můžete přepsat pomocí **nastavit * vlastnost*** nebo připojených pomocí **přidat*vlastnost ***. Například můžete přidat další příjemce pomocí

    email.addTo('jeff@contoso.com');

pomocí nebo nastavit filtr

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Další informace najdete v tématu [sendgrid vám umožňuje nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití služeb další sendgrid vám umožňuje
Sendgrid vám umožňuje nabízí rozhraní API založené na webu, který můžete využít další funkce sendgrid vám umožňuje aplikaci Azure. Úplné podrobnosti najdete v tématu [dokumentaci k rozhraní API sendgrid vám umožňuje][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy služby sendgrid vám umožňuje e-mailu, postupujte podle následujících odkazech na další informace.

* Úložiště modul Node.js sendgrid vám umožňuje: [sendgrid vám umožňuje nodejs][sendgrid-nodejs]
* Dokumentaci k rozhraní API sendgrid vám umožňuje: <https://sendgrid.com/docs>
* Sendgrid vám umožňuje speciální nabídka pro zákazníky, Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[cloudový e-mailovou službu]: https://sendgrid.com/email-solutions
[doručování e-mailem transakční]: https://sendgrid.com/transactional-email
