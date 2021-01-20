---
title: Vytvoření sdílené složky NFS – soubory Azure (Preview)
description: Naučte se, jak vytvořit sdílenou složku Azure, kterou je možné připojit pomocí protokolu síťového systému souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602669"
---
# <a name="how-to-create-an-nfs-share"></a>Postup vytvoření sdílené složky systému souborů NFS

Sdílené složky Azure jsou plně spravované sdílené složky, které jsou v cloudu živé. K nim lze přistupovat buď pomocí protokolu protokolu serveru Message Block, nebo protokolu NFS (Network File System). Tento článek popisuje vytvoření sdílené složky, která používá protokol NFS. Další informace o obou protokolech najdete v tématu [protokoly sdílení souborů Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Omezení

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Požadavky

- Vytvořte [účet úložiště](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > Ke sdíleným složkám NFS se dá dostat jenom z důvěryhodných sítí. Připojení ke sdílené složce systému souborů NFS musí pocházet z jednoho z následujících zdrojů:

    - Buď [vytvořte privátní koncový bod](storage-files-networking-endpoints.md#create-a-private-endpoint) (doporučeno), nebo [omezte přístup ke svému veřejnému koncovému bodu](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Nakonfigurujte síť VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Nakonfigurujte síť VPN typu Site-to-site pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).
    - Nakonfigurujte [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registrace protokolu NFS 4,1

Pokud používáte modul Azure PowerShell nebo rozhraní příkazového řádku Azure CLI, zaregistrujte svou funkci pomocí následujících příkazů:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>Ověřit registraci funkce

Schválení registrace může trvat až hodinu. Chcete-li ověřit, zda byla registrace dokončena, použijte následující příkazy:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>Ověřit druh účtu úložiště

V současné době můžou sdílené složky systému souborů NFS vytvářet jenom účty úložiště. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete ověřit, jaký druh účtu úložiště máte, přejděte k němu v Azure Portal. Pak z účtu úložiště vyberte **vlastnosti**. V okně Vlastnosti zkontrolujte hodnotu v části **druh účtu**. Tato hodnota by měla být **Storage**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete ověřit, že máte účet úložiště, můžete použít následující příkaz:

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

Výstup by měl být **úložiště**, pokud není, váš účet úložiště je nesprávného typu. Pokud chcete vytvořit účet **úložiště** souborů, přečtěte si téma [jak vytvořit sdílenou složku Azure Premium](storage-how-to-create-premium-fileshare.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pokud chcete ověřit, že máte účet úložiště, můžete použít následující příkaz:

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

Výstup by měl obsahovat **"druh": "Storage**", pokud ne, váš účet úložiště je nesprávného typu. Pokud chcete vytvořit účet **úložiště** souborů, přečtěte si téma [jak vytvořit sdílenou složku Azure Premium](storage-how-to-create-premium-fileshare.md).

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
    
   Pokud chcete upgradovat verzi PowerShellu, přečtěte si téma [upgrade existujícího prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) .
    
1. Nainstalujte nejnovější verzi modulu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zavřete a znovu otevřete konzolu PowerShellu.

1. Nainstalujte modul **AZ. Storage** Preview verze **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0) .
   
1. K vytvoření sdílené složky Premium pomocí modulu Azure PowerShell použijte rutinu [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Velikost zřízených sdílených složek je určena kvótou sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit sdílenou složku Premium pomocí Azure CLI, použijte příkaz [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Velikost zřízených sdílených složek je určena kvótou sdílené složky. sdílené složky se účtují podle zřízené velikosti. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili sdílenou složku NFS, budete ji muset připojit na svém klientském počítači se systémem Linux. Podrobnosti najdete v tématu [Postup připojení sdílené složky systému souborů NFS](storage-files-how-to-mount-nfs-shares.md).

Pokud se setkáte s problémy, přečtěte si téma [řešení potíží s sdílenými složkami souborů Azure NFS](storage-troubleshooting-files-nfs.md)
