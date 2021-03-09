---
title: Verze rozložení stránky
titleSuffix: Azure AD B2C
description: Historie verzí rozložení stránky pro přizpůsobení uživatelského rozhraní v uživatelských zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b15c63545c71d4513abe9102b4de165e2ab5857a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499845"
---
# <a name="page-layout-versions"></a>Verze rozložení stránky

Balíčky rozložení stránky se pravidelně aktualizují, aby zahrnovaly opravy a vylepšení jejich prvků stránky. Následující protokol změn určuje změny zavedené v každé verzi.

## <a name="self-asserted-page-selfasserted"></a>Stránka s vlastním kontrolním výrazem (selfasserted)

**2.1.2**
- Opravili jsme problém s kódováním lokalizace pro jazyky, jako je španělština a francouzština.

**2.1.1**

- Přidali jsme UXString, aby `heading` `intro` se zobrazila na stránce jako název. Ve výchozím nastavení je to skryté.
- Přidání podpory pro ukládání hesel do iCloud řetězce klíčů
- Přidání podpory pro použití zásad nebo parametru QueryString `pageFlavor` pro výběr rozložení (Classic, oceanBlue nebo slateGray).
- Přidání zřeknutích se omezení na stránce s vlastním kontrolním jménem.
- Fokus je nyní umístěn do prvního upravitelného pole při načtení stránky.
- Fokus je nyní umístěn do prvního pole chyby, když má více polí chybu.
- Po ověření ověřovacího kódu e-mailu se teď fokus umístí na tlačítko změnit.

**2.1.0**

- Lokalizace a opravy usnadnění.

**2.0.0**

- Přidali jsme podporu pro [ovládací prvky zobrazení](display-controls.md) ve vlastních zásadách.

**1.2.0**

- Pole uživatelské jméno, e-mail a heslo nyní pomocí `form` elementu HTML povolí Edge a Internet Explorer (IE) pro správné uložení těchto informací.
- Pro vylepšené uživatelské prostředí bylo přidáno zpoždění ověřování uživatelem (konfigurovatelného) vstupu uživatele.
- Opravy usnadnění
- Opravili jsme problém s přístupností, takže se v programu Narrator čtou chybové zprávy. 
- Po ověření e-mailu se teď fokus umístí do pole heslo.
- Odebráno `autofocus` z ovládacího prvku CheckBox. 
- Přidání podpory pro ovládací prvek zobrazení pro ověřování pomocí telefonního čísla.
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome
- Přidání podpory pro branding společnosti na stránkách toku uživatele.

**1.1.0**

- Odebrání výstrahy zrušení
- Třída CSS pro chybové prvky
- Zobrazit/skrýt vylepšenou logiku chyb
- Bylo odebráno výchozí CSS.

**1.0.0**

- Původní vydaná verze

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Přihlašovací stránka jednotného přihlašování s odkazem na resetování hesla (unifiedssp)

> [!TIP]
> Pokud vaše stránka bude lokalizována pro podporu více národních prostředí nebo jazyků v toku uživatele. Článek s [ID lokalizace](localization-string-ids.md) poskytuje seznam identifikátorů lokalizace, které můžete použít pro vybranou verzi stránky.

**2.1.2**
- Opravili jsme problém s kódováním lokalizace pro jazyky, jako je španělština a francouzština.
- Povoluje se odkaz "Zapomenuté heslo", který se použije jako výměna deklarací identity. Další informace najdete v tématu [Samoobslužné resetování hesla](add-password-reset-policy.md#self-service-password-reset-recommended).

**2.1.1**
- Přidali jsme UXString, aby `heading` `intro` se zobrazila na stránce jako název. Ve výchozím nastavení je to skryté.
- Přidání podpory pro použití zásad nebo parametru QueryString `pageFlavor` pro výběr rozložení (Classic, oceanBlue nebo slateGray).
- Přidání podpory pro ukládání hesel do iCloud řetězce klíčů
- Fokus je nyní umístěn do prvního pole chyby, když má více polí chybu.
- Fokus je nyní umístěn do prvního upravitelného pole při načtení stránky.
- Bylo přidáno nové umístění pro odkaz výběru zprostředkovatele deklarací `bottomUnderFormClaimsProviderSelections` .
- Odebrané UXStringsy, které se už nepoužívají.

**2.1.0**

- Přidala se podpora pro více odkazů na registraci.
- Byla přidána podpora ověřování vstupu uživatele podle pravidel predikátů definovaných v zásadách.

**1.2.0**

- Pole uživatelské jméno, e-mail a heslo nyní pomocí `form` elementu HTML povolí Edge a Internet Explorer (IE) pro správné uložení těchto informací.
- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome
- Přidání podpory pro branding klienta na stránkách toku uživatele.

**1.1.0**

- Přidání ovládacího prvku zůstat přihlášeni (políčko zůstat přihlášeni)

**1.0.0**

- Původní vydaná verze

## <a name="mfa-page-multifactor"></a>Stránka MFA (více faktů)

**1.2.2**
- Opravili jsme problém s automatickým vyplněním ověřovacího kódu při použití iOS.
- Opravili jsme problém s přesměrováním tokenu na předávající stranu z WebView pro Android. 
- Přidali jsme UXString, aby `heading` `intro` se zobrazila na stránce jako název. Ve výchozím nastavení je to skryté.  
- Přidání podpory pro použití zásad nebo parametru QueryString `pageFlavor` pro výběr rozložení (Classic, oceanBlue nebo slateGray).

**1.2.1**

- Opravy přístupnosti u výchozích šablon

**1.2.0**

- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome
- Přidání podpory pro branding klienta na stránkách toku uživatele.

**1.1.0**

- Odebralo se tlačítko Potvrdit kód.
- Vstupní pole pro kód nyní bere pouze vstup až šest (6) znaků.
- Stránka se automaticky pokusí ověřit kód zadaný při zadání čtyřmístného kódu, aniž by bylo nutné kliknout na tlačítko.
- Pokud je kód nesprávný, je vstupní pole automaticky vymazáno.
- Po třech (3) pokusům o zadání nesprávného kódu pošle B2C chybu zpět předávající straně.
- Opravy usnadnění
- Bylo odebráno výchozí CSS.

**1.0.0**

- Původní vydaná verze

## <a name="exception-page-globalexception"></a>Stránka výjimky (globalexception)

**1.2.0**

- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

**1.1.0**

- Oprava přístupnosti
- Pokud není žádný kontakt ze zásad, odebrala se výchozí zpráva.
- Bylo odebráno výchozí CSS.

**1.0.0**

- Původní vydaná verze

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Další stránky (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

**1.0.0**

- Původní vydaná verze

## <a name="next-steps"></a>Další kroky

Podrobnosti o tom, jak přizpůsobit uživatelské rozhraní vašich aplikací ve vlastních zásadách, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady](customize-ui-with-html.md).
