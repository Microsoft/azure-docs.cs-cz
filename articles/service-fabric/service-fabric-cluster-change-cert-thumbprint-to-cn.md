---
title: Aktualizovat cluster Azure Service Fabric používat běžný název certifikátu | Dokumentace Microsoftu
description: Zjistěte, jak přepnout cluster Service Fabric pomocí kryptografické otisky certifikátů pomocí běžný název certifikátu.
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
ms.date: 01/01/2019
ms.author: ryanwi
ms.openlocfilehash: e1a52aff0890e32ae739285c0380258939f29597
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312867"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Změnit z kryptografický otisk certifikátu clusteru na běžný název
Žádné dva certifikáty můžou mít se stejným kryptografickým otiskem, což znesnadňuje clusteru certifikáty vyměnit nebo správy. Více certifikátů, ale mají stejný běžný název nebo předmětu.  Přepínání nasazeném clusteru pomocí kryptografické otisky certifikátů k běžnému názvu certifikátu pomocí certifikátu značně zjednodušuje správu. Tento článek popisuje postup aktualizace spuštěný cluster Service Fabric běžný název certifikátu použít místo kryptografického otisku certifikátu.

>[!NOTE]
> Pokud máte dva kryptografický otisk deklarované v šabloně, budete muset provést dvě nasazení.  První nasazení se provádí před provedením kroků v tomto článku.  První nasazení nastaví vaše **kryptografický otisk** vlastnosti v šabloně certifikátu používanému a odebere **thumbprintSecondary** vlastnost.  Druhé nasazení postupujte podle kroků v tomto článku.
 
## <a name="get-a-certificate"></a>Získání certifikátu
Nejprve Získejte certifikát [certifikační autority (CA)](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být název hostitelského clusteru.  Například "myclustername.southcentralus.cloudapp.azure.com".  

Pro účely testování, může získat certifikát podepsaný certifikační Autoritou z bezplatné nebo otevřete certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch, které generuje při nasazování clusteru Service Fabric na webu Azure Portal, se nepodporují.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrajte certifikát a nainstalovat ve škálovací sadě
V Azure se nasadí cluster Service Fabric na škálovací sadu virtuálních počítačů.  Nahrajte certifikát do služby key vault a nainstalujte ho na škálovací sadu virtuálních počítačů, spuštěné v clusteru.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Škálovací sada s tajnými kódy nepodporují stejné ID prostředku pro dva samostatné tajné kódy, jako každý tajný kód je označené verzí, jedinečný prostředek. 

## <a name="download-and-update-the-template-from-the-portal"></a>Stažení a aktualizace šablony z portálu
Certifikát je nainstalovaný na základní škálovací sadu, ale je také potřeba aktualizovat cluster Service Fabric používat tento certifikát a jeho běžný název.  Stáhněte šablonu pro vaše nasazení clusteru.  Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do skupiny prostředků, který je hostitelem clusteru.  V **nastavení**vyberte **nasazení**.  Vyberte poslední nasazení a klikněte na tlačítko **zobrazit šablonu**.

![Zobrazení šablony][image1]

Stáhněte si šablonu a parametry soubory JSON do místního počítače.

Nejprve otevřete soubor parametrů v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

V dalším kroku v textovém editoru otevřete soubor šablony a proveďte tři aktualizace pro podporu běžný název certifikátu.

1. V **parametry** části, přidejte *certificateCommonName* parametr:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Také zvažte odebrání *certificateThumbprint*, možná už nepotřebujete.

2. V **Microsoft.Compute/virtualMachineScaleSets** prostředků, aktualizujte rozšíření virtuálního počítače. Chcete-li použít běžný název v nastavení certifikátu místo kryptografického otisku.  V **virtualMachineProfile**->**extensionProfile**->**rozšíření**->**vlastnosti** -> **nastavení**->**certifikát**, přidejte `"commonNames": ["[parameters('certificateCommonName')]"],` a odebrat `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

3.  V **Microsoft.ServiceFabric/clusters** prostředků, verze aktualizace rozhraní API pro "2018-02-01".  Také přidat **certificateCommonNames** nastavení **commonNames** vlastnost a odebrat **certifikát** nastavení (pomocí vlastnosti kryptografický otisk), viz následující příklad Příklad:
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
Nasaďte aktualizovanou šablonu znovu po provedení změn.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Další postup
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* Zjistěte, jak [výměna certifikátů clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
