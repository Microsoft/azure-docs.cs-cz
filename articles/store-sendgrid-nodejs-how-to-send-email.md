---
title: Jak používat e-mailové služby SendGrid (Node.js) | Dokumentace Microsoftu
description: Zjistěte, jak poslat e-mailu pomocí e-mailové služby SendGrid v Azure. Ukázky kódu jsou vytvořeny pomocí rozhraní API Node.js.
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
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421279"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Odeslání e-mailů pomocí Sendgridu z Node.js

Tato příručka ukazuje, jak k provádění běžných programovacích úloh s e-mailové služby SendGrid v Azure. Ukázky jsou napsané pomocí rozhraní API Node.js. Mezi popsané scénáře patří **vytváření e-mailu**, **odesílání e-mailů**, **přidávání příloh**, **pomocí filtrů**a **aktualizují se vlastnosti**. Další informace o SendGrid a odesílání e-mailu, najdete v článku [další kroky](#next-steps) oddílu.

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailové služby SendGrid?

SendGrid je [založené na cloudu e-mailové služby] , která poskytuje spolehlivé [doručování transakční e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilní API, která usnadňují vlastní integraci. Obvyklé scénáře použití SendGrid patří:

* Automatické odesílání oznámení pro zákazníky
* Správa distribuce jsou uvedené pro odesílání zákazníkům měsíčních e letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro takové věci, jako jsou e-mailu blokovaný a odezvy zákazníků
* Generování sestav vám pomůže identifikovat trendy
* Předávání dotazy zákazníků
* E-mailových oznámení z aplikace

Další informace najdete na adrese [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odkazovat na modul Node.js SendGrid

Modul SendGrid pro Node.js můžete nainstalovat pomocí node package Manageru (npm) pomocí následujícího příkazu:

```bash
npm install sendgrid
```

Po dokončení instalace můžete vyžadovat, aby modul ve vaší aplikaci pomocí následujícího kódu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid modul exportuje **SendGrid** a **e-mailu** funkce.
**SendGrid** je odpovědná za zasílání e-mailu prostřednictvím webového rozhraní API, zatímco **e-mailu** zapouzdřuje e-mailovou zprávu.

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu

Vytvoření e-mailu pomocí Sendgridu modulu zahrnuje nejdříve vytvořením e-mailu pomocí e-mailové funkce a potom odešlete pomocí funkce služby SendGrid. Následuje příklad vytvoření nové zprávy pomocí funkce e-mailu:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Můžete také zadat zprávu ve formátu HTML pro klienty, kteří jej podporují tak, že nastavíte vlastnost html. Příklad:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Nastavení vlastnosti text a html poskytuje bezproblémové nouzového řešení ověření pomocí textového obsahu pro klienty, kteří nemohou podporovat zprávy ve formátu HTML.

Další informace o všech vlastnostech nepodporuje funkci e-mailu, najdete v části [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailu

Po vytvoření e-mailovou zprávu pomocí funkce e-mailu, můžete odeslat pomocí webového rozhraní API, které SendGrid poskytuje. 

### <a name="web-api"></a>Web API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Při výše uvedené příklady ukazují předávání v e-mailu funkci objektu a zpětné volání, můžete také přímo vyvolat funkce pro odeslání přímo zadáním vlastnosti e-mailů. Příklad:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Přílohy se dají přidat na zprávu tak, že zadáte názvy souborů a cesty ve **soubory** vlastnost. Následující příklad ukazuje odesílání přílohy:

```javascript
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
```

> [!NOTE]
> Při použití **soubory** vlastnost, soubor musí být přístupné prostřednictvím [fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Pokud je soubor, který chcete připojit hostována ve službě Azure Storage, jako je například kontejner objektů Blob, je nutné nejprve zkopírovat soubor do místního úložiště nebo na Azure disk před odesláním jako přílohy s použitím **soubory** vlastnost.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Postupy: použití filtrů k povolení zápatí a sledování

SendGrid umožňuje zajistit další e-mailové funkce pomocí filtrů. Toto jsou nastavení, které lze přidat do e-mailovou zprávu povolit konkrétní funkce, jako například povolení sledování kliknutí, Google analytics, předplatné, sledování a tak dále. Úplný seznam filtrů, naleznete v tématu [nastavení filtru][Filter Settings].

Filtry lze použít na zprávu s použitím **filtry** vlastnost.
Každý filtr je určená hodnotu hash, který obsahuje nastavení specifická pro filtr.
Následující příklady ukazují zápatí a klikněte na tlačítko filtry pro sledování:

### <a name="footer"></a>Zápatí

```javascript
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
```

### <a name="click-tracking"></a>Sledování kliknutí.

```javascript
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
```

## <a name="how-to-update-email-properties"></a>Postupy: aktualizovat vlastnosti e-mailu

Některé vlastnosti e-mailu můžete přepsat pomocí **setProperty** nebo připojených pomocí **addProperty**. Například můžete přidat další příjemce pomocí

```javascript
email.addTo('jeff@contoso.com');
```

nebo nastavte filtr s použitím

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Další informace najdete v tématu [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití služeb další SendGrid

SendGrid nabízí rozhraní API založeného na webu, která vám umožní využívat další funkce SendGrid z aplikace Azure. Úplné podrobnosti najdete v tématu [dokumentace k rozhraní API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy služby e-mailu Sendgridu, použijte tyto odkazy na další informace.

* Úložiště modul SendGrid Node.js: [sendgrid nodejs][sendgrid-nodejs]
* Dokumentace k rozhraní API SendGrid: <https://sendgrid.com/docs>
* SendGrid speciální nabídka pro zákazníky Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[založené na cloudu e-mailové služby]: https://sendgrid.com/email-solutions
[doručování transakční e-mailů]: https://sendgrid.com/transactional-email
