---
title: Konfigurace replikace objektů
titleSuffix: Azure Storage
description: Naučte se konfigurovat replikaci objektů na asynchronní kopírování objektů blob bloku z kontejneru v jednom účtu úložiště do jiného.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 48831a9482087dbeed0952cc30fcbc9c14fbaed0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715631"
---
# <a name="configure-object-replication-for-block-blobs"></a>Konfigurace replikace objektů pro objekty blob bloku

Replikace objektů asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Další informace o replikaci objektů najdete v tématu [replikace objektů](object-replication-overview.md).

Když konfigurujete replikaci objektů, vytvoříte zásadu replikace, která určuje zdrojový a cílový účet úložiště. Zásada replikace obsahuje jedno nebo více pravidel, která určují zdrojový kontejner a cílový kontejner, a označuje, které objekty blob bloku ve zdrojovém kontejneru budou replikovány.

Tento článek popisuje, jak nakonfigurovat replikaci objektů pro svůj účet úložiště pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Ke konfiguraci replikace objektů můžete použít taky jednu z Azure Storage klientských knihoven poskytovatele prostředků.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Vytvoření zásad a pravidel pro replikaci

Před konfigurací replikace objektů vytvořte zdrojové a cílové účty úložiště, pokud ještě neexistují. Oba účty musí být účty úložiště pro obecné účely v2. Další informace najdete v tématu [Vytvoření účtu Azure Storage](../common/storage-account-create.md).

Replikace objektů vyžaduje, aby bylo pro zdrojový i cílový účet povoleno správu verzí objektů BLOB a aby byl pro zdrojový účet povolený informační kanál pro změny objektů BLOB. Další informace o tom, jak se správou verzí objektů blob, najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md). Další informace o službě Change feed najdete v tématu [Změna podpory kanálu v Azure Blob Storage](storage-blob-change-feed.md). Mějte na paměti, že povolení těchto funkcí může mít za následek další náklady.

Účet úložiště může sloužit jako zdrojový účet pro až dva cílové účty. Zdrojové a cílové účty můžou být ve stejné oblasti nebo v různých oblastech. Můžou se také nacházet v různých předplatných a v různých klientech Azure Active Directory (Azure AD). Pro každou dvojici účtů se dá vytvořit jenom jedna zásada replikace.

Při konfiguraci replikace objektů se v cílovém účtu vytvoří zásada replikace prostřednictvím poskytovatele prostředků Azure Storage. Po vytvoření zásady replikace jim Azure Storage přiřadí ID zásad. Pak musíte tyto zásady replikace přidružit ke zdrojovému účtu pomocí ID zásad. Aby replikace mohla probíhat, musí být ID zásad ve zdrojovém a cílovém účtu stejné.

Pokud chcete nakonfigurovat zásadu replikace objektu pro účet úložiště, musíte mít přiřazenou roli **přispěvatele** Azure Resource Manager vymezenou na úroveň účtu úložiště nebo vyšší. Další informace najdete v dokumentaci k [předdefinovaným rolím Azure](../../role-based-access-control/built-in-roles.md) v tématu řízení přístupu na základě role Azure (Azure RBAC).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Konfigurace replikace objektů, když máte přístup k oběma účtům úložiště

Pokud máte přístup ke zdrojovému i cílovému účtu úložiště, můžete nakonfigurovat zásady replikace objektů u obou účtů.

Před konfigurací replikace objektů v Azure Portal vytvořte zdrojové a cílové kontejnery v příslušných účtech úložiště, pokud ještě neexistují. Také povolte správu verzí objektů BLOB a změnu kanálu na zdrojovém účtu a povolte správu verzí objektů BLOB v cílovém účtu.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Po nakonfigurování pro cílový účet Azure Portal automaticky vytvoří zásadu na zdrojovém účtu.

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

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

1. Ve výchozím nastavení je obor kopírování nastaven na Kopírovat pouze nové objekty. Chcete-li zkopírovat všechny objekty v kontejneru nebo zkopírovat objekty počínaje vlastním datem a časem, vyberte odkaz **změnit** a nakonfigurujte obor kopírování pro dvojici kontejnerů.

    Následující obrázek ukazuje vlastní obor kopírování, který kopíruje objekty ze zadaného data a času.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

1. Vyberte **Save and Apply (Uložit a použít** ) a vytvořte zásadu replikace a spusťte replikaci dat.

