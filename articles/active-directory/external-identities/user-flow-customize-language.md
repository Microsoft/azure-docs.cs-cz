---
title: Přizpůsobení jazyka v tocích uživatelů Azure AD
description: Přečtěte si, jak přizpůsobit prostředí jazyka v uživatelském toku v Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: a199c207e8ea35f1471df9bfd0c4134551b9995f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653486"
---
# <a name="language-customization-in-azure-active-directory"></a>Přizpůsobení jazyka v Azure Active Directory

Přizpůsobení jazyka v Azure Active Directory (Azure AD) umožňuje, aby tok uživatelů vyhovoval různým jazykům, aby vyhovoval potřebám vašich uživatelů. Společnost Microsoft poskytuje překlady pro [jazyky 36](#supported-languages). I v případě, že je vaše prostředí k dispozici pouze pro jeden jazyk, můžete přizpůsobit názvy atributů na stránce kolekce atributů.

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka

Ve výchozím nastavení je přizpůsobení jazyka povoleno pro uživatele, kteří se registrují, aby zajistili jednotné prostředí pro registraci. Jazyky lze použít k úpravě řetězců zobrazených uživatelům jako součást procesu shromažďování atributů během registrace.

> [!NOTE]
> Pokud používáte vlastní atributy uživatele, je třeba zadat vlastní překlady. Další informace najdete v tématu [přizpůsobení řetězců](#customize-your-strings).

## <a name="customize-your-strings"></a>Přizpůsobení řetězců

Přizpůsobení jazyka umožňuje přizpůsobení libovolného řetězce v toku uživatele.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure** vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **toky uživatelů**.
3. Vyberte tok uživatele, který chcete povolit pro překlady.
4. Vyberte **jazyky**.
5. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete přizpůsobit.
6. Rozbalte **stránku kolekce atributů**.
7. Vyberte možnost **Stáhnout výchozí** (nebo **Stáhnout přepsání** , pokud jste tento jazyk už dříve upravovali).

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

Pokud chcete poskytnout seznam sad hodnot pro odpovědi, je nutné vytvořit `LocalizedCollections` atribut. `LocalizedCollections` je pole `Name` a `Value` páry. Pořadí položek bude zobrazeno v pořadí. Chcete-li přidat `LocalizedCollections` , použijte následující formát:

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

* `ElementId` je atributem uživatele, `LocalizedCollections` na který je tento atribut odpovědí.
* `Name` je hodnota, která se zobrazí uživateli.
* `Value` je to, co se v deklaraci identity vrátí, když je tato možnost vybraná.

### <a name="upload-your-changes"></a>Nahrání změn

1. Po dokončení změn v souboru JSON se vraťte do svého tenanta.
1. Vyberte **toky uživatelů** a klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte jazyk, na který chcete překládat.
1. Vyberte **stránku kolekce atributů**.
1. Vyberte ikonu složky a vyberte soubor JSON, který se má nahrát.

Změny se uloží do toku uživatele automaticky.

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

Azure AD zahrnuje podporu pro následující jazyky. Jazyky toku uživatelů poskytuje Azure AD. Jazyky oznámení služby Multi-Factor Authentication (MFA) poskytuje [Azure AD MFA](../authentication/concept-mfa-howitworks.md).

| Jazyk              | Kód jazyka | Toky uživatele         | Oznámení MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabština                | snížen            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Bulharština             | BG            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Bengálština                | bn            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Katalánština               | určen            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Čeština                 | cs            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Dánština                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Němčina                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Řečtina                 | El            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| angličtina               | en            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| španělština               | es            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Estonština              | et            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Baskičtina                | Balijšitna            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Finština               | WiFi            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Francouzština                | FR            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Galicijština              | GL            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Gudžarátština              | Gu            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Hebrejština                | uvede            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Hindština                 | Ahoj            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Chorvatština              | hod            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Maďarština             | hu            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Indonéština            | id            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Italština               | její            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Japonština              | dža            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Kazaština                | kk            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Kannadština               | KN            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Korejština                | Ko            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Litevština            | lt            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Lotyština               | Lotyšsko            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Malajalámština             | ml            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Maráthština               | Vážený            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Malajština                 | Arial            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Norština – Bokmal      | NB            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Nizozemština                 | belgick            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Norština             | ne            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Paňdžábština               | PA            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Polština                | pl            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Portugalština – Brazílie   | pt-br         | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Portugalština – Portugalsko | pt-pt         | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Rumunština              | loď            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Ruština               | ru            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Slovenština                | SM            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Slovinština             | SSL            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Srbština – cyrilice    | SR-cryl-cs    | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Srbština – latinka       | SR-Latn-cs    | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Švédština               | činí            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Tamilština                 | Ta            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Telugština                | te            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![X značí ne.](./media/user-flow-customize-language/no.png) |
| Thajština                  | Kolik            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Turečtina               | recenzent            | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Ukrajinština             | Velká Británie            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Vietnamština            | InterDev            | ![X značí ne.](./media/user-flow-customize-language/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Čínština – zjednodušená  | zh – Hans       | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |
| Čínština – tradiční | zh – Hant       | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-customize-language/yes.png) |