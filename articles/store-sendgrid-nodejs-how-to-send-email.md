---
title: Jak používat e-mailovou službu SendGrid (Node.js) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odesílat e-maily pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané pomocí rozhraní NODE.js API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60931700"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Jak odeslat e-mail pomocí SendGrid z Node.js

Tato příručka ukazuje, jak provádět běžné úlohy programování s e-mailovou službou SendGrid v Azure. Ukázky jsou zapsány pomocí rozhraní NODE.js API. Zahrnuté scénáře zahrnují **vytváření e-mailů**, **odesílání e-mailů**, **přidávání příloh**, použití **filtrů**a **aktualizaci vlastností**. Další informace o sendgridu a odesílání e-mailů najdete v části [Další kroky.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?

SendGrid je [cloudová e-mailová služba,] která poskytuje spolehlivé [transakční doručování e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními api, která usnadňují vlastní integraci. Běžné scénáře použití SendGrid zahrnují:

* Automatické odesílání příjmů zákazníkům
* Správa distribučních seznamů pro zasílání měsíčních e-letců a speciálních nabídek
* Shromažďování metrik v reálném čase pro věci, jako je blokovaný e-mail a reakce zákazníků
* Generování sestav, které pomáhají identifikovat trendy
* Předávání dotazů zákazníků
* E-mailová oznámení z vaší aplikace

Další informace naleznete [https://sendgrid.com](https://sendgrid.com)v tématu .

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odkaz na modul SendGrid Node.js

Modul SendGrid pro soubor Node.js lze nainstalovat prostřednictvím správce balíčků uzlů (npm) pomocí následujícího příkazu:

```bash
npm install sendgrid
```

Po instalaci můžete modul v aplikaci vyžadovat pomocí následujícího kódu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Modul SendGrid exportuje funkce **SendGrid** a **Email.**
**SendGrid** je zodpovědný za odesílání e-mailů prostřednictvím webového rozhraní API, zatímco **e-mail** zapouzdřuje e-mailovou zprávu.

## <a name="how-to-create-an-email"></a>Postup: Vytvoření e-mailu

Vytvoření e-mailové zprávy pomocí modulu SendGrid zahrnuje nejprve vytvoření e-mailové zprávy pomocí funkce E-mail a následné odeslání pomocí funkce SendGrid. Následuje příklad vytvoření nové zprávy pomocí funkce E-mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Můžete také zadat zprávu HTML pro klienty, kteří ji podporují nastavením vlastnosti html. Například:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Nastavení vlastností textu i html poskytuje klientům, kteří nemohou podporovat zprávy HTML, zajistit bezproblémovou záložní textovou část.

Další informace o všech vlastnostech podporovaných funkcí E-mail naleznete v [tématu sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Postup: Odeslání e-mailu

Po vytvoření e-mailové zprávy pomocí funkce E-mail ji můžete odeslat pomocí webového rozhraní API poskytovaného službou SendGrid. 

### <a name="web-api"></a>Web API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Zatímco výše uvedené příklady ukazují předávání v e-mailového objektu a funkce zpětného volání, můžete také přímo vyvolat funkci odeslat přímo zadáním vlastností e-mailu. Například:  
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

## <a name="how-to-add-an-attachment"></a>Postup: Přidání přílohy
Přílohy lze přidat do zprávy zadáním názvu souboru a cesty ve vlastnosti **soubory.** Následující příklad ukazuje odeslání přílohy:

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
> Při použití vlastnosti **files** musí být soubor přístupný prostřednictvím [souboru fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Pokud je soubor, který chcete připojit, hostovaný ve službě Azure Storage, například v kontejneru objektů Blob, musíte nejprve zkopírovat soubor do místního úložiště nebo na jednotku Azure, než ho můžete odeslat jako přílohu pomocí **vlastnosti files.**
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Postup: Povolení zápatí a sledování pomocí filtrů

SendGrid poskytuje další funkce e-mailu pomocí filtrů. Jedná se o nastavení, která lze přidat do e-mailové zprávy, aby bylo možné povolit konkrétní funkce, jako je povolení sledování kliknutí, analýzy Google, sledování předplatného a tak dále. Úplný seznam filtrů naleznete v tématu [Nastavení filtrů][Filter Settings].

Filtry lze použít na zprávu pomocí vlastnosti **filters.**
Každý filtr je určen hash obsahující nastavení specifická pro filtr.
Následující příklady ukazují filtry pro sledování zápatí a kliknutí:

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

### <a name="click-tracking"></a>Klikněte na Sledování

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

## <a name="how-to-update-email-properties"></a>Postup: Aktualizace vlastností e-mailu

Některé vlastnosti e-mailu lze přepsat pomocí **setProperty** nebo připojeny pomocí **addProperty**. Můžete například přidat další příjemce pomocí

```javascript
email.addTo('jeff@contoso.com');
```

nebo nastavte filtr pomocí

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Další informace naleznete v tématu [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Postup: Použití dalších služeb SendGrid

SendGrid nabízí webová řešení API, která můžete použít k využití dalších funkcí SendGrid z vaší aplikace Azure. Podrobné informace naleznete v [dokumentaci rozhraní API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili základy služby SendGrid Email, postupujte podle těchto odkazů, abyste se dozvěděli více.

* Úložiště modulů SendGrid Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* Dokumentace rozhraní API SendGrid:<https://sendgrid.com/docs>
* Speciální nabídka SendGrid pro zákazníky Azure:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[cloudová e-mailová služba]: https://sendgrid.com/email-solutions
[transakční doručování e-mailů]: https://sendgrid.com/transactional-email
