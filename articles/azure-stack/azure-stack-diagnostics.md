---
title: Diagnostika v Azure Stacku
description: Jak chcete shromažďovat soubory protokolů pro diagnostiku v Azure zásobníku
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 50fef25a3b7b71821e64638729eb8d93f65b9e31
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063784"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure zásobníku diagnostické nástroje

Zásobník Azure je velké kolekce součástí spolupráci a interakci mezi sebou. Všechny tyto součásti generovat vlastní jedinečné protokoly. Diagnostika problémů může být náročné úlohy, hlavně pro chyby pocházejících z několika interakci součásti zásobníku Azure. 

Naše diagnostické nástroje pomoci, ujistěte se, že kolekce mechanismu protokolu snadnou a efektivní. Následující diagram ukazuje jak protokolu nástroje kolekce v pracovním zásobník Azure:

![Azure zásobníku diagnostické nástroje](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Kolekce trasování
 
Kolektor trasování je ve výchozím nastavení povolené a nepřetržitě běží na pozadí shromažďovat všechny protokoly trasování událostí pro Windows (ETW) ze služby komponent Azure zásobníku. Protokoly trasování událostí pro Windows jsou uloženy v běžné místní sdílené složky s maximální stáří pět den. Po dosažení tohoto limitu jsou nejstarší soubory odstranit, protože se vytvářejí nové. Výchozí maximální velikost povolenou pro každý soubor je 200 MB. Zkontrolujte velikost dojde každé dvě minuty, a pokud je aktuální soubor > = 200 MB, bude uložený a se generuje nový soubor. Je také omezení 8 GB na celkové velikosti generovaný podle relace události. 

## <a name="log-collection-tool"></a>Nástroj pro shromažďování protokolů
 
Rutiny prostředí PowerShell **Get-AzureStackLog** slouží k shromažďování protokolů ze všech komponent v prostředí Azure zásobníku. Ukládá je do zip soubory v umístění definovaný uživatelem. Pokud tým technické podpory zásobník Azure potřebuje vaše protokoly, které pomůžou vyřešit nějaký problém, se může požádat o spuštění tohoto nástroje.

> [!CAUTION]
> Tyto soubory protokolu může obsahovat identifikovatelné osobní údaje (PII). Vzít v úvahu před veřejně post všechny soubory protokolů.
 
Následuje několik příkladů typů protokolu, které byly shromážděny:
*   **Protokoly nasazení Azure zásobníku**
*   **Protokoly událostí systému Windows**
*   **Protokoly Panther**
*   **Protokoly clusteru**
*   **Diagnostické protokoly úložiště**
*   **Protokoly trasování událostí pro Windows**

Tyto soubory jsou shromážděny a uloženy ve sdílené složce kolekce trasování. **Get-AzureStackLog** rutiny prostředí PowerShell pak umožňuje shromažďovat je, pokud je to nezbytné.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Ke spuštění v Azure zásobníku Get-AzureStackLog integrované systémy 
Chcete-li spustit nástroj kolekce protokolu na integrovaný systém, mají přístup do privilegované koncového bodu (období). Tady je ukázkového skriptu můžete spustit pomocí období pro shromažďování protokolů na integrovaný systém:

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

- Parametry **OutputSharePath** a **OutputShareCredential** se používají k odeslání protokolů k externí sdílené složce.
- Jak je uvedeno v předchozím příkladu **FromDate** a **ToDate** parametry lze shromažďovat protokoly pro konkrétní časové období. To může se hodit pro scénáře, jako je shromažďování protokolů po použití balíčku aktualizace na integrovaný systém.


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Ke spuštění Get-AzureStackLog v systému Azure zásobníku Development Kit (ASDK)
1. Přihlaste se jako **AzureStack\CloudAdmin** na hostiteli.
2. Otevřete okno prostředí PowerShell jako správce.
3. Spustit **Get-AzureStackLog** rutiny prostředí PowerShell.

**Příklady:**

  Shromážděte všechny protokoly pro všechny role:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Shromažďování protokolů z virtuálních počítačů a BareMetal rolí:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Shromažďování protokolů z role virtuálních počítačů a BareMetal s datem filtrování za posledních 8 hodin pro soubory protokolů:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Shromažďování protokolů z role virtuálních počítačů a BareMetal filtrování pro soubory protokolu pro toto časové období před 8 hodin až 2 hodiny před datem:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Parametr aspekty ASDK a integrované systémy

- Pokud **FromDate** a **ToDate** nebyly zadány parametry, se shromáždí protokoly pro poslední čtyři hodiny ve výchozím nastavení.
- Můžete použít **TimeOutInMinutes** parametr nastavit časový limit pro shromažďování protokolů. Ve výchozím nastavení, jinak je nastavená na 150 (2,5 hodiny).
- Ve verzi 1805 a novější se ve výchozím nastavení vypnutá shromažďování výpisu souborů protokolu. Chcete-li ji povolit, použijte **IncludeDumpFile** přepínač parametr. 
- V současné době můžete použít **FilterByRole** parametr do kolekce filtrů protokolu podle následujících rolí:

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | Síť                    |
   | ACSFabric              | Doména                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | Výrobce OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | Galerie                          | SlbVips                    |
   | ACSWac                 | brána                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Úložiště                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | IDN                             | Klient                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | Infrastruktura                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | Cloud                  | KeyVaultControlPlane             | virtuálních počítačů            |
   | Cluster                | KeyVaultDataPlane                | BYL                        |
   | Compute                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | ISP                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP.                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>Shromažďování protokolů pomocí grafického uživatelského rozhraní
Místo poskytuje požadované parametry pro rutinu Get-AzureStackLog získat protokoly zásobník Azure, můžete využít i nástroje zásobník Azure k dispozici s otevřeným zdrojem umístěný v hlavní zásobník Azure nástrojů nástroje úložiště GitHub v http://aka.ms/AzureStackTools.

**ERCS_AzureStackLogs.ps1** skript prostředí PowerShell je uložen v úložišti GitHub nástroje a se aktualizuje v pravidelných intervalech. Pro jistotu, že máte k dispozici nejnovější verzi, by měla stáhnout přímo z http://aka.ms/ERCS. Skript spustit z relace prostředí PowerShell pro správu, připojí k privilegované koncového bodu a spustí Get-AzureStackLog pomocí zadaných parametrů. Pokud jsou zadány žádné parametry, skript výchozí výzvy pro parametry přes grafické uživatelské rozhraní.

Další informace o skriptu prostředí PowerShell ERCS_AzureStackLogs.ps1, můžete sledovat [krátké video](https://www.youtube.com/watch?v=Utt7pLsXEBc) nebo zobrazit tento skript [souboru readme](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) umístěný v úložišti GitHub nástroje Azure zásobníku. 

### <a name="additional-considerations"></a>Další aspekty

* Příkaz přijímá chvíli ke spuštění na základě na rolí, které shromažďují v protokolech. Přispívajících faktorů také zahrnovat doby trvání, zadaný pro shromažďování protokolů a počtu uzlů v prostředí Azure zásobníku.
* Podle protokolu spustí kolekce, zkontrolujte do nové složky vytvořené v **OutputSharePath** zadaný v příkazu parametr.
* Každá role má protokoly uvnitř zip jednotlivé soubory. V závislosti na velikosti shromažďovat protokoly může mít roli protokoly rozdělit do několika souborů zip. Pro roli Pokud chcete mít všechny soubory protokolu v rozbalené do jediné složky, použijte nástroj, který můžete rozbalte hromadně (například 7zip). Vyberte všechny komprimované soubory pro roli a vyberte **extrahovat zde**. To unzips všechny soubory protokolu pro tuto roli v jedné sloučené složce.
* Soubor nazývá **Get-AzureStackLog_Output.log** se také vytvoří ve složce, která obsahuje soubory komprimované protokolu. Tento soubor je protokolu výstupu příkazu, který můžete použít při řešení problémů během shromáždění protokolů. Někdy protokolový soubor zahrnuje `PS>TerminatingError` položky, které můžete bezpečně ignorovat, pokud očekávaný protokolu souborů chybí po protokolu spustí kolekce.
* Prozkoumat konkrétní chyby, může být potřeba protokolů z více než jedna součást.
    -   Systém a protokoly událostí pro všechny virtuální počítače infrastruktury se shromažďují v *VirtualMachines* role.
    -   Systém a protokoly událostí pro všechny hostitele se shromažďují v *BareMetal* role.
    -   Protokoly událostí clusteru převzetí služeb při selhání a technologie Hyper-V se shromažďují v *úložiště* role.
    -   Protokoly služby ACS se shromažďují v *úložiště* a *ACS* role.

> [!NOTE]
> Velikost a stáří limity prosazují na protokoly shromážděny, protože to je nezbytné k zajištění efektivní využití prostoru úložiště zajistit, že není získají přenášeny s protokoly. Při diagnostice problém někdy však protokoly, které už možná neexistuje z důvodu těchto mezních hodnot. Z toho důvodu je **důrazně doporučujeme** snižování zátěže protokolů k prostorem externího úložiště (účet úložiště v Azure, zařízení s další místní úložiště atd.) každých 8 až 12 hodin a zůstanou existuje pro 1 – 3 měsíce, v závislosti na vaší požadavky. Také zkontrolujte, zda že toto umístění úložiště je zašifrovaná.

## <a name="next-steps"></a>Další postup
[Řešení potíží s Microsoft Azure zásobníku](azure-stack-troubleshooting.md)

