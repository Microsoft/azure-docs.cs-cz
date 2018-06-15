---
title: Šifrování disků pro sadách škálování Azure pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Další informace o použití Azure CLI 2.0 k šifrování instance virtuálních počítačů a připojené disky ve škálovací sadě virtuálních počítačů Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776410"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Šifrování operačního systému a připojené datové disky v škálování virtuálního počítače s 2.0 rozhraní příkazového řádku Azure (Preview)

K ochraně a ochrany dat v klidovém stavu pomocí technologie oborový standard šifrování, podporují sady škálování virtuálního počítače Azure Disk Encryption (ADE). Šifrování lze povolit pro Linux a Windows virtuálního počítače sady škálování. Další informace najdete v tématu [Azure Disk Encryption pro Linux a Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption pro sady škálování virtuálního počítače je aktuálně ve verzi public preview, k dispozici ve všech oblastech Azure veřejné.

Šifrování Azure disku je podporován:
- pro škálování sady vytvořené s spravovaných disků a nejsou podporovány pro sady škálování nativní (nebo nespravovaný) disku.
- pro svazky operačního systému a dat v systému Windows sady škálování. Zakázat šifrování je podporované pro svazky operačního systému a dat pro sady škálování systému Windows.
- pro datové svazky v sadách škálování Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux sady škálování.

Operace obnovení z Image a upgrade sady virtuálních počítačů škálování nejsou podporovány v aktuální verzi preview. Azure disk šifrování pro náhled sady škálování virtuálního počítače se doporučuje jenom v testovacích prostředích. Ve verzi preview nepovolujte šifrování disku v produkčním prostředí, kde je třeba upgradovat image operačního systému v sadě škálování šifrované.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, tento kurz vyžaduje, že používáte Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Registrace pro náhled šifrování disku

Azure disk encryption pro verzi preview sadách škálování virtuálních počítačů vyžaduje, abyste registrovat předplatné s [zaregistrovat funkci az](/cli/azure/feature#az_feature_register). Potřebujete jenom poprvé, že používáte funkci náhledu šifrování disku proveďte následující kroky:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

To může trvat až 10 minut, než požadavek na rozšíření. Můžete zkontrolovat stav registrace s [zobrazit funkce az](/cli/azure/feature#az_feature_show). Když `State` sestavy *registrovaná*, znovu zaregistrovat *Mirosoft.Compute* zprostředkovatele s [registrace zprostředkovatele az](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#az_vmss_create). Následující příklad vytvoří škálovací sadu *myScaleSet* nastavenou tak, aby se při provedení změn automaticky aktualizovala, a vygeneruje klíče SSH v adresáři *~/.ssh/id_rsa*, pokud ještě neexistují. Datový disk 32Gb je připojen k jednotlivým instancím virtuálních počítačů a Azure [rozšíření vlastních skriptů](../virtual-machines/linux/extensions-customscript.md) slouží k přípravě datových disků s [az vmss rozšíření sady](/cli/azure/vmss/extension#az_vmss_extension_set):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření Azure trezoru klíčů pro šifrování disku povoleno

Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití.

Definovat vlastní jedinečné *keyvault_name*. Pak vytvořte KeyVault s [vytvořit az keyvault](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ve stejném předplatném, oblasti jako měřítka nastavit a *– povoleno pro--šifrování disku* zásada přístupu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující Key Vault

Tento krok jenom je potřeba, pokud máte existující Key Vault, kterou chcete použít s šifrování disku. Tento krok přeskočte, pokud jste vytvořili v předchozí části Key Vault.

Definovat vlastní jedinečné *keyvault_name*. Potom aktualizovat vaše KeyVault s [az keyvault aktualizace](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) a nastavte *– povoleno pro--šifrování disku* zásada přístupu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Povolit šifrování

K šifrování instancí virtuálního počítače ve škálovací sadě, nejdřív získejte některé informace o ID prostředku Key Vault s [az keyvault zobrazit](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Tyto proměnné se používají k spusťte proces šifrování s [povolit šifrování vmss az](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Může trvat minutu nebo dvě pro proces šifrování spustit.

Jako sada škálování je zásad upgradu na rozsahu nastavit vytvořený v předchozím kroku je nastaven na *automatické*, instance virtuálních počítačů automaticky spustit proces šifrování. Na sady škálování, kde je ruční zásad upgradu, spusťte na zásady šifrování na instance virtuálních počítačů s [az vmss aktualizace instance](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [az vmss šifrování zobrazit](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Jsou šifrovaná instance virtuálních počítačů, stavový kód sestavy *EncryptionState/šifrované*, jak ukazuje následující příklad výstupu:

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

Pokud již nechcete používat šifrované disky instancí virtuálních počítačů, můžete zakázat šifrování s [az vmss šifrování zakázat](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) následujícím způsobem:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Další postup

V tomto článku jste použili 2.0 rozhraní příkazového řádku Azure k šifrování škálovací sadu virtuálních počítačů. Můžete také použít [prostředí Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) nebo šablon pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Příklad souboru začátku do konce dávky pro šifrování disku Linux škálování sady dat lze nalézt [zde](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Tento příklad vytvoří skupinu prostředků, sadě škálování Linux, připojí 5 GB datový disk a šifruje škálovací sadu virtuálních počítačů.