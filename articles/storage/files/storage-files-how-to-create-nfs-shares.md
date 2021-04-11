---
title: Vytvoření sdílené složky NFS – soubory Azure (Preview)
description: Naučte se, jak vytvořit sdílenou složku Azure, kterou je možné připojit pomocí protokolu síťového systému souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521233"
---
# <a name="how-to-create-an-nfs-share"></a>Postup vytvoření sdílené složky systému souborů NFS
Sdílené složky Azure jsou plně spravované sdílené složky, které jsou v cloudu živé. Tento článek popisuje vytvoření sdílené složky, která používá protokol NFS. Další informace o obou protokolech najdete v tématu [protokoly sdílení souborů Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Omezení
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Požadavky
- Ke sdíleným složkám NFS se dá dostat jenom z důvěryhodných sítí. Připojení ke sdílené složce systému souborů NFS musí pocházet z jednoho z následujících zdrojů:
    - Buď [vytvořte privátní koncový bod](storage-files-networking-endpoints.md#create-a-private-endpoint) (doporučeno), nebo [omezte přístup ke svému veřejnému koncovému bodu](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Nakonfigurujte síť VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Nakonfigurujte síť VPN typu Site-to-site pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).
    - Nakonfigurujte [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrace protokolu NFS 4,1
Pokud používáte modul Azure PowerShell nebo rozhraní příkazového řádku Azure CLI, zaregistrujte svou funkci pomocí následujících příkazů:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
K registraci funkce NFS 4,1 pro soubory Azure použijte buď Azure PowerShell, nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

Schválení registrace může trvat až hodinu. Chcete-li ověřit, zda byla registrace dokončena, použijte následující příkazy:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pomocí Azure PowerShell nebo Azure CLI ověřte registraci funkce NFS 4,1 pro soubory Azure. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Vytvoření účtu úložiště úložiště
Sdílené složky NFS 4,1 jsou v současné době k dispozici pouze jako sdílené složky prémiových souborů. Pokud chcete nasadit prémiovou sdílenou složku s podporou protokolu NFS 4,1, musíte nejdřív vytvořit účet úložiště úložiště. Účet úložiště je objekt nejvyšší úrovně v Azure, který představuje sdílený fond úložiště, který se dá použít k nasazení několika sdílených složek Azure.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete vytvořit účet úložiště úložiště, přejděte na Azure Portal.

1. V Azure Portal v nabídce vlevo vyberte **účty úložiště** .

    ![Azure Portal hlavní stránka – Výběr účtu úložiště](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
3. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
4. Vyberte skupinu prostředků, ve které se má účet úložiště vytvořit.

5. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
6. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.
7. Pro **výkon** vyberte **Premium**.

    V rozevíracím seznamu **druh účtu** musíte vybrat možnost **Premium** for **Storage** jako dostupnou.

8. Vyberte **druh účtu** a zvolte **úložiště**.
9. Ponechte **replikační** sadu nastavenou na výchozí hodnotu **místně redundantního úložiště (LRS)**.

    ![Jak vytvořit účet úložiště pro sdílenou složku Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.
11. Vyberte **Vytvořit**.

Po vytvoření prostředku účtu úložiště přejděte na něj.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete vytvořit účet úložiště úložiště, otevřete příkazový řádek PowerShellu a proveďte následující příkazy, které zapamatujete a nahraďte `<resource-group>` `<storage-account>` odpovídajícími hodnotami pro vaše prostředí.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete vytvořit účet úložiště úložiště, otevřete terminál a spusťte následující příkazy, které zapamatujete a nahraďte `<resource-group>` `<storage-account>` odpovídajícími hodnotami pro vaše prostředí.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>Vytvoření sdílené složky NFS

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Teď, když jste vytvořili účet úložiště souborů a nakonfigurovali síť, můžete vytvořit sdílenou složku NFS. Proces se podobá vytvoření sdílené složky SMB a při vytváření sdílené složky se místo **protokolu SMB** vybere **systém souborů NFS** .

1. Přejděte do svého účtu úložiště a vyberte **sdílené složky**.
1. Pokud chcete vytvořit novou sdílenou složku, vyberte **+ Shared File** .
1. Zadejte název sdílené složky a vyberte zřízenou kapacitu.
1. V případě **protokolu** vyberte **systém souborů NFS (Preview)**.
1. Pro **kořenový "squash** udělejte výběr.

    - Root "squash (výchozí) – přístup pro vzdáleného uživatele (root) je namapován na UID (65534) a GID (65534).
    - Žádná kořenová "squash – vzdálený uživatel (root) obdrží přístup jako kořen.
    - Všechny "squash – přístup všech uživatelů je namapován na UID (65534) a GID (65534).
    
1. Vyberte **Vytvořit**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Snímek obrazovky okna pro vytvoření sdílené složky":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ujistěte se, že je nainstalováno rozhraní .NET Framework. Viz [stažení .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Pomocí následujícího příkazu ověřte, že verze prostředí PowerShell, která je nainstalovaná, je `5.1` nebo vyšší.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) .
    
1. Nainstalujte nejnovější verzi modulu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zavřete a znovu otevřete konzolu PowerShellu.

1. Nainstalujte modul **AZ. Storage** Preview verze **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .
   
1. K vytvoření sdílené složky Premium pomocí modulu Azure PowerShell použijte rutinu [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Soubory úrovně Premium se účtují pomocí zřízeného modelu. Zřízená velikost sdílené složky je určená `QuotaGiB` níže. Další informace najdete v tématu [Princip zřizovacího modelu](understanding-billing.md#provisioned-model) a stránky s [cenami za soubory Azure](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete vytvořit sdílenou složku Premium pomocí Azure CLI, použijte příkaz [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Soubory úrovně Premium se účtují pomocí zřízeného modelu. Zřízená velikost sdílené složky je určená `quota` níže. Další informace najdete v tématu [Princip zřizovacího modelu](understanding-billing.md#provisioned-model) a stránky s [cenami za soubory Azure](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili sdílenou složku NFS, budete ji muset připojit na svém klientském počítači se systémem Linux. Podrobnosti najdete v tématu [Postup připojení sdílené složky systému souborů NFS](storage-files-how-to-mount-nfs-shares.md).

Pokud se setkáte s problémy, přečtěte si téma [řešení potíží s sdílenými složkami souborů Azure NFS](storage-troubleshooting-files-nfs.md)