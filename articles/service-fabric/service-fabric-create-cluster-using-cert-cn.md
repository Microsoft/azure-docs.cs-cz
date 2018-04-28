---
title: Vytvoření clusteru služby Azure Service Fabric pomocí běžný název certifikátu | Microsoft Docs
description: Zjistěte, jak vytvořit cluster Service Fabric pomocí běžný název certifikátu ze šablony.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 84d9ae29840841d930fa70dc30dcda800a1b46c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Nasadit cluster Service Fabric, která používá běžný název certifikátu místo kryptografický otisk
Dva certifikáty, může mít stejné kryptografický otisk, což znesnadňuje clusteru certifikáty vyměnit nebo správu. Více certifikátů, však může mít stejný běžný název nebo předmětu.  Cluster pomocí certifikátu běžné názvy usnadňuje správu certifikátů mnohem jednodušší. Tento článek popisuje, jak nasadit cluster Service Fabric používat běžný název certifikátu místo kryptografický otisk certifikátu.
 
## <a name="get-a-certificate"></a>Získejte certifikát
Nejdřív získat certifikát z [certifikátu certifikační autority](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být název hostitele clusteru.  Například "myclustername.southcentralus.cloudapp.azure.com".  

Pro účely testování, může získat certifikát podepsaný certifikační Autoritou od volné nebo otevřít certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch, které generuje při nasazování clusteru Service Fabric na portálu Azure nejsou podporovány.

## <a name="upload-the-certificate-to-a-key-vault"></a>Nahrajte certifikát do trezoru klíčů
V Azure je nasadit cluster Service Fabric na škálovací sadu virtuálních počítačů.  Nahrajte certifikát do trezoru klíčů.  Když se nasadí do clusteru, certifikát se nainstaluje na škálovací sadu virtuálních počítačů, která v clusteru je spuštěn na.

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
```

## <a name="download-and-update-a-sample-template"></a>Stáhněte si a aktualizovat vzorové šablony
Tento článek používá [příklad 5 uzlu clusteru zabezpečené](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) šablony a parametry šablony. Stažení *azuredeploy.json* a *azuredeploy.parameters.json* soubory do počítače.

### <a name="update-parameters-file"></a>Aktualizace souboru parametrů
První, otevřete *azuredeploy.parameters.json* soubor v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Dále nastavte *certificateCommonName*, *sourceVaultValue*, a *certificateUrlValue* hodnoty parametrů na ty, vrácený uvedený skript:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualizace souboru šablony
Dále otevřete *azuredeploy.json* soubor v textovém editoru a ujistěte se, tři aktualizace pro podporu běžný název certifikátu.

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

2. Nastavte hodnotu *sfrpApiVersion* proměnné "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. V **Microsoft.Compute/virtualMachineScaleSets** prostředků, aktualizovat rozšíření virtuálního počítače používat běžný název v nastavení certifikátu místo kryptografický otisk.  V **virtualMachineProfile**->**extenstionProfile**->**rozšíření**->**vlastnosti** -> **nastavení**->**certifikát**, přidejte `"commonNames": ["[parameters('certificateCommonName')]"],` a odeberte `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

4.  V **Microsoft.ServiceFabric/clusters** prostředků, verze aktualizace rozhraní API "2018-02-01".  Také přidat **certificateCommonNames** nastavení s **commonNames** vlastnost a odebrat **certifikát** nastavení (s kryptografickým otiskem vlastností) jako v následujícím příkladu Příklad:
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
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Další postup
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* Zjistěte, jak [výměny certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a spravovat certifikáty clusteru](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png