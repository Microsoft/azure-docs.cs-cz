---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302338"
---
## <a name="deploy-template-from-cloud-shell"></a>Nasazení šablony ze služby Cloud Shell

K nasazení šablony můžete použít [Cloud Shell](../articles/cloud-shell/overview.md). Pokud chcete nasadit šablonu externí, zadejte identifikátor URI šablony stejným způsobem jako u jakéhokoli externího nasazení. Pokud chcete nasadit šablonu místní, musí nejdřív načíst šablony do účtu úložiště pro službu Cloud Shell. Tato část popisuje, jak načíst šablonu do účtu cloudového prostředí a nasaďte ho jako místní soubor. Pokud jste ještě nepoužívali Cloud Shell, přečtěte si téma [Přehled služby Azure Cloud Shell](../articles/cloud-shell/overview.md) informace o jeho nastavení.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte vaši skupinu prostředků služby Cloud Shell. Vzor názvů je `cloud-shell-storage-<region>`.

   ![Výběr skupiny prostředků](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Vyberte účet úložiště pro službu Cloud Shell.

   ![Výběr účtu úložiště](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Vyberte **Objekty blob**.

   ![Výběr objektů blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Vyberte **+ Kontejner**.

   ![Přidání kontejneru](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Zadejte svůj kontejner názvu a úroveň přístupu. Ukázková šablona v tomto článku obsahuje žádné citlivé informace, takže povolit anonymní přístup pro čtení. Vyberte **OK**.

   ![Zadejte hodnoty kontejneru](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Vyberte kontejner, který jste vytvořili.

   ![Vybrat nový kontejner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Vyberte **Nahrát**.

   ![Nahrát objekt blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Vyhledejte a nahrajte vaši šablonu.

   ![Nahrání souboru](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po odeslal, vyberte šablonu.

   ![Vyberte novou šablonu](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Zkopírujte adresu URL.

   ![Zkopírovat adresu URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otevřete příkazový řádek.

   ![Otevření služby Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
