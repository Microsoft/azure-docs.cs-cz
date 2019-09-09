---
title: Zobrazení historie aktivit a auditu pro role prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Podívejte se na historii aktivit a auditu pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804045"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Zobrazení historie aktivit a auditu pro role prostředků Azure v PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zobrazit aktivity, aktivace a historii auditu pro role prostředků Azure v rámci vaší organizace. Patří sem předplatná, skupiny prostředků a i virtuální počítače. Všechny prostředky v rámci Azure Portal, které využívají funkci řízení přístupu na základě role (RBAC) v Azure, můžou využívat možnosti správy zabezpečení a životního cyklu v PIM.

## <a name="view-activity-and-activations"></a>Zobrazit aktivity a aktivace

Pokud chcete zjistit, jaké akce určitý uživatel trval v různých prostředcích, můžete zobrazit aktivitu prostředků Azure, která je přidružená k danému období aktivace.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, pro který chcete zobrazit aktivitu a aktivace.

1. Klikněte na **role** nebo **Členové**.

1. Klikněte na uživatele.

    Zobrazí se grafické zobrazení akcí uživatele v prostředcích Azure podle data. Zobrazuje také poslední aktivaci rolí v rámci tohoto časového období.

    ![Podrobnosti o uživateli s přehledem aktivit prostředků a aktivací rolí](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Kliknutím na aktivaci konkrétní role zobrazíte podrobnosti a odpovídající aktivity prostředků Azure, k nimž došlo v době, kdy byl tento uživatel aktivní.

    ![Vybraná aktivace role a podrobnosti o aktivitě zobrazené podle data](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Export přiřazení rolí s podřízenými položkami

Můžete mít požadavek na dodržování předpisů, kde musíte poskytnout úplný seznam přiřazení rolí auditorům. PIM vám umožní dotazovat se na přiřazení rolí v konkrétním prostředku, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dřív bylo obtížné, aby správci získali úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek. Pomocí PIM můžete zadat dotaz na všechna aktivní a oprávněná přiřazení rolí v rámci předplatného, včetně přiřazení rolí pro všechny skupiny prostředků a prostředky.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, pro který chcete exportovat přiřazení rolí, jako je například předplatné.

1. Klikněte na tlačítko **Členové**.

1. Kliknutím na **exportovat** otevřete podokno exportovat členství.

    ![Exportovat podokno členství pro export všech členů](media/azure-pim-resource-rbac/export-membership.png)

1. Kliknutím na **exportovat všechny členy** exportujte všechna přiřazení rolí v souboru CSV.

    ![Export přiřazení rolí v CSV náhl jako zobrazení v Excelu](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu prostředků

Audit prostředků poskytuje zobrazení všech aktivit role pro určitý prostředek.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, pro který chcete zobrazit historii auditu.

1. Klikněte na **audit prostředků**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu prostředků s filtry](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Jako **typ auditu**vyberte **aktivovat (přiřazeno + aktivované)** .

    ![Seznam auditu prostředků, který je filtrovaný podle typu aktivovat audit](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. V části **Akce**klikněte na **(aktivita)** pro uživatele a podívejte se na podrobnosti o aktivitách tohoto uživatele v prostředcích Azure.

    ![Podrobnosti o aktivitě uživatele pro určitou akci](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit aktivitu osobní role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, pro který chcete zobrazit historii auditu.

1. Klikněte na **Moje audit**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
- [Schválení nebo zamítnutí žádostí o role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [Zobrazení historie auditu pro role Azure AD v PIM](pim-how-to-use-audit-log.md)
