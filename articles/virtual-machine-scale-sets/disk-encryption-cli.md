---
title: Šifrování disků pro škálovací sady Azure pomocí azure cli
description: Zjistěte, jak pomocí Azure PowerShellu šifrovat instance virtuálních počítačů a připojené disky ve škálovací sadě virtuálních počítačů s Windows.
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279079"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Šifrování operačního systému a připojených datových disků ve škálovací sadě virtuálních počítačů pomocí příkazového příkazového příkazového příkazu Azure

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí azure cli vytvořit a šifrovat škálovací sadu virtuálních strojů. Další informace o použití šifrování Azure Disku na škálovací sadu virtuálních počítačů najdete v [tématu Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkazcli místně, tento kurz vyžaduje, abyste spouštěli Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss). Následující příklad vytvoří škálovací sadu *myScaleSet* nastavenou tak, aby se při provedení změn automaticky aktualizovala, a vygeneruje klíče SSH v adresáři *~/.ssh/id_rsa*, pokud ještě neexistují. K každé instanci virtuálního počítače se připojuje 32Gb datový disk a [rozšíření Vlastní skript](../virtual-machines/linux/extensions-customscript.md) Azure se používá k přípravě datových disků se [sadou rozšíření AZ VMSS](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření trezoru klíčů Azure povoleného pro šifrování disku

Azure Key Vault můžete ukládat klíče, tajné klíče nebo hesla, které vám umožní bezpečně implementovat ve vašich aplikacích a službách. Kryptografické klíče jsou uloženy v trezoru klíčů Azure pomocí softwarové ochrany nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2. Tyto kryptografické klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Zachováte kontrolu nad těmito kryptografickými klíči a můžete auditovat jejich použití.

Definujte svůj vlastní jedinečný *keyvault_name*. Potom vytvořte keyvault s [az keyvault vytvořit](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ve stejném předplatném a oblasti jako škálovací sada a nastavte *--enabled-for-disk-encryption* zásady přístupu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Použití existujícího trezoru klíčů

Tento krok je vyžadován pouze v případě, že máte existující trezor klíčů, který chcete použít s šifrováním disku. Tento krok přeskočte, pokud jste v předchozí části vytvořili trezor klíčů.

Definujte svůj vlastní jedinečný *keyvault_name*. Potom aktualizoval i technologii KeyVault aktualizací az keyvault a nastavil zásadu přístupu k šifrování --enabled-for-disk.Then, updated your KeyVault with [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) and set *the --enabled-for-disk-encryption* access policy.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Povolit šifrování

Chcete-li šifrovat instance virtuálních počítačců ve škálovací sadě, nejprve získejte některé informace o ID prostředku trezoru klíčů s [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Tyto proměnné se používají k následnému spuštění procesu šifrování pomocí [šifrování AZ VMSS:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

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

Vzhledem k tomu, že škálovací sada je zásada upgradu na škálovací sadě vytvořené v předchozím kroku nastavena na *automatické*, instance virtuálních počítačů automaticky spustí proces šifrování. Na škálovacích sadách, kde je zásada upgradu ruční, spusťte zásady šifrování na instancích virtuálních počítačů s [instancemi aktualizace az vmss](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolení šifrování pomocí kek zabalit klíč

Šifrovací klíč klíče můžete také použít pro zvýšení zabezpečení při šifrování škálovací sady virtuálních strojů.

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
>  Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxe pro hodnotu parametru klíč šifrování klíč je úplné URI k KEK jako v:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Když jsou instance virtuálních zařízení zašifrovány, stavový kód hlásí *EncryptionState/encrypted*, jak je znázorněno v následujícím příkladu výstupu:

```bash
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

Pokud už nechcete používat šifrované disky instancí virtuálních počítačů, můžete zakázat šifrování [šifrováním az vmss zakácet](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) následujícím způsobem:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Další kroky

- V tomto článku jste použili Azure CLI k šifrování škálovací sady virtuálních strojů. Můžete taky použít azure [powershellnebo](disk-encryption-powershell.md) [azure resource manager šablony](disk-encryption-azure-resource-manager.md).
- Pokud chcete, aby bylo šifrování disku Azure použito po zřízení jiného rozšíření, můžete použít [řazení rozšíření](virtual-machine-scale-sets-extension-sequencing.md). 
- Příklad dávkového souboru začátku do konce pro šifrování datového disku škálovací sady [Linuxu](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)naleznete zde . Tento příklad vytvoří skupinu prostředků, škálovací sadu Linuxu, připojí datový disk o velikosti 5 GB a zašifruje škálovací sadu virtuálních počítačů.
