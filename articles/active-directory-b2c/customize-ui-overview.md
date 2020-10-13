---
title: Přizpůsobení uživatelského rozhraní
titleSuffix: Azure AD B2C
description: Naučte se, jak přizpůsobit uživatelské rozhraní pro aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84f488863a39307ac7b55d5238fab786084a3614
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259539"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní v Azure Active Directory B2C

Branding a přizpůsobení uživatelského rozhraní, které Azure Active Directory B2C (Azure AD B2C) zobrazuje vašim zákazníkům, pomáhá zajistit bezproblémové uživatelské prostředí ve vaší aplikaci. Mezi tyto možnosti patří registrace, přihlašování, úpravy profilu a resetování hesla. Tento článek představuje metody přizpůsobení uživatelského rozhraní (UI) pro uživatelské toky i pro vlastní zásady.

## <a name="ui-customization-in-different-scenarios"></a>Přizpůsobení uživatelského rozhraní v různých scénářích

Existuje několik způsobů, jak přizpůsobit uživatelské rozhraní aplikace, které je vhodné pro různé scénáře.

### <a name="user-flows"></a>Toky uživatele

Pokud používáte [toky uživatelů](user-flow-overview.md), můžete změnit vzhled stránek toku uživatelů pomocí integrovaných *šablon rozložení stránky*nebo pomocí vlastních HTML a šablon stylů CSS. Obě metody jsou popsány dále v tomto článku.

Pomocí [Azure Portal](tutorial-customize-ui.md) můžete nakonfigurovat přizpůsobení uživatelského rozhraní pro toky uživatelů.

