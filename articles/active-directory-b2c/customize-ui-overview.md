---
title: Informace o přizpůsobení uživatelského rozhraní v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak přizpůsobit uživatelské rozhraní pro aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 08cd16cb9edc5606a18fc5d425910185f7747179
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187517"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Informace o přizpůsobení uživatelského rozhraní v Azure Active Directory B2C

Možnost vytvoření a přizpůsobení uživatelského rozhraní (UI), které obsluhuje Azure Active Directory (Azure AD) B2C pro vaše aplikace je důležité pro poskytuje bezproblémové prostředí pro vaše zákazníky. Tyto zkušenosti zahrnují upravování profilu registrace, přihlášení a resetování hesla. Tento článek obsahuje informace, které vám umožní přizpůsobit uživatelské rozhraní vaší aplikace.

V závislosti na vašich potřebách při rozhodování o těchto možností, přizpůsobení uživatelského rozhraní aplikace různými způsoby. Příklad:

- Pokud používáte [toky uživatelů](active-directory-b2c-reference-policies.md) k zadání hesla registrace / přihlášení, resetování nebo upravování profilu prostředí ve vaší aplikaci, můžete použít [web Azure portal a přizpůsobení uživatelského rozhraní](tutorial-customize-ui.md).
- Pokud zadáváte přihlásit pouze, stránku pro resetování hesla jeho související a ověření e-mailem, můžete použít stejný postup přizpůsobení, které se používají pro [přihlašovací stránku Azure AD](../active-directory/fundamentals/customize-branding.md).
- Pokud zákazníci se pokusí upravovat svůj profil, než se přihlásí, bude přesměrován na stránku přizpůsobit pomocí stejného postupu, které se používají pro přizpůsobení na přihlašovací stránku Azure AD.
- Pokud používáte [vlastní zásady](active-directory-b2c-overview-custom.md) registrace / přihlášení, heslo resetovat, nebo profil úpravy ve vaší aplikaci, použijte [soubory zásad přizpůsobit uživatelské rozhraní](active-directory-b2c-ui-customization-custom.md).
- Pokud je třeba zadat dynamický obsah podle zákazníka rozhodnutí, můžete použít [obsahu stránky vlastní zásady, které můžete změnit](active-directory-b2c-ui-customization-custom-dynamic.md) v závislosti na parametr, který se odešle v řetězci dotazu. Například obrázek pozadí na stránce registrace nebo přihlášení Azure AD B2C se změní, založené na parametr předat z vašich webových nebo mobilních aplikací.

Azure AD B2C kód v prohlížeči vašeho zákazníka a využívá moderní přístup a volá [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/). V době běhu obsah je načten z adresy URL, kterou zadáte v toku uživatele nebo zásad. Můžete zadat různé adresy URL pro různé stránky. Po načtení obsahu z vaší adresy URL je sloučen s fragment HTML z Azure AD B2C vložili a následně se zobrazí na vaše zákazníky.

Než začnete, projděte si následující pokyny:

- Azure AD B2C sloučí obsah HTML stránky. Nemáte zkopírujte a pokuste se změnit výchozí obsah, který poskytuje Azure AD B2C. Doporučujeme vytvářet obsah HTML úplně od začátku a použít výchozí obsah jako odkaz.
- Z bezpečnostních důvodů nejsou povoleny zahrnout jazyka JavaScript v obsahu.
- Podporovaný prohlížeč verze jsou: 
    - Aplikace Internet Explorer 11, 10 a Microsoft Edge
    - Omezená podpora pro Internet Explorer 9 a 8
    - Google Chrome 42.0 a vyšší
    - Mozilla Firefox 38.0 a vyšší
- Ujistěte se, že nejsou zahrnuty značky v kódu HTML, protože to naruší to operací POST generovaný vložený kód HTML z Azure AD B2C.

## <a name="where-do-i-store-ui-content"></a>Kde můžu ukládat obsah uživatelského rozhraní?

