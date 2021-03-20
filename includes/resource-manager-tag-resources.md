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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80132162"
---
Pokud uživatel nemá požadovaný přístup pro použití značek, můžete uživateli přiřadit roli **Přispěvatel značek** . Další informace najdete v tématu [kurz: udělení přístupu uživatele k prostředkům Azure pomocí RBAC a Azure Portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Pokud chcete zobrazit značky pro prostředek nebo skupinu prostředků, hledejte existující značky v přehledu. Pokud jste značky ještě nepoužili, seznam je prázdný.

   ![Zobrazit značky pro prostředek nebo skupinu prostředků](./media/resource-manager-tag-resources/view-tags.png)

1. Chcete-li přidat značku, vyberte **kliknutím sem přidejte značky**.

1. Zadejte název a hodnotu.

   ![Přidat značku](./media/resource-manager-tag-resources/add-tag.png)

1. V případě potřeby pokračujte v přidávání značek. Až budete hotovi, vyberte **Uložit**.

   ![Uložit značky](./media/resource-manager-tag-resources/save-tags.png)

1. Značky jsou nyní zobrazeny v přehledu.

   ![Zobrazit značky](./media/resource-manager-tag-resources/view-new-tags.png)

1. Chcete-li přidat nebo odstranit značku, vyberte možnost **změnit**.

1. Chcete-li odstranit značku, vyberte ikonu odpadkového koše. Pak vyberte **Uložit**.

   ![Odstranit značku](./media/resource-manager-tag-resources/delete-tag.png)

Hromadné přiřazení značek k více prostředkům:

1. V jakémkoli seznamu prostředků zaškrtněte políčko u prostředků, kterým chcete značku přiřadit. Pak vyberte **přiřadit značky**.

   ![Výběr více prostředků](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Přidejte názvy a hodnoty. Až budete hotovi, vyberte **Uložit**.

   ![Vybrat přiřadit](./media/resource-manager-tag-resources/select-assign.png)

Zobrazení všech prostředků se značkou:

1. V nabídce Azure Portal vyhledejte **značky**. Vyberte ho z dostupných možností.

   ![Najít podle značky](./media/resource-manager-tag-resources/find-tags-general.png)

1. Vyberte značku pro zobrazení prostředků.

   ![Vybrat značku](./media/resource-manager-tag-resources/select-tag.png)

1. Zobrazí se všechny prostředky s touto značkou.

   ![Zobrazit prostředky podle značky](./media/resource-manager-tag-resources/view-resources-by-tag.png)
