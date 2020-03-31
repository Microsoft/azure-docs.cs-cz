---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74451463"
---
## <a name="deploy-template-from-cloud-shell"></a>Nasazení šablony ze služby Cloud Shell

K nasazení šablony můžete použít [Cloud Shell](../articles/cloud-shell/overview.md). Chcete-li nasadit externí šablonu, zadejte identifikátor URI šablony přesně tak, jak byste měli pro jakékoli externí nasazení. Chcete-li nasadit místní šablonu, musíte nejprve načíst šablonu do účtu úložiště pro cloudové prostředí. Tato část popisuje, jak načíst šablonu do účtu cloudshellu a nasadit ji jako místní soubor. Pokud jste cloudshell nepoužili, přečtěte si [část Přehled Azure Cloud Shell,](../articles/cloud-shell/overview.md) kde najdete informace o jeho nastavení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte vaši skupinu prostředků služby Cloud Shell. Vzor názvů je `cloud-shell-storage-<region>`.

   ![Výběr skupiny prostředků](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Vyberte účet úložiště pro službu Cloud Shell.

   ![Výběr účtu úložiště](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Vyberte **objekty BLOB**.

   ![Výběr objektů blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Vyberte **+ Kontejner**.

   ![Přidání kontejneru](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Pojmenujte kontejner a úroveň přístupu. Ukázková šablona v tomto článku neobsahuje žádné citlivé informace, proto povolte anonymní přístup pro čtení. Vyberte **OK**.

   ![Zadejte hodnoty kontejneru](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Vyberte kontejner, který jste vytvořili.

   ![Vybrat nový kontejner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Vyberte **Nahrát**.

   ![Nahrát objekt blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Vyhledejte a nahrajte vaši šablonu.

   ![Nahrání souboru](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po nahrání vyberte šablonu.

   ![Vybrat novou šablonu](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Zkopírujte adresu URL.

   ![Zkopírování adresy URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otevřete příkazový řádek.

   ![Otevření služby Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
