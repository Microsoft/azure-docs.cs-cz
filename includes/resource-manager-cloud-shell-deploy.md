---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74451463"
---
## <a name="deploy-template-from-cloud-shell"></a>Nasazení šablony ze služby Cloud Shell

K nasazení šablony můžete použít [Cloud Shell](../articles/cloud-shell/overview.md). Pokud chcete nasadit externí šablonu, zadejte identifikátor URI šablony přesně stejně jako u jakéhokoli externího nasazení. Pokud chcete nasadit místní šablonu, musíte nejdřív načíst šablonu do účtu úložiště pro vaši Cloud Shell. Tato část popisuje, jak načíst šablonu do účtu Cloud Shell a jak ji nasadit jako místní soubor. Pokud jste nepoužili Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](../articles/cloud-shell/overview.md) , kde najdete informace o jeho nastavení.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyberte vaši skupinu prostředků služby Cloud Shell. Vzor názvů je `cloud-shell-storage-<region>`.

   ![Výběr skupiny prostředků](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Vyberte účet úložiště pro službu Cloud Shell.

   ![Výběr účtu úložiště](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Vyberte **objekty blob**.

   ![Výběr objektů blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Vyberte **+ Kontejner**.

   ![Přidání kontejneru](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Dejte kontejneru název a úroveň přístupu. Ukázková šablona v tomto článku neobsahuje žádné citlivé informace, takže umožňuje anonymní přístup pro čtení. Vyberte **OK**.

   ![Zadat hodnoty kontejneru](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Vyberte kontejner, který jste vytvořili.

   ![Vybrat nový kontejner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Vyberte **Nahrát**.

   ![Nahrát objekt BLOB](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Vyhledejte a nahrajte vaši šablonu.

   ![Nahrání souboru](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po nahrání vyberte šablonu.

   ![Vybrat novou šablonu](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Zkopírujte adresu URL.

   ![Zkopírování adresy URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otevřete příkazový řádek.

   ![Otevření Cloud Shellu](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
