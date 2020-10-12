---
title: Zabezpečený přístup ke Key Vaultu s využitím služby Batch
description: Naučte se programově přistupovat k přihlašovacím údajům z Key Vault pomocí Azure Batch.
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079434"
---
# <a name="securely-access-key-vault-with-batch"></a>Zabezpečený přístup ke Key Vaultu s využitím služby Batch

V tomto článku se dozvíte, jak nastavit uzly Batch pro zabezpečený přístup k přihlašovacím údajům uloženým v Azure Key Vault. K dispozici není žádný bod pro vložení přihlašovacích údajů správce do Key Vault a pak pevně zakódování přihlašovacích údajů pro přístup Key Vault ze skriptu. Řešením je použít certifikát, který uděluje uzlům služby Batch přístup k Key Vault. Pomocí několika kroků můžeme implementovat zabezpečené úložiště klíčů pro dávku.

K ověření Azure Key Vault z uzlu Batch budete potřebovat:

- Přihlašovací údaje pro Azure Active Directory (Azure AD)
- Certifikát
- Účet Batch
- Fond dávek s alespoň jedním uzlem

## <a name="obtain-a-certificate"></a>Získání certifikátu

Pokud certifikát ještě nemáte, nejjednodušší způsob, jak ho získat, je vytvořit certifikát podepsaný svým držitelem pomocí `makecert` nástroje příkazového řádku.

Obvykle můžete najít `makecert` v této cestě: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` . Otevřete příkazový řádek jako správce a přejděte k `makecert` části použití následujícího příkladu.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Dále použijte `makecert` Nástroj k vytvoření souborů certifikátů podepsaných svým držitelem s názvem `batchcertificate.cer` a `batchcertificate.pvk` . Použitý běžný název (CN) není pro tuto aplikaci důležitý, ale je vhodné ho využít k tomu, abyste si využívali, k čemu se certifikát používá.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch vyžaduje `.pfx` soubor. Použijte nástroj [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) k převodu `.cer` `.pvk` souborů a vytvořených pomocí `makecert` do jediného `.pfx` souboru.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Přístup k Key Vault je udělen buď **uživateli** , nebo **instančnímu objektu**. Pro přístup k Key Vault programově použijte instanční objekt s certifikátem, který jsme vytvořili v předchozím kroku.

Další informace o instančních objektech Azure najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Instanční objekt musí být ve stejném tenantovi služby Azure AD jako Key Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Adresy URL pro aplikaci nejsou důležité, protože je používáme pro přístup k Key Vault.

## <a name="grant-rights-to-key-vault"></a>Udělit práva Key Vault

Instanční objekt vytvořený v předchozím kroku potřebuje oprávnění k načtení tajných kódů z Key Vault. Oprávnění lze udělit buď prostřednictvím Azure Portal, nebo pomocí příkazu PowerShellu níže.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Přiřazení certifikátu k účtu Batch

Vytvořte fond dávek a pak ve fondu klikněte na kartu certifikát a přiřaďte certifikát, který jste vytvořili. Certifikát je nyní na všech uzlech Batch.

Dál je potřeba přiřadit k účtu Batch certifikát. Když k účtu přiřadíte certifikát, umožníme mu přiřadit fondy a pak uzly. Nejjednodušší způsob, jak to provést, je přejít na účet Batch na portálu, přejít na **certifikáty**a vybrat **Přidat**. Nahrajte `.pfx` soubor, který jsme vygenerovali v části [získání certifikátu](#obtain-a-certificate) , a zadejte heslo. Po dokončení se certifikát přidá do seznamu a tento kryptografický otisk můžete ověřit.

Když teď vytvoříte fond služby Batch, můžete přejít na **certifikáty** v rámci fondu a přiřadit certifikát, který jste vytvořili do tohoto fondu. Když to uděláte, ujistěte se, že jste pro umístění úložiště vybrali možnost **LocalMachine** . Certifikát je načtený na všech uzlech Batch ve fondu.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

Pokud plánujete přístup k Key Vault pomocí skriptů PowerShellu na uzlech, budete potřebovat nainstalovanou knihovnu Azure PowerShell. Existuje několik způsobů, jak to provést, pokud jsou v uzlech nainstalované rozhraní Windows Management Framework (WMF) 5, můžete ho stáhnout pomocí příkazu install-Module. Pokud používáte uzly, které nemají WMF 5, nejjednodušší způsob, jak ji nainstalovat, je `.msi` soubor Azure PowerShell pomocí dávkových souborů odinstalujte a potom zavolejte instalační program jako první část spouštěcího skriptu služby Batch. Podrobnosti najdete v tomto příkladu:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Přístup ke službě Key Vault

Nyní máme všechna nastavení pro přístup k Key Vault ve skriptech spuštěných na uzlech Batch. Aby bylo možné získat přístup k Key Vault ze skriptu, stačí pro váš skript ověřit službu Azure AD pomocí certifikátu. Pokud to chcete provést v prostředí PowerShell, použijte následující příklady příkazů. Zadejte odpovídající identifikátor GUID pro **kryptografický otisk**, **ID aplikace** (ID objektu služby) a **ID tenanta** (tenant, ve kterém se nachází instanční objekt).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Po ověření se přístup k trezoru klíčů vyřadí obvyklým způsobem.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Toto jsou přihlašovací údaje, které se mají použít ve vašem skriptu.
