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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
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
    Zkontroluje, síť SAN obsahuje relevantní názvy DNS pro každý koncový bod nebo pokud podpůrný nachází zástupný znak.
- **Použití klíče**  
    Zkontroluje, zda obsahuje použití klíče, digitální podpis a šifrování klíče a rozšířené použití klíče obsahuje ověření serveru a ověřování klientů.
- **Velikost klíče**  
    Ověří, zda je velikost klíče 2048 nebo větší.
- **Řetězec pořadí**  
    Ověří pořadí jiných certifikátů ověřuje, zda je správný pořadí.
- **Další certifikáty**  
    Zkontrolujte, že žádné další certifikáty byly zabaleny v PFX než listu relevantní certifikát a jeho řetězec.

> [!IMPORTANT]  
> Certifikát PKI, je soubor PFX a heslo by měl být považován za citlivé informace.

## <a name="prerequisites"></a>Požadavky

Váš systém by měl splňovat následující požadavky před ověřením certifikáty PKI pro nasazení služby Azure zásobníku:

- Kontrola připravenosti zásobníku Microsoft Azure
- Certifikáty SSL exportovat následující [pokyny k přípravě](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 nebo Windows Server 2016

## <a name="perform-certificate-validation"></a>Ověření certifikátu

Tyto kroky použijte k přípravě a k ověření certifikátů PKI zásobník Azure:

1. Instalace AzsReadinessChecker z řádku prostředí PowerShell (5.1 nebo novější), spuštěním následující rutiny:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Vytvořte strukturu adresáře certifikátu. V následujícím příkladu můžete změnit `<c:\certificates>` novou cestu adresáře podle svého výběru.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Místní certifikátů v příslušné adresáře vytvořené v předchozím kroku. Příklad:  
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - a tak dále... 

3. V okně prostředí PowerShell, spusťte:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Zkontrolujte výstup k ověření, že všechny certifikáty předána testy. Příklad:

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
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
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

## <a name="using-validated-certificates"></a>Pomocí ověřovaných certifikátů

Po ověření certifikátů pomocí AzsReadinessChecker jste připraveni používat ve vašem nasazení zásobník Azure nebo Azure zásobníku tajný otočení. 

 - Pro nasazení, bylo možné bezpečněji přenášet certifikáty na pracovníka nasazení tak, aby je mohli zkopírovat do hostitele nasazení uvedených v [dokumentace Azure zásobníku infrastruktury veřejných KLÍČŮ požadavky](azure-stack-pki-certs.md).
 - Pro tajný otočení, můžete použít certifikáty a aktualizovat staré certifikáty pro prostředí Azure zásobníku infrastruktury veřejných koncových bodů podle [dokumentace Azure zásobníku tajný klíč otočení](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Další postup

[Integrace identit Datacenter](azure-stack-integrate-identity.md)
