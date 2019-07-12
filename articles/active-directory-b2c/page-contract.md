---
title: Vyberte stránku smlouvy – Azure Active Directory B2C
description: Další informace o tom, jak vybrat stránku smlouvy v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c3ee05096b0bfd071ea569105973097ce9727b07
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604525"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Vyberte stránku smlouvu v Azure Active Directory B2C pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Kód jazyka JavaScript na straně klienta můžete povolit v zásadách pro Azure Active Directory (Azure AD) B2C, jestli používáte toky uživatelů nebo vlastními zásadami. Pokud chcete povolit JavaScript pro aplikace, je nutné přidat element, který má vaše [vlastní zásady](active-directory-b2c-overview-custom.md), vyberte smlouvy stránky a použijte [b2clogin.com](b2clogin.md) v požadavcích.

Kontrakt stránky je sdružení prvky, které poskytuje Azure AD B2C a obsah, který zadáte.

Tento článek popisuje, jak vybrat stránku smlouvy v Azure AD B2C nakonfigurováním vlastních zásad.

> [!NOTE]
> Pokud chcete povolit JavaScript pro toky uživatelů, přečtěte si téma [jazyka JavaScript a stránku smlouvy verze v Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Nahraďte parametr hodnoty

Ve vaší vlastní zásady, můžete mít [ContentDefinitions](contentdefinitions.md) , které definují HTML šablony použité v cestě uživatele. **ContentDefinition** obsahuje **parametr** , který odkazuje na prvky stránky poskytuje Azure AD B2C. **LoadUri** je relativní cesta k obsahu HTML a CSS, který zadáte.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Pokud chcete vybrat stránku smlouvy, můžete změnit **parametr** hodnoty v vaše [ContentDefinitions](contentdefinitions.md) v zásady. Přepnutím ze starého **parametr** hodnoty na nové hodnoty, vybíráte nezměnitelný balíček. Výhodou používání tohoto balíčku je, že budete vědět, nelze změnit a způsobit neočekávané chování na stránce.

Nastavení stránky smlouvy, použijte následující tabulku k vyhledání **parametr** hodnoty.

| Původní hodnota parametr | Nová hodnota pro parametr |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Verze protokolu změn

Stránka smlouvy balíčky jsou pravidelně aktualizovány na zahrnovat opravy a vylepšení v jejich prvky stránky. Následující protokol změn určuje změny zavedené v každé verzi.

### <a name="110"></a>1.1.0

- Stránce výjimek (globalexception)
  - Oprava usnadnění přístupu
  - Odebrat výchozí zprávu, pokud neexistuje žádný kontakt ze zásad
  - Výchozí šablony stylů CSS odebrat
- Stránka MFA (vícefaktorové)
  - "Potvrdit kód" tlačítko Odebrat
  - Vstupní pole pro přijímá jenom kód zadávat znaky až šest (6)
  - Na stránce se automaticky pokusí ověřit kód zadá, když je zadán 6místným číselným kódem, bez jakékoli tlačítko museli být kliknutí
  - Pokud kód je chybný, že pak je vstupní pole automaticky vymazány
  - Po tři (3) pokusů o nesprávné kódem B2C odešle zpět k předávající straně chybu
  - Oprav týkajících se přístupnosti
  - Výchozí šablony stylů CSS odebrat
- Stránka s vlastním potvrzením (selfasserted)
  - Odebrané zrušit upozornění
  - Třídu šablony stylů CSS pro elementy chyba
  - Zobrazit/Skrýt chyby logiky vylepšené
  - Výchozí šablony stylů CSS odebrat
- Sjednocené SSP (unifiedssp)
  - Přidání zachovat zůstat přihlášeni ovládacího prvku (políčko zůstat Přihlášeni)

### <a name="100"></a>1.0.0

- Původní vydaná verze

## <a name="next-steps"></a>Další postup

Další informace o tom, jak můžete přizpůsobit uživatelského rozhraní aplikací v [přizpůsobit uživatelské rozhraní vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
