---
title: Vytváření a Správa oborů šifrování (Preview)
description: Naučte se vytvořit rozsah šifrování pro izolaci dat objektů blob na úrovni kontejneru nebo objektu BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d5590ff275ce821c81f5751f4d92972c49adaafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209587"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>Vytváření a Správa oborů šifrování (Preview)

Obory šifrování (Preview) umožňují spravovat šifrování na úrovni jednotlivých objektů BLOB nebo kontejnerů. Obor šifrování izoluje data objektu blob v zabezpečené enklávě v rámci účtu úložiště. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům. Další informace o oborech šifrování najdete v tématu [obory šifrování pro úložiště objektů BLOB (Preview)](encryption-scope-overview.md).

Tento článek ukazuje, jak vytvořit rozsah šifrování. Také ukazuje, jak určit rozsah šifrování při vytváření objektu BLOB nebo kontejneru.

> [!IMPORTANT]
> Obory šifrování jsou momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.
>
> Chcete-li se vyhnout neočekávaným nákladům, je nutné zakázat všechny obory šifrování, které aktuálně nepotřebujete.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Vytvoření oboru šifrování

Rozsah šifrování můžete vytvořit pomocí klíče spravovaného společností Microsoft nebo pomocí klíče spravovaného zákazníkem, který je uložený v Azure Key Vault nebo Azure Key Vault modelu HSM (Managed hardware Security model) (ve verzi Preview). Pokud chcete vytvořit rozsah šifrování s klíčem spravovaným zákazníkem, musíte nejdřív vytvořit Trezor klíčů nebo spravovaný modul HSM a přidat klíč, který chcete použít pro tento obor. Trezor klíčů nebo spravovaný modul HSM musí mít povolenou ochranu vyprázdnění a musí být ve stejné oblasti jako účet úložiště.

Rozsah šifrování je automaticky povolen při jeho vytváření. Po vytvoření oboru šifrování ho můžete zadat při vytváření objektu BLOB. Výchozí rozsah šifrování můžete zadat také při vytváření kontejneru, který se automaticky použije u všech objektů BLOB v kontejneru.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete vytvořit rozsah šifrování v Azure Portal, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. Vyberte nastavení **šifrování** .
1. Vyberte kartu **obory šifrování** .
1. Kliknutím na tlačítko **Přidat** přidejte nový rozsah šifrování.
1. V podokně vytvořit **obor šifrování** zadejte název nového oboru.
1. Vyberte typ šifrování, buď **klíče spravované společností Microsoft** , nebo **klíče spravované zákazníkem**.
    - Pokud jste vybrali **klíče spravované společností Microsoft**, kliknutím na **vytvořit** vytvořte rozsah šifrování.
    - Pokud jste vybrali **klíče spravované zákazníkem**, zadejte Trezor klíčů nebo spravovaný modul HSM, klíč a verzi klíče, který chcete použít pro tento obor šifrování, jak je znázorněno na následujícím obrázku.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Snímek obrazovky znázorňující vytvoření oboru šifrování v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit rozsah šifrování pomocí PowerShellu, nejdřív nainstalujte verzi modulu AZ. Storage Preview. Doporučuje se použít nejnovější verzi Preview, ale obory šifrování se podporují ve verzi 1.13.4-Preview a novější. Odeberte jakékoli jiné verze modulu AZ. Storage.

Následující příkaz nainstaluje AZ. Storage [2.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) Module:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Vytvoří rozsah šifrování chráněný klíči spravovanými Microsoftem.

Pokud chcete vytvořit nový rozsah šifrování, který je chráněný pomocí klíčů spravovaných Microsoftem, zavolejte příkaz **New-AzStorageEncryptionScope** s `-StorageEncryption` parametrem.

Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Vytvoření rozsahu šifrování chráněného zákazníky spravovanými klíči

Pokud chcete vytvořit nový obor šifrování, který je chráněný pomocí klíčů spravovaných zákazníkem, které jsou uložené v trezoru klíčů nebo spravovaném HSM, nejdřív nakonfigurujte klíče spravované zákazníkem pro účet úložiště. K účtu úložiště musíte přiřadit spravovanou identitu a potom pomocí spravované identity nakonfigurovat zásady přístupu pro Trezor klíčů nebo spravovaný HSM, aby měl účet úložiště oprávnění k přístupu.

