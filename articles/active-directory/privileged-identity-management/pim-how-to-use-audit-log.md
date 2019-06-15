---
title: Zobrazení historie auditů pro role Azure AD v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit historie auditů pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053940"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Zobrazení historie auditů pro role Azure AD v PIM

Pokud chcete zobrazit všechna přiřazení rolí a počet aktivací za posledních 30 dnů pro všechny privilegované role můžete použít historie auditu služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Pokud chcete zobrazit úplnou historii auditu aktivity ve vašem adresáři, včetně správce, koncový uživatel a aktivitu synchronizace můžete použít [sestav zabezpečení a aktivit Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Zobrazení historie auditů

Postupujte podle těchto kroků pro zobrazení historie auditů pro role Azure AD.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD**.

1. Klikněte na tlačítko **historie auditu rolí adresářů**.

    V závislosti na vaší historie auditu se sloupcový graf zobrazí celkový počet aktivací, maximální počet aktivací za den a průměrný počet aktivací za den.

    ![Historie auditu rolí adresářů](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    V dolní části stránky se zobrazí tabulku s informacemi o každou akci do historie auditu k dispozici. Sloupce, které mají následující význam:

    | Sloupec | Popis |
    | --- | --- |
    | Čas | Když akce došlo k chybě. |
    | Žadatel | Uživatel, který požadované aktivaci role nebo změnit. Pokud je hodnota **systém Azure**, zkontrolujte historii auditování Azure pro další informace. |
    | Akce | Akce prováděné žadatel. Akce můžou zahrnovat přiřazení, zrušit přiřazení, aktivovat, deaktivovat nebo AddedOutsidePIM. |
    | Člen | Uživatel, který je aktivace nebo přiřazený k roli. |
    | Role | Role přiřazené nebo uživatel aktivoval. |
    | Uvažování | Text, který jste zadali do pole Důvod při aktivaci. |
    | vypršení platnosti | Kdy vyprší platnost aktivovanou roli. Platí jenom pro přiřazení oprávněné role. |

1. Historie auditu seřadit, klikněte na tlačítko **čas**, **akce**, a **Role** tlačítka.

## <a name="filter-audit-history"></a>Filtrovat historie auditu

1. V horní části na stránce Historie auditu, klikněte na tlačítko **filtr** tlačítko.

    **Aktualizovat parametry grafu** otevře se podokno.

1. V **časový rozsah**, vyberte časový rozsah.

1. V **role**, přidání značek zaškrtnutí pro role, které chcete zobrazit.

    ![Aktualizovat podokno parametry grafu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Klikněte na tlačítko **provádí** historie auditu filtrované zobrazení.

## <a name="next-steps"></a>Další postup

- [Zobrazit historii aktivit a auditu pro role prostředků Azure v PIM](azure-pim-resource-rbac.md)
