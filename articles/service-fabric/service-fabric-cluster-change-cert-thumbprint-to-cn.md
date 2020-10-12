---
title: Aktualizace clusteru tak, aby používal běžný název certifikátu
description: Naučte se, jak přepínat Cluster Service Fabric, aby používal kryptografické otisky certifikátů k použití běžného názvu certifikátu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: a90290430616302dbbe9ab9cf717510070936529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247910"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Změna z kryptografického otisku certifikátu clusteru na běžný název
Žádné dva certifikáty nemohou mít stejný kryptografický otisk, což způsobuje, že je změna nebo Správa certifikátu clusteru obtížná. Více certifikátů však může mít stejný společný název nebo předmět.  Přepnutím nasazeného clusteru z použití kryptografických otisků certifikátů na použití běžných názvů certifikátů je Správa certifikátů mnohem jednodušší. Tento článek popisuje, jak aktualizovat běžící cluster Service Fabric tak, aby místo kryptografického otisku certifikátu používal běžný název certifikátu.

>[!NOTE]
> Pokud máte v šabloně deklarované dva kryptografické otisky, musíte provést dvě nasazení.  První nasazení se provádí před provedením kroků v tomto článku.  První nasazení nastaví vlastnost **kryptografický otisku** v šabloně na použitý certifikát a odstraní vlastnost **thumbprintSecondary** .  Pro druhé nasazení postupujte podle kroků v tomto článku.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Získat certifikát
Nejdřív Získejte certifikát od certifikační [autority (CA)](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být pro vlastní doménu, kterou vlastníte, a koupit z doménového registrátora. Například "azureservicefabricbestpractices.com"; Uživatelé, kteří nejsou zaměstnanci Microsoftu, nemůžou zřídit certifikáty pro domény MS, takže nebudete moct používat jako běžné názvy pro svůj certifikát názvy DNS ani Traffic Manager, a pokud vaše vlastní doména budete moct přeložit v Azure, budete muset zřídit [Azure DNS zónu](../dns/dns-delegate-domain-azure-dns.md) . Pokud chcete, aby portál odrážel vlastní alias domény pro váš cluster, budete také chtít deklarovat vlastní doménu, kterou vlastníte jako "managementEndpoint" clusteru.

Pro účely testování byste mohli získat certifikát podepsaný certifikační autoritou od bezplatné nebo otevřené certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch generovaných při nasazení clusteru Service Fabric v Azure Portal, nejsou podporovány. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrajte certifikát a nainstalujte ho do sady škálování.
V Azure je Cluster Service Fabric nasazený v sadě škálování virtuálního počítače.  Nahrajte certifikát do trezoru klíčů a nainstalujte ho do sady škálování virtuálních počítačů, na které je cluster spuštěný.

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
> Tajné klíče sady škálování nepodporují stejné ID prostředku pro dvě samostatné tajné klíče, protože každý tajný klíč je jedinečným prostředkem verze. 

## <a name="download-and-update-the-template-from-the-portal"></a>Stažení a aktualizace šablony z portálu
Certifikát je nainstalovaný v základní sadě škálování, ale musíte taky aktualizovat Cluster Service Fabric, aby se tento certifikát a jeho běžný název používal.  Teď Stáhněte šablonu pro nasazení clusteru.  Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do skupiny prostředků hostující cluster.  V **Nastavení**vyberte **nasazení**.  Vyberte nejnovější nasazení a klikněte na **Zobrazit šablonu**.

![Zobrazit šablony][image1]

Stáhněte si soubory JSON šablony a Parameters do svého místního počítače.

Nejprve v textovém editoru otevřete soubor parametrů a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Pak otevřete soubor šablony v textovém editoru a proveďte tři aktualizace, aby podporovaly běžný název certifikátu.

1. Do části **Parameters (parametry** ) přidejte parametr *certificateCommonName* :
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Zvažte také odebrání *certificateThumbprint*, již na něj nelze odkazovat v šabloně správce prostředků.

2. V prostředku **Microsoft. COMPUTE/virtualMachineScaleSets** aktualizujte rozšíření virtuálního počítače tak, aby místo kryptografického otisku používalo běžný název v nastavení certifikátu.  V **virtualMachineProfile** -> nastavení rozšíření virtualMachineProfile**extensionProfile** -> **Extensions**– -> **vlastnosti** -> **settings** -> **certifikátu**, přidat `"commonNames": ["[parameters('certificateCommonName')]"],` a odebrat `"thumbprint": "[parameters('certificateThumbprint')]",` .
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

3.  V prostředku **Microsoft. ServiceFabric/clustery** aktualizujte verzi rozhraní API na "2018-02-01".  Přidejte také nastavení **certificateCommonNames** s vlastností **commonNames** a odeberte nastavení **certifikátu** (s vlastností kryptografický otisk), jak je znázorněno v následujícím příkladu:
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

Další informace najdete v tématu [nasazení Service Fabric clusteru, který místo kryptografického otisku používá běžný název certifikátu.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Po provedení změn znovu nasaďte aktualizovanou šablonu.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Informace o tom, jak [vyměnit certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
