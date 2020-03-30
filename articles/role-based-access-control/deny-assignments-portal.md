---
title: Seznam odepřít přiřazení pro prostředky Azure s portálem Azure
description: Zjistěte, jak pomocí portálu Azure na webu Azure na webu Azure nawebují uživatele, skupiny, instanční objekty a spravované identity, kterým byl odepřen přístup ke konkrétním akcím prostředků Azure v konkrétních oborech.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137427"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Seznam odepřít přiřazení pro prostředky Azure pomocí portálu Azure

[Odepřít přiřazení](deny-assignments.md) blokovat uživatelům provádět konkrétní akce prostředků Azure i v případě, že přiřazení role jim uděluje přístup. Tento článek popisuje, jak vypsat odepřít přiřazení pomocí portálu Azure.

> [!NOTE]
> Nelze přímo vytvořit vlastní přiřazení odepřít. Informace o způsobu vytváření přiřazení odepření naleznete v tématu [Odepřít přiřazení](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o odepření přiřazení, musíte mít:

- `Microsoft.Authorization/denyAssignments/read`oprávnění, které je součástí většiny [předdefinovaných rolí pro prostředky Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Zobrazení seznamu zamítnutých přiřazení

Podle těchto kroků vypsat odepřít přiřazení v oboru předplatného nebo skupiny pro správu.

1. Na webu Azure Portal klikněte na **Všechny služby** a potom na **Skupiny pro správu** nebo **Předplatná**.

1. Klikněte na skupinu pro správu nebo předplatné, které chcete uvést.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Odepřít přiřazení** (nebo klikněte na tlačítko **Zobrazit** na dlaždici Zobrazit odepřít přiřazení).

    Pokud existují všechna odepření přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedeny.

    ![Řízení přístupu – karta Odepřít přiřazení](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Chcete-li zobrazit další sloupce, klepněte na tlačítko **Upravit sloupce**.

    ![Odepřít přiřazení – sloupce](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Název** | Název odepřít přiřazení. |
    | **Typ objektu zabezpečení** | Uživatel, skupina, systémem definovaná skupina nebo instanční objekt služby. |
    | **Denied**  | Název zaregistrovaný objekt zabezpečení, který je součástí přiřazení odepřít. |
    | **ID** | Jedinečný identifikátor pro odepření přiřazení |
    | **Vyloučené objekty zabezpečení** | Zda existují objekty zabezpečení, které jsou vyloučeny z odepřít přiřazení. |
    | **Nevztahuje se na děti** | Určuje, zda je přiřazení zamítnutí zděděno do podoborů. |
    | **Systém chráněn** | Určuje, zda je přiřazení zamítnutí spravováno azure. V současné době vždy Ano. |
    | **Rozsah** | Skupina pro správu, předplatné, skupina prostředků nebo prostředek. |

1. Přidejte zaškrtnutí některé z povolených položek a klepnutím na **tlačítko OK** zobrazte vybrané sloupce.

## <a name="list-details-about-a-deny-assignment"></a>Podrobnosti seznamu o odepření přiřazení

Chcete-li uvést další podrobnosti o odepření přiřazení, postupujte takto.

1. Otevřete podokno **Odepřít přiřazení,** jak je popsáno v předchozí části.

1. Kliknutím na název odepřít přiřazení otevřete okno **Uživatelé.**

    ![Odepřít přiřazení – uživatelé](./media/deny-assignments-portal/deny-assignment-users.png)

    Okno **Uživatelé** obsahuje následující dvě části.

    |  |  |
    | --- | --- |
    | **Odepřít přiřazení se vztahuje na**  | Objekty zabezpečení, na které se vztahuje přiřazení odepřít. |
    | **Odepřít vyloučení přiřazení** | Objekty zabezpečení, které jsou vyloučeny z odepřít přiřazení. |

    **Objekt Zabezpečení definované systémem** představuje všechny uživatele, skupiny, instanční objekty a spravované identity v adresáři Azure AD.

1. Chcete-li zobrazit seznam odepřených oprávnění, klepněte na tlačítko **Odepřená oprávnění**.

    ![Odepřít přiřazení – odepřená oprávnění](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akce | Popis |
    | --- | --- |
    | **Akce**  | Byly odepřeny operace správy. |
    | **NotActions** | Operace správy vyloučené z operace odepření správy. |
    | **Akce data**  | Odmítnuté datové operace. |
    | **NotDataActions** | Operace s daty vyloučené z operace odepřených dat. |

    Příklad uvedený na předchozím snímku obrazovky jsou následující platná oprávnění:

    - Všechny operace úložiště na rovině dat jsou odepřeny s výjimkou výpočetních operací.

1. Chcete-li zobrazit vlastnosti přiřazení zamítnutí, klepněte na tlačítko **Vlastnosti**.

    ![Odepřít přiřazení - Vlastnosti](./media/deny-assignments-portal/deny-assignment-properties.png)

    V okně **Vlastnosti** se zobrazí název, ID, popis a obor, který popírá. **Nevztahuje se na podřízené** přepínače označuje, zda odepřít přiřazení je zděděna podoborů. Systém **chráněný** přepínač označuje, zda toto přiřazení odepřít spravuje Azure. V současné době je to **ano** ve všech případech.

## <a name="next-steps"></a>Další kroky

* [Principy odepření přiřazení pro prostředky Azure](deny-assignments.md)
* [Seznam odepřít přiřazení pro prostředky Azure pomocí Azure PowerShell](deny-assignments-powershell.md)
