---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 131b21ea7bc47df9654dd7c163eb22adb68e6678
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971568"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvoření prostředku prostorových kotev

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levém podokně vyberte **vytvořit prostředek**.

Pomocí vyhledávacího pole vyhledejte **prostorové kotvy**.

![Snímek obrazovky znázorňující výsledky hledání prostorových ukotvení.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Vyberte **prostorové kotvy** a pak vyberte **vytvořit**.

V podokně **účet prostorových kotev** udělejte toto:

* Zadejte jedinečný název prostředku s použitím běžných alfanumerických znaků.  
* Vyberte předplatné, ke kterému chcete prostředek připojit.  
* Vytvořte skupinu prostředků výběrem možnosti **vytvořit nový**. Pojmenujte ji **myResourceGroup** a pak vyberte **OK**.  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* Vyberte umístění (oblast), do kterého se má prostředek umístit.  
* Pokud chcete začít vytvářet prostředek, vyberte **Nový** .

![Snímek obrazovky s podoknem prostorových ukotvení pro vytvoření prostředku](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po vytvoření prostředku Azure Portal zobrazí, že vaše nasazení bylo dokončeno. 
   
![Snímek obrazovky zobrazující, že se nasazení prostředků dokončilo](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Vyberte **Přejít k prostředku**. Nyní můžete zobrazit vlastnosti prostředku. 
   
Zkopírujte hodnotu **ID účtu** prostředku do textového editoru pro pozdější použití.

![Snímek obrazovky s podoknem vlastností prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Zkopírujte také hodnotu **Doména účtu** prostředku do textového editoru pro pozdější použití.

![Snímek obrazovky zobrazující hodnotu domény účtu prostředku](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

V části **Nastavení** vyberte **klíč**. Zkopírujte hodnotu **primárního klíče** a **klíč účtu** do textového editoru pro pozdější použití.

![Snímek obrazovky s podoknem klíčů pro účet](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Začněte tím, že připravíte prostředí pro rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Po přihlášení pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set) vyberte předplatné, ve kterém se má nastavit účet prostorových kotev:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Spuštěním příkazu [AZ Group Create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků nebo použijte existující skupinu prostředků:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Aktuální účty prostorových kotev pro skupinu prostředků můžete zobrazit pomocí příkazu [AZ prostor-kotvy-Account list](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) :

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Můžete si také zobrazit účty prostorových kotev pro vaše předplatné:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Spuštěním příkazu [AZ prostor-anchores-Account Create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) vytvořte svůj účet prostorových kotev:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Zobrazte vlastnosti prostředku pomocí příkazu [AZ prostor-kotvy-Account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) :

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Zkopírujte hodnotu **ID účtu** prostředku a hodnotu **Doména účtu** prostředku do textového editoru pro pozdější použití.

1. Spuštěním příkazu [AZ prostor-anchores-Account Key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) získáte primární a sekundární klíče:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Zkopírujte hodnoty klíče do textového editoru pro pozdější použití.

   Pokud potřebujete znovu vygenerovat klíče, použijte příkaz [AZ prostor-kotvy-účet obnovení klíče](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) :

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Účet můžete odstranit pomocí příkazu [AZ prostor-kotvy-Account Delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) :

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

---
