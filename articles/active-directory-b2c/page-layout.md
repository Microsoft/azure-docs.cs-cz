---
title: Verze rozložení stránky
titleSuffix: Azure AD B2C
description: Historie verzí rozložení stránky pro vlastní zásady uživatelského rozhraní.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183971"
---
# <a name="page-layout-versions"></a>Verze rozložení stránky

Balíčky rozložení stránky jsou pravidelně aktualizovány tak, aby obsahovaly opravy a vylepšení v jejich prvcích stránky. Následující protokol změn určuje změny zavedené v každé verzi.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Sebe-tvrdil stránky`selfasserted`( )
  - Byla přidána podpora [pro ovládací prvky zobrazení](display-controls.md) ve vlastních zásadách.

## <a name="120"></a>1.2.0

- Všechny stránky
  - Opravy usnadnění přístupu
  - Nyní můžete přidat `data-preload="true"` atribut [ve značkách HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) a řídit pořadí načítání pro CSS a JavaScript.
    - Načíst propojené CSS soubory ve stejnou dobu jako vaše HTML šablony, takže to není 'blikání' mezi načítání souborů.
    - Před načtením `script` stránky můžete řídit pořadí načítání a spouštění značek.
  - Pole e-mailu je nyní `type=email` a mobilní klávesnice budou poskytovat správné návrhy
  - Podpora překladu prohlížeče Chrome
- Sjednocené a sebestředné stránky
  - Pole uživatelského jména a e-mailu a hesla nyní používají element `form` HTML k tomu, aby edge a internet explorer (IE) mohly tyto informace správně uložit.

## <a name="110"></a>1.1.0

- Stránka výjimky (globální výjimka)
  - Oprava přístupnosti
  - Byla odebrána výchozí zpráva, pokud ze zásady není žádný kontakt.
  - Výchozí CSS odebrán
- Stránka Vícefaktorové (vícefaktorové)
  - Tlačítko Potvrdit kód bylo odstraněno
  - Vstupní pole pro kód nyní trvá pouze zadá až šest (6) znaků
  - Stránka se automaticky pokusí ověřit kód zadaný při zadání šestimístného kódu, aniž by bylo nutné klepnout na tlačítko
  - Pokud je kód chybný, vstupní pole se automaticky vymaže
  - Po třech (3) pokusech s nesprávným kódem odešle B2C chybu zpět předávající straně
  - Opravy usnadnění přístupu
  - Výchozí CSS odebrán
- Sebe-tvrdil stránky (selfasserted)
  - Odstraněná výstraha zrušení
  - Třída CSS pro chybové prvky
  - Zobrazit/skrýt logiku chyb
  - Výchozí CSS odebrán
- Jednotné ssp (unifiedssp)
  - Přidáno keep mě přihlášen (KMSI) kontrolu

## <a name="100"></a>1.0.0

- Původní vydaná verze

## <a name="next-steps"></a>Další kroky

Podrobnosti o přizpůsobení uživatelského rozhraní aplikací ve vlastních zásadách naleznete v [tématu Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad](custom-policy-ui-customization.md).
