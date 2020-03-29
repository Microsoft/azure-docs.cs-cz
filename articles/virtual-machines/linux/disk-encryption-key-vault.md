---
title: Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption
description: Tento článek obsahuje postup pro vytvoření a konfiguraci trezoru klíčů pro použití s azure disk encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970630"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných kódů disku.  Další informace o trezorech klíčů najdete [v tématu Začínáme s trezorem klíčů Azure](../../key-vault/key-vault-get-started.md) a [Zabezpečte trezor klíčů](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Vytváření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze).](disk-encryption-key-vault-aad.md)

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvoříte skupinu prostředků.
2. Vytvoření trezoru klíčů. 
3. Nastavení pokročilých zásad přístupu trezoru klíčů.

Tyto kroky jsou znázorněny v následujících rychlých startech:

- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](disk-encryption-cli-quickstart.md)

Pokud chcete, můžete také vygenerovat nebo importovat šifrovací klíč (KEK).

> [!Note]
> Kroky v tomto článku jsou automatizované v [předpokladech Azure Disk Encryption CLI skript](https://github.com/ejarvi/ade-cli-getting-started) a [Azure Disk Encryption předpoklady PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalace nástrojů a připojení k Azure

Kroky v tomto článku můžete dokončit pomocí [rozhraní příkazového příkazu Azure ,](/cli/azure/) [modulu Azure PowerShell Az](/powershell/azure/overview)nebo [portálu Azure](https://portal.azure.com). 

Zatímco portál je přístupný prostřednictvím vašeho prohlížeče, Azure CLI a Azure PowerShell vyžadují místní instalaci; podrobnosti najdete v [tématu Azure Disk Encryption for Linux: Nainstalujte nástroje](disk-encryption-linux.md#install-tools-and-connect-to-azure) pro podrobnosti.

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Před použitím Azure CLI nebo Azure PowerShell, musíte se nejprve připojit k předplatnému Azure. Uděláte to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [přihlásíte se pomocí Azure Powershellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0)nebo po zobrazení výzvy zadáte svá pověření na portál Azure.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

*Pokud již skupinu prostředků máte, můžete přeskočit na [Vytvořit trezor klíčů](#create-a-key-vault).*

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Vytvořte skupinu prostředků pomocí [skupiny az vytvořit](/cli/azure/group?view=azure-cli-latest#az-group-create) příkaz Azure CLI, příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell nebo z [portálu Azure](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

*Pokud již trezor klíčů máte, můžete přeskočit na [nastavit rozšířené zásady přístupu trezoru klíčů](#set-key-vault-advanced-access-policies).*

Vytvořte trezor klíčů pomocí příkazu [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, příkazu [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell, [portálu Azure](https://portal.azure.com)nebo [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Chcete-li zajistit, že tajné kódy šifrování nepřekračují místní hranice, Azure Disk Encryption vyžaduje trezor klíčů a virtuálnípočítače, které mají být umístěny společně ve stejné oblasti. Vytvořte a použijte trezor klíčů, který je ve stejné oblasti jako virtuální chod, který má být šifrován. 

Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.

### <a name="azure-cli"></a>Azure CLI

Při vytváření trezoru klíčů pomocí rozhraní příkazového příkazu Azure přidejte příznak "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Při vytváření trezoru klíčů pomocí Azure PowerShell, přidejte příznak "EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Šablona Resource Manageru

Trezor klíčů můžete také vytvořit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně Azure quickstart klikněte na **Deploy to Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, název trezoru klíčů, ID objektu, právní podmínky a smlouvu a klikněte na **Koupit**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Nastavte pokročilé zásady přístupu trezoru klíčů.

Platforma Azure potřebuje přístup k šifrovacím klíčům nebo tajným klíčům ve vašem trezoru klíčů, aby byly k dispozici virtuálnímu počítači pro zavádění a dešifrování svazků. 

Pokud jste v době vytvoření nepovolili trezor klíčů pro šifrování disku, nasazení nebo nasazení šablony (jak je znázorněno v předchozím kroku), je nutné aktualizovat jeho rozšířené zásady přístupu.  

### <a name="azure-cli"></a>Azure CLI

Pomocí [aktualizace az keyvault](/cli/azure/keyvault#az-keyvault-update) povolte šifrování disku pro trezor klíčů. 

 - **Povolit trezor klíčů pro šifrování disku:** Je vyžadováno šifrování pro disk. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **V případě potřeby povolte trezor klíčů pro nasazení:** Umožňuje poskytovateli prostředků Microsoft.Compute načíst tajné klíče z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **V případě potřeby povolte trezor klíčů pro nasazení šablony:** Povolte Správci prostředků načíst tajné klíče z úložiště.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Pomocí rutiny trezoru klíčů PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) povolte šifrování disku pro trezor klíčů.

  - **Povolit trezor klíčů pro šifrování disku:** Pro šifrování disku Azure je vyžadováno technologie EnabledForDiskEncryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **V případě potřeby povolte trezor klíčů pro nasazení:** Umožňuje poskytovateli prostředků Microsoft.Compute načíst tajné klíče z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **V případě potřeby povolte trezor klíčů pro nasazení šablony:** Umožňuje Azure Resource Manager získat tajné klíče z tohoto trezoru klíčů, když tento trezor klíčů odkazuje v nasazení šablony.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>portál Azure

1. Vyberte trezor klíčů, přejděte na **zásady přístupu**a **klepnutím zobrazte pokročilé zásady přístupu**.
2. Zaškrtněte políčko **Povolit přístup k šifrování disku Azure pro šifrování svazku**.
3. Vyberte **Povolit přístup k virtuálním počítačům Azure pro nasazení** a/nebo **Povolit přístup ke Správci prostředků Azure pro nasazení šablony**, v případě potřeby. 
4. Klikněte na **Uložit**.

    ![Rozšířené zásady přístupu trezoru klíčů Azure](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Nastavení šifrovacího klíče (KEK)

Pokud chcete použít šifrovací klíč klíče (KEK) pro další vrstvu zabezpečení šifrovacích klíčů, přidejte k trezoru klíčů kek. Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů.

Novou kek můžete vygenerovat pomocí příkazu azure CLI [az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) rutinou [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) nebo [portálu Azure](https://portal.azure.com/). Je nutné vygenerovat typ klíče RSA; Azure Disk Encryption ještě nepodporuje použití klíčů elliptic Curve.

Místo toho můžete importovat KEK z místnísprávy klíčů HsM. Další informace naleznete v [tématu Dokumentace trezoru klíčů](../../key-vault/key-vault-hsm-protected-keys.md). 

Adresy URL kek trezoru klíčů musí být verzí. Azure vynucuje toto omezení správy verzí. Platné adresy URL tajných kódů a KEK najdete v následujících příkladech:

* Příklad platné tajné adresy URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Příklad platné adresy URL KEK:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nepodporuje určení čísla portů jako součást tajných kódů trezoru klíčů a adresy URL KEK. Příklady nepodporovaných a podporovaných adres URL trezoru klíčů naleznete v následujících příkladech:

  * Adresa URL přijatelného trezoru klíčů:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nepřijatelná adresa URL trezoru klíčů:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu vytvoření klíče Azure CLI [az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) vygenerujte novou kek a uložte ji do trezoru klíčů.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Místo toho můžete importovat soukromý klíč pomocí příkazu importu klíče Azure CLI [az keyvault:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

V obou případech zadáte název vašeho KEK do azure CLI [az vm šifrování povolit](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key parametr. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Pomocí rutiny [add-azKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) azure prostředí vygenerujete novou kek a uložte ji do trezoru klíčů.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Místo toho můžete importovat soukromý klíč pomocí příkazu [importu klíče AZ](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) Azure PowerShell.

V obou případech zadáte ID trezoru klíčů KEK a adresu URL vašeho KEK do parametrů Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId a -KeyEncryptionKeyUrl. Všimněte si, že tento příklad předpokládá, že používáte stejný trezor klíčů pro šifrovací klíč disku i KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption požadavky CLI skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption požadavky PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Naučte [se scénáře šifrování disku Azure na virtuálních počítačích s Linuxem](disk-encryption-linux.md)
- Přečtěte si, jak [řešit potíže s šifrováním disku Azure](disk-encryption-troubleshooting.md)
- Čtení [ukázkových skriptů azure diskového šifrování](disk-encryption-sample-scripts.md)
