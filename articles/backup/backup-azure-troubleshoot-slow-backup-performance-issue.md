---
title: Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
description: Poskytuje pokyny k odstraňování problémů, které vám pomohou diagnostikovat příčiny problémů s výkonem Azure Backup
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704941"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
Tento článek obsahuje pokyny k odstraňování problémů, které vám pomohou diagnostikovat příčiny pomalého výkonu zálohování souborů a složek, při použití Azure Backup. Pokud používáte agenta Azure Backup k zálohování souborů, záložní proces může trvat déle než obvykle. Toto zpoždění může být způsobena jednou nebo více z následujících akcí:

* [Na počítači, který je právě zálohován nejsou problémových míst výkonu.](#cause1)
* [Jiný proces nebo antivirový software je zasahovala do procesu Azure Backup.](#cause2)
* [Agent zálohování běží na virtuálním počítači Azure (VM).](#cause3)  
* [Zálohujete velké množství souborů (miliony).](#cause4)

Předtím, než se pustíte do odstraňování problémů, doporučujeme stáhnout a nainstalovat [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent). Usnadňujeme časté aktualizace pro agenta zálohování různé problémy, přidat funkce, a zlepšit výkon.

Také důrazně doporučujeme, abyste se seznámili [nejčastějších dotazech ke službě Azure Backup](backup-azure-backup-faq.md) abyste měli jistotu, že některé běžné problémy s konfigurací nejde.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: Kritické body výkonu na počítači
Kritická místa v počítači, který je právě zálohován může to způsobit prodlevy. Počítači umožňuje čtení nebo zápisu na disk nebo dostupnou šířku pásma pro odesílání dat přes síť, například může způsobit kritické body.

Windows poskytuje integrované nástroje, která je volána [sledování výkonu](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) ke zjištění těchto kritické body.

Tady jsou některé čítače výkonu a rozsahy, které může být užitečné při diagnostice problémová místa pro optimální zálohování.

| Čítač | Stav |
| --- | --- |
| Logický Disk (fyzického disku)--% nečinnosti |• 100 % nečinnosti na 50 % nečinný = v pořádku</br>• 49 % nečinnosti 20 % nečinnosti = upozornění nebo monitorování</br>• 19 % nečinnosti na 0 % nečinný = důležité aktualizace nebo z specifikace |
| Logický Disk (fyzického disku)--% střední Disku čtení nebo zápisu |• 0,001 ms pro 0,015 ms = v pořádku</br>• 0,015 ms pro 0,025 ms = upozornění nebo monitorování</br>• 0.026 ms nebo už = důležité aktualizace nebo z specifikace |
| Logický Disk (fyzického disku) – aktuální délka fronty disku (pro všechny instance) |80 požadavků po dobu více než 6 minut |
| Paměť – bajty stránkovaného fondu bez |• Méně než 60 % fondu spotřebované = v pořádku<br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = důležité aktualizace nebo z specifikace |
| Paměť – bajty stránkovaného fondu |• Méně než 60 % fondu spotřebované = v pořádku</br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = důležité aktualizace nebo z specifikace |
| Paměť – počtu dostupných megabajtů |• 50 % podíl volné paměti, které jsou k dispozici nebo více = v pořádku</br>• 25 % podíl volné paměti, které jsou k dispozici = monitorování</br>• 10 % podíl volné paměti, které jsou k dispozici = upozornění</br>• Méně než 100 MB nebo 5 % volné paměti k dispozici = důležité aktualizace nebo z specifikace |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Procesor –\`času procesoru (všechny instance)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Méně než 60 % využité = v pořádku</br>• 61 % až 90 % využité = monitorování nebo upozornění</br>• 91 až 100 % využité = kritický |

> [!NOTE]
> Pokud zjistíte, že infrastruktura nadměrné spotřeby, doporučujeme defragmentaci disky pravidelně pro zajištění lepšího výkonu.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Příčina: Jiný proces nebo antivirový software zasahovala do Azure Backup
Zaznamenali jsme několik instancí, kde jiné procesy v systému Windows negativně ovlivněna výkon samotného procesu agenta Azure Backup. Například pokud používáte agenta Azure Backup a jiný program k zálohování dat, nebo pokud antivirový software běží, má zámek na soubory, které mají být zazálohovány násobek uzamčení na souborů může způsobit kolize. V takovém případě zálohování může selhat nebo úlohy může trvat déle než obvykle.

Chcete-li vypnout zálohovacím programu a podívejte se, zda čas zálohování pro agenta Azure Backup změní je nejlepší doporučení v tomto scénáři. Ujistěte se, že několik úloh zálohování nejsou spuštěné ve stejnou dobu je obvykle dostatečná a zabrání tak jejich vzájemného ovlivnění.

Antivirové programy doporučujeme můžete vyloučit následující soubory a umístění:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* Složky C:\Program Files\Microsoft Azure Recovery Services Agent\
* Dočasné umístění (Pokud nechcete použít standardní umístění)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Agent zálohování spuštěný na virtuálním počítači Azure
Pokud používáte agenta zálohování na virtuálním počítači, sníží se výkon než při spuštění na fyzickém počítači. Očekává se z důvodu omezení vstupně-výstupních operací.  Můžete však optimalizovat výkon díky přepínání datových jednotek, které jsou zálohovány do služby Azure Premium Storage. Pracujeme na opravě tohoto problému a oprava bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: Zálohování velké množství souborů (miliony)
Přesouvání velkého objemu dat bude trvat déle než přesouvání menších objemech dat. V některých případech se čas zálohování se týká nejen velikost dat, ale i počet souborů nebo složek. To platí zejména při miliony malých souborů (několik bajtů do několika kilobajtů) jsou zálohované.

K tomuto chování dochází, protože při zálohování dat a jeho přesun do Azure, Azure je současně do katalogu souborů. V některých výjimečných případech katalogu operace může trvat déle než obvykle.

Následující ukazatele vám může pomoct pochopit, kritický bod a odpovídajícím způsobem pracovat na další kroky:

* **Uživatelské rozhraní se zobrazuje průběh za přenos dat**. Data se stále se přenesou. Šířka pásma sítě nebo velikosti dat může způsobovat zpoždění.
* **Uživatelské rozhraní se za přenos dat nezobrazuje průběh**. Otevřete umístění C:\Program Files\Microsoft Azure Recovery Services Agent\Temp protokoly a zkontrolujte položku FileProvider::EndData v protokolech. Tato položka označuje, že dokončení přenosu dat a operace katalogu se děje. Nepřerušujte úlohy zálohování. Místo toho počkejte trochu déle na dokončení operace katalogu. Pokud se problém nevyřeší, obraťte se na [podpory Azure](https://portal.azure.com/#create/Microsoft.Support).