> [!TIP]
> Chcete-li upravit pouze logo banner, obrázek pozadí a barvu pozadí stránek toku uživatele, můžete vyzkoušet funkci [Branding společnosti](#company-branding) popsanou dále v tomto článku.

### <a name="custom-policies"></a>Vlastní zásady

Pokud používáte [vlastní zásady](custom-policy-overview.md) pro poskytování registrace nebo přihlašování, resetování hesel nebo úprav profilů v aplikaci, použijte [k přizpůsobení uživatelského rozhraní soubory zásad](custom-policy-ui-customization.md).

Pokud potřebujete poskytnout dynamický obsah na základě rozhodnutí zákazníka, použijte vlastní zásady, které mohou [dynamicky měnit obsah stránky](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) v závislosti na parametru, který je odeslán v řetězci dotazu. Můžete například změnit obrázek pozadí na Azure AD B2C přihlašovací stránku nebo přihlašovací stránku na základě parametru, který předáte z vaší webové nebo mobilní aplikace.

### <a name="javascript"></a>JavaScript

Kód JavaScriptu na straně klienta můžete povolit jak v [toku uživatelů](user-flow-javascript-overview.md) , tak i ve [vlastních zásadách](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Přizpůsobení uživatelského rozhraní pouze pro přihlášení

Pokud poskytujete pouze přihlášení, společně s příslušnou stránkou pro resetování hesla a e-mailem pro ověřování, použijte stejný postup přizpůsobení, který se používá pro [přihlašovací stránku služby Azure AD](../active-directory/fundamentals/customize-branding.md).

Pokud se zákazníci pokusí upravit svůj profil ještě předtím, než se přihlásí, přesměruje se na stránku, kterou přizpůsobíte, pomocí stejných kroků, které se použijí k přizpůsobení přihlašovací stránky Azure AD.

## <a name="page-layout-templates"></a>Šablony rozložení stránky

Toky uživatelů poskytují několik integrovaných šablon, ze kterých si můžete vybrat a poskytnout tak profesionální vzhled na stránkách uživatele. Tyto šablony rozložení také mohou sloužit jako výchozí bod pro vlastní přizpůsobení.

V části **přizpůsobit** v nabídce vlevo vyberte **rozložení stránky** a pak vyberte **Šablona**.

![Rozevírací seznam pro výběr šablony na stránce toku uživatele Azure Portal](media/customize-ui-overview/template-selection.png)

Potom v seznamu vyberte šablonu. Tady jsou příklady přihlašovacích stránek pro každou šablonu:

| Mořská modrá | Břidlicová šedá | Klasický |
|:-:|:-:|:-:|
|![Příklad šablony oceánu Blue vygenerované na přihlašovací stránce pro registraci](media/customize-ui-overview/template-ocean-blue.png)|![Příklad šablony břidlicově šedé vygenerované na přihlašovací stránce pro registraci](media/customize-ui-overview/template-slate-gray.png)|![Příklad klasické šablony vykreslené na přihlašovací stránce pro registraci](media/customize-ui-overview/template-classic.png)|

Když zvolíte šablonu, vybrané rozložení se použije na všechny stránky v toku uživatele a v poli **URI vlastní stránky** se zobrazí identifikátor URI pro každou stránku.

## <a name="custom-html-and-css"></a>Vlastní HTML a CSS

Pokud chcete navrhnout vlastní rozložení zásad pomocí přizpůsobených HTML a šablon stylů CSS, můžete to udělat tak, že přepnete přepínač použít vlastní obsah stránky pro každé názvy rozložení přítomné ve vaší zásadě. Postupujte prosím podle následujících pokynů týkajících se vlastních konfigurací rozložení:

Azure AD B2C spouští kód v prohlížeči zákazníka pomocí přístupu označovaného jako [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

V době běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo vlastní zásady. Každá stránka v uživatelském prostředí načte svůj obsah z adresy URL, kterou zadáte pro tuto stránku. Po načtení obsahu z vaší adresy URL se sloučí s fragmentem kódu HTML vloženým pomocí Azure AD B2C a potom se stránka zobrazí vašemu zákazníkovi.

Než použijete vlastní soubory HTML a CSS k přizpůsobení uživatelského rozhraní, přečtěte si následující pokyny:

- Azure AD B2C **sloučí** obsah HTML na vaše stránky. Nekopírujte a nepokoušíte se změnit výchozí obsah, který Azure AD B2C poskytovat. Nejlepší je vytvořit obsah HTML od začátku a použít výchozí obsah jako referenční.
- **JavaScript** může být součástí vlastního obsahu pro [uživatelské toky](user-flow-javascript-overview.md) i pro [vlastní zásady](javascript-samples.md).
- Podporované **verze prohlížeče** :
  - Internet Explorer 11, 10 a Microsoft Edge
  - Omezená podpora pro Internet Explorer 9 a 8
  - Google Chrome 42,0 a novější
  - Mozilla Firefox 38,0 a novější
  - Safari pro iOS a macOS, verze 12 a vyšší
- Nezahrnujte **značky formuláře** do HTML. Značky formuláře jsou v konfliktu s operacemi POST generovanými kódem HTML, které jsou vložené pomocí Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Kde mám ukládat obsah uživatelského rozhraní?

Při použití vlastních souborů HTML a CSS k přizpůsobení uživatelského rozhraní můžete hostovat obsah svého uživatelského rozhraní na jakémkoli veřejně dostupném koncovém bodu HTTPS, který podporuje CORS. Například [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), webové servery, sítě CDN, AWS S3 nebo souborové systémy pro sdílení souborů.

Důležitým bodem je, že je hostitelem obsahu veřejně dostupného koncového bodu HTTPS s [povoleným CORS](https://enable-cors.org/server.html). Při zadání v obsahu je nutné použít absolutní adresu URL.

> [!NOTE]
> Podrobnosti o vytváření obsahu HTML, nahrávání obsahu do úložiště objektů BLOB v Azure a konfiguraci CORS najdete v části [návod k vlastnímu obsahu stránky](custom-policy-ui-customization.md#custom-page-content-walkthrough) v článku přizpůsobení uživatelského rozhraní.

## <a name="get-started-with-custom-html-and-css"></a>Začínáme s vlastními HTML a CSS

Začněte používat vlastní HTML a CSS na stránkách uživatelského prostředí pomocí následujících pokynů.

- Vytvoří obsah HTML ve správném formátu s prázdným `<div id="api"></div>` elementem umístěným někde v `<body>` . Tento prvek označuje, kde je vložen Azure AD B2C obsah. Následující příklad ukazuje minimální stránku:

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

- Hostování obsahu na koncovém bodu HTTPS (s povolenými CORS) Při konfiguraci CORS musí být povolené metody GET i OPTIONS.
- Vytvořte nebo upravte tok uživatele nebo vlastní zásady, abyste mohli používat obsah, který jste vytvořili.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmenty kódu HTML z Azure AD B2C

V následující tabulce jsou uvedeny fragmenty kódu HTML, které se Azure AD B2C sloučí do `<div id="api"></div>` prvku umístěného v obsahu.

| Vložená stránka | Popis HTML |
| ------------- | ------------------- |
| Výběr zprostředkovatele identity | Obsahuje seznam tlačítek pro zprostředkovatele identity, ze kterých si může zákazník vybrat při registraci nebo přihlašování. Mezi tato tlačítka patří poskytovatelé sociálních identit, jako je Facebook, Google nebo místní účty (na základě e-mailové adresy nebo uživatelského jména). |
| Registrace místního účtu | Obsahuje formulář pro registraci místního účtu na základě e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je textové pole, vstupní pole pro heslo, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| Registrace účtu sociální sítě | Může se zobrazit při registraci pomocí existujícího účtu od poskytovatele sociální identity, jako je Facebook nebo Google. Používá se, když je potřeba shromáždit další informace od zákazníka pomocí registračního formuláře. |
| Jednotné registrace nebo přihlašování | Zpracovává registraci i přihlašování zákazníků, kteří můžou používat poskytovatele sociálních identit, jako je Facebook, Google nebo místní účty. |
| Ověřování pomocí služby Multi-Factor Authentication | Zákazníci si můžou při registraci nebo přihlašování ověřit telefonní čísla (pomocí textu nebo hlasu). |
| Chyba | Poskytne zákazníkovi informace o chybě. |

## <a name="company-branding"></a>Branding společnosti

Můžete přizpůsobit stránky toku uživatele pomocí loga nápisu, obrázku pozadí a barvy pozadí pomocí Azure Active Directory [Branding společnosti](../active-directory/fundamentals/customize-branding.md).

Chcete-li přizpůsobit stránky toku uživatele, je třeba nejprve nakonfigurovat Branding společnosti v Azure Active Directory a pak ji povolit v rozložení stránky vašich uživatelských toků v Azure AD B2C.

### <a name="configure-company-branding"></a>Konfigurace brandingu společnosti

Začněte tím, že nastavíte logo banner, obrázek pozadí a barvu pozadí v rámci **brandingu společnosti**.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat**vyberte **Branding společnosti**.
1. Postupujte podle pokynů v části [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](../active-directory/fundamentals/customize-branding.md).

Při konfiguraci brandingu společnosti v Azure AD B2C mějte na paměti tyto věci:

* Branding společnosti v Azure AD B2C je aktuálně omezen na **obrázek na pozadí**, **logo banneru**a přizpůsobení **barvy pozadí** . Ostatní vlastnosti v podokně Branding společnosti, například v části **Rozšířená nastavení**, nejsou *podporovány*.
* Na stránkách toku uživatele se zobrazuje barva pozadí před načtením obrázku na pozadí. Doporučujeme, abyste si zvolili barvu pozadí, která se přesně shoduje s barvami v obrázku na pozadí pro účely hladkého načítání.
* Logo banner se zobrazí v ověřovacích e-mailech odesílaných uživatelům při zahájení uživatelského toku registrace.

### <a name="enable-branding-in-user-flow-pages"></a>Povolit brandingování na stránkách toku uživatele

Po nakonfigurování firemního brandingu ho Povolte ve svých uživatelských tocích.

1. V levé nabídce Azure Portal vyberte **Azure AD B2C**.
1. V části **zásady**vyberte **toky uživatelů (zásady)**.
1. Vyberte tok uživatele, pro který chcete povolit Branding společnosti. Branding společnosti není **podporován** pro standardní *přihlašování* a standardní *profily pro úpravu* typů toku uživatele.
1. V části **přizpůsobit**vyberte **rozložení stránky**a potom vyberte rozložení, které chcete označit jako značku. Vyberte například možnost **sjednocení registrace nebo přihlášení na přihlašovací stránce**.
1. Pro **verzi rozložení stránky (Preview)** vyberte verze **1.2.0** nebo vyšší.
1. Vyberte **Uložit**.

Chcete-li označit všechny stránky v toku uživatele, nastavte pro každé rozložení stránky v toku uživatele verzi rozložení stránky.

![Výběr rozložení stránky v Azure AD B2C Azure Portal](media/customize-ui-overview/portal-02-page-layout-select.png)

Tento příklad poznámky ukazuje vlastní logo banneru a obrázek na pozadí na stránce flow ( *registrace) a přihlášení* uživatele, která používá základní šablonu oceánu:

![Přihlašovací stránka s brandingem, která je obsluhována Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Použití prostředků brandingu společnosti ve vlastním HTML

Chcete-li použít prostředky značky vaší společnosti ve vlastním HTML, přidejte následující značky mimo `<div id="api">` značku:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Zdroj obrázku je nahrazen symbolem obrázku pozadí a logem banner. Jak je popsáno v části Začínáme [s vlastními HTML a CSS](#get-started-with-custom-html-and-css) , použijte třídy CSS pro styly a umístění prostředků na stránce.

## <a name="localize-content"></a>Lokalizace obsahu

Obsah HTML můžete lokalizovat tím, že ve svém tenantovi Azure AD B2C povolíte [vlastní nastavení jazyka](user-flow-language-customization.md) . Povolením této funkce umožníte Azure AD B2C přeposlání parametru OpenID Connect `ui_locales` do svého koncového bodu. Server obsahu může tento parametr použít k poskytování HTML stránek specifických pro jazyk.

Obsah lze z různých míst načíst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS nastavíte strukturu složek na hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu, zavoláte tu správnou `{Culture:RFC5646}` .

Identifikátor URI vlastní stránky může vypadat například takto:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Stránku v francouzštině můžete načíst tak, že nahrajete obsah z:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Příklady

Několik ukázkových souborů šablon najdete v úložišti [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) na GitHubu.

Ukázkové soubory HTML a CSS v šablonách se nacházejí v adresáři [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Další kroky

- Pokud používáte **toky uživatelů**, můžete začít přizpůsobovat uživatelské rozhraní pomocí tohoto kurzu:

    [Přizpůsobení uživatelského rozhraní aplikací v Azure Active Directory B2C](tutorial-customize-ui.md).
- Pokud používáte **vlastní zásady**, můžete začít přizpůsobovat uživatelské rozhraní pomocí článku:

    [Přizpůsobte uživatelské rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C](custom-policy-ui-customization.md).
