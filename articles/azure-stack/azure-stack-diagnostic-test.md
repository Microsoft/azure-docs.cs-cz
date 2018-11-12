---
title: Spustit test pro ověření ve službě Azure Stack | Dokumentace Microsoftu
description: Jak chcete shromažďovat soubory protokolů pro diagnostiku ve službě Azure Stack.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 11/02/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: af601005c7c8bd8fa7fe335879991caa34187927
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236971"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Spustit test pro ověření pro službu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*
 
Můžete ověřit stav služby Azure Stack. Pokud máte potíže, požádejte zákaznickou podporu služeb Microsoftu. Podporu žádá můžete spouštět **testovací AzureStack** z uzlu pro správu. Izoluje ověřovací test chybou. Podpora pak můžete analyzovat podrobné protokoly, zaměřte se na oblasti, kde došlo k chybě a pracovat s vámi při řešení problému.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Pokud máte potíže, obraťte se na zákaznickou podporu služeb Microsoft a pak spusťte **spustit Test-AzureStack**.

1. Máte potíže.
2. Obraťte se na zákaznickou Microsoft služby podpory.
3. Spustit **testovací AzureStack** z privileged koncového bodu.
    1. Přístup k privilegovaným koncový bod. Pokyny najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Na ASDK, přihlaste se k hostiteli správu jako **AzureStack\CloudAdmin**.  
    Na integrovaný systém je potřeba použít IP adresu pro privilegovaný koncový – bod správy, které jste získali od výrobce OEM dodavatele hardwaru.
    3. Spusťte PowerShell jako správce.
    4. Spusťte: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Spusťte: `Test-AzureStack`
4. Pokud některé testy sestavy **selhání**, spusťte: `Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath “<path>” -OutputShareCredential $cred` rutina shromažďuje protokoly z AzureStack testu. Další informace o diagnostických protokolech najdete v tématu [diagnostické nástroje služby Azure Stack](azure-stack-diagnostics.md). Neměli shromažďování protokolů nebo pokud obraťte se na zákaznické služby podpory Microsoftu (CSS) testy sestavy **UPOZORNIT**.
5. Odeslat **SeedRing** protokoly do služby zákaznické podpory Microsoftu. Zákaznická podpora Microsoftu vás služby spolupracuje při řešení problému.

## <a name="reference-for-test-azurestack"></a>Referenční informace pro Test AzureStack

Tato část obsahuje přehled pro rutinu Test-AzureStack a souhrnné informace o sestavu ověření.

### <a name="test-azurestack"></a>Test-AzureStack

Ověří stav služby Azure Stack. Rutina hlásí stav služby Azure Stack hardware a software. Pracovníci podpory zákazníků můžete zkrátit čas, chcete-li vyřešit případy podpory služby Azure Stack pomocí této sestavy.

> [!Note]  
> **Test-AzureStack** může rozpoznat chyb, nezaručují výpadky cloudu, jako je jeden nepovedlo disku nebo selhání uzlu jednom fyzickém hostiteli.

#### <a name="syntax"></a>Syntaxe

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametry

| Parametr               | Hodnota           | Požaduje se | Výchozí |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | Řetězec    | Ne       | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Ne       | FALSE   |
| AdminCredential         | PSCredential    | Ne       | Není k dispozici      |
| Seznam                    | SwitchParameter | Ne       | FALSE   |
| Ignorovat                  | Řetězec          | Ne       | Není k dispozici      |
| Zahrnout                 | Řetězec          | Ne       | Není k dispozici      |
| BackupSharePath         | Řetězec          | Ne       | Není k dispozici      |
| BackupShareCredential   | PSCredential    | Ne       | Není k dispozici      |


