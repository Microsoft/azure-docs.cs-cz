---
title: Ověření integrace služby AD FS pro Azure Stack
description: Kontrola připravenosti Azure Stack slouží k ověření integrace služby AD FS pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947184"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Ověření integrace služby AD FS pro Azure Stack

Nástroj Azure Stack připravenosti kontrola (AzsReadinessChecker) k ověření, že je prostředí připravené pro integraci služby AD FS pomocí služby Azure Stack. Než začnete integrace System center data nebo před nasazením Azure Stack, měli byste ověřit integrace služby AD FS.

Kontrola připravenosti ověří:

* *Federačních metadat* obsahuje platné prvky XML pro federaci.
* *Certifikát SSL služby AD FS* lze načíst a může být sestaven řetězu certifikátů. Na razítku služby AD FS, musí důvěřovat řetězu certifikátů SSL. Certifikát musí být podepsané stejným *certifikační autorita* jako certifikáty nasazení Azure Stack nebo partner důvěryhodné kořenové autoritě. Úplný seznam důvěryhodných kořenových autority partnerů najdete v tématu: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* *Podpisový certifikát služby AD FS* je důvěryhodný a ne blíží vypršení platnosti.

Další informace o integraci System center data Azure Stack, najdete v části [integrace datových center Azure Stack – identita](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti

Stáhněte si nejnovější verzi nástroje Azure Stack připravenosti kontrola (AzsReadinessChecker) z [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky

Následující požadavky musí být splněné.

**Počítač, ve kterém se nástroj spouští:**

* Windows 10 nebo Windows serveru 2016 a připojení k doméně.
* Prostředí PowerShell 5.1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz prostředí PowerShell a pak si projděte *hlavní* verze a *menší* verze:  
   > `$PSVersionTable.PSVersion`
* Nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj.

**Prostředí Active Directory Federation Services:**

Je třeba splnit aspoň jednu z následujících forem metadat:

* Adresa URL federačních metadat služby AD FS. Například `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Soubor XML metadat federace. Například FederationMetadata.xml

## <a name="validate-adfs-integration"></a>Ověření integrace služby AD FS

1. Na počítači, který splňuje požadavky otevřete Správce příkazový řádek Powershellu a spusťte následující příkaz k instalaci AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Z příkazového řádku PowerShell spuštěním následujícího příkazu spusťte ověření. Zadejte hodnotu pro **- CustomADFSFederationMetadataEndpointUri** jako identifikátor URI pro federačních metadat:

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je v pořádku pro požadavky na integraci služby AD FS. Úspěšné ověření se zobrazí podobný následujícímu.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

V produkčním prostředí testování řetězy certifikátů vztahu důvěryhodnosti z pracovní stanice operátory nelze považovat za plně indikátorem stav důvěryhodnosti infrastruktury veřejných KLÍČŮ v infrastruktuře Azure Stack. Síť veřejných virtuálních IP adres razítko Azure Stack potřebuje připojení k seznamu odvolaných certifikátů pro infrastrukturu veřejných KLÍČŮ.

## <a name="report-and-log-file"></a>Sestavy a soubor protokolu

Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell.

Ověřování souborů můžete sdílet stav před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením. Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením.

Ve výchozím nastavení, oba soubory jsou zapsány do `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Použití:

* **-OutputPath** *cesta* parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.
* **-CleanReport** parametr na konci příkazu run se vymazat *AzsReadinessCheckerReport.json* předchozí informací sestavy. Další informace najdete v tématu [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování

Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také zaznamenává informace, které *AzsReadinessChecker.log*.

Následující příklady poskytují pokyny o běžných chyb při ověřování.

### <a name="command-not-found"></a>Příkaz nenalezen

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Příčina** – prostředí PowerShell Autoload se nenačetl správně modulu Kontrola připravenosti.

**Rozlišení** – Kontrola připravenosti importu modulu explicitně. Zkopírujte a vložte následující kód do prostředí PowerShell a aktualizace \<verze\> s číslem verze pro aktuálně nainstalovanou verzi.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Další kroky

[Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)  
[Důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)  
