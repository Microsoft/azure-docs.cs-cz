---
title: Přizpůsobení uživatelského rozhraní
titleSuffix: Azure AD B2C
description: Zjistěte, jak přizpůsobit uživatelské rozhraní pro vaše aplikace, které používají Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 839e13dffc7d15b8cd258dd4b7dda6776223d052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051740"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní ve službě Azure Active Directory B2C

Branding a přizpůsobení uživatelského rozhraní, které Azure Active Directory B2C (Azure AD B2C) zobrazí svým zákazníkům pomáhá zajistit bezproblémové uživatelské prostředí ve vaší aplikaci. Mezi tyto možnosti patří přihlášení, přihlášení, úpravy profilu a resetování hesla. Tento článek zavádí metody přizpůsobení uživatelského rozhraní (UI) pro toky uživatelů i vlastní zásady.

## <a name="ui-customization-in-different-scenarios"></a>Přizpůsobení uživatelského rozhraní v různých scénářích

Existuje několik způsobů, jak přizpůsobit uživatelské rozhraní uživatelského prostředí aplikace, každý vhodný pro různé scénáře.

### <a name="user-flows"></a>Toky uživatele

Pokud používáte [toky uživatelů](user-flow-overview.md), můžete změnit vzhled stránek toku uživatelů pomocí předdefinovaných *šablon rozložení stránky*nebo pomocí vlastního kódu HTML a CSS. Obě metody jsou popsány dále v tomto článku.

Portál [Azure](tutorial-customize-ui.md) slouží ke konfiguraci přizpůsobení uživatelského rozhraní pro toky uživatelů.

