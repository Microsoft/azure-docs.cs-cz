---
title: Vlastní nastavení jazyka v Azure Active Directory B2C | Microsoft Docs
description: Informace o přizpůsobení nastavení jazyka počítače.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 72a2bd20d08cd12cc1965bd06090d2cd705fc111
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711932"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Vlastní nastavení jazyka v Azure Active Directory B2C

Vlastní nastavení jazyka v Azure Active Directory B2C (Azure AD B2C) umožňuje vaše zásady pro uložení různých jazycích, aby vyhovovaly potřebám vašeho zákazníka.  Společnost Microsoft poskytuje převody pro [36 jazyků](#supported-languages), ale můžete taky zadat vlastní překladů pro žádný jazyk. I v případě, že je dispozici prostředí pouze jeden jazyk, můžete přizpůsobit jakýkoli text na stránkách.  

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka
Vyberte jazyky, které vám dobře slouží uživatele je k dispozici v použijete vlastní nastavení jazyka. Když je funkce povolená, můžete zadat parametr řetězce dotazu `ui_locales`, z vaší aplikace. Při volání do Azure AD B2C je národní prostředí, které jste označili přeložit stránku. Tento typ konfigurace vám poskytuje úplnou kontrolu nad jazyky vám dobře slouží uživatele a ignoruje nastavení jazyka prohlížeče zákazníka. 

Nemusí potřebovat tuto úroveň kontroly nad jaké jazyky uvidí vašich zákazníků. Pokud nezadáte `ui_locales` parametr zkušeností zákazníků se závisí na nastavení svého prohlížeče.  Můžete řídit jazyky, které vám dobře slouží uživatele převádějí na přidáním v podporovaném jazyce. Pokud prohlížeč zákazníka je nastavena na Zobrazit jazyk, který nechcete, aby pro podporu, zobrazí se místo toho jazyk, který jste vybrali jako výchozí hodnotu v podporované jazykové verze.

- **Zadaný jazyk uživatelského rozhraní – národní prostředí**: Jakmile povolíte jazyk přizpůsobení, vám dobře slouží uživatele převádějí na jazyk, který je zde stanovený.
- **Prohlížeč požadovaný jazyk**: Pokud žádné `ui_locales` byl zadán parametr, vám dobře slouží uživatele je přeložit na jazyk prohlížeče požadovaný *Pokud je podporován jazyk*.
- **Výchozí jazyk zásad**: Pokud v prohlížeči neurčuje jazyk nebo Určuje jednu, která není podporována, je cesty uživatele převedeny na výchozí jazyk zásad.

>[!NOTE]
>Pokud používáte vlastní uživatelské atributy, budete muset zadat vlastní překladů. Další informace najdete v tématu [přizpůsobit vaší řetězce](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Podpora požadované jazyky pro ui_locales 
Zásady, které byly vytvořeny před zveřejněním přizpůsobení jazyk je třeba nejprve povolit tuto funkci. Zásady, které byly vytvořeny po mít přizpůsobení jazyk, ve výchozím nastavení povolené. 

Když povolíte vlastní nastavení jazyka v zásadách, můžete řídit jazyk uživatele cesty přidáním `ui_locales` parametr.
1. [Přejděte na stránku funkce B2C na portálu Azure](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Vyhledejte zásadu, která chcete povolit pro překlad.
3. Vyberte **jazyk přizpůsobení**.  
4. Vyberte **povolit jazyk přizpůsobení**.
5. Přečtěte si informace v dialogovém okně a vyberte **Ano**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Vyberte jazyky, které ve vaší cesty uživatele jsou povolené. 
Povolit sadu jazyky pro vaše uživatele cestu mají být převedeny na vyžádání v prohlížeči bez `ui_locales` parametr.
1. Zajistěte, aby vaše zásady jazyk přizpůsobení z předchozích pokynů zapnout.
2. Z **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, který chcete podporovat.
4. V podokně vlastností změnit **povoleno** k **Ano**.  
5. Vyberte **Uložit** v horní části podokna Vlastnosti.

>[!NOTE]
>Pokud `ui_locales` není zadán parametr, stránky je převedeny na jazyk prohlížeče zákazníka, pouze pokud je povolen.
>

## <a name="customize-your-strings"></a>Přizpůsobení vaší řetězce
Přizpůsobení jazyka umožňuje přizpůsobit libovolného řetězce v vám dobře slouží uživatele.
1. Zajistěte, aby vaše zásady jazyk přizpůsobení z předchozích pokynů zapnout.
2. Z **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, který chcete přizpůsobit.
4. Vyberte stránku, kterou chcete upravit.
5. Vyberte **stáhnout výchozí** (nebo **stáhnout přepsání** Pokud jste dříve upravili tento jazyk). 

Tyto kroky poskytnout soubor JSON, který vám pomůže začít upravovat vaše řetězce.

### <a name="change-any-string-on-the-page"></a>Změnit libovolného řetězce na stránce
1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
2. Najděte element, který chcete změnit.  Můžete najít `StringId` řetězce díváte, nebo vyhledejte `Value` atribut, který chcete změnit.
3. Aktualizace `Value` atributem, co chcete zobrazit.
4. Pro každý řetězec, který chcete změnit, změňte `Override` k `true`.
5. Soubor uložte a odešlete své změny. (Můžete najít ovládací prvek pro uložení na stejném místě jako kam jste stáhli soubor JSON.) 

>[!IMPORTANT]
>Pokud potřebujete k přepsání řetězec, nezapomeňte nastavit `Override` hodnotu `true`.  Pokud není hodnota změněna, tato položka je ignorována. 
>

### <a name="change-extension-attributes"></a>Změna atributů rozšíření
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

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Zadejte seznam hodnot pomocí LocalizedCollections
Pokud chcete zadat seznam sadu hodnot pro odpovědi, je nutné vytvořit `LocalizedCollections` atribut.  `LocalizedCollections` je pole `Name` a `Value` páry. Pořadí položek bude pořadí, ve kterém jsou zobrazeny.  Chcete-li přidat `LocalizedCollections`, použijte následující formát:

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

* `ElementId` je uživatel atribut, který tato `LocalizedCollections` atribut je odpovědí na.
* `Name` je hodnota, která se zobrazí uživateli.
* `Value` je, co je vrácený v deklaraci identity, pokud je vybraná tato možnost.

### <a name="upload-your-changes"></a>Odeslat změny
1. Po dokončení změn do souboru JSON, přejděte zpět na svého klienta B2C.
2. Z **upravit zásady** vyberte **jazyk přizpůsobení**.
3. Vyberte jazyk, který chcete převést na.
4. Vyberte stránku, kde chcete zadat překladů.
5. Vyberte ikonu složky a vyberte soubor JSON k odeslání.
 
Změny se automaticky uloží do vaší zásady.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Přizpůsobení stránce uživatelského rozhraní pomocí vlastního nastavení jazyka

Existují dva způsoby k lokalizaci obsah HTML. Jedním ze způsobů je zapnout [jazyk přizpůsobení](active-directory-b2c-reference-language-customization.md). Povolení této funkce umožňuje Azure AD B2C předávat parametr Open ID Connect `ui-locales`, na váš koncový bod.  Vaše servery obsahu můžete použít tento parametr poskytnout vlastní stránky HTML, které jsou jenom určitých jazyků.

Alternativně můžete načítat obsah z různých místech podle národního prostředí, který se používá. Váš koncový bod s povoleným CORS můžete nastavit strukturu složek jako hostitele obsahu pro konkrétní jazyky. Ten správný budete volat, pokud nechcete použít zástupný znak hodnotu `{Culture:RFC5646}`.  Předpokládejme například, že se jedná o stránku vlastní identifikátor URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Můžete načíst stránku v `fr`. Pokud stránky vrátí kód HTML a CSS obsah, je stahování z:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Přidat vlastní jazyky

Můžete také přidat jazyky, které společnost Microsoft aktuálně neposkytuje překladů pro. Budete muset zadat překladů pro všechny řetězce v zásadách.  Jazyk a národní prostředí kódy jsou omezeny na ty ve standardu ISO 639-1. 

1. Z **upravit zásady** vyberte **jazyk přizpůsobení**.
2. Vyberte **přidat vlastní jazyk** z horní části stránky.
3. V podokně kontextu, které se otevře určete, který jazyk zadáváte překladů pro zadáním kódu platný národního prostředí.
4. Pro jednotlivé stránky můžete stáhnout sadu přepsání pro angličtinu a pracovat na překlady.
5. Jakmile jste hotovi s soubory JSON, můžete je načíst pro jednotlivé stránky.
6. Vyberte **povolit**, a zásady můžete nyní zobrazit tento jazyk pro vaše uživatele.
7. Uložte jazyk.

>[!IMPORTANT]
>Budete muset povolit vlastní jazyky nebo nahrát přepsání pro něj před uložením.
>

## <a name="additional-information"></a>Další informace

### <a name="page-ui-customization-labels-as-overrides"></a>Popisky přizpůsobení uživatelského rozhraní stránky jako přepsání
Když povolíte jazyk přizpůsobení, vaše předchozích úprav pro popisky pomocí přizpůsobení uživatelského rozhraní stránky zůstávají v souboru JSON pro angličtinu (en). Můžete změnit štítky a jiných řetězců tím, že nahrajete jazyk prostředky do vlastního nastavení jazyka.
### <a name="up-to-date-translations"></a>Aktuální převody
Společnost Microsoft se zavazuje poskytuje nejaktuálnější překladů pro vaše použití. Společnost Microsoft průběžně zvyšuje překlady a zajišťuje jejich dodržování předpisů pro vás. Společnost Microsoft bude identifikovat chyby a změny v globálních terminologie a bezproblémově zkontrolujte aktualizace, které budou fungovat v vám dobře slouží uživatele.
### <a name="support-for-right-to-left-languages"></a>Podpora jazyků zprava doleva
Microsoft aktuálně neposkytuje podporu pro jazyky zprava doleva. Toho lze dosáhnout pomocí vlastní národní prostředí a změnit způsob, jakým se zobrazí řetězce pomocí šablon stylů CSS.  Pokud potřebujete tuto funkci, prosím hlasovat pro něj [zpětnou vazbu Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Překlady zprostředkovatele sociální identity
Společnost Microsoft poskytuje `ui_locales` OIDC parametru sociálních přihlášení. Ale někteří poskytovatelé sociálních identity, včetně Facebook a Google, nemusíte respektovat je. 
### <a name="browser-behavior"></a>Chování prohlížeče
Chrome a Firefox, které obě žádosti pro své jazykové sady. Pokud je podporovaném jazyce, se zobrazí před výchozí. Hraniční aktuálně neuvede v požadavku jazyk a přejde se rovnou na výchozí jazyk.

### <a name="supported-languages"></a>Podporované jazyky

| Jazyk              | Kód jazyka |
|-----------------------|---------------|
| Bengálština                | Bn            |
| Čeština                 | cs            |
| dánština                | da            |
| Němčina                | de            |
| Řečtina                 | el            |
| Angličtina               | en            |
| Španělština               | es            |
| Finština               | fi            |
| Francouzština                | fr            |
| Gudžarátština              | gu            |
| Hindština                 | Ahoj            |
| Chorvatština              | h            |
| Maďarština             | hu            |
| italština               | it            |
| Japonština              | ja            |
| Kannadština               | KN            |
| Korejština                | ko            |
| Malajálamština             | ml            |
| Maráthština               | MR            |
| Malajština                 | ms            |
| Norská Bokmål      | nb            |
| Holandština                 | nl            |
| Paňdžábština               | pa            |
| polština                | pl            |
| Portugalština – Brazílie   | pt-br         |
| Portugalština – Portugalsko | pt-pt         |
| Rumunština              | ro            |
| ruština               | ru            |
| Slovenština                | Sk            |
| švédština               | sv            |
| Tamilština                 | tových            |
| Telugština                | te            |
| Thajština                  | .            |
| turečtina               | tr            |
| -Čínština, zjednodušená čínština  | zh-hans       |
| Tradiční čínština – | zh-hant       |
