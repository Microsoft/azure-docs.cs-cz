---
title: Povolit šifrování disku pro clustery Azure Service Fabric s Linuxem | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit disk encryption pro škálování clusteru Service Fabric v Azure nastavit pomocí Azure Resource Manageru, Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161791"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Povolit Disk encryption pro uzly clusteru service fabric Linux 
> [!div class="op_single_selector"]
> * [Disk Encryption pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Postupem uvedeným níže povolte šifrování disku na uzly clusteru Service Fabric Linux. Je potřeba provést pro každý uzel typy nebo virtuální počítače škálovacích sad. Pro uzly šifruje, jsme se využívají funkce Azure Disk Encryption ve virtual machine scale sets.

Průvodce zahrnuje následující postupy:

* Nastavit klíčové koncepty nástroje, které potřebujete znát vypnuto povolit šifrování disku ve škálovací virtuálních počítačů Linux clusteru Service Fabric.
* Požadavky na postup následovat před povolením šifrování disku ve škálovací sadě virtuálních počítačů Linux clusteru Service Fabric.
* Postup bude následovat povolit šifrování disku ve škálovací virtuálních počítačů Linux clusteru Service Fabric nastavit.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* **Samoregistračního** – Pokud chcete použít, vyžaduje samoregistračního virtuálního počítače škálovací sady disk encryption ve verzi preview
* Vaše předplatné můžete registrovat proveďte následující kroky: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Počkejte přibližně 10 minut, dokud stav jako "Registrováno". Stav můžete zkontrolovat spuštěním následujícího příkazu: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Služba Azure Key Vault** – vytvořte trezor klíčů ve stejném předplatném a oblasti, jak škálování virtuálního počítače nastavit a nastavit zásady přístupu "EnabledForDiskEncryption" v trezoru klíčů pomocí jeho PS rutiny. Můžete také nastavit zásady, pomocí uživatelského rozhraní služby KeyVault na webu Azure Portal: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) , který obsahuje nové příkazy šifrování.
* Nainstalujte nejnovější verzi [sadu SDK Azure z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Tady jsou škálovací sady virtuálních počítačů ADE rutiny umožňující ([nastavit](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) šifrování, načtení ([získat](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stav šifrování a odebrat ([zakázat](/powershell/module/az.compute/disable-azvmssdiskencryption)) stupnice šifrování nastavit instanci. 

| Příkaz | Version |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 nebo novějším | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrování virtuálního počítače škálovací sady se podporuje jenom pro škálovací sady se spravovanými disky vytvořené a není podporováno pro nativní (nebo nespravované) disku škálovací sady.
* Šifrování virtuálního počítače škálovací sady se podporuje pro objem dat pro škálovací sadu virtuálních počítačů Linux. Šifrování disku operačního systému není podporován v aktuální verzi preview pro Linux.
* Obnovení z Image virtuálního počítače sady škálování virtuálního počítače a upgradu operace nejsou podporovány v aktuální verzi preview.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru s Linuxem a povolit šifrování disku

Použijte následující příkazy k vytvoření clusteru a povolení šifrování disků pomocí šablony Azure Resource Manageru a certifikát podepsaný svým držitelem.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, která už máte 

Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, důrazně doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pro Cluster s Linuxem. 

Pokud již jste vlastní šablony, pak se ujistěte se, že Překontrolujte, že jsou následující pojmenované všechny tři související s certifikátem parametry v šabloně a soubor parametrů a hodnot mají hodnotu null následujícím způsobem.

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

Od pro škálovací sadu virtuálních počítačů Linux – pouze šifrování disku je podporován, je potřeba přidat datový disk pomocí šablony Azure Resource Manageru. Aktualizace šablony pro zřízení disku data jak je uvedeno níže:

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
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Toto je ekvivalentní příkazu rozhraní příkazového řádku škol. Změňte hodnoty v příkazech declare na odpovídající hodnoty. CLI podporuje všechny ostatní parametry, které podporuje výše uvedený příkaz powershellu.

```azurecli
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

#### <a name="linux-data-disk-mounting"></a>Připojení disku data Linux
Než budeme pokračovat s šifrováním na škálovací sadu virtuálních počítačů Linux, musíte zajistit, aby přidal datový disk je správně připojené, nebo ne. Přihlaste se k virtuálnímu počítači s Linuxem clusteru a spusťte příkaz LSBLK. Výstup by měl zobrazit tuto přidal datový disk na sloupci přípojného bodu.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric s Linuxem
Postupujte podle kroků a která vám pomůže [nasazení aplikace do clusteru](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Povolit šifrování disků pro škálovací sadu virtuálních počítačů Linux clusteru Service Fabric vytvořené výše
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Ověřte, jestli nastavení povolená pro škálování virtuálních počítačů Linux šifrování disku.
Získání stavu škálovací sady celý virtuální počítač nebo všechny instance virtuálních počítačů ve škálovací sadě. Najdete v následujících příkazů.
Uživatele můžete kromě Přihlaste se k virtuálnímu počítači s Linuxem clusteru a spusťte příkaz LSBLK. Výstup by měl zobrazit tuto přidal datový disk na typ a přípojný bod sloupec jako Crypt pro přidání datového disku.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Zakázat šifrování disků pro škálovací sadu virtuálních počítačů Service Fabric Cluster 
Zakázat šifrování disku se vztahuje na celý virtuální počítač škálovací sadu a není instancí 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tomto okamžiku máte zabezpečeného clusteru, jak povolit nebo zakázat šifrování disků pro škálovací sadu virtuálních počítačů Linux clusteru Service Fabric. Dále [Disk Encryption pro Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
