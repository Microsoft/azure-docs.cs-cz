---
title: Přidání certifikátu aplikace do clusteru v Powershellu
description: Ukázkový skript Azure PowerShellu – Přidání certifikátu aplikace do clusteru Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: d657ef8d28b36d93bc923036254e446c7be4c2c8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769525"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Přidání certifikátu aplikace do clusteru Service Fabric

Tento ukázkový skript vás provede vytvořením certifikátu v trezoru klíčů a potom jej nasadit do jedné z škálovacích sad virtuálních strojů, na které bude váš cluster spuštěn. Tento scénář nepoužívá Service Fabric přímo, ale spíše závisí na trezoru klíčů a na škálovací sady virtuálních strojů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte Azure PowerShell pomocí instrukce, která `Connect-AzAccount` se nachází v [průvodci Azure PowerShell,](/powershell/azure/overview) a pak spusťte připojení k Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Vytvoření certifikátu v trezoru klíčů

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Nebo nahrát existující certifikát do trezoru klíčů

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Aktualizace profilu škálovacích sad virtuálních strojů s certifikátem

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Aktualizace škálovací sady virtuálních strojů
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Pokud chcete, aby byl certifikát umístěn na více typech uzlů v clusteru, druhá a třetí část tohoto skriptu by měla být opakována pro každý typ uzlu, který by měl mít certifikát.

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Nový-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Vytvoří zásadu v paměti představující certifikát. |
| [Přidat-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Nasazuje zásady do certifikátů trezoru klíčů. |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Nasazuje zásady do tajných kódů trezoru klíčů. |
| [Nový-AzVmssVaultConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Vytvoří konfiguraci v paměti představující certifikát ve virtuálním míse. |
| [Získat-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Přidat-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Přidá certifikát do definice škálovací sady virtuálních strojů v paměti. |
| [Aktualizace-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Nasazuje novou definici škálovací sady virtuálních strojů. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
