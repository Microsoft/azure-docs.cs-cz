---
title: Přizpůsobení jazyka v Azure Active Directory B2C
description: Přečtěte si, jak přizpůsobit prostředí jazyka v uživatelském toku v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5bfdada12229070568bb9b0556471ea5aab306f
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94575947"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Přizpůsobení jazyka v Azure Active Directory B2C

Přizpůsobení jazyka v Azure Active Directory B2C (Azure AD B2C) umožňuje, aby tok uživatelů vyhovoval různým jazykům, aby vyhovoval vašim potřebám zákazníků. Společnost Microsoft poskytuje překlady pro [jazyky 36](#supported-languages), ale můžete také zadat vlastní překlady pro libovolný jazyk. I v případě, že je vaše prostředí k dispozici pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka

Pomocí vlastního nastavení jazyka můžete vybrat jazyky, ve kterých je tok uživatele k dispozici. Po povolení funkce můžete zadat parametr řetězce dotazu, `ui_locales` z vaší aplikace. Když zavoláte Azure AD B2C, vaše stránka je přeložena do národního prostředí, které jste uvedli. Tento typ konfigurace poskytuje plnou kontrolu nad jazyky v uživatelském toku a ignoruje nastavení jazyka v prohlížeči zákazníka.

Možná nebudete potřebovat tuto úroveň kontroly nad jazyky, které zákazník uvidí. Pokud neposkytnete `ui_locales` parametr, zkušenost zákazníka je vydiktována nastavením prohlížeče. Můžete si i nadále řídit, na které jazyky se má tok uživatele přeložit, a to tak, že ho přidáte jako podporovaný jazyk. Pokud je prohlížeč zákazníka nastavený tak, aby zobrazoval jazyk, který nechcete podporovat, zobrazí se místo toho jazyk, který jste vybrali jako výchozí v podporovaných jazykových verzích.

* **jazykově definované národní prostředí** : po povolení přizpůsobení jazyka se tok uživatele převede do jazyka, který je zde určen.
* **Požadovaný jazyk v prohlížeči** : Pokud není `ui_locales` zadaný žádný parametr, tok uživatele se převede na jazyk požadovaný prohlížečem, *Pokud je podporovaný jazyk*.
* **Výchozí jazyk zásad** : Pokud prohlížeč neurčí jazyk, nebo neurčí, který není podporovaný, tok uživatele se převede do výchozího jazyka toku uživatele.

> [!NOTE]
> Pokud používáte vlastní atributy uživatele, je třeba zadat vlastní překlady. Další informace najdete v tématu [přizpůsobení řetězců](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Podpora požadovaných jazyků pro ui_locales

Zásady, které byly vytvořené před všeobecnou dostupností přizpůsobení jazyka, musí nejdřív tuto funkci povolit. Zásady a uživatelské toky, které se vytvořily po nastavení jazyka, jsou ve výchozím nastavení povolené.

Když v toku uživatele povolíte přizpůsobení jazyka, můžete řídit jazyk toku uživatele přidáním `ui_locales` parametru.

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte možnost **Povolit přizpůsobení jazyka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Vyberte jazyky, ve kterých je tok uživatele povolený.

Povolte sadu jazyků pro váš uživatelský tok, který se bude překládat na v případě, že ho prohlížeč požaduje bez `ui_locales` parametru.

1. Ujistěte se, že tok uživatele má povolené přizpůsobení jazyka z předchozích pokynů.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete podporovat.
1. V podokně Vlastnosti změňte možnost **povoleno** na **Ano**.
1. V horní části podokna Vlastnosti vyberte **Uložit** .

>[!NOTE]
>Pokud `ui_locales` parametr není zadán, bude stránka přeložena do jazyka prohlížeče zákazníka pouze v případě, že je povolena.
>

## <a name="customize-your-strings"></a>Přizpůsobení řetězců

Přizpůsobení jazyka umožňuje přizpůsobení libovolného řetězce v toku uživatele.

1. Ujistěte se, že uživatelský tok má v předchozích pokynech povolené přizpůsobení jazyka.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete přizpůsobit.
1. V části **soubory prostředků na úrovni stránky** vyberte stránku, kterou chcete upravit.
1. Vyberte možnost **Stáhnout výchozí** (nebo **Stáhnout přepsání** , pokud jste tento jazyk už dříve upravovali).

Tyto kroky poskytují soubor JSON, který můžete použít k zahájení úprav řetězců.

### <a name="change-any-string-on-the-page"></a>Změna libovolného řetězce na stránce

1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
1. Vyhledejte prvek, který chcete změnit. Můžete vyhledat `StringId` řetězec, který hledáte, nebo vyhledat `Value` atribut, který chcete změnit.
1. Aktualizujte `Value` atribut tak, co chcete zobrazit.
1. Pro každý řetězec, který chcete změnit, změňte `Override` na `true` .
1. Uložte soubor a nahrajte změny. (Ovládací prvek nahrávání můžete najít na stejném místě, kam jste stáhli soubor JSON.)

> [!IMPORTANT]
> Pokud potřebujete přepsat řetězec, ujistěte se, že jste nastavili `Override` hodnotu na `true` . Pokud se hodnota nezmění, bude položka ignorována.

### <a name="change-extension-attributes"></a>Změnit atributy rozšíření

Pokud chcete změnit řetězec pro vlastní atribut uživatele nebo ho chcete přidat k formátu JSON, je v následujícím formátu:

```json
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

Pokud chcete poskytnout seznam sad hodnot pro odpovědi, je nutné vytvořit `LocalizedCollections` atribut. `LocalizedCollections` je pole `Name` a `Value` páry. Pořadí položek bude zobrazeno v pořadí. Chcete-li přidat `LocalizedCollections` , použijte následující formát:

```json
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

* `ElementId` je atributem uživatele, `LocalizedCollections` na který je tento atribut odpovědí.
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

Obsah HTML můžete lokalizovat dvěma způsoby. Jedním ze způsobů je zapnout [přizpůsobení jazyka](user-flow-language-customization.md). Povolením této funkce umožníte Azure AD B2C přeslat parametr OpenID Connect `ui-locales` do svého koncového bodu. Server obsahu může tento parametr použít k poskytování přizpůsobených stránek HTML, které jsou specifické pro konkrétní jazyk.

Alternativně můžete vyžádat obsah z různých míst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS můžete nastavit strukturu složky pro hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu, zavoláte tu správnou `{Culture:RFC5646}` . Předpokládejme například, že se jedná o vlastní identifikátor URI stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Můžete načíst stránku v `fr` . Když stránka vyžádá obsah HTML a CSS, stáhne se z:

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
7. Vyberte **Povolit** a tok uživatele teď může zobrazit tento jazyk pro vaše uživatele.
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
| Arabština                | snížen            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Bulharština             | BG            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Bengálština                | bn            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Katalánština               | určen            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čeština                 | cs            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Dánština                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Němčina                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Řečtina                 | El            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| angličtina               | en            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| španělština               | es            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Estonština              | et            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Baskičtina                | Balijšitna            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Finština               | WiFi            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Francouzština                | FR            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Galicijština              | GL            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Gudžarátština              | Gu            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Hebrejština                | uvede            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Hindština                 | Ahoj            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Chorvatština              | hod            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Maďarština             | hu            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Indonéština            | id            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Italština               | její            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Japonština              | dža            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Kazaština                | kk            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Kannadština               | KN            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Korejština                | Ko            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Litevština            | lt            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Lotyština               | Lotyšsko            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Malajalámština             | ml            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Maráthština               | Vážený            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Malajština                 | Arial            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Norština – Bokmal      | NB            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Nizozemština                 | belgick            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Norština             | ne            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Paňdžábština               | PA            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Polština                | pl            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Portugalština – Brazílie   | pt-br         | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Portugalština – Portugalsko | pt-pt         | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Rumunština              | loď            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Ruština               | ru            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Slovenština                | SM            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Slovinština             | SSL            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Srbština – cyrilice    | SR-cryl-cs    | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Srbština – latinka       | SR-Latn-cs    | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Švédština               | činí            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Tamilština                 | Ta            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Telugština                | te            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Thajština                  | Kolik            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Turečtina               | recenzent            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Ukrajinština             | Velká Británie            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Vietnamština            | InterDev            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čínština – zjednodušená  | zh – Hans       | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čínština – tradiční | zh – Hant       | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
