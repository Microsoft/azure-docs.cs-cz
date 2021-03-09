---
title: Nastavení ověření Azure pomocí Azure PowerShell
description: Jak nastavit a nakonfigurovat poskytovatele ověření identity pomocí Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc415411e05d6fdecee1acf2fbc02b3c170b9d6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501120"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Rychlý Start: nastavení ověření Azure pomocí Azure PowerShell

Pomocí následujících kroků vytvořte a nakonfigurujte poskytovatele ověření identity pomocí Azure PowerShell. Informace o tom, jak nainstalovat a spustit Azure PowerShell, najdete v tématu [přehled Azure PowerShell](/powershell/azure/) .

Upozorňujeme, že Galerie prostředí PowerShell má zastaralé verze protokolu TLS (Transport Layer Security) 1,0 a 1,1. Doporučuje se protokol TLS 1,2 nebo novější verze. Proto se můžete setkat s následujícími chybami:

- Upozornění: nepovedlo se přeložit zdroj https://www.powershellgallery.com/api/v2 balíčku.
- PackageManagement\Install-Package: pro zadaná kritéria vyhledávání a název modulu se nenašla žádná shoda. 

Pokud chcete pokračovat v interakci s Galerie prostředí PowerShell, spusťte před příkazy Install-Module tento příkaz.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Instalace AZ. Attestation PowerShell Module

V počítači s Azure PowerShell nainstalujte modul příkaz AZ. Attestation PowerShell, který obsahuje rutiny pro ověření identity Azure.  

### <a name="initial-installation"></a>Počáteční instalace

Ukončete všechna existující okna PowerShellu.

Chcete-li provést instalaci pro aktuálního uživatele, spusťte okno prostředí PowerShell bez zvýšené úrovně oprávnění a spusťte příkaz:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Pokud chcete nainstalovat pro všechny uživatele, spusťte okno PowerShellu se zvýšenými oprávněními a spusťte:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Zavřete konzolu konzoly PowerShell se zvýšenými oprávněními.

### <a name="update-the-installation"></a>Aktualizace instalace

Ukončete všechna existující okna PowerShellu.

Pokud chcete aktualizovat pro aktuálního uživatele, spusťte okno PowerShellu bez zvýšené úrovně oprávnění a spusťte:

```powershell
Update-Module -Name Az.Attestation
```

Pokud chcete aktualizovat pro všechny uživatele, spusťte okno PowerShellu se zvýšenými oprávněními a spusťte:

```powershell
Update-Module -Name Az.Attestation
```

Zavřete konzolu konzoly PowerShell se zvýšenými oprávněními.

### <a name="get-installed-modules"></a>Získání nainstalovaných modulů

Minimální verze AZ modules Required pro podporu operací ověření identity:
- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. Attestation 0.1.8

Spuštěním následujícího příkazu ověřte nainstalovanou verzi všech modulů AZ. 

```powershell
Get-InstalledModule
```
Pokud se verze neshodují s minimálním požadavkem, spusťte příkazy Update-Module.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure v konzole PowerShellu (bez zvýšených přístupových oprávnění).

```powershell
Connect-AzAccount
```

V případě potřeby přepněte na předplatné, které se má používat pro Azure Attestation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Zaregistrujte poskytovatele prostředků Microsoft. Attestation.

Zaregistrujte poskytovatele prostředků Microsoft. Attestation v předplatném. Další informace o poskytovatelích prostředků Azure a o tom, jak nakonfigurovat a spravovat poskytovatele prostředků, najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md). Všimněte si, že registrace poskytovatele prostředků se pro předplatné vyžaduje jenom jednou.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regionální dostupnost Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

Vytvořte skupinu prostředků pro poskytovatele ověřování identity. Všimněte si, že další prostředky Azure (včetně virtuálního počítače s instancí klientské aplikace) můžete umístit do stejné skupiny prostředků.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Vytvoření a Správa poskytovatele ověřování identity

