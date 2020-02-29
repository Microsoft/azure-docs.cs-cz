---
title: Přizpůsobení jazyka v Azure Active Directory B2C
description: Přečtěte si o přizpůsobení prostředí v uživatelských tocích.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185756"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Přizpůsobení jazyka v Azure Active Directory B2C

Přizpůsobení jazyka v Azure Active Directory B2C (Azure AD B2C) umožňuje, aby tok uživatelů vyhovoval různým jazykům, aby vyhovoval vašim potřebám zákazníků. Společnost Microsoft poskytuje překlady pro [jazyky 36](#supported-languages), ale můžete také zadat vlastní překlady pro libovolný jazyk. I v případě, že je vaše prostředí k dispozici pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka

Pomocí vlastního nastavení jazyka můžete vybrat jazyky, ve kterých je tok uživatele k dispozici. Po povolení funkce můžete do aplikace zadat parametr řetězce dotazu `ui_locales`. Když zavoláte Azure AD B2C, vaše stránka je přeložena do národního prostředí, které jste uvedli. Tento typ konfigurace poskytuje plnou kontrolu nad jazyky v uživatelském toku a ignoruje nastavení jazyka v prohlížeči zákazníka.

Možná nebudete potřebovat tuto úroveň kontroly nad jazyky, které zákazník uvidí. Pokud neposkytnete parametr `ui_locales`, zkušenost zákazníka je vydiktována nastavením prohlížeče. Můžete si i nadále řídit, na které jazyky se má tok uživatele přeložit, a to tak, že ho přidáte jako podporovaný jazyk. Pokud je prohlížeč zákazníka nastavený tak, aby zobrazoval jazyk, který nechcete podporovat, zobrazí se místo toho jazyk, který jste vybrali jako výchozí v podporovaných jazykových verzích.

* **jazykově definované národní prostředí**: po povolení přizpůsobení jazyka se tok uživatele převede do jazyka, který je zde určen.
* **Požadovaný jazyk v prohlížeči**: Pokud není zadaný žádný parametr `ui_locales`, váš uživatelský tok se převede na jazyk požadovaný prohlížečem, *Pokud je podporovaný jazyk*.
* **Výchozí jazyk zásad**: Pokud prohlížeč neurčí jazyk, nebo neurčí, který není podporovaný, tok uživatele se převede do výchozího jazyka toku uživatele.

> [!NOTE]
> Pokud používáte vlastní atributy uživatele, je třeba zadat vlastní překlady. Další informace najdete v tématu [přizpůsobení řetězců](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Podpora požadovaných jazyků pro ui_locales

Zásady, které byly vytvořené před všeobecnou dostupností přizpůsobení jazyka, musí nejdřív tuto funkci povolit. Zásady a uživatelské toky, které se vytvořily po nastavení jazyka, jsou ve výchozím nastavení povolené.

Když v toku uživatele povolíte přizpůsobení jazyka, můžete řídit jazyk toku uživatele přidáním parametru `ui_locales`.

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte možnost **Povolit přizpůsobení jazyka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Vyberte jazyky, ve kterých je tok uživatele povolený.

Povolte sadu jazyků pro tok uživatele, který se má přeložit do aplikace v případě, že ji prohlížeč požaduje bez parametru `ui_locales`.

1. Ujistěte se, že tok uživatele má povolené přizpůsobení jazyka z předchozích pokynů.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete podporovat.
1. V podokně Vlastnosti změňte možnost **povoleno** na **Ano**.
1. V horní části podokna Vlastnosti vyberte **Uložit** .

>[!NOTE]
>Pokud není zadán parametr `ui_locales`, stránka bude přeložena do jazyka prohlížeče zákazníka pouze v případě, že je povolena.
>

## <a name="customize-your-strings"></a>Přizpůsobení řetězců

Přizpůsobení jazyka umožňuje přizpůsobení libovolného řetězce v toku uživatele.

1. Ujistěte se, že uživatelský tok má v předchozích pokynech povolené přizpůsobení jazyka.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete přizpůsobit.
1. V části **soubory prostředků na úrovni stránky**vyberte stránku, kterou chcete upravit.
1. Vyberte možnost **Stáhnout výchozí** (nebo **Stáhnout přepsání** , pokud jste tento jazyk už dříve upravovali).

Tyto kroky poskytují soubor JSON, který můžete použít k zahájení úprav řetězců.

### <a name="change-any-string-on-the-page"></a>Změna libovolného řetězce na stránce

1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
1. Vyhledejte prvek, který chcete změnit. Můžete najít `StringId` pro řetězec, který hledáte, nebo vyhledat atribut `Value`, který chcete změnit.
1. Aktualizujte atribut `Value` s tím, co chcete zobrazit.
1. U každého řetězce, který chcete změnit, změňte `Override` na `true`.
1. Uložte soubor a nahrajte změny. (Ovládací prvek nahrávání můžete najít na stejném místě, kam jste stáhli soubor JSON.)

> [!IMPORTANT]
> Pokud potřebujete přepsat řetězec, ujistěte se, že jste nastavili hodnotu `Override` na `true`. Pokud se hodnota nezmění, bude položka ignorována.

### <a name="change-extension-attributes"></a>Změnit atributy rozšíření

Pokud chcete změnit řetězec pro vlastní atribut uživatele nebo ho chcete přidat k formátu JSON, je v následujícím formátu:

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

Nahraďte `<ExtensionAttribute>` názvem vlastního atributu uživatele.

Nahraďte `<ExtensionAttributeValue>` novým řetězcem, který se má zobrazit.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Zadání seznamu hodnot pomocí LocalizedCollections

Pokud chcete poskytnout seznam sad hodnot pro odpovědi, je nutné vytvořit atribut `LocalizedCollections`. `LocalizedCollections` je pole párů `Name` a `Value`. Pořadí položek bude zobrazeno v pořadí. Chcete-li přidat `LocalizedCollections`, použijte následující formát:

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

* `ElementId` je atribut uživatele, na který je tento atribut `LocalizedCollections` odpovědí.
* `Name` je hodnota, která se zobrazí uživateli.
* `Value` je to, co se v deklaraci identity vrátí, když je tato možnost vybraná.

### <a name="upload-your-changes"></a>Nahrání změn

1. Po dokončení změn souboru JSON se vraťte k B2C tenantovi.
1. Vyberte **toky uživatelů** a klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte jazyk, na který chcete překládat.
1. Vyberte stránku, kde chcete zadat překlady.
1. Vyberte ikonu složky a vyberte soubor JSON, který se má nahrát.

Změny se uloží do toku uživatele automaticky.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Přizpůsobení uživatelského rozhraní stránky pomocí přizpůsobení jazyka

Obsah HTML můžete lokalizovat dvěma způsoby. Jedním ze způsobů je zapnout [přizpůsobení jazyka](user-flow-language-customization.md). Povolením této funkce umožníte Azure AD B2C přeposlání parametru OpenID Connect `ui-locales`na koncový bod. Server obsahu může tento parametr použít k poskytování přizpůsobených stránek HTML, které jsou specifické pro konkrétní jazyk.

Alternativně můžete vyžádat obsah z různých míst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS můžete nastavit strukturu složky pro hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu `{Culture:RFC5646}`, zavoláte tu správnou. Předpokládejme například, že se jedná o vlastní identifikátor URI stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Stránku můžete načíst do `fr`. Když stránka vyžádá obsah HTML a CSS, stáhne se z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Přidat vlastní jazyky

Můžete také přidat jazyky, které Microsoft v současné době neposkytuje překlady pro. Budete muset zadat překlady pro všechny řetězce v toku uživatele. Kódy jazyků a kódů národního prostředí jsou omezené na ty, které jsou ve standardu ISO 639-1.

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
2. Klikněte na tok uživatele, do kterého chcete přidat vlastní jazyky, a pak klikněte na **jazyky**.
3. V horní části stránky vyberte **Přidat vlastní jazyk** .
4. V kontextovém podokně, které se otevře, identifikujte jazyk, pro který chcete překlady, zadáním platného kódu národního prostředí.
5. Pro každou stránku můžete stáhnout sadu přepsání pro angličtinu a pracovat s překlady.
6. Až se soubory JSON dokončí, můžete je nahrát na každou stránku.
7. Vyberte **Povolit**a tok uživatele teď může zobrazit tento jazyk pro vaše uživatele.
8. Uložte jazyk.

>[!IMPORTANT]
>Před uložením je třeba povolit vlastní jazyky nebo pro ně nahrajte přepsání.

## <a name="additional-information"></a>Další informace

### <a name="page-ui-customization-labels-as-overrides"></a>Popisky přizpůsobení uživatelského rozhraní stránky jako přepsání

Když povolíte přizpůsobení jazyka, zůstanou předchozí úpravy popisků pomocí přizpůsobení uživatelského rozhraní stránky v souboru JSON pro angličtinu (EN). Můžete pokračovat ve změnách popisků a dalších řetězců tím, že nahrajete jazykové prostředky v přizpůsobení jazyka.

### <a name="up-to-date-translations"></a>Aktuální překlady

Společnost Microsoft se zavazuje, že poskytuje nejaktuálnější překlady pro vaše používání. Microsoft průběžně vylepšuje překlady a udržuje je v souladu s nimi. Microsoft bude identifikovat chyby a změny v globální terminologii a dělat aktualizace, které budou v toku uživatelů fungovat bez problémů.

### <a name="support-for-right-to-left-languages"></a>Podpora jazyků se zápisem zprava doleva

Microsoft v současnosti neposkytuje podporu pro jazyky se zápisem zprava doleva. To lze provést pomocí vlastních národních prostředí a pomocí šablon stylů CSS změnit způsob zobrazení řetězců. Pokud tuto funkci potřebujete, Hlasujte pro ni prosím na základě [názoru na Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Překlady zprostředkovatele sociálních identit

Microsoft poskytuje `ui_locales` parametr OIDC pro přihlášení přes sociální sítě. Někteří poskytovatelé sociálních identit, včetně Facebooku a Google, je nedodržují.

### <a name="browser-behavior"></a>Chování prohlížeče

Chrome a Firefox obě požadavky na svůj jazyk sady. Pokud se jedná o podporovaný jazyk, zobrazí se před výchozím nastavením. Microsoft Edge aktuálně nepožaduje jazyk a přímo do výchozího jazyka.

## <a name="supported-languages"></a>Podporované jazyky

Azure AD B2C zahrnuje podporu pro následující jazyky. Jazyky toku uživatelů poskytuje Azure AD B2C. Jazyky oznámení služby Multi-Factor Authentication (MFA) poskytuje [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Jazyk              | Kód jazyka | Toky uživatele         | Oznámení MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| arabština                | snížen            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Bulharština             | BG            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Bengálština                | bn            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Katalánština               | určen            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čeština                 | cs            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| dánština                | da            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Němčina                | &            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Řečtina                 | el            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Angličtina               | en            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Španělština               | jednomu            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Estonština              | et            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Baskičtina                | Balijšitna            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Finština               | fi            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Francouzština                | fr            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Galicijština              | GL            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Gudžarátština              | Gu            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Hebrejština                | uvede            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Hindština                 | Ahoj            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Chorvatština              | oddělení            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Maďarština             | hu            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Indonéština            | id            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Italština               | její            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Japonština              | dža            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Kazaština                | kk            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Kannada               | KN            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Korejština                | Ko            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Litevština            | lt            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Lotyština               | lv            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Malajálamština             | ml            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Maráthština               | Vážený            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Malajština                 | ms            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Norština – Bokmal      | NB            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Holandština                 | nl            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| norština             | ne            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Paňdžábština               | pa            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Polština                | pl            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Portugalština – Brazílie   | pt – br         | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Portugalština (Portugalsko) | pt-PT         | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Rumunština              | loď            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Ruština               | ru            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Slovenština                | SM            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Slovinština             | sl            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Srbština – cyrilice    | SR-cryl-cs    | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Srbština – latinka       | SR-Latn-cs    | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| švédština               | sv            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Tamilština                 | ta            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Telugština                | te            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Thajština                  | Kolik            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Turečtina               | recenzent            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Ukrajinština             | Velká Británie            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Vietnamština            | vi            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čínština (zjednodušená)  | zh – Hans       | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čínština (tradiční) | zh – Hant       | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
