---
title: Vypsat přiřazení odmítnutí Azure pomocí Azure Portal – Azure RBAC
description: Naučte se, jak zobrazit seznam uživatelů, skupin, instančních objektů a spravovaných identit, kterým byl odepřen přístup ke konkrétním akcím prostředků Azure v konkrétních oborech pomocí Azure Portal a řízení přístupu na základě role Azure (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 92046b3a944a747ce76d2426855eec7b6bc2cd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84790242"
---
# <a name="list-azure-deny-assignments-using-the-azure-portal"></a>Výpis přiřazení odmítnutí Azure pomocí Azure Portal

[Přiřazení Azure Deny](deny-assignments.md) zablokují uživatelům, aby prováděli konkrétní akce prostředku Azure i v případě, že jim přiřazení role udělí přístup. Tento článek popisuje, jak vypsat přiřazení zamítnutí pomocí Azure Portal.

> [!NOTE]
> Nemůžete přímo vytvořit vlastní přiřazení zamítnutí. Informace o tom, jak se vytvářejí přiřazení odepřít, najdete v tématu [přiřazení odmítnutí Azure](deny-assignments.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li získat informace o přiřazení zamítnutí, je nutné mít následující:

- `Microsoft.Authorization/denyAssignments/read` oprávnění, které je součástí většiny [předdefinovaných rolí Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Zobrazení seznamu zamítnutých přiřazení

Pomocí těchto kroků můžete vypsat přiřazení zamítnutí v oboru předplatného nebo skupiny pro správu.

1. V Azure Portal klikněte na **všechny služby** a pak na **skupiny pro správu** nebo **odběry**.

1. Klikněte na skupinu pro správu nebo na předplatné, které chcete zobrazit.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Odepřít přiřazení** (nebo klikněte na tlačítko **Zobrazit** na dlaždici zobrazit přiřazení odepřít).

    Pokud jsou v tomto oboru k dispozici nějaká přiřazení odepřít nebo zděděná do tohoto oboru, budou uvedena.

    ![Řízení přístupu – karta zamítnutí přiřazení](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Chcete-li zobrazit další sloupce, klikněte na tlačítko **Upravit sloupce**.

    ![Odepřít přiřazení – sloupce](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Název** | Název přiřazení zamítnutí. |
    | **Typ objektu zabezpečení** | Uživatel, skupina, systémově definované skupiny nebo instanční objekt. |
    | **Denied**  | Název objektu zabezpečení, který je zahrnutý v přiřazení zamítnutí. |
    | **Účet** | Jedinečný identifikátor pro přiřazení zamítnutí. |
    | **Vyloučené objekty zabezpečení** | Zda existují objekty zabezpečení, které jsou vyloučeny z přiřazení zamítnutí. |
    | **Neplatí pro podřízené položky** | Zda je přiřazení zamítnutí děděno do podoborů. |
    | **Chráněno systémem** | Zda je přiřazení zamítnuto spravováno službou Azure. V současné době vždycky Ano. |
    | **Scope** | Skupina pro správu, předplatné, skupina prostředků nebo prostředek. |

1. Přidejte značku zaškrtnutí na kteroukoli z povolených položek a potom kliknutím na tlačítko **OK** Zobrazte vybrané sloupce.

## <a name="list-details-about-a-deny-assignment"></a>Vypíše podrobnosti o přiřazení zamítnutí.

Pomocí následujícího postupu můžete zobrazit další podrobnosti o přiřazení zamítnutí.

1. Otevřete podokno **zamítnout přiřazení** , jak je popsáno v předchozí části.

1. Kliknutím na název přiřazení odepřít otevřete okno **Uživatelé** .

    ![Odepřít přiřazení – uživatelé](./media/deny-assignments-portal/deny-assignment-users.png)

    Okno **Uživatelé** obsahuje následující dvě části.

    |  |  |
    | --- | --- |
    | **Přiřazení zamítnutí platí pro**  | Objekty zabezpečení, na které se vztahuje přiřazení zamítnutí. |
    | **Zakázané přiřazení – vyloučení** | Objekty zabezpečení, které jsou vyloučeny z přiřazení zamítnutí. |

    **Systémově definovaný objekt zabezpečení** představuje všechny uživatele, skupiny, instanční objekty a spravované identity v adresáři Azure AD.

1. Pokud chcete zobrazit seznam oprávnění, která jsou odepřená, klikněte na **Odepřít oprávnění**.

    ![Zamítnutí přiřazení – Odepřená oprávnění](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akce | Popis |
    | --- | --- |
    | **Akce**  | Operace správy se zamítly. |
    | **NotActions** | Operace správy vyloučené z operace odepřené správy |
    | **Akce dataactions**  | Operace s daty se zamítly. |
    | **NotDataActions** | Datové operace vyloučené z operace odepření dat |

    Pro příklad uvedený na předchozím snímku obrazovky jsou následující skutečná oprávnění:

    - Všechny operace úložiště na rovině dat jsou odepřeny s výjimkou výpočetních operací.

1. Chcete-li zobrazit vlastnosti přiřazení odepřít, klikněte na tlačítko **vlastnosti**.

    ![Odepřít přiřazení – vlastnosti](./media/deny-assignments-portal/deny-assignment-properties.png)

    V okně **vlastnosti** můžete zobrazit název, ID, popis a rozsah přiřazení zamítnutí. Přepínač **neplatí pro podřízené objekty** označuje, zda je přiřazení zamítnutí děděno do podoborů. Přepínač **chráněný systémem** označuje, jestli se toto přiřazení zamítnutí spravuje v Azure. V současné době je to u všech případů **Ano** .

## <a name="next-steps"></a>Další kroky

* [Pochopení přiřazení Azure Deny](deny-assignments.md)
* [Výpis přiřazení odmítnutí Azure pomocí Azure PowerShell](deny-assignments-powershell.md)
