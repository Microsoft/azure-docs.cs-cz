---
title: Změna certifikátu clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak chcete efekt přechodu certifikát clusteru Service Fabric identifikovaný běžný název certifikátu.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: dd4b6026772a20c522532e1ba65c6846addfa161
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159899"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ručně nespotřebujete certifikát clusteru Service Fabric
Jakmile certifikát clusteru Service Fabric se blíží vypršení platnosti, budete muset aktualizovat certifikát.  Výměny certifikátů je jednoduchý, pokud se cluster [nastavený používat certifikáty založené na běžný název](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (namísto kryptografický otisk).  Získejte nový certifikát od certifikační autority se nové datum vypršení platnosti.  Certifikáty podepsané svým držitelem nejsou podporu pro produkční clustery Service Fabric, zahrnovat certifikáty vygenerované během Azure pracovní postup vytvoření clusteru portálu. Nový certifikát musí mít stejný běžný název jako starší certifikát. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cluster Service Fabric pomocí dalších automaticky použijí deklarované certifikát do budoucí datum vypršení platnosti; Při ověření více než jeden certifikát nainstalován na hostiteli. Osvědčeným postupem je použití šablony Resource Manageru pro zřízení prostředků Azure. Následující skript pro neprodukční prostředí umožňuje nahrát nový certifikát do služby key vault a pak nainstaluje certifikát na škálovací sadu virtuálních počítačů: 

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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Vypočítá tajemství nastavit škálování virtuálního počítače nepodporují stejné id prostředku pro dva samostatné tajné kódy, jako každý tajný kód je označené verzí prostředků jedinečné. 

Další informace, přečtěte si následující:
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)