Můžete hostovat vaše uživatelské rozhraní obsahu kdekoli, jako například na [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md)webových serverů, sítě CDN, AWS S3 a sdílení systémů souborů. Důležité je obsahu na veřejně dostupný koncový bod HTTPS s povolením CORS hostitele. Při zadávání v obsahu, je nutné použít absolutní adresu URL.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Provedete následující příkaz pro přizpůsobení uživatelského rozhraní:

- Vytvořit ve správném formátu HTML obsah s prázdnou `<div id="api"></div>` element nachází někde v `<body>`. Tento element značky vloženy obsahu Azure AD B2C. Následující příklad ukazuje minimální stránka:

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

- Hostování obsahu na koncový bod HTTPS (s CORS povolené). Jak získat a možnosti požadavek metody musí být povolené při konfiguraci CORS.
- Pomocí šablon stylů CSS stylu prvků uživatelského rozhraní, které Azure AD B2C se vloží do vaší stránky. Následující příklad ukazuje jednoduchý soubor šablony stylů CSS, která také obsahuje nastavení pro registraci vloženého elementů HTML:

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

- Vytvoření nebo úprava zásad používat obsah, který jste vytvořili.

V následující tabulce jsou uvedeny fragmentů kódu HTML, které Azure AD B2C se sloučí do `<div id="api"></div>` prvek umístěný v obsahu.

| Vložené: stránka | Popis HTML |
| ------------- | ------------------- |
| Výběr zprostředkovatele identity | Obsahuje seznam tlačítek pro zprostředkovatele identity, které zákazník můžete vybírat při registraci nebo přihlášení. Tato tlačítka zahrnout zprostředkovatelů sociálních identit, jako je Facebook, Google nebo místní účty (podle e-mailové adresy nebo uživatelského jména). |
| Místní účet pro zápis | Obsahuje formulář pro místní účet pro zápis na základě e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako je například pole textového zadání, pole pro zadání hesla, přepínač, jedním výběrem rozevírací seznamy a vyberte zaškrtávací políčka. |
| Registrace sociálního účtu | Se můžou objevit při registraci pomocí existujícího účtu ze zprostředkovatele sociální identity, jako je Facebook nebo Google. Používá se při další informace musí být shromažďovány z zákazník využívající registračního formuláře. |
| Sjednocené registrace nebo přihlašování | Obslužné rutiny, jak registrace a přihlašování zákazníků, kteří můžou využívat zprostředkovatelů sociálních identit, jako je Facebook, Google nebo místní účty. |
| Ověřování pomocí služby Multi-Factor Authentication | Zákazníky můžete ověřit jejich telefonních čísel (pomocí textových nebo hlasových) během registrace nebo přihlášení. |
| Chyba | Poskytuje informace o chybě na zákazníka. |


## <a name="how-do-i-localize-content"></a>Jak můžu lokalizaci obsahu?

Lokalizace HTML obsah tím, že [přizpůsobení jazyka](active-directory-b2c-reference-language-customization.md) ve vašem tenantovi Azure AD B2C. Když se tato funkce povolí Azure AD B2C k předávání parametru Open ID Connect `ui-locales` do vašeho koncového bodu. Vaše servery obsahu můžete tento parametr uvést specifické pro jazyk HTML stránky.

Obsah můžete získaných z různých míst na základě národního prostředí, který se používá. Váš koncový bod s povoleným CORS nastavíte strukturu složek jako hostitele obsahu pro konkrétní jazyky. Ten správný budete volat, pokud použijete zástupné hodnoty {jazyková verze: RFC5646}. Například svou vlastní stránku identifikátoru URI může vypadat například takto `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. Můžete načíst stránku ve francouzštině stahují obsah z `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Příklady

Příklady přizpůsobení, stáhněte a přečtěte si tyto [ukázkové soubory šablon](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Další postup

- Pokud používáte toky uživatelů, můžete začít přizpůsobení uživatelského rozhraní s kurzem: [Přizpůsobení uživatelského rozhraní svých aplikací v Azure Active Directory B2C](tutorial-customize-ui.md).
- Pokud používáte vlastní zásady, můžete začít přizpůsobením uživatelského rozhraní s následujícím článku: [Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

