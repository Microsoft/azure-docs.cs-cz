---
title: Jak používat e-mailovou službu SendGrid (Node.js) | Microsoft Docs
description: Přečtěte si, jak odeslat e-mail pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané pomocí rozhraní Node.js API.
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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015416"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Odeslání e-mailu pomocí SendGrid z Node.js

Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí e-mailové služby SendGrid v Azure. Ukázky se napíší pomocí rozhraní Node.js API. Mezi zahrnuté scénáře patří **vytváření e-mailů**, **posílání e-mailů**, **přidávání příloh**, **používání filtrů** a **aktualizace vlastností**. Další informace o SendGrid a odesílání e-mailů najdete v části [Další kroky](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?

SendGrid je [Cloudová e-mailová služba] , která poskytuje spolehlivé zasílání [transakčních e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními rozhraními API, která usnadňují vlastní integraci. Mezi běžné scénáře použití SendGrid patří:

* Automatické odesílání účtenek zákazníkům
* Správa distribučních seznamů pro posílání zákazníků měsíčně e-letáků a speciální nabídky
* Shromažďování metrik v reálném čase pro věci, jako je Blokovaný e-mail a reakce zákazníků
* Generování sestav, které vám pomůžou identifikovat trendy
* Předávání dotazů zákazníkům
* E-mailová oznámení z vaší aplikace

Další informace najdete na webu [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odkazování na modul SendGrid Node.js

Modul SendGrid pro Node.js lze nainstalovat pomocí uzlu Správce balíčků (npm) pomocí následujícího příkazu:

```bash
npm install sendgrid
```

Po instalaci můžete v aplikaci vyžadovat modul pomocí následujícího kódu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Modul SendGrid exportuje funkce **SendGrid** a **e-mailu** .
**SendGrid** zodpovídá za posílání e-mailů přes webové rozhraní API, zatímco **e-mail** zapouzdřuje e-mailovou zprávu.

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu

Vytvoření e-mailové zprávy pomocí modulu SendGrid zahrnuje nejprve vytvoření e-mailové zprávy pomocí funkce email a následné odeslání pomocí funkce SendGrid. Následuje příklad vytvoření nové zprávy pomocí funkce email:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Můžete také zadat zprávu HTML pro klienty, kteří ji podporují, nastavením vlastnosti HTML. Například:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Nastavení vlastnosti text i HTML poskytuje pro klienty, kteří nepodporují zprávy HTML, řádný přechod na textový obsah.

Další informace o všech vlastnostech podporovaných funkcí email naleznete v tématu [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailů

Po vytvoření e-mailové zprávy pomocí funkce E-mail ji můžete odeslat pomocí webového rozhraní API, které poskytuje SendGrid. 

### <a name="web-api"></a>Webové rozhraní API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Zatímco výše uvedené příklady ukazují předávání e-mailového objektu a funkce zpětného volání, můžete také přímo vyvolat funkci Send přímo zadáním vlastností e-mailu. Například:  
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
Přílohy lze do zprávy přidat zadáním názvů souborů a cest ve vlastnosti **soubory** . Následující příklad ukazuje odeslání přílohy:

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
> Při použití vlastnosti **soubory** musí být soubor přístupný prostřednictvím [FS. readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Pokud je soubor, který chcete připojit, hostovaný v Azure Storage, jako je například v kontejneru objektů blob, musíte nejprve zkopírovat soubor do místního úložiště nebo na jednotku Azure, aby bylo možné ho odeslat jako přílohu pomocí vlastnosti **Files** .
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Postupy: použití filtrů k povolení zápatí a sledování

SendGrid poskytuje další funkce e-mailu prostřednictvím použití filtrů. Jedná se o nastavení, která se dají přidat do e-mailové zprávy, aby se povolily konkrétní funkce, jako je povolení sledování kliknutí, Google Analytics, sledování předplatného atd. Úplný seznam filtrů najdete v tématu [nastavení filtru][Filter Settings].

Filtry lze použít pro zprávu pomocí vlastnosti **Filters** .
Každý filtr je určen hodnotou hash obsahující nastavení specifické pro filtr.
Následující příklady znázorňují zápatí a klikněte na sledovací filtry:

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

### <a name="click-tracking"></a>Klikněte na sledování.

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

## <a name="how-to-update-email-properties"></a>Postupy: aktualizace vlastností e-mailu

Některé vlastnosti e-mailu lze přepsat pomocí vlastností **SetProperty** nebo připojených pomocí **AddProperty**. Můžete například přidat další příjemce pomocí

```javascript
email.addTo('jeff@contoso.com');
```

nebo nastavte filtr pomocí

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Další informace najdete v tématu [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: používání dalších služeb SendGrid

SendGrid nabízí webová rozhraní API, která můžete použít k využití dalších funkcí SendGrid z vaší aplikace Azure. Úplné podrobnosti najdete v [dokumentaci k rozhraní SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy e-mailové služby SendGrid, získáte další informace na následujících odkazech.

* Úložiště modulu SendGrid Node.js: [SendGrid-NodeJS][sendgrid-nodejs]
* Dokumentace k rozhraní SendGrid API: <https://sendgrid.com/docs>
* SendGrid speciální nabídka pro zákazníky Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[cloudové e-mailové služby]: https://sendgrid.com/email-solutions
[doručení transakčního e-mailu]: https://sendgrid.com/transactional-email
