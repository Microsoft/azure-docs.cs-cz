---
title: Konfigurace replikace objektů (Preview)
titleSuffix: Azure Storage
description: Naučte se konfigurovat replikaci objektů na asynchronní kopírování objektů blob bloku z kontejneru v jednom účtu úložiště do jiného.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9964a10b85d9f388a4498766c6e81ee52498aa37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076170"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Konfigurace replikace objektů pro objekty blob bloku (Preview)

Replikace objektů (Preview) asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Další informace o replikaci objektů najdete v tématu [replikace objektů (Preview)](object-replication-overview.md).

Když konfigurujete replikaci objektů, vytvoříte zásadu replikace, která určuje zdrojový a cílový účet úložiště. Zásada replikace obsahuje jedno nebo více pravidel, která určují zdrojový kontejner a cílový kontejner, a označuje, které objekty blob bloku ve zdrojovém kontejneru budou replikovány.

Tento článek popisuje, jak nakonfigurovat replikaci objektů pro svůj účet úložiště pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Ke konfiguraci replikace objektů můžete použít taky jednu z Azure Storage klientských knihoven poskytovatele prostředků.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Vytvoření zásad a pravidel pro replikaci

Před konfigurací replikace objektů vytvořte zdrojové a cílové účty úložiště, pokud ještě neexistují. Oba účty musí být účty úložiště pro obecné účely v2. Další informace najdete v tématu [Vytvoření účtu Azure Storage](../common/storage-account-create.md).

Účet úložiště může sloužit jako zdrojový účet pro až dva cílové účty. A cílový účet pravděpodobně nemá více než dva zdrojové účty. Zdrojové a cílové účty se můžou nacházet v různých oblastech. Můžete nakonfigurovat samostatné zásady replikace pro replikaci dat do každého cílového účtu.

Než začnete, ujistěte se, že jste se zaregistrovali v následujících náhledech funkcí:

- [Replikace objektů (Preview)](object-replication-overview.md)
- [Správa verzí objektů BLOB (Preview)](versioning-overview.md)
- [Změna podpory kanálu v Azure Blob Storage (Preview)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Před konfigurací replikace objektů v Azure Portal vytvořte zdrojové a cílové kontejnery v příslušných účtech úložiště, pokud ještě neexistují. Také povolte správu verzí objektů BLOB a kanál změn ve zdrojovém účtu a povolte správu verzí objektů BLOB v cílovém účtu.

Chcete-li vytvořit zásadu replikace v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte do zdrojového účtu úložiště.
1. V části **BLOB Service**vyberte **replikace objektů**.
1. Vyberte **nastavit pravidla replikace**.
1. Vyberte cílové předplatné a účet úložiště.
1. V části **páry kontejnerů** vyberte zdrojový kontejner ze zdrojového účtu a cílový kontejner z cílového účtu. Na jednu zásadu replikace můžete vytvořit až 10 párů kontejnerů.

    Následující obrázek ukazuje sadu pravidel replikace.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

1. V případě potřeby zadejte jeden nebo více filtrů, aby bylo možné kopírovat pouze objekty blob, které odpovídají vzoru předpony. Pokud například zadáte předponu `b` , budou replikovány pouze objekty blob, jejichž název začíná tímto písmenem. Jako součást předpony můžete zadat virtuální adresář. Řetězec předpony nepodporuje zástupné znaky.

    Následující obrázek ukazuje filtry, které omezují, které objekty BLOB se zkopírují jako součást pravidla replikace.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Snímek obrazovky znázorňující filtry pro pravidlo replikace":::

1. Ve výchozím nastavení je obor kopírování nastaven na Kopírovat pouze nové objekty. Chcete-li zkopírovat všechny objekty v kontejneru nebo zkopírovat objekty počínaje vlastním datem a časem, vyberte odkaz **změnit** a nakonfigurujte obor kopírování pro dvojici kontejnerů.

    Následující obrázek ukazuje vlastní obor kopírování.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Snímek obrazovky zobrazující vlastní obor kopírování pro replikaci objektů":::

1. Vyberte **Save and Apply (Uložit a použít** ) a vytvořte zásadu replikace a spusťte replikaci dat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit zásadu replikace pomocí PowerShellu, nejdřív nainstalujte verzi [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) nebo novější z modulu PowerShellu AZ. Storage. Pomocí těchto kroků nainstalujete modul verze Preview:

1. Odinstalujte všechny předchozí instalace Azure PowerShell ze systému Windows pomocí nastavení **aplikace & funkce** v části **Nastavení**.

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a spuštěním následujícího příkazu nainstalujte nejnovější verzi:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Po instalaci PowerShellGet zavřete a znovu otevřete okno PowerShellu.

1. Nainstalujte nejnovější verzi Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalace modulu AZ. Storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Další informace o instalaci Azure PowerShell najdete v tématu [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

Následující příklad ukazuje, jak vytvořit zásadu replikace na zdrojovém a cílovém účtu. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit zásadu replikace pomocí Azure CLI, nainstalujte nejdřív rozšíření Preview pro Azure Storage.:

```azurecli
az extension add -n storage-or-preview
```

Potom se přihlaste pomocí přihlašovacích údajů Azure:

```azurecli
az login
```

Povolte na zdrojovém a cílovém účtu úložiště správu verzí objektů BLOB a povolte na zdrojovém účtu kanál změn. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Vytvořte zdrojové a cílové kontejnery v příslušných účtech úložiště.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

Vytvořte novou zásadu replikace a související pravidla v cílovém účtu.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Vytvořte zásadu na zdrojovém účtu pomocí ID zásad.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Odebrat zásadu replikace

Pokud chcete odebrat zásadu replikace a její přidružená pravidla, použijte Azure Portal, PowerShell nebo rozhraní příkazového řádku.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete odebrat zásadu replikace v Azure Portal, postupujte takto:

1. V Azure Portal přejděte do zdrojového účtu úložiště.
1. V části **Nastavení**vyberte **replikace objektů**.
1. Klikněte na tlačítko **Další** vedle názvu zásady.
1. Vyberte **Odstranit pravidla**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete odebrat zásadu replikace, odstraňte zásadu ze zdrojového účtu i cílového účtu. Odstraněním zásady se odstraní také všechna pravidla, která jsou k němu přidružená.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odebrat zásadu replikace, odstraňte zásadu ze zdrojového účtu i cílového účtu. Odstraněním zásady se odstraní také všechna pravidla, která jsou k němu přidružená.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Další kroky

- [Přehled replikace objektů (Preview)](object-replication-overview.md)
