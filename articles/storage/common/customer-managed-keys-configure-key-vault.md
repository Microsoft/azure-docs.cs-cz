---
title: Konfigurace šifrování s použitím klíčů spravovaných zákazníkem uložených v Azure Key Vault
titleSuffix: Azure Storage
description: Naučte se konfigurovat Azure Storage šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558174"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Konfigurace šifrování s použitím klíčů spravovaných zákazníkem uložených v Azure Key Vault

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete spravovat vlastní klíče. Klíče spravované zákazníkem musí být uložené ve Azure Key Vault nebo Key Vault spravovaném modelu hardwarového zabezpečení (HSM) (ve verzi Preview).

Tento článek popisuje, jak nakonfigurovat šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v trezoru klíčů, pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Informace o tom, jak nakonfigurovat šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené ve spravovaném modulu HSM, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault MANAGED HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault a Azure Key Vault spravovaný modul HSM podporuje pro konfiguraci stejné rozhraní API a rozhraní pro správu.

## <a name="configure-a-key-vault"></a>Konfigurace trezoru klíčů

K ukládání klíčů spravovaných zákazníkem můžete použít nový nebo existující Trezor klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných.

Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage vyžaduje, aby pro Trezor klíčů byla povolená ochrana proti odstranění i vyprázdnění. Když vytváříte nový trezor klíčů a nemůžete ho zakázat, je obnovitelné odstranění ve výchozím nastavení povolené. Ochranu vyprázdnit můžete povolit buď při vytvoření trezoru klíčů, nebo po jeho vytvoření.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal](../../key-vault/general/quick-create-portal.md). Při vytváření trezoru klíčů vyberte **Povolit ochranu vyprázdnění**, jak je znázorněno na následujícím obrázku.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Snímek obrazovky ukazující, jak povolit ochranu vyprázdnit při vytváření trezoru klíčů":::

Pokud chcete povolit ochranu vyprázdnění pro existující Trezor klíčů, postupujte takto:

