---
title: Povolení šifrování disku pro clustery Windows
description: Tento článek popisuje, jak povolit šifrování disku pro uzly clusteru Azure Service Fabric pomocí Azure Key Vault v Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78251818"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Povolení šifrování disku pro uzly clusteru Azure Service Fabric ve Windows 
> [!div class="op_single_selector"]
> * [Šifrování disku pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

V tomto kurzu se dozvíte, jak povolit šifrování disku na Service Fabric uzlech clusteru v systému Windows. U každého typu uzlu a sady škálování virtuálních počítačů budete muset postupovat podle těchto kroků. Pro šifrování uzlů použijeme funkci Azure Disk Encryption ve službě Virtual Machine Scale Sets.

Průvodce obsahuje následující témata:

* Klíčové koncepty, které je třeba znát při povolování šifrování disku ve Service Fabric cluster Virtual Machine Scale Sets v systému Windows.
* Kroky, které je třeba provést před povolením šifrování disku na Service Fabric uzlech clusteru v systému Windows.
* Postup pro povolení šifrování disku na Service Fabric uzlech clusteru v systému Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

**Samoobslužná registrace** 

Náhled šifrování disku pro sadu škálování virtuálního počítače vyžaduje samoobslužnou registraci. Použijte k tomu následující postup: 

1. Nejdřív spusťte následující příkaz:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Počkejte asi 10 minut, než se *zaregistruje*stav čtení. Stav můžete zjistit spuštěním následujícího příkazu: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Vytvořte Trezor klíčů ve stejném předplatném a oblasti jako sadu škálování a pak vyberte zásadu přístupu **EnabledForDiskEncryption** k trezoru klíčů pomocí jeho rutiny PowerShellu. Zásadu můžete také nastavit pomocí uživatelského rozhraní Key Vault v Azure Portal pomocí následujícího příkazu:
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
* Pro operační systémy a datové svazky ve Windows Virtual Machine Scale Sets se podporuje šifrování. Zakázání šifrování je podporované taky pro operační systémy a datové svazky pro služby Virtual Machine Scale Sets v systému Windows.
* Operace obnovení bitové kopie virtuálního počítače a upgradu pro Virtual Machine Scale Sets nejsou v aktuální verzi Preview podporované.


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

Pokud potřebujete vytvořit vlastní šablonu, která bude vyhovovat vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici na stránce [ukázek pro vytváření clusterů Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . Pokyny k [přizpůsobení šablony clusteru][customize-your-cluster-template] najdete v následujících pokynech.

Pokud již máte vlastní šablonu, dvakrát ověřte, že všechny tři parametry související s certifikátem v šabloně a souboru parametrů jsou pojmenovány takto a tyto hodnoty jsou null následujícím způsobem:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Nasazení aplikace do clusteru Service Fabric ve Windows
Pokud chcete nasadit aplikaci do clusteru, postupujte podle kroků a pokynů v části [nasazení a odebrání aplikací pomocí PowerShellu](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Ověřte, jestli je povolené šifrování disku pro sadu škálování virtuálního počítače ve Windows.
Spusťte následující příkazy, které vám pomohou získat stav celé sady škálování virtuálního počítače nebo jakékoli instance v sadě škálování.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Kromě toho se můžete přihlásit do sady škálování virtuálních počítačů a zajistit, aby byly jednotky šifrované.

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
V tomto okamžiku byste měli mít zabezpečený cluster a víte, jak povolit a zakázat šifrování disku pro Service Fabric uzly clusteru a sady škálování virtuálních počítačů. Podobné pokyny k Service Fabric uzlů clusteru v systému Linux najdete v tématu [šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
