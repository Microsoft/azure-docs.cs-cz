---
title: Bezpečný přístup k trezoru klíčů s batch – Azure Batch
description: Zjistěte, jak programově přistupovat k přihlašovacím údajům z trezoru klíčů pomocí Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192298"
---
# <a name="securely-access-key-vault-with-batch"></a>Zabezpečený přístup ke Key Vaultu s využitím služby Batch

V tomto článku se dozvíte, jak nastavit dávkové uzly pro bezpečný přístup k přihlašovacím údajům uloženým v úložišti klíčů Azure. Nemá smysl umisčovat přihlašovací údaje správce do trezoru klíčů a pak pevně kódovat přihlašovací údaje pro přístup k trezoru klíčů ze skriptu. Řešením je použití certifikátu, který uděluje dávkovým uzlům přístup k trezoru klíčů. Pomocí několika kroků můžeme implementovat zabezpečené úložiště klíčů pro Batch.

K ověření do trezoru klíčů Azure z uzlu Batch potřebujete:

- Přihlašovací údaje služby Azure Active Directory (Azure AD)
- Certifikát
- Dávkový účet
- Fond dávek s alespoň jedním uzlem

## <a name="obtain-a-certificate"></a>Získání certifikátu

Pokud certifikát ještě nemáte, nejjednodušší způsob, jak ho získat, je vygenerovat `makecert` certifikát podepsaný svým držitelem pomocí nástroje příkazového řádku.

Obvykle můžete najít `makecert` v této `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`cestě: . Otevřete příkazový řádek jako `makecert` správce a přejděte k následujícímu příkladu.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Dále pomocí `makecert` nástroje vytvořte soubory certifikátů `batchcertificate.cer` `batchcertificate.pvk`podepsané svým držitelem s názvem a . Běžný název (CN) používá není důležité pro tuto aplikaci, ale je užitečné, aby bylo něco, co vám řekne, co certifikát se používá pro.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Dávka vyžaduje `.pfx` soubor. Pomocí nástroje [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) převeďte `.cer` soubory a `.pvk` vytvořené `makecert` na jeden `.pfx` soubor.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Přístup k trezoru klíčů je udělen **uživateli** nebo **instančnímu objektu**. Chcete-li programově přistupovat k trezoru klíčů, použijte instanční objekt s certifikátem, který jsme vytvořili v předchozím kroku.

Další informace o objektech služby Azure najdete v tématu [Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> Instanční objekt musí být ve stejném tenantovi Azure AD jako trezor klíčů.

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

Adresy URL aplikace nejsou důležité, protože je používáme pouze pro přístup k trezoru klíčů.

## <a name="grant-rights-to-key-vault"></a>Udělení práv trezoru klíčů

Instanční objekt vytvořený v předchozím kroku potřebuje oprávnění k načtení tajných klíčů z trezoru klíčů. Oprávnění lze udělit buď prostřednictvím portálu Azure nebo pomocí příkazu PowerShell níže.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Přiřazení certifikátu k účtu Batch

Vytvořte fond dávek, přejděte na kartu certifikátu ve fondu a přiřaďte certifikát, který jste vytvořili. Certifikát je nyní na všech uzlech batch.

Dále musíme přiřadit certifikát k účtu Batch. Přiřazení certifikátu k účtu nám umožňuje přiřadit jej do fondů a potom k uzlům. Nejjednodušší způsob, jak to udělat, je přejít na svůj účet Batch na portálu, přejít na **certifikáty**a vybrat **přidat**. Nahrajte `.pfx` soubor, který jsme vygenerovali, v [nabídce Získat certifikát](#obtain-a-certificate) a zadejte heslo. Po dokončení je certifikát přidán do seznamu a můžete ověřit kryptografický otisk.

Nyní, když vytvoříte fond dávek, můžete přejít na **certifikáty** v rámci fondu a přiřadit certifikát, který jste vytvořili do tohoto fondu. Pokud tak učiníte, ujistěte se, že vyberete **LocalMachine** pro umístění úložiště. Certifikát je načten na všechny uzly dávky ve fondu.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

Pokud máte v plánu získat přístup k trezoru klíčů pomocí skriptů PowerShellu ve svých uzlech, budete potřebovat nainstalovanou knihovnu Azure PowerShellu. Existuje několik způsobů, jak to udělat, pokud vaše uzly mají nainstalována rozhraní WMF (Windows Management Framework) 5, pak můžete použít příkaz install-module ke stažení. Pokud používáte uzly, které nemají WMF 5, nejjednodušší způsob, jak ji nainstalovat, je svázat soubor Azure PowerShell `.msi` se soubory Batch a pak zavolat instalační program jako první část spouštěcího skriptu Batch. Podrobnosti najdete v tomto příkladu:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Přístup ke službě Key Vault

Nyní jsme všichni nastavení pro přístup k trezoru klíčů ve skriptech běžících na uzlech Batch. Chcete-li získat přístup k trezoru klíčů ze skriptu, vše, co potřebujete, je, aby se skript ověřil proti službě Azure AD pomocí certifikátu. Chcete-li to provést v prostředí PowerShell, použijte následující ukázkové příkazy. Zadejte příslušný identifikátor GUID pro **kryptografický otisk**, **ID aplikace** (ID instančního **objektu) a ID klienta** (klient, kde instanční objekt existuje).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Po ověření, přístup KeyVault, jak byste normálně.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Toto jsou pověření, která chcete použít ve skriptu.
