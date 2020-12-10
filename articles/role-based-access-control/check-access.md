---
title: Rychlý Start – ověření přístupu uživatele k prostředkům Azure – Azure RBAC
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal a řízení přístupu založeného na rolích Azure (Azure RBAC) ověřit přístup pro sebe nebo jiného uživatele k prostředkům Azure.
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperfq2
ms.openlocfilehash: 8036bd300522000902789db59f8bebae14fedf10
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007276"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Rychlý Start: Ověřte přístup pro uživatele k prostředkům Azure.

Někdy potřebujete zjistit, jaký přístup uživatel má k sadě prostředků Azure. Svůj přístup zkontrolujete tak, že uvedete jejich přiřazení. Rychlý způsob, jak kontrolovat přístup pro jednoho uživatele, je použít funkci **Kontrola přístupu** na stránce **řízení přístupu (IAM)** .

## <a name="step-1-open-the-azure-resources"></a>Krok 1: otevření prostředků Azure

Pokud chcete zjistit přístup pro uživatele, musíte nejdřív otevřít prostředky Azure, pro které chcete ověřit přístup. Prostředky Azure jsou uspořádané do úrovní, které se obvykle nazývají *obor*. V Azure můžete určit obor na čtyřech úrovních od širokého po zúžení: skupina pro správu, předplatné, skupina prostředků a prostředek.

![Úrovně rozsahu pro Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

Pomocí těchto kroků otevřete sadu prostředků Azure, pro které chcete kontrolovat přístup.

1. Otevřete web [Azure Portal](https://portal.azure.com).

1. Otevřete sadu prostředků Azure, jako jsou **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo konkrétní prostředek.

1. Klikněte na konkrétní prostředek v daném oboru.

    Níže je uveden příklad skupiny prostředků.

    ![Přehled skupiny prostředků](./media/check-access/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Krok 2: ověření přístupu pro uživatele

Postupujte podle těchto kroků a ověřte přístup pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu k dříve vybraným prostředkům služby Azure.

1. Klikněte na **Řízení přístupu (IAM)**.

    Níže vidíte příklad stránky řízení přístupu (IAM) pro skupinu prostředků.

    ![Řízení přístupu ke skupině prostředků – kontrola přístupu na kartě přístup](./media/check-access/rg-access-control.png)

1. Na kartě **kontrolovat přístup** vyberte v seznamu **Najít** uživatele, skupinu, instanční objekt nebo spravovanou identitu, pro kterou chcete ověřit přístup.

1. Do vyhledávacího pole zadejte řetězec, ve kterém budou v adresáři hledána zobrazovaná jména, e-mailové adresy nebo identifikátory objektů.

    ![Zaškrtněte seznam pro výběr přístupu.](./media/shared/rg-check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení** .

    V tomto podokně můžete zobrazit přístup pro vybraný objekt zabezpečení v tomto oboru a zdědit ho do tohoto oboru. Přiřazení v podřízených oborech nejsou uvedena. Zobrazí se následující přiřazení:

    - Přiřazení rolí se přidala do Azure RBAC.
    - Odmítnutí přiřazení přidaných pomocí Azure modrotisky nebo spravovaných aplikací Azure
    - Klasický správce služeb nebo Co-Administrator přiřazení pro nasazení v klasických prostředích. 

    ![Podokno přiřazení rolí a zamítnutí pro uživatele](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Krok 3: ověření přístupu

Pomocí těchto kroků zkontrolujete přístup k dříve vybraným prostředkům Azure.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Na kartě **kontrolovat přístup** klikněte na tlačítko **Zobrazit vlastní přístup** .

    Zobrazí se podokno přiřazení se seznamem přístupu v tomto oboru a zděděno do tohoto oboru. Přiřazení v podřízených oborech nejsou uvedena.

    ![Podokno přiřazení rolí a odepření](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
