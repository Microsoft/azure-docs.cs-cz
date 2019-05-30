---
title: Povolit šifrování disku pro clustery Azure Service Fabric s Linuxem | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit disk encryption pro uzly clusteru Azure Service Fabric v Linuxu pomocí Azure Resource Manageru a Azure Key Vault.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258767"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Povolit šifrování disku pro uzly clusteru Azure Service Fabric v Linuxu 
> [!div class="op_single_selector"]
> * [Disk Encryption pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Disk Encryption pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

V tomto kurzu se dozvíte, jak povolit šifrování disku na uzly clusteru Azure Service Fabric v Linuxu. Bude nutné postupovat podle následujících kroků pro všechny typy uzlů a škálovací sady virtuálních počítačů. Uzly šifruje, použijeme funkci Azure Disk Encryption ve virtual machine scale sets.

V Průvodci najdete následující témata:

* Klíčové koncepty vědět při nastaví povolení šifrování disku ve virtuálním počítači na úrovni clusterů Service Fabric v Linuxu.
* Kroky, kterými se můžete řídit před povolením šifrování disku v Service Fabric jsou uzly clusteru v systému Linux.
* Postup bude následovat povolit šifrování disku na uzly clusteru Service Fabric v Linuxu.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

 **Vlastní registrace**

Náhled šifrování disků pro škálovací sadu virtuálních počítačů vyžaduje samoobslužné registrace. Použijte k tomu následující postup:

1. Spusťte následující příkaz: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Počkejte asi 10 minut, dokud nebude stav zpracování *registrované*. Stav můžete zkontrolovat spuštěním následujícího příkazu:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Vytvoření služby key vault ve stejném předplatném a oblasti jako škálovací sada. Vyberte **EnabledForDiskEncryption** přístup zásadu pro trezor klíčů s použitím jeho rutinu Powershellu. Zásady můžete vytvořit také pomocí uživatelského rozhraní Key Vault na webu Azure Portal pomocí následujícího příkazu:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Nainstalujte nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli), který obsahuje nové příkazy šifrování.

3. Nainstalujte nejnovější verzi [sadu SDK Azure z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Tady jsou škálovací sady virtuálních počítačů Azure Disk Encryption rutiny umožňující ([nastavit](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) šifrování, načtení ([získat](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stav šifrování a odebrat ([zakázat](/powershell/module/az.compute/disable-azvmssdiskencryption)) instance sady šifrování na stupnici.


| Příkaz | Version |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 nebo novějším | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrování pro škálovací sady virtuálních počítačů je podporováno pouze pro škálovací sady se spravovanými disky vytvořené. Nepodporuje se pro nativní (nebo nespravované) disku škálovací sady.
* Šifrování a zakázat šifrování jsou podporovány pro svazky operačního systému a dat ve škálovacích sadách virtuálních počítačů v systému Linux. 
* Operace obnovení z Image a upgrade virtuálního počítače (VM) pro škálovací sady virtuálních počítačů nejsou podporovány v aktuální verzi preview.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru a povolení šifrování disku

Použijte následující příkazy k vytvoření clusteru a povolení šifrování disků pomocí šablony Azure Resource Manageru a certifikát podepsaný svým držitelem.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure  

Přihlaste se pomocí následujících příkazů:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, která už máte 

Pokud je potřeba vytvořit vlastní šablonu, doporučujeme použít některou ze šablon na [ukázkové šablony vytvoření clusteru Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) stránky. 

Pokud už máte vlastní šablonu, zkontrolujte, že jsou všechny tři parametry v šabloně a soubor parametrů s související s certifikátem pojmenované následujícím způsobem. Také se ujistěte, že hodnoty jsou null následujícím způsobem:

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

Protože podporuje pouze šifrování disku škálovací sady virtuálních počítačů v systému Linux, musíte přidat datový disk pomocí šablony Resource Manageru. Aktualizace šablony pro zřízení disku data následujícím způsobem:

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

Tady je ekvivalentní příkazu rozhraní příkazového řádku. Změňte hodnoty v příkazech declare na odpovídající hodnoty. Rozhraní příkazového řádku CLI podporuje všechny ostatní parametry, které podporuje předchozí příkaz prostředí PowerShell.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Připojení datového disku k instanci systému Linux
Než budete pokračovat s šifrováním na škálovací sadu virtuálních počítačů, zajistěte, aby přidal datový disk je správně připojené. Přihlaste se do clusteru virtuálního počítače s Linuxem a spustit **LSBLK** příkazu. Výstup by měl zobrazit tuto přidal datový disk v **přípojný bod** sloupce.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Nasazení aplikace do clusteru Service Fabric v Linuxu
K nasazení aplikace do clusteru, postupujte podle kroků a pokynů v [rychlý start: Nasazení kontejnerů Linuxu do Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Povolit šifrování disků pro škálovací sady virtuálních počítačů, které jste vytvořili dříve
Povolit šifrování disku virtuálního počítače škálovací sady jste vytvořili v předchozí kroky, spusťte následující příkazy:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Ověřte, jestli je povolené šifrování disků pro škálovací sadu virtuálních počítačů v Linuxu
Získat stav škálovací sady celý virtuální počítač nebo všechny instance ve škálovací sadě, spusťte následující příkazy.
Kromě toho můžete přihlásit ke clusteru virtuálních počítačů s Linuxem a spustit **LSBLK** příkazu. Výstup by měl zobrazit přidal datový disk v **přípojný bod** sloupci a **typ** byste si přečíst sloupec *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Zakázat šifrování disků pro škálovací sadu virtuálních počítačů v clusteru Service Fabric
Zakážete šifrování disků pro škálovací sadu virtuálních počítačů nastavte spuštěním následujících příkazů. Všimněte si, že zakazuje šifrování disku platí pro celý virtuální počítač škálovací sadu a nejedná se o jednotlivých instanci.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tomto okamžiku by měl mít zabezpečený cluster a vědět, jak povolit a zakázat šifrování disků pro uzly clusteru Service Fabric a škálovací sady virtuálních počítačů. Podobné pokyny na uzly clusteru Service Fabric v Linuxu najdete v tématu [Disk Encryption pro Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
