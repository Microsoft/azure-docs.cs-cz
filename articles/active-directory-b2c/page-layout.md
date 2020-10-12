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
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852078"
---
# <a name="page-layout-versions"></a>Verze rozložení stránky

Balíčky rozložení stránky se pravidelně aktualizují, aby zahrnovaly opravy a vylepšení jejich prvků stránky. Následující protokol změn určuje změny zavedené v každé verzi.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Stránka s vlastním kontrolním výrazem (selfasserted)

**2.1.0**

- Lokalizace a opravy usnadnění.

**2.0.0**

- Přidali jsme podporu pro [ovládací prvky zobrazení](display-controls.md) ve vlastních zásadách.

**1.2.0**

- Pole uživatelské jméno, e-mail a heslo nyní pomocí `form` elementu HTML povolí Edge a Internet Explorer (IE) pro správné uložení těchto informací.
- Pro vylepšené uživatelské prostředí bylo přidáno zpoždění ověřování uživatelem (konfigurovatelného) vstupu uživatele.
- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

**1.1.0**

- Odebrání výstrahy zrušení
- Třída CSS pro chybové prvky
- Zobrazit/skrýt vylepšenou logiku chyb
- Bylo odebráno výchozí CSS.

**1.0.0**

- Původní vydaná verze

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Přihlašovací stránka jednotného přihlašování s odkazem na resetování hesla (unifiedssp)

**2.1.0**

- Přidala se podpora pro více odkazů na registraci.
- Byla přidána podpora ověřování vstupu uživatele podle pravidel predikátů definovaných v zásadách.

**1.2.0**

- Pole uživatelské jméno, e-mail a heslo nyní pomocí `form` elementu HTML povolí Edge a Internet Explorer (IE) pro správné uložení těchto informací.
- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

**1.1.0**

- Přidání ovládacího prvku zůstat přihlášeni (políčko zůstat přihlášeni)

**1.0.0**

- Původní vydaná verze

## <a name="mfa-page-multifactor"></a>Stránka MFA (více faktů)

**1.2.1**

- Opravy přístupnosti u výchozích šablon

**1.2.0**

- Opravy usnadnění
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

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
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
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
- Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
  - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
  - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
- Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
- Podpora překladu pro Chrome

**1.0.0**

- Původní vydaná verze

## <a name="next-steps"></a>Další kroky

Podrobnosti o tom, jak přizpůsobit uživatelské rozhraní vašich aplikací ve vlastních zásadách, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady](custom-policy-ui-customization.md).
