---
title: Přizpůsobení jazyka ve službě Azure Active Directory B2C
description: Přečtěte si o přizpůsobení jazykového prostředí v tocích uživatelů.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185756"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Přizpůsobení jazyka ve službě Azure Active Directory B2C

Přizpůsobení jazyka ve službě Azure Active Directory B2C (Azure AD B2C) umožňuje vašemu toku uživatelů přizpůsobit různé jazyky potřebám vašich zákazníků. Společnost Microsoft poskytuje překlady pro [36 jazyků](#supported-languages), ale můžete také poskytnout vlastní překlady pro libovolný jazyk. I když je vaše prostředí poskytováno pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka

Pomocí vlastního nastavení jazyka můžete vybrat jazyky, ve kterých je váš tok uživatelů k dispozici. Po povolení funkce můžete zadat parametr řetězce `ui_locales`dotazu , z aplikace. Při volání do Azure AD B2C, vaše stránka se přeloží do národního prostředí, které jste označili. Tento typ konfigurace poskytuje úplnou kontrolu nad jazyky v uživatelském toku a ignoruje nastavení jazyka prohlížeče zákazníka.

Tuto úroveň kontroly nad jazyky, které zákazník vidí, možná nebudete potřebovat. Pokud `ui_locales` parametr nezadáte, bude prostředí zákazníka dáno nastavením prohlížeče. Stále můžete určit, do kterých jazyků bude tok uživatelů přeložen, a toto žalostné jazyky můžete určit. Pokud je prohlížeč zákazníka nastaven tak, aby zobrazoval jazyk, který nechcete podporovat, zobrazí se místo toho jazyk, který jste vybrali jako výchozí v podporovaných jazykových verzích.

* **ui-locales zadaný jazyk**: Po povolení přizpůsobení jazyka je tok uživatelů přeložen do zde určeného jazyka.
* **Jazyk požadovaný prohlížečem** `ui_locales` : Pokud nebyl zadán žádný parametr, bude tok uživatele přeložen do jazyka požadovaného *prohlížečem, pokud je jazyk podporován*.
* **Výchozí jazyk zásad**: Pokud prohlížeč neurčuje jazyk nebo určuje jazyk, který není podporován, je tok uživatele přeložen do výchozího jazyka toku uživatele.

> [!NOTE]
> Pokud používáte vlastní uživatelské atributy, musíte zadat vlastní překlady. Další informace naleznete v [tématu Přizpůsobení řetězců](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Podpora požadovaných jazyků pro ui_locales

Zásady, které byly vytvořeny před obecnou dostupnost přizpůsobení jazyka je třeba nejprve povolit tuto funkci. Zásady a toky uživatelů, které byly vytvořeny po povolení vlastního nastavení jazyka ve výchozím nastavení.

Pokud povolíte přizpůsobení jazyka v toku uživatele, můžete řídit jazyk `ui_locales` toku uživatele přidáním parametru.

1. Ve vašem tenantovi Azure AD B2C vyberte **Toky uživatelů**.
1. Klikněte na tok uživatele, který chcete povolit pro překlady.
1. Vyberte **jazyky**.
1. Vyberte **možnost Povolit vlastní nastavení jazyka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Výběr jazyků v uživatelském toku je povolen

Povolte sadu jazyků pro tok uživatele, do kterých chcete `ui_locales` převést na žádost prohlížeče bez parametru.

1. Ujistěte se, že váš tok uživatele má jazykové přizpůsobení povoleno z předchozích pokynů.
1. Na stránce **Jazyky** pro tok uživatele vyberte jazyk, který chcete podporovat.
1. V podokně vlastností změňte **možnost Povoleno** na **Ano**.
1. V horní části podokna vlastností vyberte **Uložit.**

>[!NOTE]
>Pokud `ui_locales` parametr není k dispozici, stránka je přeložena do jazyka prohlížeče zákazníka pouze v případě, že je povolena.
>

## <a name="customize-your-strings"></a>Přizpůsobení řetězců

Přizpůsobení jazyka umožňuje přizpůsobit libovolný řetězec v toku uživatele.

1. Ujistěte se, že váš tok uživatele má jazykové přizpůsobení povoleno z předchozích pokynů.
1. Na stránce **Jazyky** pro tok uživatele vyberte jazyk, který chcete přizpůsobit.
1. V části **Soubory prostředků na úrovni stránky**vyberte stránku, kterou chcete upravit.
1. Pokud jste tento jazyk dříve upravili, vyberte **možnost Stáhnout výchozí** hodnoty (nebo Stáhnout **přepsání).**

Tyto kroky poskytují soubor JSON, který můžete použít k zahájení úprav řetězců.

### <a name="change-any-string-on-the-page"></a>Změna libovolného řetězce na stránce

1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
1. Najděte prvek, který chcete změnit. Můžete najít `StringId` řetězec, který hledáte, nebo vyhledejte `Value` atribut, který chcete změnit.
1. Aktualizujte `Value` atribut podle toho, co chcete zobrazit.
1. Pro každý řetězec, který chcete `Override` `true`změnit, změňte na .
1. Uložte soubor a nahrajte změny. (Ovládací prvek pro nahrávání najdete na stejném místě, kde jste stáhli soubor JSON.)

> [!IMPORTANT]
> Pokud potřebujete přepsat řetězec, nezapomeňte nastavit hodnotu `Override` `true`na . Pokud se hodnota nezmění, položka bude ignorována.

### <a name="change-extension-attributes"></a>Změna atributů rozšíření

Pokud chcete změnit řetězec pro vlastní atribut uživatele nebo chcete přidat do JSON, je v následujícím formátu:

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

Nahraďte `<ExtensionAttribute>` název vlastního uživatelského atributu.

Nahraďte `<ExtensionAttributeValue>` novým řetězcem, který se zobrazí.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Poskytnout seznam hodnot pomocí LocalizedCollections

Pokud chcete zadat nastavený seznam hodnot pro odpovědi, musíte `LocalizedCollections` vytvořit atribut. `LocalizedCollections`je pole `Name` a `Value` páry. Pořadí pro položky bude pořadí, které jsou zobrazeny. Chcete-li přidat `LocalizedCollections`, použijte následující formát:

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

* `ElementId`je atribut uživatele, `LocalizedCollections` na který je tento atribut odpovědí.
* `Name`je hodnota, která je zobrazena uživateli.
* `Value`je to, co je vráceno v deklaraci, když je vybrána tato možnost.

### <a name="upload-your-changes"></a>Nahrání změn

1. Po dokončení změn v souboru JSON se vraťte do svého klienta B2C.
1. Vyberte **Toky uživatelů** a klikněte na tok uživatele, který chcete povolit pro překlady.
1. Vyberte **jazyky**.
1. Vyberte jazyk, do kterého chcete překládat.
1. Vyberte stránku, na které chcete zadat překlady.
1. Vyberte ikonu složky a vyberte soubor JSON, který chcete nahrát.

Změny se automaticky uloží do toku uživatele.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Přizpůsobení uživatelského rozhraní stránky pomocí vlastního nastavení jazyka

Obsah HTML lze lokalizovat dvěma způsoby. Jedním ze způsobů je zapnout [přizpůsobení jazyka](user-flow-language-customization.md). Povolení této funkce umožňuje Azure AD B2C předat `ui-locales`parametr OpenID Connect , do koncového bodu. Server obsahu může tento parametr použít k poskytnutí přizpůsobených stránek HTML, které jsou specifické pro jazyk.

Případně můžete vyžádat obsah z různých míst na základě použitého národního prostředí. V koncovém bodě s podporou CORS můžete nastavit strukturu složek pro hostování obsahu pro určité jazyky. Pokud použijete hodnotu zástupných symbolů `{Culture:RFC5646}`, zavoláte na ten správný . Předpokládejme například, že se jedná o identifikátor URI vlastní stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Stránku můžete načíst v . `fr` Když stránka vytáhne obsah HTML a CSS, stahuje se z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Přidání vlastních jazyků

Můžete také přidat jazyky, pro které společnost Microsoft aktuálně neposkytuje překlady. Budete muset poskytnout překlady pro všechny řetězce v toku uživatele. Kódy jazyka a národního prostředí jsou omezeny na kódy ve standardu ISO 639-1.

1. Ve vašem tenantovi Azure AD B2C vyberte **Toky uživatelů**.
2. Klikněte na tok uživatele, kam chcete přidat vlastní jazyky, a potom klikněte na **Jazyky**.
3. V horní části stránky vyberte **Přidat vlastní jazyk.**
4. V podokně kontextu, které se otevře, určete, pro který jazyk poskytujete překlady, zadáním platného kódu národního prostředí.
5. Pro každou stránku si můžete stáhnout sadu přepsání pro angličtinu a pracovat na překladech.
6. Po dokončení souborů JSON je můžete nahrát pro každou stránku.
7. Vyberte **Povolit**a tok uživatelů teď může uživatelům zobrazit tento jazyk.
8. Uložte jazyk.

>[!IMPORTANT]
>Před uložením je třeba buď povolit vlastní jazyky, nebo pro ně nahrát přepsání.

## <a name="additional-information"></a>Další informace

### <a name="page-ui-customization-labels-as-overrides"></a>Popisky vlastního nastavení uživatelského rozhraní stránky jako přepsání

Pokud povolíte přizpůsobení jazyka, předchozí úpravy popisků pomocí přizpůsobení uživatelského rozhraní stránky zůstanou zachovány v souboru JSON pro angličtinu (en). Můžete pokračovat ve změně štítků a dalších řetězců nahráním jazykových prostředků v přizpůsobení jazyka.

### <a name="up-to-date-translations"></a>Aktuální překlady

Společnost Microsoft se zavazuje poskytovat nejaktuálnější překlady pro vaše použití. Společnost Microsoft neustále vylepšuje překlady a udržuje je v souladu s vámi. Společnost Microsoft identifikuje chyby a změny v globální terminologii a provede aktualizace, které budou bezproblémově fungovat ve vašem toku uživatelů.

### <a name="support-for-right-to-left-languages"></a>Podpora jazyků se zprava doleva

Společnost Microsoft v současné době neposkytuje podporu pro jazyky se zprava doleva. Toho lze dosáhnout pomocí vlastních národních prostředí a pomocí css změnit způsob, jakým jsou zobrazeny řetězce. Pokud potřebujete tuto funkci, hlasujte pro ni na [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Překlady poskytovatelů sociální identity

Společnost Microsoft `ui_locales` poskytuje parametr OIDC pro přihlášení do sociální ch odsekaných sítí. Někteří poskytovatelé sociální identity, včetně Facebooku a Googlu, je však nectí.

### <a name="browser-behavior"></a>Chování prohlížeče

Chrome a Firefox oba požádat o jejich nastavený jazyk. Pokud se jedná o podporovaný jazyk, zobrazí se před výchozím nastavením. Microsoft Edge aktuálně nepožaduje jazyk a přejde přímo do výchozího jazyka.

## <a name="supported-languages"></a>Podporované jazyky

Azure AD B2C zahrnuje podporu pro následující jazyky. Jazyky toku uživatelů jsou poskytovány Azure AD B2C. Vícefaktorové ověřování (MFA) jazyky oznámení jsou poskytovány [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Jazyk              | Kód jazyka | Toky uživatele         | Oznámení vícefaktorové a finanční hod.  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabština                | ar            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Bulharština             | Bg            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Bangla                | Bn            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Katalánština               | Ca            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čeština                 | Cs            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Dánština                | Da            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Němčina                | De            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Řečtina                 | El            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Angličtina               | en            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Španělština               | Ano            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Estonština              | Et            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Baskičtina                | Eu            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Finština               | ﬁ            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Francouzština                | Fr            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Galicijština              | Gl            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Gudžarátština              | Gu            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Hebrejština                | on            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Hindština                 | Ahoj            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Chorvatština              | hr            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Maďarština             | Hu            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Indonéština            | id            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Italština               | je to            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Japonština              | ja            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Kazaština                | Kk            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Kannadština               | kn            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Korejština                | Ko            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Litevština            | lt            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Lotyština               | Lv            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Malajalámština             | ml            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Maráthština               | Pan            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Malajština                 | Paní            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Norský Bokmal      | Nb            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Nizozemština                 | Nl            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Norština             | ne            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Paňdžábština               | Pa            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Polština                | Pl            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Portugalština – Brazílie   | pt-br         | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Portugalština – Portugalsko | pt-pt         | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Rumunština              | Ro            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Ruština               | Ru            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Slovenština                | Sk            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Slovinština             | Sl            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Srbština - cyrilice    | sr-cryl-cs    | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Srbština - latina       | sr-latn-cs    | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Švédština               | sv            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Tamilština                 | ta            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Telugština                | Te            | ![ano](./media/user-flow-language-customization/yes.png) | ![ne](./media/user-flow-language-customization/no.png) |
| Thajština                  | Th            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Turečtina               | Tr            | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Ukrajinština             | Uk            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Vietnamština            | Vi            | ![ne](./media/user-flow-language-customization/no.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čínština – zjednodušená  | zh-hans       | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
| Čínština - tradiční | zh-hant       | ![ano](./media/user-flow-language-customization/yes.png) | ![ano](./media/user-flow-language-customization/yes.png) |
