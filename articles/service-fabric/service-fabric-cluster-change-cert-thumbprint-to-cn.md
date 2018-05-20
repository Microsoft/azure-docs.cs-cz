---
title: Aktualizace clusteru služby Azure Service Fabric používat běžný název certifikátu | Microsoft Docs
description: Zjistěte, jak přepínat cluster Service Fabric pomocí kryptografické otisky certifikátů pomocí běžný název certifikátu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 39dc5800edd743cdc950c7a96f7633fb4c0a7c45
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Změňte clusteru z kryptografický otisk certifikátu běžný název
Dva certifikáty, může mít stejné kryptografický otisk, což znesnadňuje clusteru certifikáty vyměnit nebo správu. Více certifikátů, však může mít stejný běžný název nebo předmětu.  Přepínání cluster nasazené pomocí kryptografické otisky certifikátu pomocí certifikátu běžné názvy usnadňuje správu certifikátů mnohem jednodušší. Tento článek popisuje postup aktualizace spuštěného clusteru Service Fabric, aby používaly běžný název certifikátu místo kryptografický otisk certifikátu.
 
## <a name="get-a-certificate"></a>Získejte certifikát
Nejdřív získat certifikát z [certifikátu certifikační autority](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být název hostitele clusteru.  Například "myclustername.southcentralus.cloudapp.azure.com".  

Pro účely testování, může získat certifikát podepsaný certifikační Autoritou od volné nebo otevřít certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch, které generuje při nasazování clusteru Service Fabric na portálu Azure nejsou podporovány.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrát na server certifikát a nainstalujte ho v sadě škálování
V Azure je nasadit cluster Service Fabric na škálovací sadu virtuálních počítačů.  Nahrajte certifikát do trezoru klíčů a nainstalujte ji na škálovací sadu virtuálních počítačů, která v clusteru je spuštěn na.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

## <a name="download-and-update-the-template-from-the-portal"></a>Stáhněte si a aktualizovat šablonu z portálu.
Tento certifikát nainstalován na základní škálovací sadu, ale také musíte aktualizovat cluster Service Fabric, které chcete použít tento certifikát a jeho běžný název.  Nyní stáhněte si šablonu pro vaše nasazení clusteru.  Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do skupiny prostředků clusteru hostování.  V **nastavení**, vyberte **nasazení**.  Vyberte nejnovější nasazení a klikněte na tlačítko **zobrazit šablonu**.

![Zobrazení šablony][image1]

Stáhněte soubory JSON šablony a parametrů do místního počítače.

Nejprve v textovém editoru otevřete soubor parametrů a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

V dalším kroku v textovém editoru otevřete soubor šablony a proveďte tři aktualizace pro podporu běžný název certifikátu.

1. V **parametry** přidejte *certificateCommonName* parametr:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Také zvažte odebrání *certificateThumbprint*, mohou už nepotřebují.

2. V **Microsoft.Compute/virtualMachineScaleSets** prostředků, aktualizovat rozšíření virtuálního počítače používat běžný název v nastavení certifikátu místo kryptografický otisk.  V **virtualMachineProfile**->**extenstionProfile**->**rozšíření**->**vlastnosti** -> **nastavení**->**certifikát**, přidejte `"commonNames": ["[parameters('certificateCommonName')]"],` a odeberte `"thumbprint": "[parameters('certificateThumbprint')]",`.
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
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  V **Microsoft.ServiceFabric/clusters** prostředků, verze aktualizace rozhraní API "2018-02-01".  Také přidat **certificateCommonNames** nastavení s **commonNames** vlastnost a odebrat **certifikát** nastavení (s kryptografickým otiskem vlastností) jako v následujícím příkladu Příklad:
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

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Po provedení změny znovu nasaďte aktualizovanou šablonu.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>Další postup
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* Zjistěte, jak [výměny certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a spravovat certifikáty clusteru](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png