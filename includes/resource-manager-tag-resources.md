---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132162"
---
Pokud uživatel nemá požadovaný přístup k použití značek, můžete uživateli přiřadit roli **Přispěvatel značky.** Další informace najdete [v tématu Kurz: Udělit uživateli přístup k prostředkům Azure pomocí RBAC a portálu Azure](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Chcete-li zobrazit značky pro prostředek nebo skupinu prostředků, vyhledejte existující značky v přehledu. Pokud jste značky ještě nepoužili, seznam je prázdný.

   ![Zobrazení značek pro skupinu prostředků nebo prostředků](./media/resource-manager-tag-resources/view-tags.png)

1. Chcete-li přidat značku, vyberte **možnost Kliknutím sem přidáte značky**.

1. Zadejte název a hodnotu.

   ![Přidat značku](./media/resource-manager-tag-resources/add-tag.png)

1. Pokračujte v přidávání značek podle potřeby. Až budete hotovi, vyberte **Uložit**.

   ![Uložit značky](./media/resource-manager-tag-resources/save-tags.png)

1. Značky jsou nyní zobrazeny v přehledu.

   ![Zobrazit značky](./media/resource-manager-tag-resources/view-new-tags.png)

1. Chcete-li přidat nebo odstranit značku, vyberte **možnost Změnit**.

1. Chcete-li značku odstranit, vyberte ikonu koše. Potom vyberte **Uložit**.

   ![Odstranit značku](./media/resource-manager-tag-resources/delete-tag.png)

Hromadné přiřazení značek k více zdrojům:

1. V libovolném seznamu zdrojů zaškrtněte políčko u zdrojů, které chcete značku přiřadit. Potom vyberte **Přiřadit značky**.

   ![Výběr více zdrojů](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Přidejte jména a hodnoty. Až budete hotovi, vyberte **Uložit**.

   ![Vybrat přiřadit](./media/resource-manager-tag-resources/select-assign.png)

Zobrazení všech zdrojů se značkou:

1. V nabídce portálu Azure vyhledejte **značky**. Vyberte ji z dostupných možností.

   ![Najít podle značky](./media/resource-manager-tag-resources/find-tags-general.png)

1. Vyberte značku pro zobrazení zdrojů.

   ![Vybrat značku](./media/resource-manager-tag-resources/select-tag.png)

1. Zobrazí se všechny prostředky s tímto označením.

   ![Zobrazit zdroje podle značky](./media/resource-manager-tag-resources/view-resources-by-tag.png)
