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
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052811"
---
## <a name="lifecycle"></a>Životní cyklus

Na kartě **životní cyklus** zadáte, kdy vyprší platnost přiřazení uživatele k balíčku přístupu. Můžete také určit, jestli uživatelé můžou roztáhnout jejich přiřazení.

1. V části **vypršení platnosti** nastavte u **přiřazení přístupového balíčku platnost** na **Datum**, **počet dní** nebo **nikdy**.

    V poli pro **Datum** vyberte datum vypršení platnosti v budoucnosti.

    V **části počet dnů** zadejte číslo mezi 0 a 3660 dny.

    Na základě vašeho výběru vyprší přiřazení uživatele k balíčku pro přístup k určitému datu, určitému počtu dní od jejich schválení nebo nikdy.

1. Kliknutím na **Zobrazit upřesňující nastavení vypršení platnosti** zobrazíte další nastavení.

    ![Přístup k nastavení vypršení platnosti životního cyklu balíčku](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Chcete-li uživateli dovolit, aby rozšířili přiřazení, nastavte možnost **dovolit uživatelům, aby rozšířili přístup** k **Ano**.

    Pokud jsou v zásadách povolené přípony, uživatel dostane e-mail 14 dnů a také jeden den před tím, než je přiřazení balíčku přístupu nastaveno na vypršení platnosti, aby se zobrazila výzva k rozšíření přiřazení. V době, kdy si vyžádají rozšíření, musí být uživatel stále v oboru zásady. Pokud má zásada k dispozici explicitní koncové datum pro přiřazení a uživatel odešle požadavek na rozšíření přístupu, musí být datum rozšíření v požadavku nebo před vypršením platnosti přiřazení, jak je definováno v zásadách, které byly použity pro udělení přístupu uživateli k balíčku přístupu. Pokud například zásada indikuje, že je přiřazení nastavené na vypršení platnosti, 30. června, maximální rozšíření, které může uživatel požadovat, je 30. června.

    Pokud je uživatel rozšířený přístup, nebude moct po zadaném datu rozšíření požádat o přístup k balíčku (datum nastavené v časovém pásmu uživatele, který zásadu vytvořil).

1. Pokud chcete pro udělení rozšíření vyžadovat schválení, nastavte u **oprávnění vyžadovat schválení udělení rozšíření** na **Ano**.

    Bude použito stejné nastavení schválení, která byla zadána na kartě **žádosti** .

1. Klikněte na tlačítko **Další** nebo **aktualizovat**.
