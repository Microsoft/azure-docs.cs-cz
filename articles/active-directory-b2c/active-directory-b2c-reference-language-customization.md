---
title: Pomocí vlastního nastavení jazyka – Azure AD B2C | Microsoft Docs
description: Informace o přizpůsobení nastavení jazyka počítače.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 7c72c1d43d9a5fa541c72a8ba7a5ccedeafdaaff
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: Přizpůsobení jazyka pomocí

>[!NOTE]
>Tato funkce je ve verzi public preview.
>

Přizpůsobení jazyka umožňuje vaše zásady pro uložení různých jazycích, aby vyhovovaly potřebám vašeho zákazníka.  Společnost Microsoft poskytuje převody pro 36 jazyků (viz [Další informace](#additional-information)), ale můžete taky zadat vlastní překladů pro žádný jazyk.  I když prostředí je k dispozici pouze pro jeden jazyk, můžete přizpůsobit jakýkoli text na stránkách.  

## <a name="how-does-language-customization-work"></a>Jak funguje jazyk přizpůsobení?
Vlastní nastavení jazyka umožňuje vyberte jazyky, které vám dobře slouží uživatele je k dispozici v.  Jakmile je funkce zapnutá, můžete zadat parametr řetězce dotazu, ui_locales, z vaší aplikace.  Při volání do Azure AD B2C, jsme převede stránku pro národní prostředí, které jste označili.  Tento typ konfigurace vám poskytuje úplnou kontrolu nad jazyky vám dobře slouží uživatele a ignoruje nastavení jazyka prohlížeče zákazníka. Můžete taky nemusí být nutné tuto úroveň kontroly nad jaké jazyky, najdete v části vašich zákazníků.  Pokud nezadáte parametr ui_locales, zkušeností zákazníků se závisí na nastavení svého prohlížeče.  Můžete řídit jazyky, které vám dobře slouží uživatele převádějí na přidáním v podporovaném jazyce.  Pokud prohlížeč zákazníka je nastavena na Zobrazit jazyk, že nechcete, aby pro podporu, zobrazí se místo toho jazyk, který jste vybrali jako výchozí hodnotu v podporované jazykové verze.

1. **Zadaný jazyk uživatelského rozhraní – národní prostředí** -Jakmile povolíte jazyk přizpůsobení, vám dobře slouží uživatele převádějí na jazyce určeném v tomto poli
2. **Požadovaný jazyk prohlížeče** – Pokud nebyly zadány žádné uživatelské rozhraní národní prostředí, překládá do prohlížeče požadovaný jazyk, **Pokud byl součástí podporované jazyky**
3. **Výchozí jazyk zásad** – Pokud prohlížeč neurčuje jazyk nebo Určuje jednu, která není podporována, překládá pro výchozí jazyk zásad

>[!NOTE]
>Pokud používáte vlastní uživatelské atributy, budete muset zadat vlastní překladů. Najdete v části "[přizpůsobit vaší řetězce](#customize-your-strings)' Podrobnosti.
>

## <a name="support-uilocales-requested-languages"></a>Podpora ui_locales požadované jazyky 
Zásady, které byly vytvořeny před vydáním jazyk přizpůsobení obecné dostupnosti bude nutné nejprve povolit tuto funkci.  Zásady vytvořené po bude mít přizpůsobení jazyk, ve výchozím nastavení povolené.  Když zapnete jazyk přizpůsobení na zásadu, nyní je možné určit jazyk cesty uživatele tak, že přidáte parametr ui_locales.
1. [Postupujte podle těchto kroků přejít na stránku funkce B2C na portálu Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Přejděte na zásadu, která chcete povolit pro překlad.
3. Klikněte na tlačítko **jazyk přizpůsobení**.  
4. Klikněte na **povolit jazyk přizpůsobení** nahoře.
5. Přečtěte si dialogové okno a klikněte na tlačítko "Ano".

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Vyberte jazyky, které ve vaší cesty uživatele jsou povolené. 
Povolte sadu jazyky pro vaše uživatele cestu k převodu v, pokud není zadán parametr ui_locales.
1. Zajistěte, aby byl jazyk přizpůsobení z předchozích pokynů zapnout vaše zásady.
2. Z vaší **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, které chcete podporovat.
4. V podokně vlastností přepnutí **povoleno** na Ano.  
5. Klikněte na tlačítko **Uložit** v horní části podokna Vlastnosti.

>[!NOTE]
>Pokud parametr ui_locales není k dispozici, pak stránce převádějí na jazyk prohlížeče zákazníka pouze v případě, že je povoleno
>

## <a name="customize-your-strings"></a>Přizpůsobení vaší řetězce
'Jazyk vlastní nastavení' umožňuje přizpůsobit libovolného řetězce v vám dobře slouží uživatele.
1. Zajistěte, aby byl jazyk přizpůsobení z předchozích pokynů zapnout vaše zásady.
2. Z vaší **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, který chcete přizpůsobit.
4. Vyberte stránku, kterou chcete upravit.
5. Klikněte na tlačítko **stáhnout výchozí** (nebo **stáhnout přepsání** Pokud jste dříve upravili tento jazyk). 

Tyto kroky poskytnout soubor JSON, který vám pomůže začít upravovat vaše řetězce.

### <a name="changing-any-string-on-the-page"></a>Změna libovolného řetězce na stránce
1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
2. Najděte na element, který chcete změnit.  Můžete najít `StringId` řetězce, který hledáte, nebo Hledat `Value` chcete změnit.
3. Aktualizace `Value` atributem, co chcete zobrazit.
4. Pro každý řetězec, kterou chcete změnit, nezapomeňte přepnout `Override` k **True**.
5. Soubor uložte a odešlete své změny (ovládací prvek pro uložení můžete najít na stejném místě jako kam jste stáhli soubor JSON). 

>[!IMPORTANT]
>Pokud potřebujete k přepsání řetězec, nezapomeňte nastavit `Override` hodnotu `true`.  Pokud není hodnota změněna, tato položka je ignorována. 
>

### <a name="changing-extension-attributes"></a>Změna atributů rozšíření
Pokud chcete změnit řetězec pro vlastní uživatelský atribut, nebo chcete přidat do formátu JSON, je v následujícím formátu:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Nahraďte `<ExtensionAttribute>` s názvem vaší atribut vlastní uživatele.  

Nahraďte `<ExtensionAttributeValue>` nové řetězcem, který se má zobrazit.

### <a name="using-localizedcollections"></a>Pomocí LocalizedCollections
Pokud chcete zadat seznam sadu hodnot pro odpovědi, je nutné vytvořit `LocalizedCollections`.  A `LocalizedCollections` je pole `Name` a `Value` páry.  `Name` Je určeno a `Value` je co je vrácený v deklaraci identity.  Chcete-li přidat `LocalizedCollections`, má následující formát:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` je uživatel atribut, který tato `LocalizedCollections` je odpovědí na
* `Name` je hodnota zobrazí uživateli
* `Value` Co je vrácený v deklaraci identity, pokud je vybraná tato možnost je

### <a name="upload-your-changes"></a>Odeslat změny
1. Jakmile dokončíte změny do souboru JSON, přejděte zpět do svého klienta B2C.
2. Z vaší **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, které byste chtěli poskytnout překladů.
4. Vyberte stránku byste chtěli poskytnout překladů.
5. Klikněte na ikonu složky a vyberte soubor JSON k odeslání.
6. To změnit je uložit do vaší zásady automaticky.

## <a name="using-page-ui-customization-with-language-customization"></a>Přizpůsobení uživatelského rozhraní stránky pomocí vlastního nastavení jazyka

Existují dva způsoby k lokalizaci obsah HTML.  Když zapnete ['Jazyk přizpůsobení'](active-directory-b2c-reference-language-customization.md).  Povolení této funkce umožňuje Azure AD B2C předávat parametr Open ID Connect `ui-locales`, na váš koncový bod.  Vaše servery obsahu můžete zajistit vlastní stránky HTML, které jsou specifické pro jazyk použít tento parametr.

Alternativně jsme vyžádání obsahu z různých míst, podle národního prostředí používá.  V povolení CORS koncový bod, můžete nastavit strukturu složek jako hostitele obsahu pro konkrétní jazyky a zavoláme vám ten správný Pokud vložte hodnotu do zástupný znak `{Culture:RFC5646}`.  Například, pokud to je nutné jako identifikátor URI Mé vlastní stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
I můžete načíst stránka Moje v `fr` a když ho je vyžádání obsah html a css, bude načítat z:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Vlastní národní prostředí

Můžete také přidat jazyky, které společnost Microsoft aktuálně neposkytuje překladů pro.  Musíte zajistit překladů pro všechny řetězce v zásadách.

1. Z vaší **upravit zásady** vyberte **jazyk přizpůsobení**.
2. Vyberte **přidat vlastní jazyk** z horní části stránky.
3. V podokně kontextu, které se otevře určete, který jazyk je poskytují překladů pro zadáním kódu platný národního prostředí.
4. Pro jednotlivé stránky můžete stáhnout sadu přepsání pro angličtinu a pracovat na překlady.
5. Jakmile jste hotovi s soubory JSON, můžete je načíst pro jednotlivé stránky.
6. Vyberte **povolit** a zásady můžete nyní zobrazit tento jazyk pro vaše uživatele.
7. Nezapomeňte uložit jazyk Jakmile povolíte ji.

## <a name="additional-information"></a>Další informace

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Popisky přizpůsobení uživatelského rozhraní stránky jsou trvalé jako svou první sadu přepsání, jakmile je povoleno, přizpůsobení jazyk.
Když povolíte 'Jazyk přizpůsobení', vaše předchozích úprav pro popisky pomocí přizpůsobení uživatelského rozhraní stránky zůstávají v souboru JSON pro angličtinu (en).  Chcete-li změnit štítky a jiných řetězců tím, že nahrajete prostředků jazyka v 'jazyk přizpůsobení, můžete pokračovat.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Společnost Microsoft se zavazuje zajistit nejaktuálnější překladů pro vaše použití
Jsme bude nepřetržitě zlepšit překlady a zůstanou v dodržování předpisů pro vás.  Jsme se identifikovat chyby a změny v globálních terminologie a bezproblémově zkontrolujte aktualizace, které budou fungovat v vám dobře slouží uživatele.
### <a name="support-for-right-to-left-languages"></a>Podpora jazyků zprava doleva
Jsme aktuálně nejsou zajištění podpory jazyků zprava doleva, chcete-li tuto funkci prosím hlasovat pro tuto funkci na [zpětnou vazbu Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Sociální překlady zprostředkovatele Identity
Poskytujeme parametru OIDC ui_locales sociálních přihlášení, ale není dodrženo některé poskytovateli sociální identity, včetně Facebook a Google. 
### <a name="browser-behavior"></a>Chování prohlížeče
Chrome a Firefox obě žádosti pro své jazykové sady a pokud je podporovaném jazyce, se zobrazí před výchozí.  Hraniční aktuálně neuvede v požadavku jazyk a přejde se rovnou na výchozí jazyk.

### <a name="what-languages-are-supported"></a>Jaké jazyky jsou podporovány?

| Jazyk              | Kód jazyka |
|-----------------------|---------------|
| Bengálština                | bn            |
| Čeština                 | cs            |
| dánština                | da            |
| Němčina                | de            |
| Řečtina                 | el            |
| Angličtina               | en            |
| Španělština               | es            |
| Finština               | fi            |
| Francouzština                | fr            |
| Gudžarátština              | gu            |
| Hindština                 | hi            |
| Chorvatština              | h            |
| Maďarština             | hu            |
| italština               | it            |
| Japonština              | ja            |
| Kannadština               | kn            |
| Korejština                | ko            |
| Malajálamština             | ml            |
| Maráthština               | mr            |
| Malajština                 | ms            |
| Norská Bokmål      | nb            |
| Holandština                 | nl            |
| Paňdžábština               | pa            |
| polština                | pl            |
| Portugalština – Brazílie   | pt-br         |
| Portugalština – Portugalsko | pt-pt         |
| Rumunština              | ro            |
| ruština               | ru            |
| Slovenština                | sk            |
| švédština               | sv            |
| Tamilština                 | ta            |
| Telugština                | te            |
| Thajština                  | tý            |
| turečtina               | tr            |
| -Čínština, zjednodušená čínština  | zh-hans       |
| Tradiční čínština – | zh-hant       |
