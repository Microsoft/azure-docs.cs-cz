---
title: 'Úvodní příručka: Nastavení souborů Azure NetApp a svazku systému souborů NFS'
description: Úvodní příručka – popisuje, jak rychle nastavit soubory Azure NetApp a vytvořit svazek.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 12/01/2019
ms.openlocfilehash: fc7f13fb7ffe1667aaeaa4a3cc1916c6049a98c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75551637"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Úvodní příručka: Nastavení souborů Azure NetApp a vytvoření svazku systému souborů NFS 

Tento článek ukazuje, jak rychle nastavit soubory Azure NetApp a vytvořit svazek. 

V tomto rychlém startu nastavíte následující položky:

- Registrace pro soubory Azure NetApp a zprostředkovatele prostředků NetApp
- Účet NetApp
- Kapacitní fond
- Svazek systému souborů NFS pro soubory Azure NetApp

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="before-you-begin"></a>Než začnete 

> [!IMPORTANT] 
> Musíte získat přístup ke službě Azure NetApp Files.  Pokud chcete požádat o přístup ke službě, podívejte se na [stránku odeslání seznamu čekácích na soubory Azure NetApp](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Před pokračováním musíte počkat na oficiální potvrzovací e-mail od týmu Azure NetApp Files. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrace pro soubory Azure NetApp a zprostředkovatele prostředků NetApp

> [!NOTE]
> Proces registrace může nějakou dobu trvat.
>

# <a name="portal"></a>[Portál](#tab/azure-portal)

Pro kroky registrace pomocí portálu otevřete relaci cloudového prostředí, jak je uvedeno výše, a postupujte podle těchto kroků příkazového příkazu k řešení Azure:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek s návody vyžaduje modul Azure PowerShell Az verze 2.6.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud chcete, můžete místo toho použít konzolu Cloud Shell v relaci PowerShellu.

1. V příkazovém řádku Prostředí PowerShell (nebo relace Prostředí PowerShell) zadejte předplatné, které bylo na seznamu povolených pro soubory Azure NetApp:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zaregistrujte poskytovatele prostředků Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Do vyhledávacího pole na webu Azure Portal zadejte **soubory Azure NetApp** a ze seznamu, který se zobrazí, vyberte **soubory Azure NetApp.**

      ![Výběr souborů Azure NetApp](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.

     ![Vytvořit nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. V okně Nový účet NetApp zadejte následující informace: 
   1. Zadejte **můj účet1** pro název účtu. 
   2. Vyberte své předplatné.
   3. Chcete-li vytvořit novou skupinu prostředků, vyberte **možnost Vytvořit nový.** Zadejte **myRG1** pro název skupiny prostředků. Klikněte na tlačítko **OK**. 
   4. Vyberte umístění účtu.  

      ![Okno Nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Chcete-li vytvořit nový účet NetApp, klepněte na **tlačítko Vytvořit.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definujte některé proměnné, abychom na ně mohli odkazovat ve zbývajících příkladech:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Seznam podporovaných oblastí naleznete v části [Produkty dostupné podle oblastí.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)
    > Chcete-li získat název oblasti, který je podporován našimi nástroji příkazového řádku, použijte`Get-AzLocation | select Location`
    >

1. Vytvořte novou skupinu prostředků pomocí příkazu [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Vytvořte účet Azure NetApp Files pomocí [příkazu New-AzNetAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definujte některé proměnné, abychom na ně mohli odkazovat ve zbývajících příkladech:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Seznam podporovaných oblastí naleznete v části [Produkty dostupné podle oblastí.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)
    > Chcete-li získat název oblasti, který je podporován našimi nástroji příkazového řádku, použijte`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Vytvořte novou skupinu prostředků pomocí příkazu [vytvořit skupinu az:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Vytvořit účet Azure NetApp Files pomocí [příkazu vytvořit účet az netappfiles:](/cli/azure/netappfiles/account#az-netappfiles-account-create)
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. V okně pro správu souborů Azure NetApp vyberte svůj účet NetApp **(myaccount1).**

    ![Vybrat účet NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. V okně správy souborů Azure NetApp vašeho účtu NetApp klikněte na **fondy kapacity**.

    ![Klikněte na fondy kapacity](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klikněte na **+ Přidat fondy**. 

    ![Klikněte na Přidat fondy.](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Uveďte informace pro fond kapacit: 
    1. Jako název fondu zadejte **mypool1.**
    2. Vyberte **Premium** pro úroveň služeb. 
    3. Zadejte **4 (TiB)** jako velikost fondu. 

5. Klikněte na tlačítko **OK**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definování některých nových proměnných pro budoucí použití

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Vytvoření nového fondu kapacit pomocí [fondu New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definování některých nových proměnných pro budoucí použití

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Vytvoření nového fondu kapacit pomocí [fondu az netappfiles vytvořit](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Vytvoření svazku systému souborů NFS pro soubory Azure NetApp

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. V okně správy souborů Azure NetApp vašeho účtu NetApp klikněte na **Svazky**.

    ![Klikněte na Svazky](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klikněte na **+ Přidat svazek**.

    ![Klikněte na Přidat svazky.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. V okně Vytvořit svazek zadejte informace o svazku: 
   1. Jako název svazku zadejte **myvol1.** 
   2. Vyberte svůj kapacitní fond **(mypool1).**
   3. Použijte výchozí hodnotu pro kvótu. 
   4. V části Virtuální síť klikněte na **Vytvořit nový** a vytvořte novou virtuální síť Azure (Vnet).  Poté vyplňte následující informace:
       * Zadejte **myvnet1** jako název sítě.
       * Zadejte adresní prostor pro nastavení, například 10.7.0.0/16
       * Jako název podsítě zadejte **podsíť myANF.**
       * Zadejte rozsah adres podsítě, například 10.7.0.0/24. Vyhrazenou podsíť nelze sdílet s jinými prostředky.
       * Vyberte **Microsoft.NetApp/svazky** pro delegování podsítě.
       * Chcete-li vytvořit virtuální síť, klepněte na tlačítko **OK.**
   5. V podsíti vyberte nově vytvořenou síť Vnet **(myvnet1**) jako podsíť delegáta.

      ![Vytvoření okna svazku](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Vytvořit okno virtuální sítě](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klepněte na **položku Protokol**a proveďte následující akce: 
    * Jako typ protokolu pro svazek vyberte možnost **NFS.**  
    * Jako cestu k souboru, která bude použita k vytvoření cesty exportu svazku, zadejte **myfilepath1.**  
    * Vyberte verzi systému nfs (**NFSv3** nebo **NFSv4.1**) pro svazek.  
      Podívejte se [na důležité informace](azure-netapp-files-create-volumes.md#considerations) a osvědčené [postupy](azure-netapp-files-create-volumes.md#best-practice) týkající se verzí systému nfs. 
      
  ![Zadání protokolu nfs pro rychlý start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Zkontrolujte informace o svazku a klepněte na tlačítko **Vytvořit**.  
    Vytvořený svazek se zobrazí v okně Svazky.

    ![Vytvořený svazek](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte delegování podsítě na "Microsoft.NetApp/volumes" pomocí [příkazu New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Vytvořte konfiguraci podsítě pomocí [příkazu New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Vytvořte virtuální síť pomocí příkazu [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Vytvořte svazek pomocí příkazu [New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)
   
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

1. Definování některých proměnných pro pozdější použití.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Vytvořte virtuální síť bez podsítě pomocí příkazu [vytvořit síť AZ.](/cli/azure/network/vnet#az-network-vnet-create)
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Vytvořte delegovanou podsíť pomocí [příkazu vytvořit podsíť sítě AZ.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Vytvořte svazek pomocí příkazu [az netappfiles volume create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) .
   
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

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

# <a name="portal"></a>[Portál](#tab/azure-portal)

Až budete hotovi a pokud chcete, můžete skupinu prostředků odstranit. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky ve skupinách prostředků budou trvale odstraněny a nelze je vrátit zpět. 

1. Do vyhledávacího pole na webu Azure Portal zadejte **soubory Azure NetApp** a ze seznamu, který se zobrazí, vyberte **soubory Azure NetApp.**

2. V seznamu odběrů klikněte na skupinu prostředků (myRG1), kterou chcete odstranit. 

    ![Přechod na skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stránce skupiny prostředků klikněte na **Odstranit skupinu prostředků**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Otevře se okno s upozorněním týkajícím se prostředků, které budou odstraněny s vybranou skupinou prostředků.

4. Zadejte název skupiny prostředků (myRG1), potvrďte, že chcete trvale odstranit skupinu prostředků a všechny prostředky v ní, a klepněte na tlačítko **Odstranit**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Až budete hotovi a pokud chcete, můžete skupinu prostředků odstranit. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky ve skupinách prostředků budou trvale odstraněny a nelze je vrátit zpět.

1. Odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Až budete hotovi a pokud chcete, můžete skupinu prostředků odstranit. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky ve skupinách prostředků budou trvale odstraněny a nelze je vrátit zpět.

1. Odstranit skupinu prostředků pomocí příkazu [delete skupiny az.](/cli/azure/group#az-group-delete)
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Správa svazků pomocí souborů Azure NetApp](azure-netapp-files-manage-volumes.md)  
