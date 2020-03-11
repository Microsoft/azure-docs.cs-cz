---
title: Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption
description: Tento článek popisuje kroky pro vytvoření a konfiguraci trezoru klíčů pro použití s Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970630"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku.  Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md) .

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvořte skupinu prostředků.
2. Vytváří se Trezor klíčů. 
3. Nastavují se zásady rozšířeného přístupu trezoru klíčů.

Tyto kroky jsou znázorněné v následujících rychlých startech:

- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](disk-encryption-cli-quickstart.md)

Můžete také, pokud chcete, vygenerovat nebo importovat klíč šifrování klíče (KEK).

> [!Note]
> Kroky v tomto článku jsou automatizovány ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) a v tématu [Azure Disk Encryption předpoklady PowerShellu](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

Kroky v tomto článku se dají dokončit pomocí [Azure CLI](/cli/azure/), [Azure PowerShell Az Module](/powershell/azure/overview)nebo [Azure Portal](https://portal.azure.com). 

I když je portál přístupný prostřednictvím prohlížeče, Azure CLI a Azure PowerShell vyžadují místní instalaci; Podrobnosti najdete v tématu [Azure Disk Encryption pro Linux: Instalace nástrojů](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Než začnete používat rozhraní příkazového řádku Azure CLI nebo Azure PowerShell, musíte se nejdřív připojit k předplatnému Azure. Provedete to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [přihlásíte se pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0)nebo po zobrazení výzvy dodáte přihlašovací údaje do Azure Portal.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

*Pokud již máte skupinu prostředků, můžete přeskočit a [vytvořit Trezor klíčů](#create-a-key-vault).*

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell nebo z [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure Powershell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

*Pokud už máte Trezor klíčů, můžete přeskočit k [nastavení zásad pokročilého přístupu trezoru klíčů](#set-key-vault-advanced-access-policies).*

Vytvořte Trezor klíčů pomocí příkazu [AZ Key trezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, příkazu [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) azure PowerShellu, [Azure Portal](https://portal.azure.com)nebo [šabloně správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Aby se šifrovací tajemství neprováděla mezi regionální hranicemi, Azure Disk Encryption vyžaduje, aby se Key Vault a virtuální počítače ve stejné oblasti společně nacházejí. Vytvořte a použijte Key Vault, který je ve stejné oblasti jako virtuální počítače, které mají být zašifrované. 

Každý Key Vault musí mít jedinečný název. V následujících příkladech nahraďte < název trezoru klíčů jedinečných > s názvem vašeho trezoru klíčů.

### <a name="azure-cli"></a>Azure CLI

Při vytváření trezoru klíčů pomocí Azure CLI přidejte příznak "--Enabled-for-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure Powershell

Při vytváření trezoru klíčů pomocí Azure PowerShell přidejte příznak "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Šablona Resource Manageru

Trezor klíčů můžete vytvořit také pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, Key Vault název, ID objektu, právních podmínek a smlouvy, a pak klikněte na **koupit**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů

Platforma Azure potřebuje přístup k šifrování klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač pro spuštění a dešifrování svazky. 

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

###  <a name="azure-powershell"></a>Azure Powershell
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

### <a name="azure-portal"></a>Azure Portal

1. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu**a **kliknutím zobrazte zásady pokročilého přístupu**.
2. Zaškrtněte políčko s názvem **Povolit přístup k Azure Disk Encryption pro šifrování svazku**.
3. V případě potřeby vyberte **Povolit přístup k Azure Virtual Machines pro nasazení** nebo **povolit přístup k Azure Resource Manager pro nasazení šablony**. 
4. Klikněte na **Uložit**.

    ![Azure key vaultu pokročilé zásady přístupu](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Nastavení klíče šifrovacího klíče (KEK)

Pokud chcete použít šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče, přidejte do trezoru klíčů KEK. Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault.

Novou KEK můžete vygenerovat pomocí příkazu Azure CLI [AZ klíčů trezor Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) Azure PowerShell, rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) nebo [Azure Portal](https://portal.azure.com/). Musíte vygenerovat typ klíče RSA; Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

Místo toho můžete importovat KEK z místního modulu hardwarové správy klíčů. Další informace najdete v [dokumentaci Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

Adresy URL vašeho trezoru klíčů KEK musí být ve verzi. Azure vynucuje toto omezení správy verzí. Platný tajný kód a adresy URL KEK viz následující příklady:

* Příklad platné tajné adresy URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Příklad platné adresy URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů služby key vault a KEK adresy URL. Příkladem adresy URL není podporováno a podporované služby key vault najdete v následující příklady:

  * Přijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nepřijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu Azure CLI [AZ klíčů trezor Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) vytvořte novou KEK a uložte ji do svého trezoru klíčů.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Místo toho můžete importovat privátní klíč pomocí příkazu Azure CLI [AZ klíčů trezor Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

V obou případech dáte název KEK do Azure CLI [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure Powershell 

Pomocí rutiny Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) vygenerujte nové KEK a uložte je do trezoru klíčů.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Místo toho můžete importovat privátní klíč pomocí příkazu Azure PowerShell [AZ klíčů trezor Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

V obou případech poskytnete ID vašeho trezoru klíčů KEK a adresu URL vašeho KEKu do parametrů Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId a-KeyEncryptionKeyUrl. Všimněte si, že v tomto příkladu se předpokládá, že používáte stejný Trezor klíčů pro šifrovací klíč disku i pro KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Další kroky

- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Informace o [Azure Disk Encryption scénářích pro virtuální počítače se systémem Linux](disk-encryption-linux.md)
- Postup [řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Přečtěte si [ukázkové skripty Azure Disk Encryption](disk-encryption-sample-scripts.md)
