---
title: Převrácení certifikátu clusteru Azure Service Fabric
description: Přečtěte si, jak převést certifikát clusteru Service Fabric identifikovaný běžným názvem certifikátu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451961"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ruční převrácení certifikátu clusteru Service Fabric
Pokud se certifikát clusteru Service Fabric blíží vypršení platnosti, je třeba certifikát aktualizovat.  Přechod certifikátu je jednoduchý, pokud byl cluster [nastaven tak, aby používal certifikáty založené na běžném názvu](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (namísto kryptografického otisku).  Získejte nový certifikát od certifikační autority s novým datem vypršení platnosti.  Certifikáty podepsané svým držitelem nejsou pro produkční clustery Service Fabric support, které zahrnují certifikáty generované během pracovního postupu vytváření clusteru portálu Azure. Nový certifikát musí mít stejný běžný název jako starší certifikát. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cluster Service Fabric bude automaticky používat deklarovaný certifikát s dalším datem vypršení platnosti. pokud je na hostiteli nainstalováno více certifikátů ověření. Osvědčeným postupem je použití šablony Správce prostředků k zřizování prostředků Azure. V neprodukčním prostředí lze k nahrání nového certifikátu do trezoru klíčů použít následující skript a potom jej nainstalovat do škálovací sady virtuálních strojů: 

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Vypočítá tajné kódy sady velikosti virtuálního počítače nepodporují stejné id prostředků pro dva samostatné tajné klíče, protože každý tajný klíč je jedinečný prostředek s verzí. 

## <a name="next-steps"></a>Další kroky

* Informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
