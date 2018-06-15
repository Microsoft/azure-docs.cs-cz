---
title: Změna certifikátu služby Azure Service Fabric clusteru | Microsoft Docs
description: Zjistěte, jak k výměny certifikát clusteru Service Fabric identifikovaný běžný název certifikátu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: df919e23fd608cdf41e93844f13342ca00657adb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205140"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ručně mění certifikát clusteru Service Fabric
Pokud je certifikát clusteru Service Fabric blíží vypršení platnosti, budete muset aktualizovat certifikát.  Změna certifikátu je jednoduché, pokud byl cluster [nastavena tak, aby používat certifikáty založené na běžný název](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (namísto kryptografický otisk).  Získejte nový certifikát od certifikační autority s novým datem vypršení platnosti.  Certifikáty podepsané svým držitelem, včetně těch, které generuje při nasazování clusteru Service Fabric na portálu Azure nejsou podporovány.  Nový certifikát musí mít stejný běžný název jako starší certifikát. 

Následující skript odešle nový certifikát do trezoru klíčů a potom nainstaluje certifikát na škálovací sadu virtuálních počítačů.  Cluster Service Fabric automaticky použije certifikát s nejnovější datum vypršení platnosti.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

Další informace, přečtěte si následující:
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* [Aktualizace a spravovat certifikáty clusteru](service-fabric-cluster-security-update-certs-azure.md)

