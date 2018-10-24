---
title: Ověření Azure Graph integrace pro Azure Stack
description: Kontrola připravenosti Azure Stack slouží k ověření grafu integrace pro Azure Stack.
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
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947181"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Ověření grafu integrace pro Azure Stack

Nástroj Azure Stack připravenosti kontrola (AzsReadinessChecker) k ověření, že je prostředí připravené pro integraci graphu pomocí služby Azure Stack. Než začnete integrace System center data nebo před nasazením Azure Stack, měli byste ověřit integraci graphu.

Kontrola připravenosti ověří:

* Přihlašovací údaje pro účet služby pro integraci graphu vytvořit nemáte příslušná oprávnění k dotazování služby Active Directory.
* *Globálního katalogu* lze vyřešit a je dotknout.
* Služba KDC lze vyřešit a je dotknout.
* Připojení k síti nutné je na místě.

Další informace o integraci System center data Azure Stack, najdete v části [integrace datových center Azure Stack – identita](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Získejte nástroj prerequisite checker připravenosti

Stáhněte si nejnovější verzi nástroje Azure Stack připravenosti kontrola (AzsReadinessChecker) z [PSGallery](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Požadavky

Následující požadavky musí být splněné.

**Počítač, ve kterém se nástroj spouští:**

* Windows 10 nebo Windows serveru 2016 a připojení k doméně.
* Prostředí PowerShell 5.1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz prostředí PowerShell a pak si projděte *hlavní* verze a *menší* verze:  
   > `$PSVersionTable.PSVersion`
* Modul služby Active Directory pro prostředí PowerShell
* Nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj

**Prostředí Active Directory:**

* Určit uživatelské jméno a heslo pro účet služby graph v existující služby Active Directory
* Identifikujte kořenová doména doménové struktury služby Active Directory plně kvalifikovaný název domény

## <a name="validate-graph"></a>Ověření grafu

1. Na počítači, který splňuje požadavky otevřete Správce příkazový řádek Powershellu a spusťte následující příkaz k instalaci AzsReadinessChecker.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Z příkazového řádku PowerShell, spusťte následující příkaz pro nastavení *$graphCredential* proměnné pro účet grafů. Nahraďte `contoso\graphservice` pomocí vašeho účtu pomocí `domain\username` formátu.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. Z příkazového řádku PowerShell spuštěním následujícího příkazu spusťte ověření pro graf. Zadejte hodnotu pro **- ForestFQDN** jako plně kvalifikovaný název domény pro kořen doménové struktury:

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Po spuštění nástroje, prohlédněte si výstup. Potvrďte, že stav je v pořádku pro požadavky na integraci grafu. Úspěšné ověření se zobrazí podobný následujícímu:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

V produkčním prostředí testování připojení k síti z pracovní stanice operátory nelze považovat za plně orientační připojení dostupné pro Azure Stack. Razítko Azure stacku veřejnou síť virtuálních IP adres musíte připojení pro přenos pomocí protokolu LDAP k provedení integrace identit.

## <a name="report-and-log-file"></a>Sestavy a soubor protokolu

Každé ověření při spuštění, zaznamená výsledky do **AzsReadinessChecker.log** a **AzsReadinessCheckerReport.json**. Umístění těchto souborů se zobrazí s výsledky ověření v prostředí PowerShell.

Ověřování souborů můžete sdílet stav před nasazením služby Azure Stack nebo prozkoumat zaznamenané problémy s ověřením. Oba soubory zachovat výsledky každé následné ověření. Tato sestava poskytuje vaše nasazení team potvrzení konfigurace identity. Soubor protokolu mohou pomoci týmu nasazení nebo odborné pomoci prozkoumat problémy s ověřením.

Ve výchozím nastavení oba soubory jsou zapsány do `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

Použití:

* **-OutputPath** *cesta* parametr na konci spuštění příkazového řádku a zadejte umístění různých sestav.
* **-CleanReport** parametr na konci příkazu run se vymazat *AzsReadinessCheckerReport.json* předchozí informací sestavy. Další informace najdete v tématu [sestavu ověření služby Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Chyby ověřování

Pokud se ověření nezdaří, v okně Powershellu zobrazovat podrobnosti o chybě. Nástroj také zaznamenává informace, které *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Další kroky

[Podívejte se na sestavu připravenosti](azure-stack-validation-report.md)  
[Důležité informace o integraci Azure Stack obecné](azure-stack-datacenter-integration.md)  
