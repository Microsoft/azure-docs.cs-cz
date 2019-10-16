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
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389382"
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

    Pokud jsou v zásadách povolené přípony, uživatel dostane e-mail 14 dní a také 1 den před tím, než je přiřazení balíčku přístupu nastavené na vypršení platnosti, aby se toto přiřazení rozšířilo.

    Pokud je uživatel rozšířený přístup, nebude moct po zadaném datu rozšíření požádat o přístup k balíčku (datum nastavené v časovém pásmu uživatele, který zásadu vytvořil).

1. Pokud chcete pro udělení rozšíření vyžadovat schválení, nastavte u **oprávnění vyžadovat schválení udělení rozšíření** na **Ano**.

    Bude použito stejné nastavení schválení, která byla zadána na kartě **žádosti** .

1. Klikněte na tlačítko **Další** nebo **aktualizovat**.
