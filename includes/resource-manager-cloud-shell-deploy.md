---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: a2ee8705be3f34b6df113c68d88e375411f84bf2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440062"
---
## <a name="deploy-template-from-cloud-shell"></a>Nasazení šablony ze služby Cloud Shell

K nasazení šablony můžete použít [Cloud Shell](../articles/cloud-shell/overview.md). Však můžete musí nejprve šablonu nahrát do účtu úložiště pro službu Cloud Shell. Pokud jste ještě službu Cloud Shell nepoužívali, přečtěte si téma [Přehled služby Azure Cloud Shell](../articles/cloud-shell/overview.md), kde najdete informace o jejím nastavení.

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

   ![Zkopírujte adresu URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otevřete příkazový řádek.

   ![Otevření služby Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
