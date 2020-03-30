---
title: Povolení šifrování disku pro clustery Windows
description: Tento článek popisuje, jak povolit šifrování disku pro uzly clusteru Azure Service Fabric pomocí Azure Key Vault ve Správci prostředků Azure.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251818"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Povolení šifrování disku pro uzly clusteru Azure Service Fabric v systému Windows 
> [!div class="op_single_selector"]
> * [Šifrování disku pro systém Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

V tomto kurzu se dozvíte, jak povolit šifrování disku v uzlech clusteru Service Fabric v systému Windows. Budete muset postupovat podle těchto kroků pro každý z typů uzlů a škálovací sady virtuálních počítačů. Pro šifrování uzlů použijeme funkci Azure Disk Encryption na škálovacích sadách virtuálních počítačů.

Průvodce se zabývá následujícími tématy:

* Klíčové koncepty, které je třeba znát při povolení šifrování disku ve škálovacích sadách virtuálních počítačů clusteru Service Fabric v systému Windows.
* Kroky, které je třeba dodržovat před povolením šifrování disku v uzlech clusteru Service Fabric v systému Windows.
* Kroky, které je třeba dodržovat, aby bylo možné povolit šifrování disku v uzlech clusteru Service Fabric v systému Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

**Vlastní registrace** 

Náhled šifrování disku pro škálovací sadu virtuálních počítačů vyžaduje vlastní registraci. Použijte k tomu následující postup: 

1. Nejprve spusťte následující příkaz:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Počkejte asi 10 minut, dokud stav nepřečte *registrované*. Stav můžete zkontrolovat spuštěním následujícího příkazu: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Vytvořte trezor klíčů ve stejném předplatném a oblasti jako škálovací sada a pak vyberte zásadu přístupu **EnabledForDiskEncryption** v trezoru klíčů pomocí rutiny prostředí PowerShell. Zásadu můžete nastavit také pomocí uznatých klíčů na webu Azure Portal pomocí následujícího příkazu:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Nainstalujte nejnovější verzi [příkazového příkazu Konto ,](/cli/azure/install-azure-cli)která má nové příkazy šifrování.
3. Nainstalujte nejnovější verzi [sady Azure SDK z verze Azure PowerShellu.](https://github.com/Azure/azure-powershell/releases) Následují škálovací sada virtuálních počítačů Rutiny Azure Disk Encryption pro povolení[(set)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)šifrování, načtení[(získat)](/powershell/module/az.compute/get-azvmssvmdiskencryption)stav šifrování a odebrání[(zakázat)](/powershell/module/az.compute/disable-azvmssdiskencryption)šifrování v instanci škálovací sady.

| Příkaz | Version |  Zdroj  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStav   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssVMDiskEncryptionStav   | 1.0.0 nebo novější | Az.Compute |
| Zakázat-AzVmssDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 nebo novější | Az.Compute |
| Rozšíření Set-AzVmssDiskEncryptionExtension   | 1.0.0 nebo novější | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrování škálovacích sad virtuálních počítačů je podporováno pouze pro škálovací sady vytvořené pomocí spravovaných disků. Není podporována pro nativní (nebo nespravované) sady měřítka disku.
* Šifrování je podporováno pro operační systém a datové svazky ve škálovacích sadách virtuálních strojů v systému Windows. Zakázání šifrování je také podporováno pro operační systém a datové svazky pro škálovací sady virtuálních strojů v systému Windows.
* Operace reimage virtuálního počítače a upgradu pro škálovací sady virtuálních strojů nejsou v aktuálním náhledu podporované.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru a povolení šifrování disku

Pomocí následujících příkazů vytvořte cluster a povolte šifrování disku pomocí šablony Správce prostředků Azure a certifikátu podepsaného svým držitelem.

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

### <a name="use-the-custom-template-that-you-already-have"></a>Použijte vlastní šablonu, kterou již máte. 

Pokud potřebujete vytvořit vlastní šablonu tak, aby vyhovovala vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici na stránce [ukázky šablony vytvoření clusteru Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Část [šablony clusteru si můžete přizpůsobit][customize-your-cluster-template] v následujících pokynech.

Pokud již vlastní šablonu máte, zkontrolujte, zda jsou všechny tři parametry související s certifikátem v šabloně a v souboru parametrů pojmenovány následujícím způsobem a že hodnoty jsou null:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Nasazení aplikace do clusteru Service Fabric v systému Windows
Chcete-li nasadit aplikaci do clusteru, postupujte podle pokynů a pokynů na [adrese Nasazení a odebrání aplikací pomocí Prostředí PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Povolení šifrování disku pro škálovací sady virtuálních počítačů vytvořené dříve

Chcete-li povolit šifrování disku pro škálovací sady virtuálních počítačů, které jste vytvořili v předchozích krocích, spusťte následující příkazy:
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Ověření, zda je pro škálovací sadu virtuálních počítačů v systému Windows povoleno šifrování disku
Pomocí následujících příkazů získáte stav celé škálovací sady virtuálních strojů nebo libovolné instance ve škálovací sadě.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Kromě toho se můžete přihlásit k škálovací sadě virtuálních strojů a ujistěte se, že jsou jednotky zašifrované.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Zakázání šifrování disku pro škálovací sadu virtuálních počítačů v clusteru Service Fabric 
Zakažte šifrování disku pro škálovací sadu virtuálních počítačů spuštěním následujících příkazů. Všimněte si, že zakázání šifrování disku platí pro celou škálovací sadu virtuálních počítačů a ne na jednotlivé instance.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další kroky
V tomto okamžiku byste měli mít zabezpečený cluster a vědět, jak povolit a zakázat šifrování disku pro uzly clusteru Service Fabric a škálovací sady virtuálních počítačů. Podobné pokyny k uzlům clusteru Service Fabric v Linuxu naleznete v [tématu Šifrování disku pro Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
