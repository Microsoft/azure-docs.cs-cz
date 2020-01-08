---
title: Vybrat rozložení stránky – Azure Active Directory B2C
description: Přečtěte si, jak vybrat rozložení stránky v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8bf44893bf23502aaf8c446d9e6d7c9022bfce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425645"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Výběr rozložení stránky v Azure Active Directory B2C pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Můžete povolit JavaScriptový kód na straně klienta v zásadách Azure Active Directory B2C (Azure AD B2C) bez ohledu na to, jestli používáte toky uživatelů nebo vlastní zásady. Pro povolení JavaScriptu pro vaše aplikace musíte přidat element do [vlastních zásad](active-directory-b2c-overview-custom.md), vybrat rozložení stránky a ve svých žádostech použít [b2clogin.com](b2clogin.md) .

Rozložení stránky je přidružení prvků, které Azure AD B2C poskytuje, a obsahu, který zadáte.

Tento článek popisuje, jak vybrat rozložení stránky v Azure AD B2C konfigurací v vlastní zásadě.

> [!NOTE]
> Pokud chcete povolit JavaScript pro toky uživatelů, přečtěte si téma [verze JavaScriptu a rozložení stránky v Azure Active Directory B2C](user-flow-javascript-overview.md).

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

Pokud chcete vybrat rozložení stránky, změňte hodnoty **DataUri** ve vašich [ContentDefinitions](contentdefinitions.md) ve svých zásadách. Přepnutím ze starého **parametr** hodnoty na nové hodnoty, vybíráte nezměnitelný balíček. Výhodou použití tohoto balíčku je, že víte, že se nezmění a nezpůsobí neočekávané chování na stránce.

Chcete-li určit rozložení stránky ve vlastních zásadách, které používají starou hodnotu **DataUri** , vložte `contract` mezi `elements` a typem stránky (například `selfasserted`) a zadejte číslo verze. Příklad:

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

## <a name="version-change-log"></a>Protokol změn verze

Balíčky rozložení stránky se pravidelně aktualizují, aby zahrnovaly opravy a vylepšení jejich prvků stránky. Následující protokol změn určuje změny zavedené v každé verzi.

### <a name="200"></a>2.0.0

- Stránka s vlastním kontrolním výrazem (`selfasserted`)
  - Přidali jsme podporu pro [ovládací prvky zobrazení](display-controls.md) ve vlastních zásadách.

### <a name="120"></a>1.2.0

- Všechny stránky
  - Opravy usnadnění
  - Nyní můžete přidat atribut `data-preload="true"` do značek jazyka HTML pro řízení pořadí načítání pro šablony stylů CSS a JavaScript. Mezi scénáře patří:
    - Použijte tento odkaz na CSS pro načtení šablony stylů CSS ve stejnou dobu jako HTML tak, aby při načítání souborů nebliká.
    - Tento atribut umožňuje řídit pořadí, ve kterém jsou značky skriptu načteny a provedeny před načtením stránky.
  - Pole e-mail je nyní `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
  - Podpora překladu pro Chrome
- Sjednocená a samoobslužná stránka
  - Pole uživatelské jméno, e-mail a heslo nyní používají prvek HTML Form.  Tím se teď umožní, aby Edge a IE tyto informace správně uložily.

### <a name="110"></a>1.1.0

- Stránka výjimky (globalexception)
  - Oprava přístupnosti
  - Pokud není žádný kontakt ze zásad, odebrala se výchozí zpráva.
  - Bylo odebráno výchozí CSS.
- Stránka MFA (více faktů)
  - Odebralo se tlačítko Potvrdit kód.
  - Vstupní pole pro kód nyní bere pouze vstup až šest (6) znaků.
  - Stránka se automaticky pokusí ověřit kód zadaný při zadání čtyřmístného kódu, aniž by bylo nutné kliknout na tlačítko.
  - Pokud je kód nesprávný, je vstupní pole automaticky vymazáno.
  - Po třech (3) pokusům o zadání nesprávného kódu pošle B2C chybu zpět předávající straně.
  - Opravy usnadnění
  - Bylo odebráno výchozí CSS.
- Stránka s vlastním kontrolním výrazem (selfasserted)
  - Odebrání výstrahy zrušení
  - Třída CSS pro chybové prvky
  - Zobrazit/skrýt vylepšenou logiku chyb
  - Bylo odebráno výchozí CSS.
- Sjednocení SSP (unifiedssp)
  - Přidání ovládacího prvku zůstat přihlášeni (políčko zůstat přihlášeni)

### <a name="100"></a>1.0.0

- Původní vydaná verze

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete přizpůsobit uživatelského rozhraní aplikací v [přizpůsobit uživatelské rozhraní vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