New-AzAttestation vytvoří poskytovatele ověření identity.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile je soubor, který určuje sadu důvěryhodných podpisových klíčů. Pokud je pro parametr PolicySignerCertificateFile zadaný název souboru, zprostředkovatel ověření identity se dá nakonfigurovat jenom se zásadami v podepsaném formátu JWT. Zásada else se dá nakonfigurovat v textovém formátu nebo ve formátu nepodepsaného JWT.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Ukázku PolicySignersCertificateFile najdete v tématu [příklady certifikátu podepsaného zásad](policy-signer-examples.md).

Get-AzAttestation načte vlastnosti poskytovatele ověření identity, jako je stav a AttestURI. Poznamenejte si AttestURI, jak bude potřeba později.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Výše uvedený příkaz by měl mít výstup podobný následujícímu: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Zprostředkovatele ověření identity je možné odstranit pomocí rutiny Remove-AzAttestation.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Správa zásad

Aby bylo možné spravovat zásady, vyžaduje uživatel Azure AD následující oprávnění pro akce:
- Microsoft. Attestation/attestationProviders/ověření/čtení
- Microsoft. Attestation/attestationProviders/Attestation/Write
- Microsoft. Attestation/attestationProviders/Attestation/DELETE

Tato oprávnění je možné přiřadit uživateli služby AD prostřednictvím role, jako je "vlastník" (oprávnění zástupných znaků), přispěvatele (oprávnění zástupných znaků) nebo "Přispěvatela ověření" (specifická oprávnění pouze pro Azure Attestation).  

Aby bylo možné číst zásady, vyžaduje uživatel Azure AD následující oprávnění pro akce:
- Microsoft. Attestation/attestationProviders/ověření/čtení

Toto oprávnění může být přiřazeno uživateli služby AD prostřednictvím role, jako je například čtenář (oprávnění ke zástupným znakem) nebo "čtecí modul", "oprávnění pro Azure".

Níže uvedené rutiny prostředí PowerShell poskytují správu zásad pro poskytovatele ověření identity (jeden TEE v jednom okamžiku).

Get-AzAttestationPolicy vrátí aktuální zásadu pro zadaný TEE. Rutina zobrazí zásady ve formátu text i JWT.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Podporované typy TEE jsou "SgxEnclave", "OpenEnclave" a "VbsEnclave".

Set-AttestationPolicy nastaví novou zásadu pro zadaný TEE. Rutina akceptuje zásady ve formátu text nebo JWT a je ovládána parametrem PolicyFormat. "Text" je výchozí hodnota pro PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Pokud je k dispozici PolicySignerCertificateFile při vytváření poskytovatele ověření identity, můžete zásady nakonfigurovat pouze ve formátu podepsaných JWT. Zásada else se dá nakonfigurovat v textovém formátu nebo ve formátu nepodepsaného JWT.

Zásada ověření identity ve formátu JWT musí obsahovat deklaraci identity s názvem "AttestationPolicy". U podepsaných zásad musí být token JWT podepsaný privátním klíčem odpovídajícím všem existujícím certifikátům podepsané zásady.

Ukázky zásad najdete v tématu [Příklady zásad ověřování identity](policy-examples.md).

Reset-AzAttestationPolicy obnoví výchozí nastavení zásad pro zadaný TEE.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Správa certifikátů podepsaných zásadou

Níže uvedené rutiny prostředí PowerShell poskytují správu certifikátů Signer pro poskytovatele ověření identity:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Certifikát podepsané zásady je podepsaný token JWT s deklarací s názvem "Maa-policyCertificate". Hodnota deklarace identity je JWK, který obsahuje důvěryhodný podpisový klíč, který se má přidat. Token JWT musí být podepsán pomocí privátního klíče odpovídajícího všem existujícím certifikátům podepsaného zásad.

Veškerá sémantická manipulace s certifikátem podepsané zásady se musí provádět mimo PowerShell. V případě, že je to prostředí PowerShell, jedná se o jednoduchý řetězec.

Ukázku certifikátu pro podepsání zásad najdete v tématu [příklady certifikátu podepsaného zásad](policy-signer-examples.md).

Další informace o rutinách a jejích parametrech najdete v tématu [rutiny PowerShellu pro ověření Azure](/powershell/module/az.attestation/#attestation) . 

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Ověření SGX enklávy pomocí ukázek kódu](/samples/browse/?expanded=azure&terms=attestation)