Aby bylo možné konfigurovat klíče spravované zákazníkem pro použití s oborem šifrování, musí být v trezoru klíčů nebo spravovaném modulu HSM povolena ochrana vyprázdnění. Trezor klíčů nebo spravovaný modul HSM musí být ve stejné oblasti jako účet úložiště.

Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Dále zavolejte příkaz **New-AzStorageEncryptionScope** s `-KeyvaultEncryption` parametrem a zadejte identifikátor URI klíče. Nezapomeňte zahrnout verzi klíče do identifikátoru URI klíče. Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete vytvořit rozsah šifrování pomocí Azure CLI, nejdřív nainstalujte Azure CLI verze 2.4.0 nebo novější.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Vytvoří rozsah šifrování chráněný klíči spravovanými Microsoftem.

Pokud chcete vytvořit nový obor šifrování, který je chráněný pomocí klíčů spravovaných Microsoftem, zavolejte na příkaz [AZ Storage Account Encryption-Scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) a určete `--key-source` parametr jako `Microsoft.Storage` . Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Vytvoření rozsahu šifrování chráněného zákazníky spravovanými klíči

Pokud chcete vytvořit nový obor šifrování, který je chráněný pomocí klíčů spravovaných Microsoftem, zavolejte na příkaz [AZ Storage Account Encryption-Scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) a určete `--key-source` parametr jako `Microsoft.Storage` . Nezapomeňte nahradit hodnoty zástupných symbolů vlastními hodnotami:

Pokud chcete vytvořit nový rozsah šifrování, který je chráněný pomocí klíčů spravovaných zákazníkem v trezoru klíčů nebo spravovaném HSM, nejdřív nakonfigurujte klíče spravované zákazníkem pro účet úložiště. K účtu úložiště musíte přiřadit spravovanou identitu a potom pomocí spravované identity nakonfigurovat zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. Další informace najdete v tématu [klíče spravované zákazníkem pro Azure Storage šifrování](../common/customer-managed-keys-overview.md).

Aby bylo možné konfigurovat klíče spravované zákazníkem pro použití s oborem šifrování, musí být v trezoru klíčů nebo spravovaném modulu HSM povolena ochrana vyprázdnění. Trezor klíčů nebo spravovaný modul HSM musí být ve stejné oblasti jako účet úložiště.

Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

V dalším kroku zavolejte příkaz **AZ Storage Account Encryption-Scope Create** s `--key-uri` parametrem a zadejte identifikátor URI klíče. Nezapomeňte zahrnout verzi klíče do identifikátoru URI klíče. Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Informace o tom, jak nakonfigurovat šifrování Azure Storage pomocí klíčů spravovaných zákazníkem v trezoru klíčů, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md). Informace o konfiguraci klíčů spravovaných zákazníkem ve spravovaném modulu HSM najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravované HSM (Preview)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Vypsat obory šifrování pro účet úložiště

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete zobrazit obory šifrování pro účet úložiště v Azure Portal, přejděte do nastavení **obory šifrování** pro účet úložiště. V tomto podokně můžete povolit nebo zakázat rozsah šifrování nebo změnit klíč pro rozsah šifrování.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Snímek obrazovky zobrazující seznam rozsahů šifrování v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zobrazit seznam rozsahů šifrování dostupných pro účet úložiště pomocí PowerShellu, zavolejte příkaz **Get-AzStorageEncryptionScope** . Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Pokud chcete zobrazit seznam všech rozsahů šifrování ve skupině prostředků podle účtu úložiště, použijte syntaxi kanálu:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete zobrazit seznam rozsahů šifrování dostupných pro účet úložiště pomocí Azure CLI, zavolejte příkaz [AZ Storage Account Encryption-Scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) . Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Vytvoření kontejneru s výchozím rozsahem šifrování

Při vytváření kontejneru můžete zadat výchozí rozsah šifrování. Objekty BLOB v tomto kontejneru budou ve výchozím nastavení používat tento obor.

