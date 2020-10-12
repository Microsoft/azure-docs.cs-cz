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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694497"
---
## <a name="lifecycle"></a>Životní cyklus

Na kartě **životní cyklus** zadáte, kdy vyprší platnost přiřazení uživatele k balíčku přístupu. Můžete také určit, jestli uživatelé můžou roztáhnout jejich přiřazení.

1. V části **vypršení platnosti** nastavte u **přiřazení přístupového balíčku platnost** na **Datum**, **počet dní**nebo **nikdy**.

    V poli pro **Datum**vyberte datum vypršení platnosti v budoucnosti.

    V **části počet dnů**zadejte číslo mezi 0 a 3660 dny.

    Na základě vašeho výběru vyprší přiřazení uživatele k balíčku pro přístup k určitému datu, určitému počtu dní od jejich schválení nebo nikdy.

1. Kliknutím na **Zobrazit upřesňující nastavení vypršení platnosti** zobrazíte další nastavení.

    ![Přístup k nastavení vypršení platnosti životního cyklu balíčku](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Chcete-li uživateli dovolit, aby rozšířili přiřazení, nastavte možnost **dovolit uživatelům, aby rozšířili přístup** k **Ano**.

    Pokud jsou v zásadách povolené přípony, uživatel dostane e-mail 14 dní a také 1 den před tím, než je přiřazení balíčku přístupu nastavené na vypršení platnosti, aby se toto přiřazení rozšířilo. Pokud uživatel odešle požadavek na rozšíření přístupu, musí být datum rozšíření na nebo před vypršením platnosti přiřazení, jak je definováno v zásadách, které byly použity pro udělení přístupu k balíčku přístupu. Pokud například zásada indikuje, že je přiřazení nastavené na vypršení platnosti, 30. června, maximální rozšíření, které může uživatel požadovat, je 30. června.

    Pokud je uživatel rozšířený přístup, nebude moct po zadaném datu rozšíření požádat o přístup k balíčku (datum nastavené v časovém pásmu uživatele, který zásadu vytvořil).

1. Pokud chcete pro udělení rozšíření vyžadovat schválení, nastavte u **oprávnění vyžadovat schválení udělení rozšíření** na **Ano**.

    Bude použito stejné nastavení schválení, která byla zadána na kartě **žádosti** .

1. Klikněte na tlačítko **Další** nebo **aktualizovat**.
