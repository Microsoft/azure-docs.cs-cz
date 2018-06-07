---
title: Povolit šifrování disku pro clustery služby infrastruktury Linux | Microsoft Docs
description: Tento článek popisuje, jak povolit šifrování disku pro škálování clusteru Service Fabric nastavit v Azure pomocí Azure Resource Manager, Azure Key Vault.
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
ms.openlocfilehash: 46f7f88768ab7ae9d84f392f340750865fef3b96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655000"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Povolit šifrování disku pro uzly clusteru Linux service fabric 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Použijte následující postup pro zapnutí šifrování disku na uzly clusteru Linux Service Fabric. Musíte provést tyto pro jednotlivé sady škálování uzlu typy nebo virtuální počítače. Pro šifrování uzly, jsme bude využívat možnosti Azure Disk Encryption na sady škálování virtuálního počítače.

Průvodce zahrnuje následující postupy:

* Nastavit klíčové koncepty, které je nutné znát vypnuto povolit šifrování disku na Service Fabric Linux Cluster škálování virtuálních počítačů.
* Předpoklady postup provést před povolením šifrování disku na škálovací sadu virtuálních počítačů Service Fabric Linux Cluster.
* Postup pro povolení šifrování disku na škálování virtuálních počítačů Service Fabric Linux Cluster být sledována nastavit.


## <a name="prerequisites"></a>Požadavky

1. **Vlastní registraci** – Pokud chcete použít, virtuální počítač škálování sadu disku šifrování preview vyžaduje vlastní registraci
2. Vaše předplatné mohou registrovat spuštěním následující kroky: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Počkejte přibližně 10 minut, dokud se stav jako "Registrovaná". Stav můžete zkontrolovat spuštěním následujícího příkazu: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** -vytvořte KeyVault ve stejném předplatném, oblasti, jak nastavit škálování virtuálních počítačů a nastavte zásady přístupu, EnabledForDiskEncryption' na KeyVault pomocí jeho PS rutiny. Můžete také nastavit zásady, pomocí uživatelského rozhraní KeyVault na portálu Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Nainstalujte nejnovější [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , který má nové příkazy šifrování.
6. Nainstalujte nejnovější verzi [Azure SDK z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) verzi. Toto jsou rutiny VMSS ADE povolit ([nastavit](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) šifrování, načíst ([získat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stav šifrování a odebrat ([zakázat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) šifrování na škálování nastavit instanci. 

| Příkaz | Verze |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 nebo novější | AzureRM.Compute |
| Zakázat AzureRmVmssDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 nebo novější | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 nebo novější | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrovací sady škálování virtuálního počítače je podporována pouze pro sady škálování vytvořit s disky, spravované a nejsou podporovány pro sady škálování nativní (nebo nespravovaný) disku.
* Šifrovací sady škálování virtuálního počítače je podporována pro datový svazek pro škálovací sadu virtuálních počítačů Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux.
* Sady škálování virtuálního počítače znovu přeinstalovat image virtuálních počítačů a operace upgradu nejsou podporovány v aktuální verzi preview.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Vytvořte nový cluster s Linuxem a povolte šifrování disku

Použijte následující příkazy k vytvoření clusteru a povolení šifrování disku pomocí šablony Azure Resource Manager & vlastního certifikátu podepsaného držitelem.

### <a name="log-in-to-azure"></a>Přihlášení k Azure  

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, kterou už máte 

Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, důrazně doporučujeme začínat jednu z šablon, které jsou dostupné na [ukázky šablony služby azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pro Cluster s Linuxem. 

Pokud již máte vlastní šablony a poté se ujistěte se, zda Překontrolujte, že jsou všechny tři související s certifikátem parametry v šabloně i v souboru parametrů takto pojmenované a hodnoty mají hodnotu null následujícím způsobem.

```Json
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

Od škálovací sadu virtuálních počítačů Linux - šifrování disku pouze data jsou podporovány, je potřeba přidat datový disk pomocí šablony Azure Resource Manager. Aktualizaci šablony pro přidělení disku data jak je uvedeno níže:

```Json
   
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
 

```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Zde je ekvivalentní příkaz rozhraní příkazového řádku o stejnou akci. Změňte hodnoty v příkazech declare odpovídající hodnoty. Rozhraní příkazového řádku podporuje všech dalších parametrů, které podporuje výše uvedeném příkazu prostředí powershell.

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

#### <a name="linux-data-disk-mounting"></a>Připojování disku data Linux
Před pokračováním se šifrováním na škálovací sadu virtuálních počítačů Linux, musíme Ujistěte se, že přidané datový disk je správně připojit nebo ne. Přihlášení k virtuálním počítačům s Linuxem clusteru a spusťte příkaz LSBLK. Výstup by měl zobrazit tento disk přidaný dat ve sloupci přípojného bodu.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Nasazení aplikací do clusteru Linux Service Fabric
Postupujte podle kroků a pokyny, které [nasazení aplikací do clusteru](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Povolit šifrování disku pro škálovací sadu virtuálních počítačů pro Service Fabric Linux Cluster vytvořili výše
 
```Powershell
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Ověřte, jestli nastavení pro škálování virtuálních počítačů Linux, zapnuté šifrování disku.
Získáte stav sadu škálování celý virtuální počítač nebo jakoukoli instanci virtuálního počítače ve škálovací sadě. Najdete v níže uvedené příkazy.
Uživatele můžete kromě přihlášení do virtuálního počítače s Linuxem clusteru a spusťte příkaz LSBLK. Výstup by měl zobrazit tento disk přidaný data na přípojného bodu sloupce a sloupce typu jako Crypt pro přidání datový disk.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```



#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Zakázat šifrování disku pro škálovací sadu virtuálních počítačů Service Fabric Cluster 
Zakázat šifrování disku se vztahuje k sadě škálování celý virtuální počítač a není instancí 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tuto chvíli máte zabezpečené clusteru se povolení a zakázání šifrování disku pro škálovací sadu virtuálních počítačů Linux Service Fabric Cluster. Dále [disku šifrování pro Windows](service-fabric-enable-azure-disk-encryption-windows.md) 

