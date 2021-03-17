---
title: Azure Monitor sešity Přineste vlastní úložiště.
description: Informace o tom, jak zabezpečit sešit uložením obsahu sešitu do úložiště
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611674"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Přineste si vlastní úložiště pro ukládání sešitů

Existují situace, kdy je možné, že máte dotaz nebo nějakou obchodní logiku, kterou chcete zabezpečit. Sešity poskytují možnost zabezpečit sešity uložením obsahu sešitů do úložiště. Účet úložiště se pak může šifrovat pomocí klíčů spravovaných Microsoftem nebo můžete spravovat šifrování tím, že poskytnete vlastní klíče. [Viz dokumentace k Azure v Šifrování služby Storage.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Ukládání sešitu se spravovanými identitami

1. Předtím, než sešit uložíte do úložiště, budete muset vytvořit spravovanou identitu (všechny služby > spravované identity) a udělit jí `Storage Blob Data Contributor` přístup k vašemu účtu úložiště. [Viz dokumentace k Azure na spravovaných identitách.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Snímek obrazovky znázorňující přidání přiřazení role](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Vytvoří nový sešit.
3. Kliknutím na tlačítko **Uložit** sešit uložte.
4. Existuje možnost, pokud chcete `Save content to an Azure Storage Account` Uložit účet Azure Storage, zaškrtněte políčko.

    ![Snímek obrazovky zobrazující uložený dialog](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Vyberte účet úložiště, který si přejete a kontejner. Seznam účtů úložiště je z výše vybraného předplatného.

    ![Snímek obrazovky s možností ukládání v dialogovém okně Uložit](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Pak vyberte **změnit** a vyberte spravovanou identitu, kterou jste dříve vytvořili.

    [![Snímek obrazovky zobrazující dialog změnit identitu](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Po výběru možností úložiště uložte sešit kliknutím na **Uložit** .

## <a name="limitations"></a>Omezení

- Při ukládání do vlastního úložiště nemůžete připnout jednotlivé části sešitu na řídicí panel, protože jednotlivé kódy PIN obsahují chráněné informace v samotném řídicím panelu. Pokud používáte vlastní úložiště, můžete připnout jenom odkazy na tento sešit na řídicí panely.
- Jakmile se sešit uloží do vlastního úložiště, uloží se vždycky do vlastního úložiště a tato funkce se nedá vypnout. Pokud chcete uložit jinam, můžete použít možnost Uložit jako a vybrat možnost Neukládat kopii do vlastního úložiště.
- Sešity v Application Insights prostředku jsou "starší" sešity a nepodporují vlastní úložiště. Nejnovější sešity v Application Insights prostředku jsou... Další "výběr. Starší verze sešitů nemají při ukládání možnost předplatného.

   ![Snímek obrazovky, který zobrazuje starší sešit](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak v sešitech vytvořit vizualizaci [mapy](workbooks-map-visualizations.md) .
- Naučte se používat [skupiny v sešitech](../visualize/workbooks-groups.md).