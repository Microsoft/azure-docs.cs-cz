---
title: Příprava pro rozšíření hostitele pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro rozšíření hostitele, který je automaticky povolený prostřednictvím balíčku budoucí aktualizaci služby Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 01/25/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 3e838f932299a9ea7f837570f9d15725230eb4f8
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660081"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Příprava pro rozšíření hostitele pro Azure Stack

Hostitel rozšíření zabezpečení služby Azure Stack snížením počtu požadované porty TCP/IP. Tento článek ukazuje Příprava služby Azure Stack pro rozšíření hostitele, který je automaticky povolený prostřednictvím Azure Stack aktualizovat balíček po 1808 aktualizaci. Tento článek se týká služby Azure Stack aktualizace. 1808 1809 a 1811.

## <a name="certificate-requirements"></a>Požadavky na certifikát

Hostitel rozšíření implementuje dvě nové domény obory názvů zajistit záznamy hostitele pro každé rozšíření na portálu. Nové obory názvů domény potřebujete dva další certifikáty zástupný znak – pro zajištění zabezpečené komunikace.

V tabulce jsou uvedeny nové obory názvů a přidružené certifikáty:

| Složka pro nasazení | Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN) | Obor (podle oblasti) | SubDomain namespace |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Hostitel Správce rozšíření | *.adminhosting.\<region>.\<fqdn> (Wildcard SSL Certificates) | Hostitel Správce rozšíření | adminhosting.\<region>.\<fqdn> |
| Veřejná rozšiřující hostitele | *.hosting.\<region>.\<fqdn> (Wildcard SSL Certificates) | Veřejná rozšiřující hostitele | hostování. \<oblast >. \<plně kvalifikovaný název domény > |

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

| IP adresa | Název hostitele | Type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | A |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | A |

Přidělené IP adresy se dá načíst pomocí privilegovaných koncového bodu spuštěním rutiny **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porty a protokoly

V článku [integrace datových center Azure Stack – publikování koncových bodů](azure-stack-integrate-endpoints.md), zahrnuje porty a protokoly, které vyžadují příchozí komunikaci pro publikování Azure Stack před zavedením rozšíření hostitele.

### <a name="publish-new-endpoints"></a>Publikovat nové koncové body

Existují dvě nové koncové body, které jsou potřebné k publikování přes bránu firewall. Přidělené IP adresy z fondu veřejných virtuálních IP adres se dá načíst pomocí následujícího kódu, který se musí spustit prostřednictvím služby Azure Stack [prostředí na privilegovaný koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Ukázkový výstup

```PowerShell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

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
| Portál (správce) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portál (uživatel) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (správce) | HTTPS | 30024 |
| Azure Resource Manageru (uživatel) | HTTPS | 30024 |

## <a name="next-steps"></a>Další postup

- Další informace o [integrace s branou Firewall](azure-stack-firewall.md).
- Další informace o [podepisování generování žádosti o certifikáty Azure Stack](azure-stack-get-pki-certs.md)