Po nakonfigurování replikace objektů Azure Portal zobrazí zásadu a pravidla replikace, jak je znázorněno na následujícím obrázku.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit zásadu replikace pomocí PowerShellu, nejdřív nainstalujte verzi [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) nebo novější z modulu PowerShellu AZ. Storage. Další informace o instalaci Azure PowerShell najdete v tématu [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

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

Pokud chcete vytvořit zásadu replikace pomocí Azure CLI, nejdřív nainstalujte Azure CLI verze 2.11.1 nebo novější. Další informace najdete v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

V dalším kroku povolte správu verzí objektů blob na zdrojovém a cílovém účtu úložiště a povolte na zdrojovém účtu kanál změn pomocí příkazu [AZ Storage Account BLOB-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) . Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Vytvořte zdrojové a cílové kontejnery v příslušných účtech úložiště.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Vytvořte novou zásadu replikace a přidružené pravidlo pro cílový účet voláním [AZ Storage Account nebo-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Azure Storage nastaví ID zásad při vytvoření nové zásady. Pokud chcete do zásady přidat další pravidla, zavolejte [pravidlo AZ Storage Account nebo-Policy Add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) a poskytněte ID zásad.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

V dalším kroku vytvořte zásadu na zdrojovém účtu pomocí ID zásad.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Konfigurace replikace objektů, když máte přístup jenom k cílovému účtu

Pokud nemáte oprávnění ke zdrojovému účtu úložiště, můžete na cílovém účtu nakonfigurovat replikaci objektů a zadat soubor JSON, který obsahuje definici zásady, a vytvořit tak stejné zásady na zdrojovém účtu. Pokud je například zdrojový účet v jiném tenantovi služby Azure AD z cílového účtu, můžete tento přístup použít ke konfiguraci replikace objektů.

Mějte na paměti, že musíte mít přiřazenou roli **přispěvatele** Azure Resource Manager vymezenou na úrovni cílového účtu úložiště nebo vyšší, aby bylo možné zásadu vytvořit. Další informace najdete v dokumentaci k [předdefinovaným rolím Azure](../../role-based-access-control/built-in-roles.md) v tématu řízení přístupu na základě role Azure (Azure RBAC).

Následující tabulka shrnuje, které hodnoty se mají použít pro ID zásad a ID pravidel v souboru JSON v jednotlivých scénářích.

| Při vytváření souboru JSON pro tento účet... | Pro tuto hodnotu nastavte ID zásad a ID... |
|-|-|
| Cílový účet | Hodnota řetězce je *výchozí*. Azure Storage pro vás vytvoří ID zásad a ID pravidel. |
| Zdrojový účet | Hodnoty ID zásad a identifikátorů pravidel vrácených při stažení zásad definovaných v cílovém účtu jako soubor JSON. |

Následující příklad definuje zásadu replikace v cílovém účtu s jedním pravidlem, které odpovídá předponě *b* , a nastavuje minimální dobu vytváření objektů blob, které se mají replikovat. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Při konfiguraci replikace objektů na cílovém účtu se souborem JSON v Azure Portal postupujte takto:

1. Vytvořte místní soubor JSON, který definuje zásadu replikace v cílovém účtu. Nastavte pole **policyId** na **výchozí** hodnotu, aby Azure Storage definovat ID zásad.

    Snadný způsob, jak vytvořit soubor JSON, který definuje zásadu replikace, je nejdříve vytvořit zásadu pro replikaci testů mezi dvěma účty úložiště v Azure Portal. Pak můžete stáhnout pravidla replikace a podle potřeby upravit soubor JSON.

1. Přejděte do nastavení **replikace objektů** pro cílový účet v Azure Portal.
1. Vyberte **Odeslat pravidla replikace**.
1. Nahrajte soubor JSON. Azure Portal zobrazí zásady a pravidla, která budou vytvořena, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

1. Vyberte **Odeslat** a vytvořte tak zásadu replikace v cílovém účtu.

Pak můžete stáhnout soubor JSON obsahující definici zásady, kterou můžete zadat jinému uživateli ke konfiguraci zdrojového účtu. Pro stažení tohoto souboru JSON postupujte takto:

1. Přejděte do nastavení **replikace objektů** pro cílový účet v Azure Portal.
1. Vyberte tlačítko **Další** vedle zásady, kterou chcete stáhnout, a pak vyberte **Stáhnout pravidla**, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Snímek obrazovky zobrazující pravidla replikace v Azure Portal":::

1. Uložte soubor JSON do místního počítače, abyste ho mohli sdílet s jiným uživatelem a nakonfigurovat zásady na zdrojovém účtu.

Stažený soubor JSON obsahuje ID zásad, které Azure Storage vytvořili pro zásady na cílovém účtu. Ke konfiguraci replikace objektů na zdrojovém účtu musíte použít stejné ID zásad.

Pamatujte, že nahrání souboru JSON pro vytvoření zásad replikace pro cílový účet prostřednictvím Azure Portal nevytvoří automaticky stejné zásady ve zdrojovém účtu. Než Azure Storage začne replikovat objekty, musí na zdrojovém účtu vytvořit zásadu jiného uživatele.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete stáhnout soubor JSON, který obsahuje definici zásad replikace pro cílový účet z PowerShellu, zavolejte příkaz [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) , který vrátí zásady. Pak tuto zásadu převeďte na JSON a uložte ji jako místní soubor, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty v lomených závorkách a cestu k souboru vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Chcete-li použít soubor JSON ke konfiguraci zásad replikace ve zdrojovém účtu pomocí prostředí PowerShell, načtěte místní soubor a převeďte jej z formátu JSON na objekt. Pak zavolejte příkaz [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) a nakonfigurujte zásady na zdrojovém účtu, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty v lomených závorkách a cestu k souboru vlastními hodnotami:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit definici zásady replikace pro cílový účet do souboru JSON z Azure CLI, zavolejte příkaz [AZ Storage Account nebo-Policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) a výstup do souboru.

V následujícím příkladu je zapsána definice zásady do souboru JSON s názvem *policy.jsv*. Nezapomeňte nahradit hodnoty v lomených závorkách a cestu k souboru vlastními hodnotami:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Pokud chcete použít soubor JSON ke konfiguraci zásad replikace ve zdrojovém účtu pomocí Azure CLI, zavolejte příkaz [AZ Storage Account nebo-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) a odkazujte na *policy.jsv* souboru. Nezapomeňte nahradit hodnoty v lomených závorkách a cestu k souboru vlastními hodnotami:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
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
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Další kroky

- [Přehled replikace objektů](object-replication-overview.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
- [Zpracování kanálu změn ve službě Azure Blob Storage](storage-blob-change-feed-how-to.md)
