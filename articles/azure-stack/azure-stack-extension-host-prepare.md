---
title: Příprava pro rozšíření hostitele pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro rozšíření hostitele, který je automaticky povolený prostřednictvím balíčku budoucí aktualizaci služby Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 11/09/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 049e859f1d736e7c06ac5d40e33d91d1540c3d9e
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514362"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Příprava pro rozšíření hostitele pro Azure Stack

Hostitel rozšíření zabezpečení služby Azure Stack snížením počtu požadované porty TCP/IP. Tento článek ukazuje Příprava služby Azure Stack pro rozšíření hostitele, který je automaticky povolený prostřednictvím Azure Stack aktualizovat balíček po 1808 aktualizaci.

## <a name="certificate-requirements"></a>Požadavky na certifikát

Hostitel rozšíření implementuje dvě nové domény obory názvů zajistit záznamy hostitele pro každé rozšíření na portálu. Nové obory názvů domény potřebujete dva další certifikáty zástupný znak – pro zajištění zabezpečené komunikace.

V tabulce jsou uvedeny nové obory názvů a přidružené certifikáty:

| Složka pro nasazení | Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN) | Obor (podle oblasti) | SubDomain namespace |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Hostitel Správce rozšíření | *.adminhosting. \<oblast >. \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel Správce rozšíření | adminhosting. \<oblast >. \<plně kvalifikovaný název domény > |
| Veřejná rozšiřující hostitele | * .hosting. \<oblast >. \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Veřejná rozšiřující hostitele | hostování. \<oblast >. \<plně kvalifikovaný název domény > |

Požadavky na podrobné certifikát najdete v [požadavky na certifikáty infrastruktury veřejných klíčů služby Azure Stack](azure-stack-pki-certs.md) článku.

## <a name="create-certificate-signing-request"></a>Vytvořit žádost o podepsání certifikátu

Nástroj prerequisite Checker Azure Stack připravenosti poskytuje možnost vytvářet žádost o podepsání certifikátu pro certifikáty SSL dva nové, se vyžaduje. Postupujte podle kroků v článku [podepisování generování žádosti o certifikáty Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Můžete přeskočit tento krok v závislosti na tom, jak jste požádali své certifikáty protokolu SSL.

## <a name="validate-new-certificates"></a>Ověření nové certifikáty

1. Otevřete prostředí PowerShell s oprávněními na hostiteli životního cyklu hardwaru nebo pracovní stanici správy služby Azure Stack.
2. Spuštěním následující rutiny můžete nainstalovat nástroj prerequisite Checker připravenosti Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Spusťte následující skript k vytvoření požadované složce struktury:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Pokud provádíte nasazení s Azure Active Directory Federated Services (AD FS) v následujících adresářích musí být přidané do **$directories** ve skriptu: `ADFS`, `Graph`.

4. Spuštěním následující rutiny spuštění kontroly certifikátu:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

5. Vaše certifikáty umístíte v příslušné adresáře.

6. Zkontrolujte výstup a všechny jejich certifikáty projít všemi testy.


## <a name="import-extension-host-certificates"></a>Import certifikátů hostitele rozšíření

Použijte počítač, který lze připojit ke koncovému bodu Azure Stack privilegovaného pro další kroky. Ujistěte se, že budete mít přístup k nové soubory certifikát z tohoto počítače.

1. Použijte počítač, který lze připojit ke koncovému bodu Azure Stack privilegovaného pro další kroky. Ujistěte se, že přístup k nové soubory certifikát z tohoto počítače.
2. Otevřete prostředí PowerShell ISE a provést další bloky skriptu
3. Importujte certifikát pro správu, který je hostitelem koncového bodu.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importujte certifikát pro koncový bod služby hostingu.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>Aktualizovat konfiguraci DNS

> [!Note]  
> Tento krok není povinný, pokud jste použili delegování zóny DNS pro integraci DNS.
Pokud záznamy o jednotlivého hostitele bylo nakonfigurováno pro publikování koncových bodů služby Azure Stack, musíte vytvořit dva záznamy A další hostitele:

| IP adresa | Název hostitele | Typ |
|----|------------------------------|------|
| \<IP &GT; | Adminhosting. <Region>.<FQDN> | A |
| \<IP &GT; | Hostování. <Region>.<FQDN> | A |

Přidělené IP adresy se dá načíst pomocí privilegovaných koncového bodu spuštěním rutiny **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porty a protokoly

V článku [integrace datových center Azure Stack – publikování koncových bodů](azure-stack-integrate-endpoints.md), zahrnuje porty a protokoly, které vyžadují příchozí komunikaci pro publikování Azure Stack před zavedením rozšíření hostitele.

### <a name="publish-new-endpoints"></a>Publikovat nové koncové body

Existují dvě nové koncové body, které jsou potřebné k publikování přes bránu firewall. Přidělené IP adresy z fondu veřejných virtuálních IP adres se dá načíst pomocí rutiny **Get-AzureStackStampInformation**.

> [!Note]  
> Provedení této změny před povolením rozšíření hostitele. To umožňuje na portálech Azure Stack nepřetržitě dostupná.

| Koncový bod (VIP) | Protocol (Protokol) | Porty |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hostování | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aktualizovat existující pravidla pro publikování (Post povolování rozšíření hostitele)

> [!Note]  
> Nemá balíček aktualizace Azure Stack. 1808 **není** ještě povolit rozšíření hostitele. Je možné připravit pro rozšíření hostitele importováním požadované certifikáty. Předtím, než hostitel rozšíření se automaticky povolí prostřednictvím Azure Stack aktualizovat balíček po aktualizaci 1808 nezavírejte žádné porty.

Následující existující porty koncového bodu musí být uzavřena v existující pravidla brány firewall.

> [!Note]  
> Doporučuje se zavřít tyto porty po úspěšném ověření.

| Koncový bod (VIP) | Protocol (Protokol) | Porty |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portál (správce) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Portál (uživatel) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (správce) | HTTPS | 30024 |
| Azure Resource Manageru (uživatel) | HTTPS | 30024 |

## <a name="next-steps"></a>Další postup

- Další informace o [integrace s branou Firewall](azure-stack-firewall.md).
- Další informace o [podepisování generování žádosti o certifikáty Azure Stack](azure-stack-get-pki-certs.md)
