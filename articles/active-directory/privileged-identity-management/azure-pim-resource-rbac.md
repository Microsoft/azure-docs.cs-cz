---
title: Zobrazit historii aktivit a auditu pro role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zobrazení aktivit a auditu historie pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 01/24/2019
ms.author: rolyon
ms.openlocfilehash: 21decb8260abfe98df913763a2338b34aacb1f91
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911452"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Zobrazit historii aktivit a auditu pro role prostředků Azure v PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete zobrazit aktivitu, aktivace a historie auditů pro role prostředků Azure v rámci vaší organizace. To zahrnuje předplatná, skupiny prostředků a dokonce i virtuální počítače. Prostředek na portálu Azure portal, který využívá funkce řízení přístupu na základě rolí Azure můžete využít výhod zabezpečení a životního cyklu možnosti správy v PIM.

## <a name="view-activity-and-activations"></a>Zobrazit aktivitu a aktivace

Pokud chcete zjistit, jaké akce v různých prostředků provedli konkrétního uživatele, můžete zobrazit aktivity prostředků Azure, který je spojen s danou aktivační období.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, které chcete zobrazit aktivity a aktivací pro.

1. Klikněte na tlačítko **role** nebo **členy**.

1. Klepněte na uživatele.

    Zobrazí grafické zobrazení akcí uživatele na prostředky Azure podle data. Také ukazuje poslední aktivace rolí za stejné období čas.

    ![Podrobnosti uživatele](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Kliknutím na konkrétní roli aktivace zobrazit podrobnosti a odpovídající aktivity prostředků Azure, ke které došlo během tento uživatel byl aktivní.

    ![Vyberte aktivace role](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportovat přiřazení rolí s podřízených objektů

Může mít požadavek na dodržování předpisů, kde je nutné zadat úplný seznam přiřazení rolí k auditoři. PIM umožňuje dotaz přiřazení rolí na konkrétní prostředek, který obsahuje přiřazení rolí pro všechny podřízené prostředky. Dříve bylo obtížné pro správce, chcete-li získat úplný seznam přiřazení rolí pro předplatné a měli zajišťující export přiřazení rolí pro každou konkrétní prostředek. Použití PIM, můžete zadat dotaz na všechna přiřazení rolí aktivní a bude možné v rámci předplatného, včetně přiřazení rolí pro všechny skupiny prostředků a prostředky.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, kterou chcete exportovat přiřazení rolí, jako je například předplatné.

1. Klikněte na tlačítko **členy**.

1. Klikněte na tlačítko **exportovat** a otevřete tak podokno členství exportu.

    ![Export podokně členství](media/azure-pim-resource-rbac/export-membership.png)

1. Klikněte na tlačítko **exportovat všechny členy** exportovat všechna přiřazení rolí v souboru CSV.

    ![Exportujte soubor CSV](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Zobrazení historie auditu prostředků

Audit prostředků umožňuje zobrazit všechny aktivity role pro prostředek.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, které chcete zobrazit historie auditů pro.

1. Klikněte na tlačítko **audit prostředků**.

1. Historie pomocí předdefinované datové nebo vlastní rozsah filtru.

    ![Filtr auditu prostředků](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Pro **typ auditu**vyberte **aktivovat (přiřazeno + aktivováno)**.

    ![Podrobnosti o aktivitě](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. V části **akce**, klikněte na tlačítko **(aktivita)** pro uživatele zobrazíte podrobnosti o aktivitě uživatele v prostředcích Azure.

    ![Podrobnosti o aktivitě uživatelů](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit vaše osobní role aktivity.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, které chcete zobrazit historie auditů pro.

1. Klikněte na tlačítko **Můj audit**.

1. Historie pomocí předdefinované datové nebo vlastní rozsah filtru.

    ![Aktivita osobní role](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Další postup

- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [Zobrazení historie auditů pro role adresáře Azure AD v PIM](pim-how-to-use-audit-log.md)
