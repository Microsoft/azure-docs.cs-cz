---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021193"
---
Azure Průzkumník dat šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které budou použity k šifrování dat. Klíče spravované zákazníkem musí být uloženy v [Azure Key Vault](/azure/key-vault/key-vault-overview). Můžete vytvářet vlastní klíče a ukládat je do trezoru klíčů nebo můžete použít rozhraní Azure Key Vault API k vygenerování klíčů. Cluster Azure Průzkumník dat a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Podrobné vysvětlení klíčů spravovaných zákazníkem najdete v tématu [klíče spravované zákazníkem pomocí Azure Key Vault](/azure/storage/common/storage-service-encryption). V tomto článku se dozvíte, jak nakonfigurovat klíče spravované zákazníkem.

> [!Note]
> Ke konfiguraci klíčů spravovaných zákazníkem pomocí Azure Průzkumník dat musíte [nastavit dvě vlastnosti trezoru klíčů](/azure/key-vault/key-vault-ovw-soft-delete): **obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti nejsou ve výchozím nastavení povolené. Pokud chcete tyto vlastnosti povolit, použijte [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) nebo [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli). Podporují se jenom klíče RSA a velikost klíče 2048.

## <a name="assign-an-identity-to-the-cluster"></a>Přiřazení identity ke clusteru

Pokud chcete pro svůj cluster povolit klíče spravované zákazníkem, nejprve do clusteru přiřaďte spravovanou identitu přiřazenou systémem. Pomocí této spravované identity udělíte oprávnění clusteru přístup k trezoru klíčů. Pokud chcete konfigurovat spravované identity přiřazené systémem, přečtěte si téma [spravované identity](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Vytvořit nový trezor klíčů

Pokud chcete vytvořit nový trezor klíčů pomocí PowerShellu, volejte rutinu [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Trezor klíčů, který použijete k uložení klíčů spravovaných zákazníkem pro šifrování Azure Průzkumník dat, musí mít povolené dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **nemazatelné**. Hodnoty zástupných symbolů v závorkách nahraďte vlastními hodnotami v příkladu níže.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu trezoru klíčů

Dále nakonfigurujte zásady přístupu pro Trezor klíčů, aby měl cluster oprávnění k přístupu. V tomto kroku použijete spravovanou identitu přiřazenou systémem, kterou jste předtím přiřadili ke clusteru. Pro nastavení zásad přístupu pro Trezor klíčů volejte [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Zástupné hodnoty v závorkách nahraďte vlastními hodnotami a použijte proměnné definované v předchozích příkladech.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Vytvořit nový klíč

V dalším kroku vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Zástupné hodnoty v závorkách nahraďte vlastními hodnotami a použijte proměnné definované v předchozích příkladech.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
