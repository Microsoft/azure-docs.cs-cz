---
title: Aktualizace clusteru pro použití běžného názvu certifikátu
description: Zjistěte, jak přepnout cluster Service Fabric z použití kryptografických otisků certifikátu na použití běžného názvu certifikátu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272623"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Změna z kryptografického otisku certifikátu clusteru na běžný název
Žádné dva certifikáty mohou mít stejný kryptografický otisk, což ztěžuje přechod na certifikát y clusteru nebo správu. Více certifikátů však může mít stejný běžný název nebo předmět.  Přepnutí mno ženete nasazený cluster z použití kryptografických otisků certifikátů na běžné názvy certifikátů, což usnadňuje správu certifikátů. Tento článek popisuje, jak aktualizovat spuštěný cluster Service Fabric použít běžný název certifikátu namísto kryptografického otisku certifikátu.

>[!NOTE]
> Pokud máte dva kryptografický otisk deklarované v šabloně, je třeba provést dvě nasazení.  První nasazení se provádí před provedením kroků v tomto článku.  První nasazení nastaví vlastnost **kryptografického otisku** v šabloně na používaný certifikát a odebere **thumbprintSecondary** vlastnost.  Pro druhé nasazení postupujte podle kroků v tomto článku.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Získání certifikátu
Nejprve získejte certifikát od [certifikační autority .](https://wikipedia.org/wiki/Certificate_authority)  Běžný název certifikátu by měl být pro vlastní doménu, kterou vlastníte, a měl by být zakoupen od doménového registrátora. Například "azureservicefabricbestpractices.com"; ti, kteří nejsou zaměstnanci společnosti Microsoft, nemohou zřídit certifikáty pro domény MS, takže nemůžete používat názvy DNS vašeho LB nebo Traffic Manageru jako běžné názvy pro váš certifikát a budete muset zřídit [Zónu Azure DNS,](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) pokud má být vaše vlastní doména v Azure řešitelná. Budete také chtít deklarovat vlastní doménu, kterou vlastníte jako "managementEndpoint" vašeho clusteru, pokud chcete, aby portál odrážel vlastní alias domény pro váš cluster.

Pro účely testování můžete získat certifikát podepsaný certifikační autoritou od svobodné nebo otevřené certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně certifikátů generovaných při nasazování clusteru Service Fabric na webu Azure Portal, nejsou podporovány. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrajte certifikát a nainstalujte jej do škálovací sady
V Azure se cluster Service Fabric nasazuje na škálovací sadě virtuálních strojů.  Nahrajte certifikát do trezoru klíčů a nainstalujte jej do škálovací sady virtuálních strojů, na které je cluster spuštěn.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Tajné klíče škálovací sady nepodporují stejné ID prostředku pro dva samostatné tajné klíče, protože každý tajný klíč je jedinečný prostředek s verzí. 

## <a name="download-and-update-the-template-from-the-portal"></a>Stažení a aktualizace šablony z portálu
Certifikát byl nainstalován v základní škálovací sadě, ale je také nutné aktualizovat cluster Service Fabric, aby se tento certifikát a jeho běžný název používaly.  Nyní si stáhněte šablonu pro nasazení clusteru.  Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte na skupinu prostředků hostující cluster.  V **části Nastavení**vyberte Možnost **Nasazení**.  Vyberte nejnovější nasazení a klepněte na **tlačítko Zobrazit šablonu**.

![Zobrazit šablony][image1]

Stáhněte si šablonu a parametry JSON soubory do místního počítače.

Nejprve otevřete soubor parametrů v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Dále otevřete soubor šablony v textovém editoru a proveďte tři aktualizace pro podporu běžného názvu certifikátu.

1. V části **parametry** přidejte parametr *certificateCommonName:*
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Zvažte také odebrání *certifikátuThumbprint*, může již být odkazováno v šabloně Správce prostředků.

2. V prostředku **Microsoft.Compute/virtualMachineScaleSets** aktualizujte rozšíření virtuálního počítače tak, aby používalo běžný název v nastavení certifikátu namísto kryptografického otisku.  V rozšíření **virtualMachineProfileProfil**->**extensionProfile**->rozšíření**nastavení**-> `"commonNames": ["[parameters('certificateCommonName')]"],` `"thumbprint": "[parameters('certificateThumbprint')]",`**certifikátu****vlastností**->**properties**->, přidat a odebrat .
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  V prostředku **Microsoft.ServiceFabric/clusters** aktualizujte verzi rozhraní API na "2018-02-01".  Také přidejte nastavení **certificateCommonNames** s vlastností **commonNames** a odeberte nastavení **certifikátu** (s vlastností thumbprint) jako v následujícím příkladu:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

Další informace naleznete [v tématu Nasazení clusteru Service Fabric, který používá běžný název certifikátu namísto kryptografického otisku.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Po provedení změn znovu nasaďte aktualizovanou šablonu.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Další kroky
* Informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Přečtěte si, jak [převést certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
