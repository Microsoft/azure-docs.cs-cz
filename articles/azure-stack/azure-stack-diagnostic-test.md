---
title: Použití nástroje ověřování služby Azure Stack | Dokumentace Microsoftu
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: eca66bc2e737b0f9a9954cad21a446e82d753f84
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203807"
---
# <a name="validate-azure-stack-system-state"></a>Ověření stavu systému Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako operátory Azure stacku je nezbytné, je schopna určit stav systému na vyžádání. Nástroj pro ověřování služby Azure Stack (**testovací AzureStack**) je rutiny Powershellu, která umožňuje spustit sérii testů ve vašem systému k identifikaci selhání, pokud jsou k dispozici. Zobrazí obvykle se výzva ke spuštění tohoto nástroje [privilegovaných koncový bod (období)](azure-stack-privileged-endpoint.md) když požádáte zákaznické služby podpory Microsoftu (CSS) s problémem. Systémová stavu a aktuální informace o stavu šablon stylů CSS můžete shromažďovat a analyzovat podrobné protokoly, zaměřte se na oblasti, kde došlo k chyby a spolupracovat s vámi k vyřešení daného problému.

## <a name="running-the-validation-tool-and-accessing-results"></a>Spuštění nástroje ověřování a přístup k výsledky

Jak bylo uvedeno dříve, je spuštěn nástroj pro ověřování prostřednictvím období. Každý test vrátí **ÚSPĚŠNÉHO/NEÚSPĚŠNÉHO** stav v okně prostředí PowerShell. Kromě toho podrobnou sestavu HTML se vytvoří, který můžete získat přístup později během [protokolu kolekce](azure-stack-diagnostics.md). Tady je přehled začátku do konce ověřovací testování procesu: 

1. Přístup k privilegovaným koncový bod (období). Spusťte následující příkazy k vytvoření relace období:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pro přístup k období na hostitelském počítači ASDK, účely AzS ERCS01 - ComputerName.

