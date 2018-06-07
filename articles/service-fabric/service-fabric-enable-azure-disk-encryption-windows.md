---
title: Povolit šifrování disku pro clustery Windows fabric služby | Microsoft Docs
description: Tento článek popisuje, jak povolit šifrování disku pro uzly clusteru Service Fabric v Azure pomocí Azure Resource Manager, Azure Key Vault.
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
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654999"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Povolit šifrování disku pro uzly clusteru Windows service fabric 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Použijte následující postup pro zapnutí šifrování disku na uzly clusteru Windows Service Fabric. Musíte provést tyto pro jednotlivé sady škálování uzlu typy nebo virtuální počítače. Pro šifrování uzly, jsme bude využívat možnosti Azure Disk Encryption na sady škálování virtuálního počítače.

Průvodce zahrnuje následující postupy:

* Klíčové koncepty, které potřebujete k mějte vypnuto povolit šifrování disku na škálování virtuálních počítačů Service Fabric Windows Cluster nastavit.
* Předpoklady postup provést před povolením šifrování disku na škálovací sadu virtuálních počítačů služby Cluster Windows Fabric.
* Postup pro povolení šifrování disku na škálování virtuálních počítačů Service Fabric Windows Cluster nastavit.


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
4. **Azure Key Vault** -vytvořit KeyVault ve stejném předplatném, oblasti měřítka nastavit a nastavit zásady přístupu, EnabledForDiskEncryption' na KeyVault pomocí jeho PS rutiny. Můžete také nastavit zásady, pomocí uživatelského rozhraní KeyVault na portálu Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Nainstalujte nejnovější [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , který má nové příkazy šifrování.
6. Nainstalujte nejnovější verzi [Azure SDK z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) verzi. Následují ADE rutiny povolit nastavení škálování virtuálních počítačů ([nastavit](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) šifrování, načíst ([získat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stav šifrování a odebrat ([zakázat](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) šifrování v sadě škálování instance.

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
* Šifrovací sady škálování virtuálního počítače je podporována pro svazky operačního systému a dat pro Windows VMSS. Zakázat šifrování je podporováno pro operační systém a datové svazky pro škálovací sadu Windows.
* Sady škálování virtuálního počítače znovu přeinstalovat image virtuálních počítačů a operace upgradu nejsou podporovány v aktuální verzi preview.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru a povolení šifrování disku

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

Pokud potřebujete vytvořit vlastní šablonu, aby vyhovovala vašim potřebám, důrazně doporučujeme začínat jednu z šablon, které jsou dostupné na [ukázky šablony služby azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postupujte podle pokynů a vysvětlení [přizpůsobení šablony clusteru] [přizpůsobit si cluster šablony] části.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Nasazení aplikací do clusteru Windows Service Fabric
Postupujte podle kroků a pokyny, které [nasazení aplikací do clusteru](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Povolit šifrování disku pro vytvořili výše škálovací sadu virtuálních počítačů pro Service Fabric Cluster
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Ověřte, jestli nastavení pro škálování virtuálních počítačů Windows, zapnuté šifrování disku.
Získáte stav sadu škálování celý virtuální počítač nebo jakoukoli instanci v sadě škálování. Najdete v níže uvedené příkazy.
Kromě toho můžete uživatele přihlásit k virtuálnímu počítači v sadě škálování a ujistěte se, že jsou zašifrované jednotky

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
V tuto chvíli máte zabezpečené clusteru se povolení a zakázání šifrování disku pro škálovací sadu virtuálních počítačů Service Fabric Cluster. Dále [disku šifrování pro Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

