---
title: Povolit šifrování disku u clusterů Service Fabric Windows | Microsoft Docs
description: Tento článek popisuje, jak povolit šifrování disku pro uzly clusteru Service Fabric v Azure pomocí Azure Resource Manager a Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 58f9481fa27f44de30fd80fe52e9b6d06d341b41
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750889"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Povolit šifrování disku pro uzly clusteru Service Fabric Windows 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Pomocí následujících kroků povolte šifrování disku na uzly clusteru Azure Service Fabric Windows. Budete muset provést tyto pro jednotlivé typy uzlů nebo sady škálování virtuálního počítače. K šifrování uzly, budete používat funkce Azure Disk Encryption na sady škálování virtuálního počítače.

Průvodce zahrnuje následující postupy:

* Klíčové koncepty pro povolení šifrování disku na sady škálování virtuálního počítače pro clustery služby Windows Fabric.
* Požadovaných kroků dříve než povolíte šifrování disku na virtuálním počítači škálovat sady pro správu a údržbu prostředků infrastruktury clustery.
* Postup povolení a zákaz disku šifrování u sady škálování virtuálního počítače pro clustery služby Windows Fabric.


## <a name="prerequisites"></a>Požadavky
1. Registrovat předplatné tak, že zadáte následující příkaz:

   ```PowerShell
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
   ```
   
   Počkejte přibližně 10 minut, dokud je stav `Registered`. Stav můžete zkontrolovat spuštěním následujících příkazů:

   ```PowerShell
   Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
   ```

2. Vytvoření trezoru klíčů ve stejném předplatném, oblasti jako sada škálování. Nastavit zásady přístupu `EnabledForDiskEncryption` na trezor klíčů pomocí jeho rutiny prostředí PowerShell. Zásady můžete vytvořit také pomocí rozhraní Azure Key Vault na portálu Azure.

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
   ```

3. Nainstalujte [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), která obsahuje nejnovější příkazy šifrování.

4. Nainstalujte nejnovější verzi [Azure SDK z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Povolit pomocí následující rutiny ([nastavit](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) šifrování, načíst ([získat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stav šifrování a odebrat ([zakázat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) šifrování na škále nastavit instanci:

| Příkaz | Verze |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 nebo novější | AzureRM.Compute |
| Zakázat AzureRmVmssDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 nebo novější | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrovací sady škálování virtuálního počítače je podporována pouze pro škálování sady vytvořené pomocí spravovaného disky. Není podporováno pro sady škálování nativní (nebo nespravovaný) disku.
* Šifrovací sady škálování virtuálního počítače je podporováno pro operační systém a datové svazky pro sady škálování virtuálního počítače Windows. Můžete povolit a zakázat šifrování.
* Obnovení z Image virtuálního počítače sady škálování virtuálního počítače a operace upgradu nejsou podporovány v aktuální verzi preview.


## <a name="create-a-windows-cluster"></a>Vytvoření clusteru se systémem Windows

Použijte následující příkazy k vytvoření clusteru a povolení šifrování disku pomocí šablonu Azure Resource Manager a certifikát podepsaný svým držitelem.

### <a name="log-in-to-azure"></a>Přihlášení k Azure 

```PowerShell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-a-custom-template"></a>Použít vlastní šablonu 

Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, doporučujeme spuštění s jedním z [ukázky šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master).

Pokud již máte vlastní šablonu, ujistěte se, že jsou všechny tři související s certifikátem parametry v šabloně i v souboru parametrů pojmenované následujícím způsobem. Ujistěte se, že hodnoty jsou null následující také.

```JSON
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="deploy-an-application-to-the-windows-service-fabric-cluster"></a>Nasazení aplikace do clusteru Windows Service Fabric
Postupujte podle kroků a pokyny, které [nasazení aplikace do clusteru](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications).


## <a name="enable-disk-encryption-for-a-virtual-machine-scale-set"></a>Povolit šifrování disku pro sadu škálování virtuálního počítače
Povolte šifrování disku pro škálovací sadu virtuálních počítačů, které jste dříve vytvořili pro cluster služby Windows Fabric.
 
```PowerShell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


## <a name="validate-that-disk-encryption-is-enabled-for-a-virtual-machine-scale-set"></a>Ověřte, že disk je povolené šifrování pro sadu škálování virtuálního počítače
Použijte následující příkazy a získat stav sadu škálování celý virtuální počítač nebo jakoukoli instanci virtuálního počítače ve škálovací sadě. Můžete také přihlásit k virtuálnímu počítači v sadě škálování a ujistěte se, že jsou zašifrované jednotky.

```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


## <a name="disable-disk-encryption-for-a-virtual-machine-scale-set"></a>Zakázat šifrování disku pro sadu škálování virtuálního počítače 
Pokud je nutné zakázat šifrování disku pro škálování virtuálního počítače nastavit pro cluster Service Fabric Windows, použijte následující příkazy. Zakázáním šifrování disku se vztahuje k sadě škálování celý virtuální počítač a není instancí. 


```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tuto chvíli máte cluster zabezpečené a víte, jak povolit a zakázat šifrování disku pro cluster s podporou služby Windows Fabric. V dalším kroku Další informace o [šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md). 

