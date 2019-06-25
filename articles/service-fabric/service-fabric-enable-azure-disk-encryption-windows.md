---
title: Povolit šifrování disku u clusterů Windows Azure Service Fabric | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit disk encryption pro uzly clusteru Azure Service Fabric v Azure Resource Manageru pomocí služby Azure Key Vault.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471410"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Povolit šifrování disku pro uzly clusteru Azure Service Fabric ve Windows 
> [!div class="op_single_selector"]
> * [Disk Encryption pro Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Disk Encryption pro Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

V tomto kurzu se dozvíte, jak povolit šifrování disku na uzly clusteru Service Fabric ve Windows. Bude nutné postupovat podle následujících kroků pro všechny typy uzlů a škálovací sady virtuálních počítačů. Uzly šifruje, použijeme funkci Azure Disk Encryption ve virtual machine scale sets.

V Průvodci najdete následující témata:

* Klíčové koncepty vědět při povolení šifrování disku ve virtuálním počítači na úrovni clusterů Service Fabric nastaví ve Windows.
* Kroky, kterými se můžete řídit před povolením šifrování disku v Service Fabric jsou uzly clusteru v Windows.
* Postup bude následovat povolit šifrování disku na uzly clusteru Service Fabric ve Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

**Vlastní registrace** 

Náhled šifrování disků pro škálovací sadu virtuálních počítačů vyžaduje samoobslužné registrace. Použijte k tomu následující postup: 

1. Nejprve spusťte následující příkaz:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Počkejte asi 10 minut, dokud nebude stav zpracování *registrované*. Stav můžete zkontrolovat spuštěním následujícího příkazu: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Vytvoření služby key vault ve stejném předplatném a oblasti jako škálovací sadu a pak vyberte **EnabledForDiskEncryption** přístup zásadu pro trezor klíčů s použitím jeho rutinu Powershellu. Zásady můžete vytvořit také pomocí uživatelského rozhraní Key Vault na webu Azure Portal pomocí následujícího příkazu:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Nainstalujte nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli), který obsahuje nové příkazy šifrování.
3. Nainstalujte nejnovější verzi [sadu SDK Azure z prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Tady jsou škálovací sady virtuálních počítačů Azure Disk Encryption rutiny umožňující ([nastavit](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) šifrování, načtení ([získat](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stav šifrování a odebrat ([zakázat](/powershell/module/az.compute/disable-azvmssdiskencryption)) instance sady šifrování na stupnici.

| Příkaz | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 nebo novějším | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 nebo novějším | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 nebo novějším | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Podporované scénáře pro šifrování disku
* Šifrování pro škálovací sady virtuálních počítačů je podporováno pouze pro škálovací sady se spravovanými disky vytvořené. Nepodporuje se pro nativní (nebo nespravované) disku škálovací sady.
* Šifrování je podporováno pro operační systém a datové svazky do virtuálního počítače škálovací sady v Windows. Zakázat šifrování je také podporována pro operační systém a datové svazky pro virtuální počítač škálovací sady v Windows.
* Operace obnovení z Image a upgrade virtuálního počítače pro škálovací sady virtuálních počítačů nejsou podporovány v aktuální verzi preview.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Vytvoření nového clusteru a povolení šifrování disku

Použijte následující příkazy k vytvoření clusteru a povolení šifrování disků pomocí šablony Azure Resource Manageru a certifikát podepsaný svým držitelem.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure 
Přihlaste se pomocí následujících příkazů:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Použít vlastní šablonu, která už máte 

Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony vytvoření clusteru Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) stránky. K [přizpůsobení šablony clusteru] [ customize-your-cluster-template] části, přečtěte si následující pokyny.

Pokud už máte vlastní šablonu, zkontrolujte, že jsou všechny tři parametry v šabloně a soubor parametrů s související s certifikátem pojmenované následujícím způsobem a, hodnoty jsou null následujícím způsobem:

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
K nasazení aplikace do clusteru, postupujte podle kroků a pokynů v [nasazení a odeberte aplikací pomocí prostředí PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Ověřte, jestli je povolené šifrování disků pro škálovací sadu virtuálních počítačů, nastavte ve Windows
Získání stavu škálovací sady celý virtuální počítač nebo všechny instance ve škálovací sadě spuštěním následujících příkazů.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Kromě toho můžete přihlásit do škálovací sady virtuálního počítače a ujistěte se, že jsou zašifrované jednotky.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Zakázat šifrování disků pro škálovací sadu virtuálních počítačů v clusteru Service Fabric 
Zakážete šifrování disků pro škálovací sadu virtuálních počítačů nastavte spuštěním následujících příkazů. Všimněte si, že zakazuje šifrování disku platí pro celý virtuální počítač škálovací sadu a nejedná se o jednotlivých instanci.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Další postup
V tomto okamžiku by měl mít zabezpečený cluster a vědět, jak povolit a zakázat šifrování disků pro uzly clusteru Service Fabric a škálovací sady virtuálních počítačů. Podobné pokyny na uzly clusteru Service Fabric v Linuxu najdete v tématu [Disk Encryption pro Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