Test-AzureStack rutina podporuje běžné parametry: podrobné nastavení, ladění, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable a OutVariable. Další informace najdete v tématu [o společných parametrech](https://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Příkladem testu AzureStack

Následující příklady předpokládají, že jste přihlášeni jako **CloudAdmin** a přístup k privilegovaným koncový bod (období). Pokyny najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Interaktivní spuštění testu AzureStack bez cloudové scénáře

V relaci období spusťte:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Spustit Test AzureStack s cloudové scénáře

Můžete použít **testovací AzureStack** ke spuštění scénáře v cloudu služby Azure Stack. Mezi tyto scénáře patří:

 - Vytvoření skupiny prostředků
 - Vytváření plánů
 - Vytvoření nabídky
 - Vytváření účtů úložiště
 - Vytvoření virtuálního počítače
 - Provedení operace objektů blob pomocí účet úložiště vytvořený ve scénáři testu
 - Provedení operace fronty pomocí účet úložiště vytvořený ve scénáři testu
 - Provádět operace s tabulkou používat účet úložiště vytvořený ve scénáři testu

Cloudové scénáře vyžadují přihlašovací údaje správce ke cloudu. 
> [!Note]  
> Nelze spustit cloudové scénáře pomocí přihlašovacích údajů Active Directory Federated Services (AD FS). **Testovací AzureStack** rutina je k dispozici pouze prostřednictvím období. Ale, období nepodporuje přihlašovací údaje služby AD FS.

Zadejte uživatelské jméno správce cloudu ve formátu hlavního názvu uživatele serviceadmin@contoso.onmicrosoft.com (Azure AD). Po zobrazení výzvy zadejte heslo pro účet správce cloudu.

V relaci období spusťte:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Spustit Test AzureStack bez cloudové scénáře

V relaci období spusťte:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Seznam dostupných testovacích scénářů:

V relaci období spusťte:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Spuštění zadaného testu

V relaci období spusťte:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Vyloučit specifické testy:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Spustit Test-AzureStack infrastruktura zálohování nastavení testu

Před konfigurací zálohování infrastruktury, můžete otestovat cestu sdílené složky záloh a přihlašovacích údajů pomocí **AzsBackupShareAccessibility** testování.

V relaci období spusťte:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
Po dokončení konfigurace zálohování, můžete spustit AzsBackupShareAccessibility ověření sdílená složka přístupná z ERCS, z období relace spuštění:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Test spusťte nové přihlašovací údaje s nakonfigurovanou zálohování sdílené složky, v relaci období:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Ověřovací test

Následující tabulka shrnuje ověřovací testy spustit **testovací AzureStack**.

| Název                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure Stack na Cloudový Hosting infrastruktury souhrn                                                                                  |
| Souhrn služby úložiště Azure Stack                                                                                              |
| Souhrn Instance Role infrastruktury Azure stacku                                                                                  |
| Azure Stack na Cloudový Hosting infrastruktury využití                                                                              |
| Kapacity infrastruktury Azure stacku                                                                                               |
| Souhrn rozhraní API a portálu Azure Stack                                                                                                |
| Azure Stack Souhrn certifikátu Azure Resource Manageru                                                                                               |
| Řadič pro správu základní infrastruktury, síťový adaptér, služeb úložiště a koncový bod privilegovaných rolí infrastruktury          |
| Řadič pro správu základní infrastruktury, síťový adaptér, služeb úložiště a Privileged koncový bod instance rolí infrastruktury |
| Azure Stack infrastrukturu Role souhrn                                                                                           |
| Služeb Azure Stack Cloud Service Fabric                                                                                         |
| Azure Stack infrastrukturu Role Instance výkonu                                                                              |
| Souhrn výkonu hostitele cloudu Azure Stack                                                                                        |
| Souhrn využití prostředků služby Azure Stack                                                                                  |
| Azure Stack Škálovací jednotku kritické události (posledních 8 hodin)                                                                             |
| Souhrn fyzických disků služby úložiště Azure Stack                                                                               |
|Souhrn přístupnost sdílené složky záloh Azure Stack                                                                                     |

## <a name="next-steps"></a>Další postup

 - Další informace o problému protokolování a diagnostické nástroje služby Azure Stack, najdete v článku [ diagnostické nástroje služby Azure Stack](azure-stack-diagnostics.md).
 - Další informace o řešení potíží najdete v tématu [řešení potíží s Microsoft Azure Stack](azure-stack-troubleshooting.md)
