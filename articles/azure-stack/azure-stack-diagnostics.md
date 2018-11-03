---
title: Diagnostika v Azure Stacku
description: Tom, jak shromažďovat soubory protokolů pro diagnostiku ve službě Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/02/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 6e15fee02fd001bddd25a19b8a9420eb899d4f85
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978668"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack diagnostické nástroje

Azure Stack je velkou kolekci komponent spolupráci a komunikaci mezi sebou. Všechny tyto součásti generovat vlastní jedinečné protokoly. Diagnostika problémů může být náročné úlohy, zejména u chyby pocházející z několika interakce součástí Azure stacku. 

Naše nástroje diagnostiky pomáhají zajistit mechanismus shromažďování protokolů snadné a efektivní. Následující diagram znázorňuje jak protokolu kolekce nástrojů pro práci s Azure Stack:

![Azure Stack diagnostické nástroje](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Kolektor trasování
 
Kolektor trasování je standardně povolená a běží nepřetržitě na pozadí shromažďovat všechny protokoly trasování událostí pro Windows (ETW) ze služby Azure Stack komponent. Protokoly trasování událostí pro Windows se ukládají v běžné místní sdílené složky s limitem stáří pět den. Po dosažení tohoto limitu se odstraní nejstarší soubory vytvářené nové značky. Výchozí maximální povolenou velikost každého souboru je 200 MB. Velikost kontrola probíhá každé dvě minuty, a pokud je aktuální soubor > = 200 MB, se uloží a vygeneruje se nový soubor. Je také 8 GB omezený na celkové velikosti generovaných každý relace události. 

## <a name="log-collection-tool"></a>Nástroj pro shromažďování protokolů
 
Rutiny Powershellu **Get-AzureStackLog** je možné shromažďovat protokoly ze všech komponent v prostředí Azure Stack. Ukládá je do souborů zip v umístění definovaný uživatelem. Když tým technické podpory Azure Stack potřebuje vaše protokoly, které vám pomohou vyřešit problém, může vás požádá o spuštění tohoto nástroje.

> [!CAUTION]
> Tyto soubory protokolu může obsahovat identifikovatelné osobní údaje (PII). Vzít v úvahu předtím, než pošlete veřejně všechny soubory protokolů.
 
Tady jsou některé typy příklad protokolu, které jsou shromažďovány:
*   **Protokoly nasazení Azure Stack**
*   **Protokoly událostí Windows**
*   **Protokoly Panther**
*   **Protokoly clusteru**
*   **Diagnostické protokoly úložiště**
*   **Protokoly trasování událostí pro Windows**

Tyto soubory jsou shromažďovány a kolekcí pro trasovacího uložil ve sdílené složce. **Get-AzureStackLog** rutiny Powershellu lze poté ke shromažďování je v případě potřeby.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Spuštění Get-AzureStackLog ve službě Azure Stack integrované systémy 
Chcete-li spustit nástroj pro shromažďování protokolů na integrovaný systém, musíte mít přístup k privilegovaným koncový bod (období). Tady je ukázkového skriptu můžete spustit pomocí období pro shromažďování protokolů na integrovaný systém:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Parametry **OutputSharePath** a **OutputShareCredential** slouží k nahrávání protokolů do externí sdílené složky.
- Jak je znázorněno v předchozím příkladu **datum FromDate** a **ToDate** parametry můžete použít ke shromažďování protokolů pro určité časové období. To může hodit pro scénáře, jako je shromažďování protokolů po použití balíčku aktualizace na integrovaný systém.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Spuštění Get-AzureStackLog v systému Azure Stack Development Kit (ASDK)
1. Přihlaste se jako **AzureStack\CloudAdmin** na hostiteli.
2. Otevřete okno Powershellu jako správce.
3. Spustit **Get-AzureStackLog** rutiny Powershellu.

**Příklady:**

  Shromážděte všechny protokoly pro všechny role:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Shromažďování protokolů z virtuálních počítačů a BareMetal rolí:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Shromažďování protokolů z role virtuálních počítačů a BareMetal s datem filtrování pro soubory protokolů za posledních 8 hodin:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Shromažďování protokolů z role virtuálních počítačů a BareMetal filtrování pro soubory protokolu pro toto časové období před 8 hodin až 2 hodiny před datem:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Důležité informace o parametru pro ASDK a integrované systémy

- Pokud **datum FromDate** a **ToDate** nejsou zadány parametry, se shromáždí protokoly pro poslední 4 hodiny ve výchozím nastavení.
- Použití **FilterByNode** parametr pro filtrování protokolů podle názvu počítače. Příklad:
```powershell
Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
```
- Použití **FilterByLogType** parametr pro filtrování protokolů podle typu. Můžete filtrovat podle souborů, sdílené složky nebo WindowsEvent. Příklad:
```powershell
Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
```
- Můžete použít **TimeOutInMinutes** parametr nastavit časový limit pro shromažďování protokolů. Ve výchozím nastavení nastavená 150 (2,5 hodin).
- Ve verzi 1805 a novější je ve výchozím nastavení zakázána shromažďování výpisu stavu systému souborů protokolu. Chcete-li ji povolit, použijte **IncludeDumpFile** přepnout parametru. 
- V současné době můžete použít **FilterByRole** parametr filtru shromažďování protokolů pomocí následujících rolí:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |Výrobce OEM|
 |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |ISP                            |KeyVaultAdminResourceProvider  |POMOCÍ TECHNOLOGIE PXE|
 |ACSFrontEnd           |CRP.                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domain (Doména)                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |OSN                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Úložiště|
 |ADFS                  |FabricRing                     |MetricsServer                  |Kontroler úložiště   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |virtuálních počítačů   |
 |AzureMonitor          |brána                        |SÍŤOVÝ ADAPTÉR.                             |BYL|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Další aspekty

* Příkaz trvá nějakou dobu ke spuštění na role, které shromažďují v protokolech. Přispívající faktory zahrnují také doba zadaná pro shromažďování protokolů a počtu uzlů v prostředí Azure Stack.
* Jak protokolovat spuštěných kolekcí, zkontrolujte nové složky vytvořené v **OutputSharePath** zadaný v příkazu parametr.
* Každá role má protokolům uvnitř zip jednotlivé soubory. V závislosti na velikosti shromažďovat protokoly může mít roli protokolům rozdělit do více souborů zip. Pro roli Pokud budete chtít mít všechny soubory protokolů, odblokujte v na jedinou složku, použijte nástroj, který lze rozbalit hromadné (například 7zip). Vyberte všechny soubory ZIP pro roli a vyberte **extrahovat zde**. To unzips všech souborů protokolů pro tuto roli v jedné sloučené složce.
* Soubor s názvem **Get-AzureStackLog_Output.log** se také vytvoří ve složce, která obsahuje soubory ZIP protokolů. Tento soubor je protokol výstup příkazu, který můžete použít při řešení problémů během shromažďování protokolů. Někdy soubor protokolu obsahuje `PS>TerminatingError` položky, které můžete ignorovat, pokud chybí očekávaný protokolu soubory po spuštění shromažďování protokolů.
* Chcete-li zjistit, konkrétní selhání, může být potřeba protokoly z více než jednu součást.
    -   Protokoly událostí pro všechny virtuální počítače infrastruktury a systému jsou shromážděny v *VirtualMachines* role.
    -   Protokoly událostí pro všechny hostitele a systém se shromažďují v *BareMetal* role.
    -   Protokoly událostí clusteru převzetí služeb při selhání a technologie Hyper-V se shromažďují v *úložiště* role.
    -   Protokoly služby ACS se shromažďují v *úložiště* a *ACS* role.

> [!NOTE]
> Velikost a stáří limity se vynucují v protokolech shromažďovaných je nezbytné k zajištění efektivního využití prostoru úložiště Ujistěte se, že ho nezíská zahlcenou s protokoly. Ale při diagnostikování problému budete někdy potřebovat protokoly, které už možná z důvodu tato omezení neexistuje. Je tedy **důrazně doporučujeme** snižování zátěže protokolů do prostoru externí úložiště (účet úložiště v Azure, zařízení s další místní úložiště atd.) každých 8 až 12 hodin a udržte je tam po dobu 1-3 měsíců. v závislosti na vaší požadavky. Také se ujistěte, že toto umístění úložiště je zašifrovaný.

## <a name="next-steps"></a>Další postup
[Řešení potíží s Microsoft Azure Stack](azure-stack-troubleshooting.md)

