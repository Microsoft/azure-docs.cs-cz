---
title: Zobrazení zamítnout přiřazení pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit uživatele, skupiny, instančních objektů a spravovaných identit, které mají odepřený přístup k určitým akcím v určitém rozsahu pomocí webu Azure portal.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5870ddbbb8be0ebbeae7656485521a327b86d5b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642806"
---
# <a name="view-deny-assignments-using-the-azure-portal"></a>Zobrazení zamítnout přiřazení pomocí webu Azure portal

[Zamítnout přiřazení](deny-assignments.md) zablokuje uživatelům možnost provádět konkrétní akce i v případě přiřazení role uděluje přístup. I v případě, že nemůžete vytvořit vlastní zamítnout přiřazení, potřebujete mít možnost zobrazit zamítnout přiřazení, protože mohou ovlivnit celkový oprávnění. K získání informací o přiřazení zamítnutí potřebujete oprávnění `Microsoft.Authorization/denyAssignments/read`, které je obsaženo ve většině [předdefinovaných rolí](built-in-roles.md).

Tento článek popisuje, jak pomocí webu Azure portal zobrazit zamítnout přiřazení.

> [!NOTE]
> V tuto chvíli zamítnout přiřazení jsou jen pro čtení a lze nastavit pouze v Azure.

## <a name="view-deny-assignments"></a>Zobrazení zamítnout přiřazení

Podle těchto kroků zobrazit zamítnout přiřazení v oboru předplatného nebo správu skupiny.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a potom **skupin pro správu** nebo **předplatná**.

1. Klikněte na skupinu pro správu nebo předplatného, které chcete zobrazit.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **zamítnout přiřazení** kartu (nebo klikněte na tlačítko **zobrazení** tlačítka pro zobrazení odepřít dlaždici přiřazení).

    Pokud jsou všechny zamítnout přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedené.

    ![Řízení přístupu – odepřít kartu přiřazení](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Chcete-li zobrazit další sloupce, klikněte na tlačítko **upravit sloupce**.

    ![Zamítnout přiřazení – sloupce](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Název** | Název přiřazení odepřít. |
    | **Typ objektu zabezpečení** | Uživatele, skupiny definované v systému nebo instanční objekt služby. |
    | **Byl odepřen**  | Název objektu zabezpečení, který je součástí přiřazení odepřít. |
    | **ID** | Jedinečný identifikátor pro přiřazení odepřít. |
    | **Vyloučené objekty zabezpečení** | Ať už jsou objekty zabezpečení, které jsou vyloučené z přiřazení odepřít. |
    | **Nevztahuje se na podřízené položky** | Určuje, zda je subscopes zděděné přiřazení odepřít. |
    | **Systému chráněné** | Určuje, zda je přiřazení odepřít spravuje Azure. V současné době vždy Ano. |
    | **Rozsah** | Skupina pro správu, předplatné, skupinu prostředků nebo prostředek. |

1. Pro žádnou z povolených položek přidat značka zaškrtnutí a pak klikněte na **OK** k zobrazení vybraných sloupcích.

## <a name="view-details-about-a-deny-assignment"></a>Zobrazit podrobnosti o přiřazení Odepřít

Následujícím postupem zobrazíte další podrobnosti o přiřazení odepřít.

1. Otevřít **zamítnout přiřazení** podokně, jak je popsáno v předchozí části.

1. Klikněte na název přiřazení odepřít otevřete **uživatelé** okno.

    ![Zamítnout přiřazení – uživatelé](./media/deny-assignments-portal/deny-assignment-users.png)

    **Uživatelé** okno obsahuje následující dvě části.

    |  |  |
    | --- | --- |
    | **Zamítnout přiřazení platí pro**  | Objekty zabezpečení, pro které platí přiřazení odepřít. |
    | **Odepřít vyloučí přiřazení** | Objekty zabezpečení, které jsou vyloučené z přiřazení odepřít. |

    **Objekt zabezpečení definovaných systémem** představuje všechny uživatele, skupiny, instančních objektů a spravovaných identit v adresáři Azure AD.

1. Chcete-li zobrazit seznam oprávnění, které jsou zamítnuty, klikněte na tlačítko **odepření oprávnění**.

    ![Zamítnout přiřazení – odepření oprávnění](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akce | Popis |
    | --- | --- |
    | **Akce**  | Odepřen operace správy. |
    | **notActions** | Operace správy vyloučeny z odepřen operace správy. |
    | **DataActions**  | Odepřen operace s daty. |
    | **NotDataActions** | Operace s daty vyloučené z odepřen datové operace. |

    Například je znázorněno na předchozím snímku obrazovky tady jsou platná oprávnění:

    - Veškeré součásti úložiště, které operace v rovině dat byl odepřen, s výjimkou pro výpočetní operace.

1. Pokud chcete zobrazit vlastnosti pro přiřazení odepřít, klikněte na **vlastnosti**.

    ![Zamítnout přiřazení – vlastnosti](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na **vlastnosti** okně uvidíte název přiřazení odepřít, ID, popis a obor. **Se nevztahuje na podřízené položky** přepínač označuje, zda je subscopes zděděné přiřazení odepřít. **Systému chráněného** přepínač označuje, jestli to zamítnout přiřazení spravuje Azure. V současné době je **Ano** ve všech případech.

## <a name="next-steps"></a>Další postup

* [Vysvětlení zamítnout přiřazení](deny-assignments.md)
* [Seznam zamítnout přiřazení pomocí RBAC a rozhraní REST API](deny-assignments-rest.md)
