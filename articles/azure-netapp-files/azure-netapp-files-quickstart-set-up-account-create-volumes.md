---
title: 'Rychlý Start: nastavení Azure NetApp Files a svazku NFS'
description: Rychlý Start – popisuje, jak rychle nastavit Azure NetApp Files a vytvořit svazek.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 70441c3a1953fa1b6ebd69ef9cdb324d6cc04a5b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336431"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Rychlý Start: nastavení Azure NetApp Files a vytvoření svazku NFS

V tomto článku se dozvíte, jak rychle nastavit Azure NetApp Files a vytvořit svazek.

V tomto rychlém startu vytvoříte následující položky:

- Registrace pro poskytovatele prostředků Azure NetApp Files a NetApp
- Účet NetApp
- Fond kapacit
- Svazek NFS pro Azure NetApp Files

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

> [!IMPORTANT]
> Je potřeba udělit přístup ke službě Azure NetApp Files. Chcete-li požádat o přístup ke službě, přečtěte si [stránku odeslání Azure NetApp Files pořadníku](https://aka.ms/azurenetappfiles).  Než budete pokračovat, musíte počkat na oficiální e-mail s potvrzením od Azure NetApp Files týmu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrace pro poskytovatele prostředků Azure NetApp Files a NetApp

> [!NOTE]
> Dokončení procesu registrace může trvat delší dobu.
>

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pro kroky registrace pomocí portálu otevřete relaci Cloud Shell, jak je uvedeno výše, a postupujte podle těchto kroků Azure CLI:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek s postupem vyžaduje Azure PowerShell modul AZ verze 2.6.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud budete chtít, můžete místo toho použít konzolu Cloud Shell v relaci prostředí PowerShell.

1. V příkazovém řádku PowerShellu (nebo v relaci Cloud Shell PowerShellu) zadejte předplatné, které bylo schváleno pro Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zaregistrujte poskytovatele prostředků Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Šablona](#tab/template)

Žádné

K registraci Azure NetApp Files a poskytovatele prostředků NetApp použijte Azure Portal, PowerShell nebo Azure CLI.

Další informace najdete v tématu věnovaném [registraci Azure NetApp Files](azure-netapp-files-register.md) .

---

## <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Do vyhledávacího pole Azure Portal zadejte **Azure NetApp Files** a v seznamu, který se zobrazí, vyberte **Azure NetApp Files** .

      ![Vyberte Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.

     ![Vytvořit nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. V okně Nový účet NetApp zadejte následující informace:
   1. Jako název účtu zadejte **myaccount1** .
   2. Vyberte své předplatné.
   3. Vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. Jako název skupiny prostředků zadejte **myRG1** . Klikněte na **OK**.
   4. Vyberte umístění svého účtu.

      ![Okno nového účtu NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Okno skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknutím na **vytvořit** vytvořte nový účet NetApp.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definujte některé proměnné, na které můžeme v rámci zbývajících příkladů odkazovat:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > Seznam podporovaných oblastí najdete [v tématu dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Pokud chcete získat název oblasti, který podporuje naše nástroje příkazového řádku, použijte prosím `Get-AzLocation | select Location`
    >

1. Pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte novou skupinu prostředků:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Vytvořte Azure NetApp Files účet pomocí příkazu [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definujte některé proměnné, na které můžeme v rámci zbývajících příkladů odkazovat:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > Seznam podporovaných oblastí najdete [v tématu dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Pokud chcete získat název oblasti, který podporuje naše nástroje příkazového řádku, použijte prosím `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) vytvořte novou skupinu prostředků:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Vytvořte účet Azure NetApp Files pomocí příkazu [AZ netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Šablona](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Následující fragment kódu ukazuje, jak vytvořit účet NetApp v šabloně Azure Resource Manager (šablona ARM) pomocí prostředku [Microsoft. NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) . Pokud chcete spustit kód, Stáhněte si [úplnou šablonu ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z našeho úložiště GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V okně Správa Azure NetApp Files vyberte účet NetApp ( **myaccount1** ).

    ![Vybrat účet NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. V okně Správa Azure NetApp Files účtu NetApp klikněte na **fondy kapacit**.

    ![Klikněte na fondy kapacit](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Klikněte na **+ Přidat fondy**.

    ![Klikněte na Přidat fondy.](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Zadejte informace pro fond kapacit:
    * Jako název fondu zadejte **mypool1** .
    * Jako úroveň služby vyberte **Premium** .
    * Jako velikost fondu zadejte **4 (TIB)** .
    * Použijte typ **auto** QoS.

5. Klikněte na **Vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definování některých nových proměnných pro budoucí referenci

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Vytvoření nového fondu kapacity pomocí [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definování některých nových proměnných pro budoucí referenci

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Vytvořte nový fond kapacit pomocí funkce [AZ netappfiles Pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) .

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Šablona](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Následující fragment kódu ukazuje, jak vytvořit fond kapacit v šabloně Azure Resource Manager (šablona ARM) pomocí prostředku [Microsoft. NetApp/netAppAccounts/capacityPools](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) . Pokud chcete spustit kód, Stáhněte si [úplnou šablonu ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z našeho úložiště GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Vytvořit svazek systému souborů NFS pro Azure NetApp Files

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V okně Správa Azure NetApp Files účtu NetApp klikněte na **svazky**.

    ![Klikněte na Svazky](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Klikněte na **+ Přidat svazek**.

    ![Klikněte na Přidat svazky.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. V okně vytvořit svazek zadejte informace o svazku:
   1. Jako název svazku zadejte **myvol1** .
   2. Vyberte fond kapacit ( **mypool1** ).
   3. Pro kvótu použijte výchozí hodnotu.
   4. V části virtuální síť klikněte na **vytvořit novou** a vytvořte novou virtuální síť Azure (VNET).  Pak vyplňte následující informace:
       * Jako název virtuální sítě zadejte **myvnet1** .
       * Zadejte adresní prostor pro vaše nastavení, například 10.7.0.0/16.
       * Jako název podsítě zadejte **myANFsubnet** .
       * Zadejte rozsah adres podsítě, například 10.7.0.0/24. Vyhrazenou podsíť nelze sdílet s jinými prostředky.
       * V případě delegování podsítě vyberte **Microsoft. NetApp/** Volumes (svazky).
       * Kliknutím na **OK** vytvořte virtuální síť.
   5. V části podsíť vyberte nově vytvořenou virtuální síť ( **myvnet1** ) jako podsíť delegáta.

      ![Vytvořit okno svazku](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Okno vytvořit virtuální síť](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Klikněte na **protokol** a pak proveďte následující akce:
    * Jako typ protokolu pro svazek vyberte **systém souborů NFS** .
    * Jako cestu k souboru zadejte **myfilepath1** , který se použije k vytvoření cesty pro export pro daný svazek.
    * Vyberte verzi systému souborů NFS ( **NFSv3** nebo **nfsv 4.1** ) pro svazek.
      Podívejte se na téma [posouzení](azure-netapp-files-create-volumes.md#considerations) a [osvědčené postupy](azure-netapp-files-create-volumes.md#best-practice) pro verze systému souborů NFS.

    ![Zadat protokol NFS pro rychlý Start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Zkontrolujte informace o svazku a pak klikněte na **vytvořit**.
    Vytvořený svazek se zobrazí v okně svazky.

    ![Svazek vytvořen](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pomocí příkazu [New-AzDelegation](/powershell/module/az.network/new-azdelegation) vytvořte delegování podsítě na Microsoft. NetApp/Volumes.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Vytvořte konfiguraci podsítě pomocí příkazu [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Vytvořte virtuální síť pomocí příkazu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Pomocí příkazu [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) vytvořte svazek.

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definování proměnných pro pozdější použití.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Pomocí příkazu [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create) vytvořte virtuální síť bez podsítě.

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Pomocí příkazu [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte delegovanou podsíť.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Pomocí příkazu [AZ netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) vytvořte svazek.

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Šablona](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Následující fragmenty kódu ukazují, jak nastavit virtuální síť a vytvořit Azure NetApp Files svazek v šabloně Azure Resource Manager (šablona ARM). Instalace virtuální sítě používá prostředek [Microsoft. Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) . Vytvoření svazku používá prostředek [Microsoft. NetApp/netAppAccounts/capacityPools/](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) Volumes. Pokud chcete spustit kód, Stáhněte si [úplnou šablonu ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z našeho úložiště GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky.

1. Do vyhledávacího pole Azure Portal zadejte **Azure NetApp Files** a v seznamu, který se zobrazí, vyberte **Azure NetApp Files** .

2. V seznamu odběrů klikněte na skupinu prostředků (myRG1), kterou chcete odstranit.

    ![Navigace do skupin prostředků](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stránce skupina prostředků klikněte na **Odstranit skupinu prostředků**.

    ![Snímek obrazovky, který zvýrazní tlačítko Odstranit skupinu prostředků.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Otevře se okno s upozorněním týkajícím se prostředků, které budou odstraněny s vybranou skupinou prostředků.

4. Zadejte název skupiny prostředků (myRG1), abyste potvrdili, že chcete trvale odstranit skupinu prostředků a všechny prostředky v ní, a pak klikněte na **Odstranit**.

    ![Potvrďte odstranění skupiny prostředků.](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky.

1. Odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky.

1. Odstraňte skupinu prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) .

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Šablona](#tab/template)

Žádné

K odstranění skupiny prostředků použijte Azure Portal, PowerShell nebo Azure CLI.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [Vytvoření svazku NFS](azure-netapp-files-create-volumes.md)
