---
title: "Spustit test pro ověření v zásobníku Azure | Microsoft Docs"
description: "Jak chcete shromažďovat soubory protokolů pro diagnostiku v Azure zásobníku"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: 4f86397d4db5a0e67b294befd92087166d6b8109
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Spustit test pro ověření pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*
 
Můžete ověřit stav zásobník Azure. Pokud máte potíže, obraťte se na zákaznickou podporu služeb společnosti Microsoft. Podpory se žádostí o spustit Test AzureStack z vaší správy uzlu. Ověřovací test izoluje selhání. Podporu můžete analyzovat podrobné protokoly, soustředit na oblasti, kde došlo k chybě a pracovat s vámi při řešení problému.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Pokud máte potíže, obraťte se na zákaznickou podporu služeb společnosti Microsoft a pak spusťte **spustit Test-AzureStack**.

1. Máte potíže.
2. Obraťte se na zákaznický Microsoft služby podpory.
3. Spustit **Test AzureStack** od privilegovaného koncového bodu.
    1. Přístup k privilegované koncový bod. Pokyny najdete v tématu [pomocí privilegované koncový bod v zásobníku Azure](azure-stack-privileged-endpoint.md). 
    2. Přihlaste se jako **AzureStack\CloudAdmin** na hostiteli správy.
    3. Spusťte PowerShell jako správce.
    4. Spusťte: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Spusťte: `Test-AzureStack`
4. Pokud všechny testy sestavy služeb při selhání, spusťte: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` rutinu shromažďuje protokoly z AzureStack testu. Další informace o diagnostických protokolů najdete v tématu [zásobník Azure diagnostické nástroje](azure-stack-diagnostics.md).
5. Odeslat **SeedRing** protokoly na zákaznickou podporu služeb společnosti Microsoft. Služby podpory zákazníků společnosti Microsoft funguje s vámi k vyřešení problému.

## <a name="reference-for-test-azurestack"></a>Referenční dokumentace pro Test AzureStack

Tato část obsahuje přehled pro rutinu Test-AzureStack a souhrn ověření sestavy.

### <a name="test-azurestack"></a>Test-AzureStack

Ověří stav zásobník Azure. Rutina hlásí stav zásobník Azure hardware a software. Tato sestava pracovníky technické podpory slouží ke zkrácení doby řešení případů podpory Azure zásobníku.

> [!Note]  
> Test AzureStack může rozpoznat chyby, které nejsou vést k výpadkům cloudu, jako jednoho nepodařilo disku nebo selhání uzlu jednom fyzickém hostiteli.

#### <a name="syntax"></a>Syntaxe

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametry

| Parametr               | Hodnota           | Požaduje se | Výchozí |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | Přihlašovací údaje    | Ne       | FALSE   |
| DoNotDeployTenantVm     | Přepínací parametr | Ne       | FALSE   |
| AdminCredential         | Přihlašovací údaje    | Ne       | Není k dispozici      |
<!-- | StorageConnectionString | Řetězec          | Ne       | Není k dispozici      | není podporována v 1802-->
| Seznam                    | Přepínací parametr | Ne       | FALSE   |
| Ignorovat                  | Řetězec          | Ne       | Není k dispozici      |
| Zahrnout                 | Řetězec          | Ne       | Není k dispozici      |

Test AzureStack rutina podporuje běžné parametry: podrobné nastavení, ladění, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable a OutVariable. Další informace najdete v tématu [o společných parametrech](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Příklady AzureStack testu

Následující příklady předpokládají, že jste přihlášeni jako **CloudAdmin** a přístup k privilegované koncový bod (období). Pokyny najdete v tématu [pomocí privilegované koncový bod v zásobníku Azure](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Spustit Test AzureStack interaktivně bez scénářích cloudu

Spusťte v relaci období:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Spusťte Test AzureStack s scénářích cloudu

Test AzureStack můžete použít ke spouštění vaší zásobník Azure cloud scénáře. Mezi tyto scénáře patří:

 - Vytvoření skupiny prostředků
 - Vytváření plánů
 - Vytvoření nabídky
 - Vytvoření účtů úložiště
 - Vytvoření virtuálního počítače
 - Provedení operace objektů blob pomocí účtu úložiště vytvořeném ve scénáři testu
 - Provedení operace fronty pomocí účtu úložiště vytvořeném ve scénáři testu
 - Provést operace s tabulkou pomocí účtu úložiště vytvořeném ve scénáři testu

Cloud scénáře vyžadují přihlašovací údaje správce cloudu. 
> [!Note]  
> Nelze spustit scénářích cloudu pomocí přihlašovacích údajů pro Active Directory Federated Services (ADFS). **Test AzureStack** rutina je pouze přístupné přes období. Ale období nepodporuje přihlašovací údaje služby AD FS.

Zadejte uživatelské jméno správce cloudu ve formátu UPN serviceadmin@contoso.onmicrosoft.com (AAD). Po zobrazení výzvy zadejte heslo k účtu správce cloudu.

Spusťte v relaci období:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Spustit Test AzureStack bez scénářích cloudu

Spusťte v relaci období:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Seznam dostupných testovací scénáře:

Spusťte v relaci období:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Spuštění zadaný testu

Spusťte v relaci období:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Vyloučit konkrétní testy:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Ověřovací test

Následující tabulka shrnuje spustit Test AzureStack ověřovací testy.

| Název                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Hostování souhrn infrastruktury cloudu Azure zásobníku                                                                                  |
| Souhrn služby Azure zásobník úložiště                                                                                              |
| Souhrn Instance Role infrastruktury Azure zásobníku                                                                                  |
| Hostování využití infrastruktury cloudu Azure zásobníku                                                                              |
| Kapacita infrastruktury Azure zásobníku                                                                                               |
| Portál Azure zásobníku a souhrn rozhraní API                                                                                                |
| Azure zásobníku Souhrn certifikátu Azure Resource Manager                                                                                               |
| Řadiče pro správu infrastruktury, síťový adaptér, služby úložiště a koncový bod privilegované role infrastruktury          |
| Řadiče pro správu infrastruktury, síťový adaptér, služby úložiště a privilegovaných koncový bod instance rolí infrastruktury |
| Souhrn Azure Role infrastruktury zásobníku                                                                                           |
| Azure zásobníku cloudové služby prostředků infrastruktury služby                                                                                         |
| Azure zásobníku infrastruktury Role Instance výkonu                                                                              |
| Souhrn výkonu hostitele cloudu Azure zásobníku                                                                                        |
| Souhrn spotřeby prostředků služby Azure zásobníku                                                                                  |
| Azure zásobníku Škálovací jednotky kritické události (posledních 8 hodin)                                                                             |
| Souhrn fyzické disky služby Azure zásobník úložiště                                                                               |

## <a name="next-steps"></a>Další postup

 - Další informace o problému protokolování a diagnostické nástroje zásobník Azure najdete v tématu [ zásobník Azure diagnostické nástroje](azure-stack-diagnostics.md).
 - Další informace o řešení problémů naleznete v tématu [řešení potíží s Microsoft Azure zásobníku](azure-stack-troubleshooting.md)