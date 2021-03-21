---
title: Šifrování disků pro Azure Scale Sets pomocí Azure CLI
description: Naučte se používat rozhraní příkazového řádku Azure k šifrování instancí virtuálních počítačů a připojených disků v sadě škálování virtuálního počítače s Windows.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 302f53bd218a2e01a039be4780a0e2ff5974e7b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215948"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Šifrování operačních systémů a připojených datových disků v sadě škálování virtuálního počítače pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit a zašifrovat sadu škálování virtuálního počítače. Další informace o použití služby Azure Disk Encryption pro sadu škálování virtuálního počítače najdete v tématu [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.31 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss). Následující příklad vytvoří škálovací sadu *myScaleSet* nastavenou tak, aby se při provedení změn automaticky aktualizovala, a vygeneruje klíče SSH v adresáři *~/.ssh/id_rsa*, pokud ještě neexistují. K jednotlivým instancím virtuálních počítačů je připojen datový disk 32 GB a [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-linux.md) Azure slouží k přípravě datových disků pomocí [AZ VMSS Extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření trezoru klíčů Azure s povoleným šifrováním disku

Azure Key Vault můžou ukládat klíče, tajné klíče nebo hesla, které vám umožní je bezpečně implementovat ve svých aplikacích a službách. Kryptografické klíče jsou uložené v Azure Key Vault pomocí ochrany softwaru nebo můžete klíče importovat nebo generovat v modulech hardwarového zabezpečení (HSM) certifikovaným pro standardy standardu FIPS 140-2 úrovně 2. Tyto kryptografické klíče slouží k šifrování a dešifrování virtuálních disků připojených k vašemu VIRTUÁLNÍmu počítači. Podržíte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití.

Definujte vlastní jedinečné *keyvault_name*. Pak vytvořte Trezor klíčů pomocí AZ klíčů [Create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ve stejném předplatném a oblasti jako sadu škálování a nastavte zásady pro přístup k *šifrování na disk* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující Key Vault

Tento krok se vyžaduje jenom v případě, že máte existující Key Vault, kterou chcete používat s šifrováním disku. Tento krok přeskočte, pokud jste vytvořili Key Vault v předchozí části.

Definujte vlastní jedinečné *keyvault_name*. Potom aktualizujte svůj Trezor klíčů pomocí příkazového [trezoru AZ](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) klíčů a nastavte zásady přístupu s *povoleným šifrováním na disk* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Povolit šifrování

K šifrování instancí virtuálních počítačů v sadě škálování je třeba nejprve získat informace o Key Vault ID prostředku pomocí [AZ klíčů show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Tyto proměnné se používají ke spuštění procesu šifrování pomocí [AZ VMSS Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Spuštění procesu šifrování může trvat minutu nebo dvě.

V případě, že je nastavena zásada upgradu v sadě škálování vytvořené v předchozím kroku na hodnotu *automaticky*, instance virtuálních počítačů spustí proces šifrování automaticky. V části sady škálování, na kterých je zásada upgradu nastavena na ruční, spusťte zásadu šifrování na instancích virtuálních počítačů pomocí [AZ VMSS Update-Instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolení šifrování pomocí KEK k zabalení klíče

Při šifrování sady škálování virtuálního počítače můžete také použít šifrovací klíč klíče pro zvýšení zabezpečení.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Syntaxe pro hodnotu parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:</br>
/Subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br></br>
> Syntaxe pro hodnotu parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v:</br>
https://[Trezor klíčů-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Průběh šifrování kontroly

Pokud chcete zjistit stav šifrování disku, použijte příkaz [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Když jsou instance virtuálních počítačů šifrované, stavový kód hlásí *EncryptionState/Encrypted*, jak je znázorněno v následujícím příkladu výstupu:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Zakázat šifrování

Pokud už nechcete disky s šifrovanými instancemi virtuálních počítačů používat, můžete šifrování zakázat pomocí příkazu [AZ VMSS Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) následujícím způsobem:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Další kroky

- V tomto článku jste použili Azure CLI k šifrování sady škálování virtuálních počítačů. Můžete také použít šablony [Azure PowerShell](disk-encryption-powershell.md) nebo [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Pokud chcete po zřízení jiného rozšíření použít Azure Disk Encryption, můžete použít [sekvencování rozšíření](virtual-machine-scale-sets-extension-sequencing.md). 
- Příklad kompletního dávkového souboru pro šifrování datového disku pro systém Linux Scale set najdete [tady](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). V tomto příkladu se vytvoří skupina prostředků, sada škálování systému Linux, připojí se datový disk o velikosti 5 GB a zašifruje sadu škálování virtuálního počítače.
