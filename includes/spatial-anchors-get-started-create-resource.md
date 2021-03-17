---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628668"
---
## <a name="create-a-spatial-anchors-resource"></a>Vytvoření prostředku prostorových kotev

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Přejděte na <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

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

Začněte přípravou prostředí pro rozhraní příkazového řádku Azure:

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

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Začněte přípravou vašeho prostředí pro Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> I když je modul PowerShell **AZ. MixedReality** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Po přihlášení pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) vyberte předplatné, ve kterém se má nastavit účet prostorových kotev:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Spuštěním rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte skupinu prostředků nebo použijte existující skupinu prostředků:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Aktuální účty prostorových kotev pro skupinu prostředků můžete zobrazit pomocí rutiny [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Můžete si také zobrazit účty prostorových kotev pro vaše předplatné:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Spuštěním rutiny [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) vytvořte svůj účet prostorových kotev:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Zobrazení vlastností prostředku pomocí rutiny [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Zkopírujte hodnotu **ID účtu** vlastnosti a vlastnost **accountDomain** do textového editoru pro pozdější použití.

1. Pro získání primárního a sekundárního klíče spusťte rutinu [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Zkopírujte hodnoty klíče do textového editoru pro pozdější použití.

   Pokud potřebujete znovu vygenerovat klíče, použijte rutinu [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) :

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Účet můžete odstranit pomocí rutiny [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) :

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
