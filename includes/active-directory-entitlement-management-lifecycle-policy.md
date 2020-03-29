---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694497"
---
## <a name="lifecycle"></a>Životní cyklus

Na kartě **Životní cyklus** určíte, kdy vyprší platnost přiřazení uživatele k přístupovému balíčku. Můžete také určit, zda mohou uživatelé rozšířit svá přiřazení.

1. V části **Vypršení platnosti** nastavte, aby **platnost přiřazení balíčků aplikace Access vypršela** **na Datum**, Počet **dní**nebo **Nikdy**.

    V **části Datum zapnuto**vyberte datum vypršení platnosti v budoucnu.

    V **poli Počet dní**zadejte číslo mezi 0 a 3660 dny.

    Na základě vašeho výběru vyprší platnost přiřazení uživatele k přístupovému balíčku k určitému datu, určitému počtu dní po jejich schválení nebo nikdy.

1. Kliknutím na **Zobrazit upřesňující nastavení vypršení platnosti** zobrazíte další nastavení.

    ![Balíček Access – nastavení vypršení platnosti životního cyklu](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Chcete-li uživateli povolit rozšíření přiřazení, nastavte **možnost Povolit uživatelům rozšíření přístupu na** **aplikaci Ano**.

    Pokud jsou v zásadách povolena rozšíření, uživatel obdrží e-mail 14 dní a také 1 den před vypršením platnosti jejich přiřazení balíčku přístupu, což je výzvou k prodloužení přiřazení. Pokud uživatel odešle žádost o rozšíření přístupu, datum rozšíření musí být na nebo před přiřazení vyprší, jak je definováno v zásadách, která byla použita k udělení přístupu uživateli k balíčku přístupu. Pokud například zásada označuje, že platnost přiřazení vyprší 30.

    Pokud je přístup uživatele rozšířen, nebude moci požádat o přístupový balíček po zadaném datu rozšíření (datum nastavené v časovém pásmu uživatele, který zásadu vytvořil).

1. Chcete-li vyžadovat schválení udělení rozšíření, nastavte **Vyžadovat schválení k udělení rozšíření** pro **ano**.

    Budou použita stejná nastavení schválení, která byla zadána na kartě **Požadavky.**

1. Klepněte na tlačítko **Další** nebo **Aktualizovat**.
