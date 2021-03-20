---
title: Převeďte certifikát clusteru Azure Service Fabric.
description: Naučte se, jak převrátit Service Fabric certifikát clusteru identifikovaný běžným názvem certifikátu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88853350"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ruční vrácení Service Fabric certifikátu clusteru
Pokud se platnost certifikátu Service Fabric clusteru blíží k vypršení platnosti, budete muset certifikát aktualizovat.  Změna certifikátu je jednoduchá, pokud byl cluster [nastavený tak, aby používal certifikáty založené na běžném názvu](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (místo kryptografických otisků).  Získejte nový certifikát od certifikační autority s novým datem vypršení platnosti.  Certifikáty podepsané svým držitelem nejsou podporované pro produkční Service Fabric clustery, aby zahrnovaly certifikáty vygenerované během pracovního postupu vytvoření Azure Portal clusteru. Nový certifikát musí mít stejný běžný název jako starší certifikát. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cluster Service Fabric bude automaticky používat deklarovaný certifikát s dalšími daty k budoucímu datu vypršení platnosti. v případě, že je na hostiteli nainstalováno více než jeden ověřovací certifikát. Osvědčeným postupem je použití šablony Správce prostředků ke zřízení prostředků Azure. V neprodukčním prostředí můžete použít následující skript k nahrání nového certifikátu do trezoru klíčů a následné instalaci certifikátu do sady škálování virtuálních počítačů: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Tajné klíče COMPUTE Virtual Machine Scale set nepodporují stejné ID prostředku pro dvě samostatná tajná klíče, protože každý tajný klíč je jedinečným prostředkem verze. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Aktualizace a Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
