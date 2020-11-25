---
title: Připojení úložiště objektů BLOB v Azure pomocí protokolu NFS 3,0 (Preview) | Microsoft Docs
description: Naučte se připojit kontejner v úložišti objektů BLOB z virtuálního počítače Azure nebo klienta, který běží místně pomocí protokolu NFS 3,0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 7419e8667f07eec03e860634c7b3fddcac0e186b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95901549"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Připojení úložiště objektů BLOB pomocí protokolu NFS (Network File System) 3,0 (Preview)

Kontejner v úložišti objektů blob můžete připojit z virtuálního počítače Azure (VM) se systémem Windows nebo Linux nebo z systému Windows nebo Linux, který běží místně pomocí protokolu NFS 3,0. Tento článek poskytuje podrobné pokyny. Další informace o podpoře protokolů NFS 3,0 v BLOB Storage najdete v tématu [Podpora protokolu NFS (Network File System) 3,0 v Azure Blob Storage (Preview)](network-file-system-protocol-support.md).

> [!NOTE]
> Podpora protokolu NFS 3,0 v úložišti objektů BLOB v Azure je ve verzi Public Preview a je dostupná v těchto oblastech: USA – východ, USA – střed, USA – středozápad, Austrálie – jihovýchod, Severní Evropa, Velká Británie – západ, Korea – jih, Jižní Korea a Kanada – střed.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Krok 1: registrace funkce protokolu NFS 3,0 v rámci vašeho předplatného

1. Otevřete okno příkazového řádku PowerShellu. 

2. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

3. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

4. Zaregistrujte `AllowNFSV3` funkci pomocí následujícího příkazu.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Zaregistrujte `PremiumHns` funkci také pomocí následujícího příkazu.

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. Zaregistrujte poskytovatele prostředků pomocí následujícího příkazu.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Krok 2: Ověření registrace funkce 

Schválení registrace může trvat až hodinu. Chcete-li ověřit, zda byla registrace dokončena, použijte následující příkazy.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Krok 3: vytvoření Azure Virtual Network (virtuální síť)

Váš účet úložiště musí být obsažený v rámci virtuální sítě. Virtuální síť umožňuje klientům zabezpečené připojení k vašemu účtu úložiště. Další informace o virtuální síti a o tom, jak ji vytvořit, najdete v [dokumentaci k Virtual Network](../../virtual-network/index.yml).

> [!NOTE]
> Klienti ve stejné virtuální síti můžou ve svém účtu připojit kontejnery. Kontejner můžete také připojit z klienta, který běží v místní síti, ale budete muset nejdřív připojit místní síť k vaší virtuální síti. Viz [podporovaná síťová připojení](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Krok 4: Konfigurace zabezpečení sítě

Jediným způsobem, jak zabezpečit data v účtu, je použít virtuální síť a další nastavení zabezpečení sítě. Jakýkoli jiný nástroj, který se používá k zabezpečení dat, včetně ověřování klíčů účtu, Azure Active Directory (AD) a seznamů řízení přístupu (ACL), se zatím nepodporují v účtech s povolenou podporou protokolu NFS 3,0. 

Pokud chcete zabezpečit data v účtu, přečtěte si tato doporučení: [doporučení zabezpečení sítě pro úložiště objektů BLOB](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Krok 5: vytvoření a konfigurace účtu úložiště

Pokud chcete připojit kontejner pomocí NFS 3,0, musíte **po** registraci funkce u svého předplatného vytvořit účet úložiště. Nemůžete povolit účty, které existovaly před registrací funkce. 

V rámci verze Preview této funkce je protokol NFS 3,0 podporován pouze v účtech [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) .

Při konfiguraci účtu vyberte tyto hodnoty:

|Nastavení | Hodnota|
|----|---|
|Umístění|Jedna z následujících oblastí: USA – východ, USA – střed, USA – středozápad, Austrálie – jihovýchod, Severní Evropa, Velká Británie – západ, Korea – jih a Kanada – střed |
|Výkon|Premium|
|Druh účtu|BlockBlobStorage|
|Replikace|Místně redundantní úložiště (LRS)|
|Metoda připojení|Veřejný koncový bod (vybrané sítě) nebo soukromý koncový bod|
|Secure transfer required (Vyžádání bezpečného přenosu)|Zakázáno|
|Hierarchický obor názvů|Povoleno|
|SYSTÉM SOUBORŮ NFS V3|Povoleno|

Můžete přijmout výchozí hodnoty pro všechna ostatní nastavení. 

## <a name="step-6-create-a-container"></a>Krok 6: Vytvoření kontejneru

Vytvořte kontejner v účtu úložiště pomocí některé z těchto nástrojů nebo sad SDK:

|nástroje|Sady SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Krok 7: připojení kontejneru

Vytvořte v systému Windows nebo Linux adresář a pak připojte kontejner v účtu úložiště.

### <a name="linux"></a>[Linux](#tab/linux)

1. V systému Linux vytvořte adresář.

   ```
   mkdir -p /mnt/test
   ```

2. Připojte kontejner pomocí následujícího příkazu.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - `<storage-account-name>`Zástupný symbol, který se zobrazí v tomto příkazu, nahraďte názvem vašeho účtu úložiště.  

   - `<container-name>`Zástupný symbol nahraďte názvem vašeho kontejneru.


### <a name="windows"></a>[Windows](#tab/windows)

1. Otevřete dialogové okno **funkce systému Windows** a potom zapněte funkci **klient pro systém souborů NFS** . 

   ![Funkce Client for Network File System](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Připojte kontejner pomocí příkazu [Mount](/windows-server/administration/windows-commands/mount) .

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - `<storage-account-name>`Zástupný symbol, který se zobrazí v tomto příkazu, nahraďte názvem vašeho účtu úložiště.  

   - `<container-name>`Zástupný symbol nahraďte názvem vašeho kontejneru.

3. Pokud potřebujete oprávnění k zápisu, možná budete muset změnit výchozí UID a GID, které Windows používá pro připojení ke sdílené složce. Provedete to tak, že spustíte následující příkazy PowerShellu jako správce:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Po provedení této změny restartujte službu Klient systému souborů NFS nebo restartujte server.

---

## <a name="resolve-common-issues"></a>Řešení běžných problémů

|Problém/chyba | Řešení|
|---|---|
|`Access denied by server while mounting`|Ujistěte se, že je váš klient spuštěný v rámci podporované podsítě. Podívejte se na [podporovaná síťová umístění](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Ujistěte se, že se kontejner, který připojujete, vytvořil po ověření registrace funkce. Viz [Krok 2: ověření, zda je funkce zaregistrována](#step-2-verify-that-the-feature-is-registered). Nezapomeňte taky zadat příkaz Mount a jeho parametry přímo do terminálu. Pokud tento příkaz zkopírujete z jiné aplikace a vložíte do terminálu, skryté znaky ve vložených informacích můžou způsobit zobrazení této chyby.|

## <a name="see-also"></a>Viz také

[Podpora protokolů systému souborů NFS (Network File System) 3,0 v úložišti objektů BLOB v Azure (Preview)](network-file-system-protocol-support.md)