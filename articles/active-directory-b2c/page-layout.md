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
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78183971"
---
# <a name="page-layout-versions"></a>Verze rozložení stránky

Balíčky rozložení stránky se pravidelně aktualizují, aby zahrnovaly opravy a vylepšení jejich prvků stránky. Následující protokol změn určuje změny zavedené v každé verzi.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Stránka s vlastním kontrolním výrazem ( `selfasserted` )
  - Přidali jsme podporu pro [ovládací prvky zobrazení](display-controls.md) ve vlastních zásadách.

## <a name="120"></a>1.2.0

- Všechny stránky
  - Opravy usnadnění
  - Nyní můžete přidat `data-preload="true"` atribut [do značek HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) pro řízení pořadí načítání pro šablony stylů CSS a JavaScript.
    - Načtěte propojené soubory CSS současně s vaší šablonou HTML tak, aby neblikat mezi načítáním souborů.
    - Určuje pořadí, ve kterém `script` jsou značky načteny a provedeny před načtením stránky.
  - Pole e-mail je teď `type=email` a mobilní klávesnice budou poskytovat správné návrhy.
  - Podpora překladu pro Chrome
- Sjednocené a samoobslužné stránky
  - Pole uživatelské jméno, e-mail a heslo nyní pomocí `form` elementu HTML povolí Edge a Internet Explorer (IE) pro správné uložení těchto informací.

## <a name="110"></a>1.1.0

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

## <a name="100"></a>1.0.0

- Původní vydaná verze

## <a name="next-steps"></a>Další kroky

Podrobnosti o tom, jak přizpůsobit uživatelské rozhraní vašich aplikací ve vlastních zásadách, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady](custom-policy-ui-customization.md).
