---
title: Povolit šifrování disku u clusterů Service Fabric Linux | Microsoft Docs
description: Tento článek popisuje, jak povolit šifrování disku pro Service Fabric sadách škálování clusteru v Azure pomocí Azure Resource Manager a Azure Key Vault.
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
ms.date: 05/24/2018
ms.author: v-viban
ms.openlocfilehash: e5caa3a787ceb1c8828b4a52648a3c74546c217b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750454"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Povolit šifrování disku pro uzly clusteru Service Fabric Linux 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Pomocí následujících kroků povolte šifrování disku na uzly clusteru Azure Service Fabric Linux. Budete muset provést tyto pro jednotlivé typy uzlů nebo sady škálování virtuálního počítače. K šifrování uzly, budete používat funkce Azure Disk Encryption na sady škálování virtuálního počítače.

Průvodce zahrnuje následující postupy:

* Klíčové koncepty pro povolení šifrování disku na sady škálování virtuálního počítače pro clustery služby Fabric Linux.
* Požadovaných kroků dříve než povolíte šifrování disku na virtuálním počítači škálovat sady pro clustery služby Fabric Linux.
* Postup povolení a zákaz disku šifrování u sady škálování virtuálního počítače pro clustery služby Fabric Linux.


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
* Šifrovací sady škálování virtuálního počítače je podporováno pro datové svazky pro Linux sady škálování virtuálního počítače. Šifrování disku operačního systému není podporována v aktuální verzi preview pro Linux.
* Obnovení z Image virtuálního počítače sady škálování virtuálního počítače a operace upgradu nejsou podporovány v aktuální verzi preview.


## <a name="create-a-linux-cluster"></a>Vytvoření clusteru s podporou systému Linux

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

Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, doporučujeme spuštění s jedním z [ukázky šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pro Linux clustery. 

Pokud již máte vlastní šablonu, ujistěte se, že jsou všechny tři parametry týkající se certifikátu v šabloně i v souboru parametrů pojmenované následujícím způsobem. Ujistěte se, že hodnoty jsou null následující také.

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

Škálovací sady virtuálních počítačů Linux je podporováno pouze data šifrování disku. Proto je nutné přidat datový disk pomocí šablony Azure Resource Manager. Aktualizaci šablony pro datový disk zřizování následujícím způsobem:

```JSON
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

Tady jsou ekvivalentní příkazy příkazového řádku Azure CLI pro aktualizaci šablony. Změňte hodnoty v příkazech declare odpovídající hodnoty. Rozhraní příkazového řádku Azure podporuje všechny parametry, které podporují starší příkazy prostředí PowerShell.

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

### <a name="confirm-that-the-linux-data-disk-is-mounted"></a>Potvrďte, že je připojit datový disk Linux
Než budete pokračovat s šifrováním na škálovací sadu virtuálních počítačů Linux, ujistěte se, že přidané datový disk je správně připojené. Přihlaste se ke clusteru virtuálních počítačů Linux a spusťte příkaz LSBLK. 

Výstup by měl zobrazit přidané datový disk na sloupci přípojného bodu.


### <a name="deploy-an-application-to-the-linux-service-fabric-cluster"></a>Nasazení aplikace do clusteru Linux Service Fabric
Postupujte podle kroků a pokyny, které [nasazení aplikací do clusteru](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux).


## <a name="enable-disk-encryption-for-a-virtual-machine-scale-set"></a>Povolit šifrování disku pro sadu škálování virtuálního počítače
Povolte šifrování disku pro sadu škálování virtuálního počítače, který jste dříve vytvořili pro cluster Service Fabric Linux.
 
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
Použijte následující příkazy a získat stav sadu škálování celý virtuální počítač nebo jakoukoli instanci virtuálního počítače ve škálovací sadě. Můžete také přihlásit do clusteru virtuálních počítačů Linux a spusťte příkaz LSBLK. Výstup by měl zobrazit přidané datový disk, na sloupci přípojného bodu a `Type` sloupec jako `Crypt`.

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
Pokud je nutné zakázat šifrování disku pro škálování virtuálních počítačů pro cluster Service Fabric Linux nastavit, použijte následující příkazy. Zakázáním šifrování disku se vztahuje k sadě škálování celý virtuální počítač a není instancí. 

```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tuto chvíli máte cluster zabezpečené a víte, jak povolit a zakázat šifrování disku pro cluster Service Fabric Linux. V dalším kroku Další informace o [šifrování disku pro systém Windows](service-fabric-enable-azure-disk-encryption-windows.md). 

