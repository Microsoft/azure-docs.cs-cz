---
title: Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si, jak přizpůsobit uživatelské rozhraní pro aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bbce41271914df4bb8a75804bc66ae538fe766fa
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716752"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C

Možnost brandingu a přizpůsobení uživatelského rozhraní (UI), které Azure Active Directory (Azure AD) B2C slouží k zajištění bezproblémového prostředí pro zákazníky, je důležité pro vaše aplikace. Mezi tyto prostředí patří registrace, přihlašování, úpravy profilu a resetování hesla. Tento článek poskytuje informace, které vám pomůžou přizpůsobit uživatelské rozhraní svých aplikací.

V závislosti na vašich potřebách, když k nim dojde, přizpůsobíte uživatelské rozhraní aplikace různými způsoby. Příklad:

- Pokud používáte [toky uživatelů](active-directory-b2c-reference-policies.md) k poskytování registrace nebo přihlašování, resetování hesla nebo prostředí pro úpravu profilů v aplikaci, použijte [Azure Portal k přizpůsobení uživatelského rozhraní](tutorial-customize-ui.md).
- Pokud používáte tok uživatele v2, můžete pomocí [šablony rozložení stránky](#page-layout-templates) změnit vzhled stránek toku uživatele bez dalšího přizpůsobení. Můžete například použít modrý nebo břidlicově šedý motiv na všechny stránky v toku uživatele.
- Pokud poskytujete pouze přihlášení, jeho doprovodné stránku pro resetování hesla a ověřovací e-maily, použijete stejný postup přizpůsobení, který se používá pro [přihlašovací stránku služby Azure AD](../active-directory/fundamentals/customize-branding.md).
- Pokud se zákazníci pokusí upravit svůj profil předtím, než se přihlásí, budou přesměrováni na stránku, kterou upravíte, pomocí stejných kroků, které se použijí k přizpůsobení přihlašovací stránky Azure AD.
- Pokud používáte [vlastní zásady](active-directory-b2c-overview-custom.md) pro poskytování registrace nebo přihlašování, resetování hesel nebo úprav profilů v aplikaci, použijte [k přizpůsobení uživatelského rozhraní soubory zásad](active-directory-b2c-ui-customization-custom.md).
- Pokud potřebujete poskytnout dynamický obsah na základě rozhodnutí zákazníka, použijte [vlastní zásady, které mohou změnit obsah stránky](active-directory-b2c-ui-customization-custom-dynamic.md) v závislosti na parametru, který je odeslán v řetězci dotazu. Například obrázek pozadí na Azure AD B2C na přihlašovací stránce se změní na základě parametru, který předáte z vaší webové nebo mobilní aplikace.
- Můžete povolit JavaScriptový kód na straně klienta v [uživatelském toku](user-flow-javascript-overview.md) Azure AD B2C nebo [vlastní zásady](page-layout.md).

Azure AD B2C spouští kód v prohlížeči zákazníka a používá moderní přístup nazvaný [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). V době běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo zásadě. Pro různé stránky zadáte různé adresy URL. Po načtení obsahu z vaší adresy URL se sloučí s fragmentem HTML vloženým z Azure AD B2C a potom se zobrazí vašemu zákazníkovi.

Pokud k přizpůsobení uživatelského rozhraní používáte vlastní soubory HTML a CSS, přečtěte si následující pokyny, než začnete:

- Azure AD B2C sloučí obsah HTML na vaše stránky. Nekopírujte a nepokoušíte se změnit výchozí obsah, který Azure AD B2C poskytovat. Nejlepší je vytvořit obsah HTML od začátku a použít výchozí obsah jako referenční.
- JavaScript teď může být součástí vlastního obsahu.
- Podporované verze prohlížeče:
    - Internet Explorer 11, 10 a Microsoft Edge
    - Omezená podpora pro Internet Explorer 9 a 8
    - Google Chrome 42,0 a novější
    - Mozilla Firefox 38,0 a novější
- Ujistěte se, že nezahrnete do kódu HTML značky formuláře, protože to brání operacím POST vytvořeným vloženým kódem HTML z Azure AD B2C.

## <a name="page-layout-templates"></a>Šablony rozložení stránky

V případě toků uživatelů v2 můžete zvolit předem navrženou šablonu, která poskytuje lepší vzhled vašich výchozích stránek a slouží jako dobrý základ pro vlastní přizpůsobení.

V nabídce vlevo v části **přizpůsobit**vyberte **rozložení stránky**. Pak vyberte **Template (Preview)** .

![Rozevírací seznam pro výběr šablony na stránce toku uživatele Azure Portal](media/customize-ui-overview/template.png)

Vyberte šablonu ze seznamu. Například šablona **oceán Blue** aplikuje na stránky toku uživatele následující rozložení:

![Příklad šablony oceánu Blue vygenerované na přihlašovací stránce pro registraci](media/customize-ui-overview/ocean-blue.png)

Když zvolíte šablonu, vybrané rozložení se použije na všechny stránky v toku uživatele a v poli **URI vlastní stránky** se zobrazí identifikátor URI pro každou stránku.

## <a name="where-do-i-store-ui-content"></a>Kde mám ukládat obsah uživatelského rozhraní?

Při použití vlastních souborů HTML a CSS k přizpůsobení uživatelského rozhraní můžete hostovat obsah svého uživatelského rozhraní kdekoli, například v [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webových serverech, sítě CDN, AWS S3 nebo v systémech pro sdílení souborů. Důležitým bodem je, že je hostitelem obsahu veřejně dostupného koncového bodu HTTPS s povoleným CORS. Při zadání v obsahu je nutné použít absolutní adresu URL.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Uživatelské rozhraní upravíte takto:

- Vytvoří obsah HTML ve správném formátu s prázdným `<div id="api"></div>` elementem umístěným někde `<body>`v. Tento prvek označuje, kde je vložen Azure AD B2C obsah. Následující příklad ukazuje minimální stránku:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Hostování obsahu na koncovém bodu HTTPS (s povolenými CORS) Při konfiguraci CORS musí být povolené metody GET i OPTIONS.
- Pomocí CSS můžete naformátovat prvky uživatelského rozhraní, které Azure AD B2C vloží do stránky. Následující příklad ukazuje jednoduchý soubor CSS, který obsahuje také nastavení pro vložené prvky HTML:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Vytvořte nebo upravte zásadu pro použití obsahu, který jste vytvořili.

V následující tabulce jsou uvedeny fragmenty kódu HTML, které se Azure AD B2C `<div id="api"></div>` sloučí do prvku umístěného v obsahu.

| Vložená stránka | Popis HTML |
| ------------- | ------------------- |
| Výběr zprostředkovatele identity | Obsahuje seznam tlačítek pro zprostředkovatele identity, ze kterých si může zákazník vybrat při registraci nebo přihlašování. Mezi tato tlačítka patří poskytovatelé sociálních identit, jako je Facebook, Google nebo místní účty (na základě e-mailové adresy nebo uživatelského jména). |
| Registrace místního účtu | Obsahuje formulář pro registraci místního účtu na základě e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je textové pole, vstupní pole pro heslo, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| Registrace účtu sociální sítě | Může se zobrazit při registraci pomocí existujícího účtu od poskytovatele sociální identity, jako je Facebook nebo Google. Používá se, když je potřeba shromáždit další informace od zákazníka pomocí registračního formuláře. |
| Jednotné registrace nebo přihlašování | Zpracovává registraci i přihlašování zákazníků, kteří můžou používat poskytovatele sociálních identit, jako je Facebook, Google nebo místní účty. |
| Vícefaktorové ověřování | Zákazníci si můžou při registraci nebo přihlašování ověřit telefonní čísla (pomocí textu nebo hlasu). |
| Chyba | Poskytne zákazníkovi informace o chybě. |


## <a name="how-do-i-localize-content"></a>Návody lokalizovat obsah?

Obsah HTML můžete lokalizovat tím, že ve svém tenantovi Azure AD B2C povolíte [vlastní nastavení jazyka](active-directory-b2c-reference-language-customization.md) . Povolením této funkce umožníte Azure AD B2C přeposlání parametru `ui-locales` OpenID Connect do svého koncového bodu. Server obsahu může tento parametr použít k poskytování HTML stránek specifických pro jazyk.

Obsah lze z různých míst načíst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS nastavíte strukturu složek na hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu {Culture: RFC5646}, zavoláte tu správnou. Například váš identifikátor URI vlastní stránky může vypadat jako `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Stránku v francouzštině můžete načíst vyvoláním obsahu z`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Příklady

Příklady úprav si můžete stáhnout a zkontrolovat v těchto [ukázkových souborech šablony](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Další postup

- Pokud používáte toky uživatelů, můžete začít přizpůsobovat uživatelské rozhraní pomocí tohoto kurzu: [Přizpůsobení uživatelského rozhraní aplikací v Azure Active Directory B2C](tutorial-customize-ui.md).
- Pokud používáte vlastní zásady, můžete začít přizpůsobovat uživatelské rozhraní pomocí článku: [Přizpůsobte uživatelské rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

