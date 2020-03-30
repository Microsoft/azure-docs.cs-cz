---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297873"
---
## <a name="create-a-new-key-vault"></a>Vytvoření nového trezoru klíčů

Chcete-li vytvořit nový trezor klíčů pomocí prostředí PowerShell, zavolejte [new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault). Trezor klíčů, který používáte k ukládání klíčů spravovaných zákazníky pro šifrování Průzkumníka dat Azure, musí mít povolená dvě nastavení ochrany klíčů, **obnovitelné odstranění** a **neodstraňovat**. Nahraďte zástupné hodnoty v závorkách vlastními hodnotami v níže uvedeném příkladu.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu k trezoru klíčů

Dále nakonfigurujte zásady přístupu pro trezor klíčů tak, aby cluster měl oprávnění k přístupu k němu. V tomto kroku použijete systémem přiřazenou spravovanou identitu, kterou jste dříve přiřadili ke clusteru. Chcete-li nastavit zásady přístupu pro trezor klíčů, zavolejte [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Nahraďte zástupné hodnoty v závorkách vlastními hodnotami a použijte proměnné definované v předchozích příkladech.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Vytvoření nového klíče

Dále vytvořte nový klíč v trezoru klíčů. Chcete-li vytvořit nový klíč, zavolejte [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Nahraďte zástupné hodnoty v závorkách vlastními hodnotami a použijte proměnné definované v předchozích příkladech.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