Jednotlivý objekt BLOB se dá vytvořit s vlastním oborem šifrování, pokud není nakonfigurovaný kontejner tak, aby vyžadoval, aby všechny objekty blob používaly výchozí obor.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete vytvořit kontejner s výchozím rozsahem šifrování v Azure Portal, vytvořte nejprve rozsah šifrování, jak je popsáno v tématu [Vytvoření oboru šifrování](#create-an-encryption-scope). Potom postupujte podle těchto kroků a vytvořte kontejner:

1. Přejděte do seznamu kontejnerů ve vašem účtu úložiště a vyberte tlačítko **Přidat** a vytvořte nový kontejner.
1. Rozbalte **Upřesnit** nastavení v podokně **Nový kontejner** .
1. V rozevíracím seznamu **Rozsah šifrování** vyberte výchozí rozsah šifrování pro kontejner.
1. Pokud chcete vyžadovat, aby všechny objekty BLOB v kontejneru používaly výchozí rozsah šifrování, zaškrtněte políčko pro **použití tohoto oboru šifrování pro všechny objekty BLOB v kontejneru**. Pokud je toto políčko zaškrtnuté, nemůže jednotlivý objekt BLOB v kontejneru přepsat výchozí rozsah šifrování.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Snímek obrazovky s kontejnerem s výchozím rozsahem šifrování":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit kontejner s výchozím rozsahem šifrování pomocí PowerShellu, zavolejte příkaz [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) , který určí rozsah `-DefaultEncryptionScope` parametru. Příkaz **New-AzRmStorageContainer** vytvoří kontejner pomocí poskytovatele prostředků Azure Storage, který umožňuje konfiguraci oborů šifrování a dalších operací správy prostředků.

Chcete-li vynutit všechny objekty BLOB v kontejneru pro použití výchozího oboru kontejneru, nastavte `-PreventEncryptionScopeOverride` parametr na `true` .

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete vytvořit kontejner s výchozím rozsahem šifrování pomocí Azure CLI, zavolejte příkaz [AZ Storage Container Create](/cli/azure/storage/container#az-storage-container-create) a určete tak rozsah `--default-encryption-scope` parametru. Chcete-li vynutit všechny objekty BLOB v kontejneru pro použití výchozího oboru kontejneru, nastavte `--prevent-encryption-scope-override` parametr na `true` .

Následující příklad používá účet Azure AD k autorizaci operace vytvoření kontejneru. Můžete použít i přístupový klíč účtu. Další informace najdete v tématu [autorizace přístupu k datům BLOB nebo Queue pomocí Azure CLI](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Pokud se klient pokusí zadat obor při nahrávání objektu blob do kontejneru, který má výchozí rozsah šifrování, a je nakonfigurován tak, aby zabránil objektům blob v přepsání výchozího oboru, operace se nezdařila a zobrazí se zpráva oznamující, že je požadavek zakázán zásadami šifrování kontejneru.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Nahrání objektu BLOB s rozsahem šifrování

Když nahráváte objekt blob, můžete pro tento objekt BLOB zadat rozsah šifrování nebo pro kontejner použít výchozí rozsah šifrování, pokud je zadaný.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete nahrát objekt BLOB s rozsahem šifrování zadaným v Azure Portal, vytvořte nejprve rozsah šifrování, jak je popsáno v tématu [Vytvoření oboru šifrování](#create-an-encryption-scope). Potom postupujte podle těchto kroků a vytvořte objekt BLOB:

1. Přejděte do kontejneru, do kterého chcete nahrát objekt BLOB.
1. Vyberte tlačítko **nahrát** a vyhledejte objekt blob, který se má nahrát.
1. V podokně **nahrát objekt BLOB** rozbalte **Rozšířená** nastavení.
1. Najděte rozevírací oddíl **Rozsah šifrování** . Ve výchozím nastavení je objekt BLOB vytvořen s výchozím rozsahem šifrování pro kontejner, pokud byl zadán. Pokud kontejner vyžaduje, aby objekty blob používaly výchozí obor šifrování, je tato část zakázaná.
1. Pokud chcete pro objekt blob, který nahráváte, zadat jiný obor, vyberte **Zvolit existující obor** a v rozevíracím seznamu vyberte požadovaný obor.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Snímek obrazovky ukazující, jak nahrát objekt BLOB s rozsahem šifrování":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete nahrát objekt BLOB s oborem šifrování zadaným pomocí PowerShellu, zavolejte příkaz [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) a zadejte obor šifrování pro objekt BLOB.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete nahrát objekt BLOB s oborem šifrování zadaným pomocí Azure CLI, zavolejte příkaz [AZ Storage BLOB upload](/cli/azure/storage/blob#az-storage-blob-upload) a zadejte rozsah šifrování pro objekt BLOB.

Pokud používáte Azure Cloud Shell, postupujte podle kroků popsaných v části [nahrání objektu BLOB](storage-quickstart-blobs-cli.md#upload-a-blob) a vytvoření souboru v kořenovém adresáři. Tento soubor pak můžete nahrát do objektu BLOB pomocí následující ukázky.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Změna šifrovacího klíče pro obor

Pokud chcete změnit klíč, který chrání obor šifrování z klíče spravovaného Microsoftem na klíč spravovaný zákazníkem, nejdřív se ujistěte, že jste povolili klíče spravované zákazníky pomocí Azure Key Vault nebo Key Vault HSM pro účet úložiště. Další informace najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) nebo [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Azure Portal](#tab/portal)

Chcete-li změnit klíč, který chrání obor v Azure Portal, postupujte podle následujících kroků:

1. Přejděte na kartu **obory šifrování** a zobrazte seznam oborů šifrování pro účet úložiště.
1. Vyberte tlačítko **Další** vedle oboru, který chcete upravit.
1. V podokně **Upravit rozsah šifrování** můžete změnit typ šifrování z klíče spravovaného společností Microsoft na klíč spravovaný zákazníkem nebo naopak.
1. Pokud chcete vybrat nový klíč spravovaný zákazníkem, vyberte **použít nový klíč** a zadejte Trezor klíčů, klíč a verzi klíče.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete změnit klíč, který chrání obor šifrování z klíče spravovaného zákazníkem na klíč spravovaný společností Microsoft pomocí PowerShellu, zavolejte příkaz **Update-AzStorageEncryptionScope** a předejte `-StorageEncryption` parametr:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Dále zavolejte příkaz **Update-AzStorageEncryptionScope** a předejte `-KeyUri` `-KeyvaultEncryption` parametry a:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete změnit klíč, který chrání obor šifrování z klíče spravovaného zákazníkem na klíč spravovaný společností Microsoft pomocí Azure CLI, zavolejte příkaz [AZ Storage Account Encryption-Scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) a předejte `--key-source` parametr s hodnotou `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

V dalším kroku zavolejte příkaz **AZ Storage Account Encryption-Scope Update** , předejte `--key-uri` parametr a předejte `--key-source` parametr s hodnotou `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Zakázání oboru šifrování

Pokud je zakázaný obor šifrování, už se vám neúčtují. Zakažte všechny obory šifrování, které nepotřebujete, aby nedocházelo k zbytečným poplatkům. Další informace najdete v tématu [Azure Storage šifrování pro](../common/storage-service-encryption.md)neaktivní neaktivní data.

# <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete zakázat rozsah šifrování v Azure Portal, přejděte do nastavení **obory šifrování** pro účet úložiště, vyberte požadovaný rozsah šifrování a vyberte **Zakázat**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li zakázat rozsah šifrování pomocí prostředí PowerShell, zavolejte příkaz Update-AzStorageEncryptionScope a zahrňte `-State` parametr s hodnotou `disabled` , jak je znázorněno v následujícím příkladu. Chcete-li znovu povolit rozsah šifrování, zavolejte stejný příkaz s `-State` parametrem nastaveným na `enabled` . Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Pokud chcete zakázat rozsah šifrování pomocí Azure CLI, zavolejte příkaz [AZ Storage Account Encryption-Scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) a zahrňte `--state` parametr s hodnotou `Disabled` , jak je znázorněno v následujícím příkladu. Chcete-li znovu povolit rozsah šifrování, zavolejte stejný příkaz s `--state` parametrem nastaveným na `Enabled` . Nezapomeňte nahradit hodnoty zástupných symbolů v příkladu vlastními hodnotami:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
- [Obory šifrování pro úložiště objektů BLOB (Preview)](encryption-scope-overview.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](../common/customer-managed-keys-overview.md)