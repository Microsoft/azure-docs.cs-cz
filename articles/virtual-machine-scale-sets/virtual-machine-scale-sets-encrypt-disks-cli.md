---
title: Sady škálování virtuálního počítače Azure šifrovat disky | Microsoft Docs
description: Zjistěte, jak k šifrování připojených disků v sady škálování virtuálního počítače.
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
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Šifrování operačního systému a připojené datové disky v škálovací sadu virtuálních počítačů
Azure [sady škálování virtuálního počítače](/azure/virtual-machine-scale-sets/) podporu Azure disk encryption (ADE).  Šifrování Azure disku se dá nastavit pro Windows a Linux sadách škálování virtuálních počítačů k ochraně a zabezpečit měřítka nastaví data v klidovém stavu pomocí technologie oborový standard šifrování. Další informace najdete v tématu virtuální počítače Azure Disk Encryption pro systém Windows a Linux.

> [!NOTE]
>  Azure disk encryption pro sady škálování virtuálního počítače je aktuálně ve verzi public preview, k dispozici ve všech oblastech Azure veřejné.

Šifrování Azure disku je podporován:
- pro škálování sady vytvořené s spravovaných disků a nejsou podporovány pro sady škálování nativní (nebo nespravovaný) disku.
- pro svazky operačního systému a dat v systému Windows sady škálování. Zakázat šifrování je podporované pro svazky operačního systému a dat pro sady škálování systému Windows.
- pro datové svazky v sadách škálování Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux sady škálování.

Operace obnovení z Image a upgrade sady virtuálních počítačů škálování nejsou podporovány v aktuální verzi preview. Azure disk šifrování pro náhled sady škálování virtuálního počítače se doporučuje jenom v testovacích prostředích. Ve verzi preview nepovolujte šifrování disku v produkčním prostředí, kde je třeba upgradovat image operačního systému v sadě škálování šifrované.

Příklad souboru začátku do konce dávky pro šifrování disku Linux škálování sady dat lze nalézt [zde](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat).  Tento příklad vytvoří skupinu prostředků, sadě škálování Linux, připojí 5 GB datový disk a šifruje škálovací sadu virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky
Instalace nejnovější verze [prostředí Azure Powershell](https://github.com/Azure/azure-powershell/releases) nebo [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), které obsahují příkazy šifrování.

Azure disk šifrování pro náhled sady škálování virtuálního počítače vyžaduje, abyste samoobslužné registrace předplatného použijte následující příkazy Powershellu: 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Počkejte přibližně 10 minut, dokud nebude stav "Registrovaná" vrátí následující příkaz: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvoření Azure trezoru klíčů pro šifrování disku povoleno
Vytvoření nového trezoru klíčů ve stejném předplatném, oblasti jako měřítko a nastavte zásady přístupu, EnabledForDiskEncryption' a.

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

Nebo povolit existující trezor klíčů ve stejném předplatném, oblasti jako měřítka nastavit šifrování disku.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Povolit šifrování

Následující příkazy šifrování disku dat ve spuštěné škálování nastavit pomocí trezoru klíčů ve stejné skupině prostředků. Můžete také použít šablony k šifrování disků neběží [škálovací sadu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux škálovací sadu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Použijte následující příkazy zobrazíte stav šifrování škálovací sady.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Zakázat šifrování
Zakážete šifrování na spuštěné škálování virtuálních počítačů, nastavit pomocí následujících příkazů. Můžete také použít šablony můžete zakázat šifrování neběží [sadu škálování virtuálního počítače Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) nebo [sadu škálování virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux).

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