2. Až budete v období, spusťte: 

   ```powershell
   Test-AzureStack
   ```

   Odkazovat [důležité informace o parametru](azure-stack-diagnostic-test.md#parameter-considerations) a [případu příklady použít](azure-stack-diagnostic-test.md#use-case-examples) oddíly pro další informace.

3. Pokud některé testy sestavy **selhání**, spusťte:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Rutina shromažďuje protokoly generované AzureStack testu. Další informace o diagnostických protokolech najdete v tématu [diagnostické nástroje služby Azure Stack](azure-stack-diagnostics.md). Neměli shromažďování protokolů nebo kontaktujte CSS, pokud testy sestavy **UPOZORNIT**.

4. Pokud jste dostali pokyn ke spuštění nástroje ověření pomocí šablon stylů CSS, šablon stylů CSS zástupce bude požadovat protokoly, které jste shromáždili pokračujte vašeho problému.

## <a name="tests-available"></a>Testy, které jsou k dispozici

Ověření nástroj umožňuje spustit sérii testů úrovni systému a základní cloudové scénáře, které vám poskytnou přehled na aktuální stav a zjistit problémy v systému.

### <a name="cloud-infrastructure-tests"></a>Testy infrastruktury cloudu

Tyto testy malý vliv pracovat na úrovni infrastruktury a poskytneme vám informace o různých komponent systému a funkcí. V současné době jsou testy seskupené do následujících kategorií:

| Kategorie testu                                        | Argument – zahrnout a - ignorovat |
| :--------------------------------------------------- | :-------------------------------- |
| Souhrn upozornění služby Azure Stack                            | AzsAlertSummary                   |
| Souhrn přístupnost sdílené složky záloh Azure Stack       | AzsBackupShareAccessibility       |
| Souhrn roviny řízení služby Azure Stack                    | AzsControlPlane                   |
| Souhrn Defender Azure Stack                         | AzsDefenderSummary                |
| Souhrn Firmware infrastruktury hostování Azure Stack  | AzsHostingInfraFWSummary          |
| Azure Stack na Cloudový Hosting infrastruktury souhrn     | AzsHostingInfraSummary            |
| Azure Stack na Cloudový Hosting infrastruktury využití | AzsHostingInfraUtilization        |
| Kapacity infrastruktury Azure stacku                  | AzsInfraCapacity                  |
| Výkon infrastruktury Azure stacku               | AzsInfraPerformance               |
| Souhrn rolí infrastruktury Azure stacku              | AzsInfraRoleSummary               |
| Shrnutí aktualizací Azure Stack                           | AzsInfraUpdateSummary             |
| Souhrn rozhraní API a portálu Azure Stack                   | AzsPortalAPISummary               |
| Azure Stack Škálovací jednotku virtuálního počítače události                     | AzsScaleUnitEvents                |
| Prostředky virtuálních počítačů jednotek škálování služby Azure Stack                  | AzsScaleUnitResources             |
| Souhrn ověření SDN Azure Stack                   | AzsSDNValidation                  |
| Souhrn rolí prostředků infrastruktury služby Azure Stack              | AzsSFRoleSummary                  |
| Přehled služby Azure Stack BMC                              | AzsStampBMCSummary                |
| Souhrn služby úložiště Azure Stack                 | AzsStorageSvcsSummary             |
| Přehled Azure Stack SQL Store                        | AzsStoreSummary                   |
| Placement Summary virtuálním počítači Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Testy cloudové scénáře

Kromě výše uvedených infrastrukturu testů máte také možnost spouštět testy scénář cloudu ke kontrole funkce pro součásti infrastruktury. Ke spuštění těchto testů, protože zahrnují nasazení prostředků se vyžadují přihlašovací údaje správce cloudu. 
    > [!NOTE]
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

Následující scénáře cloudu jsou testovány pomocí nástroje ověření:
- Vytvoření skupiny prostředků   
- Vytvoření plánu              
- Vytvoření nabídky            
- Vytvoření účtu úložiště   
- Vytvoření virtuálního počítače 
- Operace úložiště objektů BLOB   
- Operace fronty úložiště  
- Operace úložiště tabulky  

## <a name="parameter-considerations"></a>Důležité informace o parametru

- Parametr **seznamu** slouží k zobrazení všech dostupných kategoriích.

- Parametry **zahrnout** a **Ignorovat** umožňuje zahrnout nebo vyloučit kategorií testů. Zobrazit [testy, které jsou k dispozici](azure-stack-diagnostic-test.md#tests-available) části Další informace o zkratku pro použití s těmito argumenty.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Tenanta, které je virtuální počítač nasazen jako součást jednoho testuje scénář cloudu. Můžete použít **DoNotDeployTenantVm** to zakázat. 

- Budete muset zadat **ServiceAdminCredential** parametr pro spouštění testů scénář cloudu, jak je popsáno v [případu příklady použít](azure-stack-diagnostic-test.md#use-case-examples) oddílu.

- **BackupSharePath** a **BackupShareCredential** se používají při testování nastavení zálohování infrastruktury, jak je znázorněno [případu příklady použít](azure-stack-diagnostic-test.md#use-case-examples) oddílu.

- Nástroj ověřování také podporuje společné parametry prostředí PowerShell: Podrobné nastavení, ladění, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable a OutVariable. Další informace najdete v tématu [o společných parametrech](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Příklady případů použití

### <a name="run-validation-without-cloud-scenarios"></a>Spustit ověření bez cloudové scénáře

Spustit nástroj ověření bez **ServiceAdminCredential** parametru pro přeskočení spouštění testů cloudové scénáře: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Spustit ověření pomocí cloudové scénáře

Poskytuje nástroj ověření s **ServiceAdminCredentials** parametr ve výchozím nastavení spustí testy cloudové scénáře: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Pokud chcete spustit pouze cloudové scénáře bez spuštění zbývající testy, můžete použít **zahrnout** parametr k tomu: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Uživatelské jméno správce cloudu musí být zadány ve formátu hlavního názvu uživatele: serviceadmin@contoso.onmicrosoft.com (Azure AD). Po zobrazení výzvy zadejte heslo pro účet správce cloudu.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Spusťte nástroj pro testování připravenosti systému před instalací aktualizace nebo opravy hotfix pro ověřování

Před zahájením instalace aktualizace nebo opravy hotfix, by měl spustit nástroj ověření a zkontrolujte stav služby Azure Stack:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Spuštění nástroje pro ověření chcete otestovat nastavení infrastruktury zálohování

*Před* konfigurace infrastruktury zálohování, můžete otestovat cestu sdílené složky záloh a přihlašovacích údajů pomocí **AzsBackupShareAccessibility** testu: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Po* konfigurace zálohování, můžete spustit **AzsBackupShareAccessibility** ověření sdílená složka přístupná z ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Pokud chcete otestovat nové přihlašovací údaje s nakonfigurovanou sdílené složky záloh, spusťte: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Další postup

Další informace o problému protokolování a diagnostické nástroje služby Azure Stack, najdete v článku [diagnostické nástroje služby Azure Stack](azure-stack-diagnostics.md).

Další informace o řešení potíží najdete v tématu [řešení potíží s Microsoft Azure Stack](azure-stack-troubleshooting.md).