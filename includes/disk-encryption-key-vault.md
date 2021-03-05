---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 26a9e931c42822218e7935f50c1f222ac33c34f2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210087"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

*Pokud již máte skupinu prostředků, můžete přeskočit a [vytvořit Trezor klíčů](#create-a-key-vault).*

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) Azure CLI, příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell nebo z [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

*Pokud už máte Trezor klíčů, můžete přeskočit k [nastavení zásad pokročilého přístupu trezoru klíčů](#set-key-vault-advanced-access-policies).*

Vytvořte Trezor klíčů pomocí příkazu [AZ Key trezor Create](/cli/azure/keyvault#az-keyvault-create) Azure CLI, příkazu [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) azure PowerShellu, [Azure Portal](https://portal.azure.com)nebo [šabloně správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Váš Trezor klíčů a virtuální počítače musí být ve stejném předplatném. Pro zajištění, že šifrovací tajná klíče nepatří mezi regionální hranice, Azure Disk Encryption vyžaduje, aby se Key Vault a virtuální počítače ve stejné oblasti společně nacházejí. Vytvořte a použijte Key Vault, který se nachází ve stejném předplatném a oblasti jako virtuální počítače, které mají být zašifrované. 

Každý Key Vault musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

### <a name="azure-cli"></a>Azure CLI

Při vytváření trezoru klíčů pomocí Azure CLI přidejte příznak "--Enabled-for-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Při vytváření trezoru klíčů pomocí Azure PowerShell přidejte příznak "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Šablona Resource Manageru

Trezor klíčů můžete vytvořit také pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, Key Vault název, ID objektu, právních podmínek a smlouvy, a pak klikněte na **koupit**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Nastavte pokročilé zásady přístupu trezoru klíčů.

Platforma Azure potřebuje přístup k šifrovacím klíčům nebo tajným klíčům v trezoru klíčů, aby byly k dispozici pro virtuální počítač, aby bylo možné spouštět a dešifrovat svazky. 

Pokud jste nepovolili použití trezoru klíčů pro šifrování disků, nasazení nebo nasazení šablon v době vytváření (jak je znázorněno v předchozím kroku), musíte aktualizovat své pokročilé zásady přístupu.  

### <a name="azure-cli"></a>Azure CLI

K povolení šifrování disku pro Trezor klíčů použijte [AZ Key trezor Update](/cli/azure/keyvault#az-keyvault-update) . 

 - **Povolit Key Vault pro šifrování disku:** Je nutné povolit šifrování disku. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **V případě potřeby povolte Key Vault pro nasazení:** Povolí poskytovateli prostředků Microsoft. COMPUTE načíst tajné kódy z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **V případě potřeby povolte Key Vault pro nasazení šablony:** Povolí Správce prostředků načtení tajných kódů z trezoru.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Pomocí rutiny PowerShellu pro Trezor klíčů [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Povolte šifrování disku pro Trezor klíčů.

  - **Povolit Key Vault pro šifrování disku:** EnabledForDiskEncryption se vyžaduje pro Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **V případě potřeby povolte Key Vault pro nasazení:** Povolí poskytovateli prostředků Microsoft. COMPUTE načíst tajné kódy z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **V případě potřeby povolte Key Vault pro nasazení šablony:** Umožňuje Azure Resource Manager získávat tajné klíče z tohoto trezoru klíčů, když se tento trezor klíčů odkazuje v nasazení šablony.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>portál Azure

1. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu** a **kliknutím zobrazte zásady pokročilého přístupu**.
2. Zaškrtněte políčko s názvem **Povolit přístup k Azure Disk Encryption pro šifrování svazku**.
3. V případě potřeby vyberte **Povolit přístup k Azure Virtual Machines pro nasazení** nebo **povolit přístup k Azure Resource Manager pro nasazení šablony**. 
4. Klikněte na **Uložit**.

    ![Zásady rozšířeného přístupu ke službě Azure Key trezor](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Nastavení klíče šifrovacího klíče (KEK)

Pokud chcete pro další vrstvu zabezpečení pro šifrovací klíče použít klíč šifrovacího klíče (KEK), přidejte do trezoru klíčů KEK. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault.

Novou KEK můžete vygenerovat pomocí příkazu Azure CLI [AZ klíčů trezor Key create](/cli/azure/keyvault/key#az-keyvault-key-create) Azure PowerShell, rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) nebo [Azure Portal](https://portal.azure.com/). Musíte vygenerovat typ klíče RSA; Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

Místo toho můžete importovat KEK z místního modulu hardwarové správy klíčů. Další informace najdete v [dokumentaci Key Vault](../articles/key-vault/keys/hsm-protected-keys.md).

Adresy URL vašeho trezoru klíčů KEK musí být ve verzi. Azure vynutilo toto omezení správy verzí. Platné tajné a KEK adresy URL najdete v následujících příkladech:

* Příklad platné tajné adresy URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Příklad platné adresy URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů trezoru klíčů a adres URL KEK. Příklady nepodporovaných a podporovaných adres URL trezoru klíčů najdete v následujících příkladech:

  * Přijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nepřijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu Azure CLI [AZ klíčů trezor Key Create](/cli/azure/keyvault/key#az-keyvault-key-create) vytvořte novou KEK a uložte ji do svého trezoru klíčů.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Místo toho můžete importovat privátní klíč pomocí příkazu Azure CLI [AZ klíčů trezor Key import](/cli/azure/keyvault/key#az-keyvault-key-import) :

V obou případech dáte název KEK do Azure CLI [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Pomocí rutiny Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) vygenerujte nové KEK a uložte je do trezoru klíčů.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Místo toho můžete importovat privátní klíč pomocí příkazu Azure PowerShell [AZ klíčů trezor Key import](/cli/azure/keyvault/key#az-keyvault-key-import) .

V obou případech poskytnete ID vašeho trezoru klíčů KEK a adresu URL vašeho KEKu do parametrů Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId a-KeyEncryptionKeyUrl. Všimněte si, že v tomto příkladu se předpokládá, že používáte stejný Trezor klíčů pro šifrovací klíč disku i pro KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```