> [!TIP]
> Pokud chcete upravit pouze logo banneru, obrázek pozadí a barvu pozadí stránek toku uživatelů, můžete zkusit funkci [značky společnosti (náhled)](#company-branding-preview) popsanou dále v tomto článku.

### <a name="custom-policies"></a>Vlastní zásady

Pokud používáte [vlastní zásady](custom-policy-overview.md) k zajištění registrace nebo přihlášení, resetování hesla nebo úpravy profilu v aplikaci, použijte [soubory zásad k přizpůsobení ui](custom-policy-ui-customization.md).

Pokud potřebujete poskytnout dynamický obsah na základě rozhodnutí zákazníka, použijte vlastní zásady, které mohou [dynamicky měnit obsah stránky](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) v závislosti na parametru odeslaném v řetězci dotazu. Například můžete změnit image pozadí na stránce registrace nebo přihlášení Azure AD B2C na základě parametru, který předáte z webové nebo mobilní aplikace.

### <a name="javascript"></a>JavaScript

Kód JavaScriptu na straně klienta můžete povolit jak v [uživatelských tocích,](user-flow-javascript-overview.md) tak ve [vlastních zásadách](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Přizpůsobení uživatelského rozhraní pouze pro přihlášení

Pokud poskytujete pouze přihlášení, spolu s jeho doprovodnou stránku pro resetování hesla a ověřovací e-maily, použijte stejné kroky přizpůsobení, které se používají pro [přihlašovací stránku Azure AD](../active-directory/fundamentals/customize-branding.md).

Pokud se zákazníci pokusí upravit svůj profil před přihlášením, budou přesměrováni na stránku, kterou si přizpůsobíte pomocí stejných kroků, které se používají k přizpůsobení přihlašovací stránky Azure AD.

## <a name="page-layout-templates"></a>Šablony rozložení stránky

Toky uživatelů poskytují několik předdefinovaných šablon, ze kterých si můžete vybrat, abyste svým stránkám uživatelského prostředí poskytli profesionální vzhled. Tyto šablony rozložení mohou také sloužit jako výchozí bod pro vlastní přizpůsobení.

V části **Přizpůsobit** v levé nabídce vyberte **Rozložení stránky** a pak vyberte **Šablona**.

![Rozevírací seznam Výběr šablony na stránce toku uživatelů na webu Azure Portal](media/customize-ui-overview/template-selection.png)

Dále vyberte šablonu ze seznamu. Tady jsou příklady přihlašovacích stránek pro každou šablonu:

| Oceán modrá | Břidlice šedá | Classic |
|:-:|:-:|:-:|
|![Příklad šablony Ocean Blue vykreslené na přihlašovací stránce](media/customize-ui-overview/template-ocean-blue.png)|![Příklad šablony Břidlice šeda vykreslená na přihlašovací stránce registrace](media/customize-ui-overview/template-slate-gray.png)|![Příklad klasické šablony vykreslené na přihlašovací stránce registrace](media/customize-ui-overview/template-classic.png)|

Když zvolíte šablonu, vybrané rozložení se použije na všechny stránky v toku uživatele a identifikátor URI pro každou stránku se zobrazí v poli **Identifikátor URI vlastní stránky.**

## <a name="custom-html-and-css"></a>Vlastní HTML a CSS

Chcete-li navrhnout vlastní rozložení zásad s přizpůsobeným formátem HTML a šablonou služby CSS, můžete tak učinit přepnutím přepínače Použít vlastní obsah stránky pro každý název rozložení, který je ve vaší zásadě k dispozici. Postupujte podle níže uvedených pokynů týkajících se vlastní konfigurace rozložení:

Azure AD B2C spustí kód v prohlížeči zákazníka pomocí přístupu s názvem [Sdílení prostředků mezi zdroji (CORS).](https://www.w3.org/TR/cors/)

Za běhu se obsah načte z adresy URL, kterou zadáte v toku uživatele nebo vlastní chod zásad. Každá stránka v uživatelském prostředí načte svůj obsah z adresy URL, kterou pro danou stránku zadáte. Po načtení obsahu z adresy URL se sloučí s fragmentem HTML vloženým službou Azure AD B2C a stránka se zobrazí zákazníkovi.

Před použitím vlastních souborů HTML a CSS k přizpůsobení hlavního nastavení si přečtěte následující pokyny:

- Azure AD B2C **sloučí** obsah HTML na vaše stránky. Nekopírujte a zkuste změnit výchozí obsah, který poskytuje Azure AD B2C. Nejlepší je vytvořit obsah HTML od začátku a použít výchozí obsah jako referenci.
- **JavaScript** může být součástí vašeho vlastního obsahu jak pro [toky uživatelů,](user-flow-javascript-overview.md) tak pro [vlastní zásady](javascript-samples.md).
- Podporované **verze prohlížeče** jsou:
  - Internet Explorer 11, 10 a Microsoft Edge
  - Omezená podpora pro internet explorer 9 a 8
  - Google Chrome 42.0 a vyšší
  - Mozilla Firefox 38.0 a vyšší
  - Safari pro iOS a macOS, verze 12 a vyšší
- Do kódu HTML nezahrnujte **značky formulářů.** Značky formuláře interferují s operacemi POST generovanými html vloženým službou Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Kam najdu obsah ui?

Při použití vlastních souborů HTML a CSS k přizpůsobení ui můžete hostovat obsah ui na libovolném veřejně dostupném koncovém bodu HTTPS, který podporuje CORS. Například [azure blob úložiště](../storage/blobs/storage-blobs-introduction.md), webové servery, CDNs, AWS S3 nebo systémy pro sdílení souborů.

Důležité je, že hostujete obsah na veřejně dostupném koncovém bodu HTTPS s povoleným CORS. Při zadávání v obsahu je nutné použít absolutní adresu URL.

## <a name="get-started-with-custom-html-and-css"></a>Začínáme s vlastním html a CSS

Pomocí těchto pokynů můžete začít používat vlastní kód HTML a CSS na stránkách uživatelského prostředí.

- Vytvořte dobře formátovaný obsah `<div id="api"></div>` HTML s `<body>`prázdným prvkem umístěným někde v rozhraní . Tento prvek označuje, kde je vložen obsah Azure AD B2C. Následující příklad ukazuje minimální stránku:

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

- CsS slouží ke stylu prvků uživatelského rozhraní, které vloží Azure AD B2C na vaši stránku. Následující příklad ukazuje jednoduchý soubor CSS, který obsahuje také nastavení pro prvky HTML vložené do registrace:

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

- Hostujte svůj obsah na koncovém bodu HTTPS (s povoleným CORS). Při konfiguraci corsu musí být povoleny metody požadavku GET i OPTIONS.
- Vytvořte nebo upravte tok uživatele nebo vlastní zásady pro použití obsahu, který jste vytvořili.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmenty HTML z Azure AD B2C

V následující tabulce jsou uvedeny fragmenty HTML, které `<div id="api"></div>` Azure AD B2C sloučí do prvku umístěného ve vašem obsahu.

| Vložená stránka | Popis HTML |
| ------------- | ------------------- |
| Výběr zprostředkovatele identity | Obsahuje seznam tlačítek pro poskytovatele identit, ze kterých si zákazník může vybrat během registrace nebo přihlášení. Tato tlačítka zahrnují poskytovatele sociální identity, jako je Facebook, Google nebo místní účty (na základě e-mailové adresy nebo uživatelského jména). |
| Registrace místního účtu | Obsahuje formulář pro registraci místního účtu na základě e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, například textové vstupní pole, pole pro zadání hesla, přepínací tlačítko, rozevírací políčka s jedním výběrem a vícenásobná zaškrtávací políčka. |
| Registrace k sociálnímu účtu | Může se zobrazit při registraci pomocí existujícího účtu od poskytovatele sociální identity, jako je Facebook nebo Google. Používá se v případě, že je nutné získat další informace od zákazníka pomocí registračního formuláře. |
| Sjednocená registrace nebo přihlášení | Zpracovává registraci i přihlášení zákazníků, kteří mohou používat poskytovatele sociální identity, jako je Facebook, Google nebo místní účty. |
| Ověřování pomocí služby Multi-Factor Authentication | Zákazníci mohou ověřit svá telefonní čísla (pomocí textu nebo hlasu) během registrace nebo přihlášení. |
| Chyba | Poskytuje informace o chybě zákazníkovi. |

## <a name="company-branding-preview"></a>Firemní značka (náhled)

Stránky toku uživatelů můžete přizpůsobit pomocí loga banneru, obrázku pozadí a barvy pozadí pomocí [značky Azure](../active-directory/fundamentals/customize-branding.md)Active Directory Company .

Chcete-li přizpůsobit stránky toku uživatelů, nejprve nakonfigurujte firemní značky ve službě Azure Active Directory a pak ji povolte v rozloženích stránek vašich uživatelských toků v Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Konfigurace brandingu společnosti

Začněte nastavením loga banneru, obrázku na pozadí a barvy pozadí v rámci **značky společnosti**.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **Správa**vyberte **značku společnosti**.
1. Postupujte podle pokynů v [části Přidání značky na přihlašovací stránku služby Azure Active Directory vaší organizace](../active-directory/fundamentals/customize-branding.md).

Při konfiguraci firemníznačky ve Službě Azure AD B2C mějte na paměti tyto věci:

* Firemní značky v Azure AD B2C je aktuálně omezena na **obrázek na pozadí**, **logo banneru**a **přizpůsobení barev pozadí.** Ostatní vlastnosti v podokně značky společnosti, například v **rozšířeném nastavení**, nejsou *podporovány*.
* Na stránkách toku uživatele se barva pozadí zobrazí před načtením obrázku pozadí. Doporučujeme zvolit barvu pozadí, která přesně odpovídá barvám v obrázku pozadí pro plynulejší načítání.
* Logo banneru se zobrazí v ověřovacích e-mailech odeslaných uživatelům při zahájení toku uživatelů registrace.

### <a name="enable-branding-in-user-flow-pages"></a>Povolení brandingu na stránkách toku uživatelů

Po konfiguraci firemní značky ji povolte v uživatelských tocích.

1. V levé nabídce portálu Azure vyberte **Azure AD B2C**.
1. V části **Zásady**vyberte **Toky uživatelů (zásady).**
1. Vyberte tok uživatele, pro který chcete povolit firemní značky. Firemní značka **není podporována** pro typy toku *uživatelů pro přihlášení v1* a *profil v1.*
1. V části **Přizpůsobit**vyberte **Rozložení stránky**a pak vyberte rozložení, které chcete označit. Vyberte například **Sjednocené přihlášení nebo přihlášení stránky**.
1. Pro **verzi rozložení stránky (preview)** zvolte verzi **1.2.0** nebo vyšší.
1. Vyberte **Uložit**.

Pokud chcete označit všechny stránky v toku uživatele, nastavte verzi rozložení stránky pro každé rozložení stránky v toku uživatele.

![Výběr rozložení stránky ve Službě Azure AD B2C na webu Azure Portal](media/customize-ui-overview/portal-02-page-layout-select.png)

Tento příklad s poznámkami ukazuje vlastní logo banneru a obrázek pozadí na stránce *Registrace a přihlášení* k toku uživatelů, která používá šablonu Ocean Blue:

![Značková přihlašovací/přihlašovací stránka obsluhovaná Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Použití prostředků značky společnosti ve vlastním HTML

Chcete-li používat datové zdroje značky vaší společnosti `<div id="api">` ve vlastním kódu HTML, přidejte mimo značku následující značky:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Zdroj obrázku je nahrazen zdrojem obrázku na pozadí a logem banneru. Jak je popsáno v části [Začínáme s vlastními kódy HTML a CSS,](#get-started-with-custom-html-and-css) pomocí tříd CSS můžete přizpůsobit a umístit datové zdroje na stránce .

## <a name="localize-content"></a>Lokalizovat obsah

Lokalizujete obsah HTML povolením [přizpůsobení jazyka](user-flow-language-customization.md) v tenantovi Azure AD B2C. Povolení této funkce umožňuje Azure AD B2C předat `ui-locales` parametr OpenID Connect do koncového bodu. Server obsahu může tento parametr použít k poskytnutí stránek HTML specifických pro daný jazyk.

Obsah lze stáhnout z různých míst na základě národního prostředí, které se používá. V koncovém bodě s podporou CORS nastavíte strukturu složek pro hostování obsahu pro určité jazyky. Pokud použijete hodnotu zástupných symbolů `{Culture:RFC5646}`, zavoláte na ten správný .

Identifikátor URI vlastní stránky může vypadat například takto:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Stránku můžete načíst ve francouzštině vytažením obsahu z:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Příklady

Několik ukázkových souborů šablon najdete v úložišti [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) na GitHubu.

Ukázkové soubory HTML a CSS v šablonách jsou umístěny v adresáři [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Další kroky

- Pokud používáte **toky uživatelů**, můžete začít přizpůsobovat uživatelské rozhraní pomocí kurzu:

    [Přizpůsobte si uživatelské rozhraní svých aplikací ve službě Azure Active Directory B2C](tutorial-customize-ui.md).
- Pokud používáte **vlastní zásady**, můžete začít přizpůsobovat ui s článkem:

    [Přizpůsobte uživatelské rozhraní aplikace pomocí vlastní chod zásad ve službě Azure Active Directory B2C](custom-policy-ui-customization.md).