1. V Azure Portal přejděte do svého trezoru klíčů.
1. V části **Nastavení** vyberte **vlastnosti**.
1. V části **Vymazat ochranu** vyberte možnost **Povolit ochranu vyprázdnění**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete vytvořit nový trezor klíčů pomocí PowerShellu, nainstalujte verzi 2.0.0 nebo novější z modulu příkazového prostředí PowerShell [AZ. Key trezor](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Pak zavolejte [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) a vytvořte nový trezor klíčů. Při vytváření nového trezoru klíčů je ve výchozím nastavení standardně povolená verze 2.0.0 a novější z modulu AZ. klíčů trezor.

Následující příklad vytvoří nový trezor klíčů s povolenou ochranou obnovitelného odstranění i vyprázdnění. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Informace o tom, jak povolit ochranu vyprázdnění pro existující Trezor klíčů pomocí PowerShellu, najdete v tématu [Použití obnovitelného odstranění pomocí PowerShellu](../../key-vault/general/key-vault-recovery.md).

V dalším kroku přiřaďte vašemu účtu úložiště spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu k trezoru klíčů. Další informace o spravovaných identitách přiřazených systémem najdete v tématu [co jsou spravované identity pro prostředky Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Pokud chcete přiřadit spravovanou identitu pomocí PowerShellu, volejte rutinu [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Nakonec nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Pro nastavení zásad přístupu pro Trezor klíčů volejte [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li vytvořit nový trezor klíčů pomocí rozhraní příkazového řádku Azure, zavolejte volání [AZ datatrezor Create](/cli/azure/keyvault#az-keyvault-create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Informace o tom, jak povolit ochranu vyprázdnění pro existující Trezor klíčů pomocí Azure CLI, najdete v tématu [Použití obnovitelného odstranění pomocí rozhraní](../../key-vault/general/key-vault-recovery.md)příkazového řádku (CLI).

V dalším kroku přiřaďte k účtu úložiště spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte účtu úložiště oprávnění k přístupu k trezoru klíčů. Další informace o spravovaných identitách přiřazených systémem najdete v tématu [co jsou spravované identity pro prostředky Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

K přiřazení spravované identity pomocí Azure CLI volejte volání [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Nakonec nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl účet úložiště oprávnění k přístupu. V tomto kroku použijete spravovanou identitu, kterou jste dříve přiřadili k účtu úložiště.

Chcete-li nastavit zásady přístupu pro Trezor klíčů, zavolejte volání [AZ webtrezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Přidat klíč

V dalším kroku přidejte klíč do trezoru klíčů.

Šifrování Azure Storage podporuje RSA a klíče RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu [o klíčích](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Další informace o tom, jak přidat klíč s Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení klíče z Azure Key Vault pomocí Azure Portal](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li přidat klíč pomocí prostředí PowerShell, zavolejte rutinu [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete přidat klíč pomocí rozhraní příkazového řádku Azure, volejte volání [AZ klíčů trezor Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurace šifrování s využitím klíčů spravovaných zákazníkem

V dalším kroku nakonfigurujte účet Azure Storage tak, aby používal klíče spravované zákazníkem s Azure Key Vault, a pak zadejte klíč, který chcete přidružit k účtu úložiště.

Při konfiguraci šifrování s použitím klíčů spravovaných zákazníkem můžete zvolit automatickou aktualizaci verze klíče používaného pro Azure Storage šifrování pokaždé, když je v přidruženém trezoru klíčů k dispozici nová verze. Alternativně můžete explicitně zadat verzi klíče, která se má použít pro šifrování, dokud se verze klíče ručně neaktualizuje.

> [!NOTE]
> Pokud chcete klíč otočit, vytvořte v Azure Key Vault novou verzi klíče. Azure Storage nezpracovává rotaci klíče v Azure Key Vault, takže budete muset klíč otočit ručně nebo vytvořit funkci, která ho otočí podle plánu.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Konfigurace šifrování pro automatické aktualizace verzí klíčů

Azure Storage může automaticky aktualizovat klíč spravovaný zákazníkem, který se používá k šifrování, aby používal nejnovější verzi klíče. Při otočení klíče spravovaného zákazníkem v Azure Key Vault Azure Storage automaticky začít používat k šifrování nejnovější verzi klíče.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete konfigurovat klíče spravované zákazníkem pomocí automatických aktualizací verze klíče v Azure Portal, postupujte takto:

1. Přejděte na svůj účet úložiště.
1. V okně **Nastavení** pro účet úložiště klikněte na **šifrování**. Ve výchozím nastavení je Správa klíčů nastavená na **spravované klíče společnosti Microsoft**, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky portálu ukazující možnost šifrování](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Vyberte možnost **Customer Managed Keys** .
1. Zvolte možnost **vybrat z Key Vault** .
1. Vyberte **možnost vyberte Trezor klíčů a klíč**.
1. Vyberte Trezor klíčů obsahující klíč, který chcete použít.
1. Vyberte klíč z trezoru klíčů.

   ![Snímek obrazovky ukazující, jak vybrat Trezor klíčů a klíč](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Uložte provedené změny.

Po zadání klíče Azure Portal indikuje, že je povolená Automatická aktualizace verze klíče, a zobrazuje verzi klíče, která se právě používá pro šifrování.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Snímek obrazovky s automatickou aktualizací aktivované verze klíče":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete konfigurovat klíče spravované zákazníkem pomocí automatické aktualizace verze klíče pomocí PowerShellu, nainstalujte modul [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , verze 2.0.0 nebo novější.

Pokud chcete automatickou aktualizaci verze klíče pro klíč spravovaný zákazníkem, vynechejte verzi klíče při konfiguraci šifrování s použitím klíčů spravovaných zákazníkem pro účet úložiště. Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu, a zahrňte možnost **-KeyvaultEncryption** pro povolení klíčů spravovaných zákazníkem pro účet úložiště.

Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete konfigurovat klíče spravované zákazníky pomocí automatických aktualizací verze klíče pomocí Azure CLI, nainstalujte [Azure CLI verze 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) nebo novější. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Pokud chcete automatickou aktualizaci verze klíče pro klíč spravovaný zákazníkem, vynechejte verzi klíče při konfiguraci šifrování s použitím klíčů spravovaných zákazníkem pro účet úložiště. Chcete-li aktualizovat nastavení šifrování účtu úložiště, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source` parametr a nastavte ho tak, aby `Microsoft.Keyvault` umožňoval pro tento účet klíče spravované zákazníkem.

Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurace šifrování pro ruční aktualizace verzí klíčů

Pokud upřednostňujete ruční aktualizaci verze klíče, pak explicitně zadejte verzi v době, kdy nakonfigurujete šifrování pomocí klíčů spravovaných zákazníkem. V takovém případě nebude Azure Storage automaticky aktualizovat verzi klíče, pokud je v trezoru klíčů vytvořena nová verze. Chcete-li použít novou verzi klíče, je nutné ručně aktualizovat verzi používanou pro Azure Storage šifrování.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete konfigurovat klíče spravované zákazníkem s ruční aktualizací verze klíče v Azure Portal, zadejte identifikátor URI klíče, včetně verze. Pokud chcete zadat klíč jako identifikátor URI, použijte následující postup:

1. Pokud chcete najít identifikátor URI klíče v Azure Portal, přejděte do trezoru klíčů a vyberte nastavení **klíče** . Vyberte požadovaný klíč a potom kliknutím na klíč zobrazte jeho verze. Vyberte verzi klíče a zobrazte nastavení této verze.
1. Zkopírujte hodnotu pole **identifikátor klíče** , které poskytuje identifikátor URI.

    ![Snímek obrazovky s identifikátorem URI klíče trezoru klíčů](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. V nastavení **šifrovacího klíče** pro váš účet úložiště klikněte na možnost **zadat identifikátor URI klíče** .
1. Vložte identifikátor URI, který jste zkopírovali do pole **identifikátor URI klíče** . Pokud chcete povolit automatickou aktualizaci verze klíče, vynechejte verzi klíče z identifikátoru URI.

   ![Snímek obrazovky ukazující, jak zadat identifikátor URI klíče](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Zadejte předplatné, které obsahuje Trezor klíčů.
1. Uložte provedené změny.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete konfigurovat klíče spravované zákazníkem s ruční aktualizací verze klíče, při konfiguraci šifrování pro účet úložiště explicitně poskytněte tuto verzi klíče. Voláním [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aktualizujte nastavení šifrování účtu úložiště, jak je znázorněno v následujícím příkladu, a zahrňte možnost **-KeyvaultEncryption** pro povolení klíčů spravovaných zákazníkem pro účet úložiště.

Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Když ručně aktualizujete verzi klíče, budete muset aktualizovat nastavení šifrování účtu úložiště tak, aby používalo novou verzi. Nejdřív zavolejte [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , abyste získali nejnovější verzi klíče. Pak zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozím příkladu.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete konfigurovat klíče spravované zákazníkem s ruční aktualizací verze klíče, při konfiguraci šifrování pro účet úložiště explicitně poskytněte tuto verzi klíče. Chcete-li aktualizovat nastavení šifrování účtu úložiště, použijte příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v následujícím příkladu. Zahrňte `--encryption-key-source` parametr a nastavte ho tak, aby `Microsoft.Keyvault` umožňoval pro tento účet klíče spravované zákazníkem.

Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Když ručně aktualizujete verzi klíče, budete muset aktualizovat nastavení šifrování účtu úložiště tak, aby používalo novou verzi. Nejdřív dotaz na identifikátor URI trezoru klíčů zavoláním [AZ klíčů show](/cli/azure/keyvault#az-keyvault-show)a pro verzi klíče zavoláním [AZ Key trezor Key list-versionss](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Pak zavolejte [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a aktualizujte nastavení šifrování účtu úložiště tak, aby používala novou verzi klíče, jak je znázorněno v předchozím příkladu.

---

## <a name="change-the-key"></a>Změnit klíč

Klíč, který používáte pro Azure Storage šifrování, můžete kdykoli změnit.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete změnit klíč pomocí Azure Portal, postupujte takto:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Vyberte Trezor klíčů a zvolte nový klíč.
1. Uložte provedené změny.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete změnit klíč pomocí PowerShellu, zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , jak je znázorněné v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys) , a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, musíte taky aktualizovat identifikátor URI trezoru klíčů.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete změnit klíč pomocí Azure CLI, zavolejte na příkaz [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , jak je znázorněno v části [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem](#configure-encryption-with-customer-managed-keys) , a zadejte nový název a verzi klíče. Pokud je nový klíč v jiném trezoru klíčů, musíte taky aktualizovat identifikátor URI trezoru klíčů.

---

## <a name="revoke-customer-managed-keys"></a>Odvolání klíčů spravovaných zákazníkem

Odvolání klíče spravovaného zákazníkem odeberete přidružení mezi účtem úložiště a trezorem klíčů.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete odvolat klíče spravované zákazníkem pomocí Azure Portal, zakažte klíč, jak je popsáno v tématu [zakázání klíčů spravovaných zákazníkem](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Klíče spravované zákazníkem můžete odvolat odebráním zásad přístupu trezoru klíčů. Pokud chcete odebrat klíč spravovaný zákazníkem pomocí PowerShellu, zavolejte příkaz [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Klíče spravované zákazníkem můžete odvolat odebráním zásad přístupu trezoru klíčů. Pokud chcete odvolat klíč spravovaný zákazníkem pomocí Azure CLI, zavolejte příkaz [AZ Key trezor Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Zakázat klíče spravované zákazníkem

Když zakážete klíče spravované zákazníkem, váš účet úložiště se znovu zašifruje pomocí klíčů spravovaných Microsoftem.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

K zakázání klíčů spravovaných zákazníkem v Azure Portal použijte následující postup:

1. Přejděte k účtu úložiště a zobrazte nastavení **šifrování** .
1. Zrušte zaškrtnutí políčka u nastavení **použít vlastní klíč** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zakázat klíče spravované zákazníkem pomocí PowerShellu, zavolejte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) s `-StorageEncryption` možností, jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zakázat klíče spravované zákazníkem pomocí Azure CLI, zavolejte na [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) a nastavte na `--encryption-key-source parameter` `Microsoft.Storage` , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami a použít proměnné definované v předchozích příkladech.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Klíče spravované zákazníkem pro šifrování Azure Storage](customer-managed-keys-overview.md)
- [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault spravovaném HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md)
