---
title: Ověření certifikátů infrastruktury veřejných klíčů Azure zásobníku pro nasazení Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje, jak k ověření certifikátů PKI zásobník Azure pro Azure zásobníku integrované systémy. Popisuje použití nástroje Azure zásobníku certifikát kontrola.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605606"
---
# <a name="validate-azure-stack-pki-certificates"></a>Ověření Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů

Nástroj kontrolu připravenosti zásobník Azure popsané v tomto článku je k dispozici [z Galerie Powershellu](https://aka.ms/AzsReadinessChecker). Tento nástroj slouží k ověření, že [generované certifikáty PKI](azure-stack-get-pki-certs.md) jsou vhodné pro před nasazením. Měli byste ověřit certifikáty ponecháním dostatek času k testování a v případě potřeby obnášet opětovné vystavení certifikátů.

Nástroj pro kontrolu připravenosti provádí následující ověření certifikátu:

- **Čtení PFX**  
    Kontroluje platný soubor PFX, správné heslo a zobrazí upozornění, pokud informace o veřejném není chráněn heslem. 
- **Algoritmus podpisu**  
    Ověří, že algoritmus podpisu není SHA1.
- **Privátní klíč**  
    Kontroluje, že privátní klíč je k dispozici a je exportován s atributem místního počítače. 
- **Řetěz certifikátů**  
    Řetěz certifikátů kontroly je v pořádku, včetně kontrolu pro certifikáty podepsané svým držitelem.
- **Názvy DNS**  
    Zkontroluje, síť SAN obsahuje relevantní názvy DNS pro každý koncový bod, nebo pokud podpůrný nachází zástupný znak.
- **Použití klíče**  
    Zkontroluje, zda obsahuje použití klíče, digitální podpis a šifrování klíče a rozšířené použití klíče obsahuje ověření serveru a ověřování klientů.
- **Velikost klíče**  
    Ověří, zda je velikost klíče 2048 nebo větší.
- **Řetězec pořadí**  
    Ověří pořadí jiných certifikátů ověřuje, zda je správný pořadí.
- **Další certifikáty**  
    Zkontrolujte, že žádné další certifikáty byly zabaleny v PFX než listu relevantní certifikát a jeho řetězec.
- **Žádný profil**  
    Ověří, že nového uživatele můžete načíst PFX data bez profilu uživatele načíst, mimicking chování účty gMSA během obsluhy certifikátu.

> [!IMPORTANT]  
> Certifikát PKI, je soubor PFX a heslo by měl být považován za citlivé informace.

## <a name="prerequisites"></a>Požadavky

Váš systém by měl splňovat následující požadavky před ověřením certifikáty PKI pro nasazení služby Azure zásobníku:

- Kontrola připravenosti zásobníku Microsoft Azure
- Certifikáty SSL exportovat následující [pokyny k přípravě](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 nebo Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Provést základní ověření certifikátu služby

Tyto kroky použijte pro přípravu a k ověření certifikátů PKI zásobník Azure pro nasazení a tajný otočení:

1. Nainstalujte **AzsReadinessChecker** z příkazovém řádku prostředí PowerShell (5.1 nebo novější), spuštěním následující rutiny:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Vytvořte strukturu adresáře certifikátu. V následujícím příkladu můžete změnit `<c:\certificates>` novou cestu adresáře podle svého výběru.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > Pokud používáte systém identity služby AD FS se vyžaduje služba AD FS a grafu.
    
     - Místní certifikátů v příslušné adresáře vytvořené v předchozím kroku. Příklad:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. V okně prostředí PowerShell změně hodnot **RegionName** a **plně kvalifikovaný název domény** příslušným do prostředí Azure zásobníku a spusťte následující příkaz:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Zkontrolujte výstup a všechny certifikáty projít všemi testy. Příklad:

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Známé problémy

**Příznaky**: testy se přeskočí.

**Příčina**: AzsReadinessChecker přeskočí určité testy, pokud nejsou splněny závislost:

 - Další certifikáty se přeskočí, pokud se nezdaří řetěz certifikátů.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Řešení**: postupujte podle pokynů nástroje v sekci podrobností pod každou sadu testů pro každý certifikát.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Platformy, proveďte ověření certifikátu služby

Pomocí těchto kroků můžete připravit a ověření certifikátů PKI zásobník Azure pro platformu jako certifikáty služby (PaaS), pokud máte v plánu nasazení SQL nebo MySQL nebo aplikační služby.

1.  Nainstalujte **AzsReadinessChecker** z příkazovém řádku prostředí PowerShell (5.1 nebo novější), spuštěním následující rutiny:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Vytvořte vnořené zatřiďovací tabulku obsahující cesty a heslo pro každý certifikát PaaS nutnosti ověření. V okně prostředí PowerShell, spusťte:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Změna hodnot **RegionName** a **plně kvalifikovaný název domény** tak, aby odpovídaly prostředí Azure zásobníku zahájíte ověřování. Potom spusťte:

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Zkontrolujte výstup a že všechny certifikáty projít všemi testy.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Pomocí ověřovaných certifikátů

Po ověření certifikátů pomocí AzsReadinessChecker jste připraveni používat ve vašem nasazení zásobník Azure nebo Azure zásobníku tajný otočení. 

 - Pro nasazení, bylo možné bezpečněji přenášet certifikáty na pracovníka nasazení tak, aby je mohli zkopírovat do hostitele nasazení uvedených v [dokumentace Azure zásobníku infrastruktury veřejných KLÍČŮ požadavky](azure-stack-pki-certs.md).
 - Pro tajný otočení, můžete použít certifikáty a aktualizovat staré certifikáty pro prostředí Azure zásobníku infrastruktury veřejných koncových bodů podle [dokumentace Azure zásobníku tajný klíč otočení](azure-stack-rotate-secrets.md).
 - Pro služby PaaS, můžete použít certifikáty k instalaci SQL, MySQL a zprostředkovatelé prostředků služeb aplikace v zásobníku Azure pomocí následujících [přehled nabídky služeb v dokumentaci k Azure zásobníku](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Další postup

[Integrace identit Datacenter](azure-stack-integrate-identity.md)