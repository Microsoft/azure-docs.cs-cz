---
title: Povolení šifrování disku pro clustery se systémem Linux
description: Tento článek popisuje, jak povolit šifrování disku pro uzly clusteru Azure Service Fabric v systému Linux pomocí Azure Resource Manager a Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "78252814"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Povolení šifrování disku pro uzly clusteru Azure Service Fabric v systému Linux 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

V tomto kurzu se dozvíte, jak povolit šifrování disku na uzlech clusteru Azure Service Fabric v systému Linux. U každého typu uzlu a sady škálování virtuálních počítačů budete muset postupovat podle těchto kroků. Pro šifrování uzlů použijeme funkci Azure Disk Encryption ve službě Virtual Machine Scale Sets.

Průvodce obsahuje následující témata:

* Klíčové koncepty, které je třeba znát při povolování šifrování disku v Service Fabric cluster Virtual Machine Scale Sets v systému Linux.
* Postup, který se má provést, než povolíte šifrování disku na Service Fabric uzlech clusteru v systému Linux.
* Postup pro povolení šifrování disku na Service Fabric uzlech clusteru v systému Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

 **Samoobslužná registrace**

Náhled šifrování disku pro sadu škálování virtuálního počítače vyžaduje samoobslužnou registraci. Použijte k tomu následující postup:

1. Spusťte následující příkaz: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Počkejte asi 10 minut, než se *zaregistruje* stav čtení. Stav můžete zjistit spuštěním následujícího příkazu:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Vytvořte Trezor klíčů ve stejném předplatném a oblasti jako sadu škálování. Pak vyberte zásadu přístupu **EnabledForDiskEncryption** k trezoru klíčů pomocí jeho rutiny prostředí PowerShell. Zásadu můžete také nastavit pomocí uživatelského rozhraní Key Vault v Azure Portal pomocí následujícího příkazu:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Nainstalujte nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli), který obsahuje nové příkazy šifrování.

3. Nainstalujte si nejnovější verzi [sady Azure SDK z verze Azure PowerShell](https://github.com/Azure/azure-powershell/releases) . Níže jsou uvedené rutiny škálování virtuálního počítače Azure Disk Encryption, které umožňují ([nastavit](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) šifrování, načíst ([získat](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stav šifrování a odebrat ([Zakázat](/powershell/module/az.compute/disable-azvmssdiskencryption)) šifrování u instance sady škálování.


| Příkaz | Verze |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 nebo novější | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 nebo novější | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disků
* Šifrování pro Virtual Machine Scale Sets se podporuje jenom pro sady škálování vytvořené pomocí spravovaných disků. U nativních (nebo nespravovaných) sad diskových škálování se nepodporuje.
* Šifrování i Zakázání šifrování se podporuje pro operační systémy a datové svazky ve virtuálních počítačích se systémem Linux. 
* Operace obnovení bitové kopie virtuálního počítače (VM) a upgradu pro Virtual Machine Scale Sets nejsou v aktuální verzi Preview podporované.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru a povolení šifrování disku

Pomocí následujících příkazů vytvořte cluster a povolte šifrování disku pomocí šablony Azure Resource Manager a certifikátu podepsaného svým držitelem.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure  

Přihlaste se pomocí následujících příkazů:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Použijte vlastní šablonu, kterou už máte. 

Pokud potřebujete vytvořit vlastní šablonu, důrazně doporučujeme použít jednu ze šablon na stránce [ukázek pro vytváření clusterů Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . 

Pokud již máte vlastní šablonu, dvakrát ověřte, zda všechny tři parametry související s certifikátem v šabloně a souboru parametrů jsou pojmenovány následovně. Také zajistěte, aby hodnoty byly null následujícím způsobem:

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

Vzhledem k tomu, že pro sadu škálování virtuálního počítače v systému Linux je podporována pouze šifrování datových disků, je nutné přidat datový disk pomocí šablony Správce prostředků. Aktualizujte šablonu pro zajištění datového disku následujícím způsobem:

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

Toto je ekvivalentní příkaz CLI. Změňte hodnoty v příkazech Declare na příslušné hodnoty. Rozhraní příkazového řádku podporuje všechny ostatní parametry, které podporuje předchozí příkaz prostředí PowerShell.

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
Než budete pokračovat v šifrování u sady škálování virtuálních počítačů, ujistěte se, že je přidaný datový disk správně připojený. Přihlaste se k virtuálnímu počítači clusteru se systémem Linux a spusťte příkaz **LSBLK** . Výstup by měl zobrazit, že se do sloupce **přípojného bodu** přidal datový disk.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Nasazení aplikace do clusteru Service Fabric v systému Linux
Pokud chcete nasadit aplikaci do clusteru, postupujte podle kroků a pokynů v části [rychlý Start: nasazení kontejnerů Linux do Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Povolit šifrování disku pro sady škálování virtuálních počítačů vytvořené dříve
Pokud chcete povolit šifrování disku pro sady škálování virtuálních počítačů, které jste vytvořili v předchozích krocích, spusťte následující příkazy:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Ověří, jestli je povolené šifrování disku pro sadu škálování virtuálního počítače v systému Linux.
Pokud chcete získat stav celé sady škálování virtuálních počítačů nebo jakékoli instance v sadě škálování, spusťte následující příkazy.
Kromě toho se můžete přihlásit k virtuálnímu počítači clusteru se systémem Linux a spustit příkaz **LSBLK** . Výstup by měl zobrazit přidaný datový disk ve sloupci **přípojný bod** a sloupec **typ** by měl číst *kryptografii*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Zakázat šifrování disku pro sadu škálování virtuálního počítače v clusteru Service Fabric
Zakažte šifrování disku pro sadu škálování virtuálního počítače spuštěním následujících příkazů. Všimněte si, že zakázání šifrování disku se vztahuje na celou sadu škálování virtuálního počítače a ne na jednotlivé instance.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další kroky
V tomto okamžiku byste měli mít zabezpečený cluster a víte, jak povolit a zakázat šifrování disku pro Service Fabric uzly clusteru a sady škálování virtuálních počítačů. Podobné pokyny týkající se Service Fabric uzlů clusteru v systému Linux najdete v tématu [šifrování disku pro systém Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
