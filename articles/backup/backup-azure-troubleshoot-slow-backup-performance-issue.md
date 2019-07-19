---
title: Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
description: Poskytuje pokyny k odstraňování potíží, které vám pomůžou diagnostikovat příčinu potíží s výkonem Azure Backup.
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "67704941"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
Tento článek poskytuje pokyny k odstraňování potíží, které vám pomůžou diagnostikovat příčinu pomalého výkonu zálohování souborů a složek při použití Azure Backup. Když použijete agenta Azure Backup k zálohování souborů, může proces zálohování trvat déle, než se čekalo. Tato prodleva může být způsobena jedním nebo více následujícími akcemi:

* [U počítače, který se zálohuje, dochází k kritickým bodům výkonu.](#cause1)
* [Jiný proces nebo antivirový software brání procesu Azure Backup.](#cause2)
* [Agent zálohování je spuštěný na virtuálním počítači Azure (VM).](#cause3)  
* [Zálohujete velký počet (milionů) souborů.](#cause4)

Než začnete řešit problémy, doporučujeme si stáhnout a nainstalovat [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent). Častým aktualizacím agenta zálohování je řešení různých problémů, přidávání funkcí a zlepšení výkonu.

Důrazně doporučujeme, abyste si přesvědčili, že [služba Azure Backup Service na nejčastější dotazy](backup-azure-backup-faq.md) , abyste se ujistili, že nedošlo k žádným běžným problémům s konfigurací.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: Kritická místa výkonu v počítači
Kritická místa v zálohovaném počítači můžou způsobit zpoždění. Například možnost počítače ke čtení nebo zápisu na disk nebo k dostupné šířce pásma pro posílání dat přes síť může způsobit problémová místa.

Systém Windows poskytuje integrovaný nástroj s názvem [sledování výkonu](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (PerfMon) k detekci těchto kritických bodů.

Tady jsou některé čítače a rozsahy výkonu, které mohou být užitečné při diagnostice kritických bodů pro optimální zálohování.

| Čítač | Stav |
| --- | --- |
| Logický disk (fyzický disk) –% nečinný |• 100% nečinných až 50% nečinných = v pořádku</br>• 49% nečinných až 20% nečinných = Warning nebo monitor</br>• 19% nečinných po 0% nečinnosti = kritická nebo mimo specifikaci |
| Logický disk (fyzický disk)--% prům. Čtení nebo zápis disku za sekundu |• 0,001 MS až 0,015 MS = v pořádku</br>• 0,015 ms až 0,025 MS = Warning nebo monitor</br>• 0,026 MS nebo delší = kritická nebo mimo specifikaci |
| Logický disk (fyzický disk) – aktuální délka fronty disku (pro všechny instance) |80 požadavků po dobu více než 6 minut |
| Paměť – fond bez stránkovaného fondu |• Méně než 60% spotřebovaného fondu = v pořádku<br>• 61% až 80% spotřebovaného fondu = upozornění nebo monitorování</br>• Více než 80% spotřebovaného fondu = kritické nebo mimo specifikaci |
| Paměť – bajty stránkovaného fondu |• Méně než 60% spotřebovaného fondu = v pořádku</br>• 61% až 80% spotřebovaného fondu = upozornění nebo monitorování</br>• Více než 80% spotřebovaného fondu = kritické nebo mimo specifikaci |
| Paměť – dostupné megabajty |• 50% volné paměti k dispozici nebo je více = v pořádku</br>• k dispozici je 25% volné paměti = monitor</br>• 10% dostupné volné paměti = upozornění</br>• Je k dispozici méně než 100 MB nebo 5% volné paměti = kritické nebo mimo specifikaci. |
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
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Procesor –\`čas procesoru (všechny instance)es and folders in Azure Backup
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
| Processor--\%Processor Time (all instances) |• Méně než 60% spotřebované = dobrý stav</br>• 61% až 90% spotřebované = monitor nebo upozornění</br>• 91% až 100% spotřebované = kritické |

> [!NOTE]
> Pokud zjistíte, že infrastruktura je příčinou, doporučujeme pravidelně defragmentovat disky pro lepší výkon.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Příčina: Jiný proces nebo antivirový software narušující Azure Backup
Zjistili jsme několik instancí, kdy jiné procesy v systému Windows mají negativně vliv na výkon procesu Azure Backup agenta. Pokud například použijete agenta Azure Backup i jiný program k zálohování dat nebo pokud je antivirový software spuštěný a má zámek na zálohovaných souborech, může více zámků souborů způsobit kolizi. V takovém případě může zálohování selhat nebo může trvat déle, než se čekalo.

Nejlepším doporučením v tomto scénáři je vypnout jiný zálohovací program a zjistit, zda se změní čas zálohování agenta Azure Backup. Obvykle je potřeba zajistit, aby více úloh zálohování neběžely současně, aby nedošlo k jejich vzájemnému ovlivňování.

Pro antivirové programy doporučujeme, abyste vyloučili následující soubory a umístění:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services agent \ Folders
* Pomocné umístění (Pokud nepoužíváte standardní umístění)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Agent zálohování spuštěný na virtuálním počítači Azure
Pokud používáte agenta Zálohování na VIRTUÁLNÍm počítači, bude výkon pomalejší než při jeho spuštění na fyzickém počítači. Očekává se kvůli omezením IOPS.  Výkon ale můžete optimalizovat tak, že přepnete datové jednotky, které se zálohují do Azure Premium Storage. Pracujeme na vyřešení tohoto problému a oprava bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: Zálohování velkého počtu souborů (miliony)
Přesun velkého objemu dat bude trvat déle, než přesunete menší objem dat. V některých případech se čas zálohování vztahuje nejen na velikost dat, ale také na počtu souborů nebo složek. To platí hlavně v případě, že jsou zálohovány miliony malých souborů (několik bajtů až po několik kilobajtů).

K tomuto chování dochází, protože když zálohujete data a přesouváte je do Azure, Azure současně ukládá do katalogu soubory. V některých výjimečných scénářích může Katalogová operace trvat déle, než se čekalo.

Následující indikátory vám pomohou pochopit kritické body a odpovídajícím způsobem pracovat s dalšími kroky:

* **Uživatelské rozhraní zobrazuje průběh přenosu dat**. Data se pořád přenáší. Šířka pásma sítě nebo velikost dat můžou způsobovat zpoždění.
* **Uživatelské rozhraní nezobrazuje průběh přenosu dat**. Otevřete protokoly nacházející se v umístění C:\Program Files\Microsoft Azure Recovery Services Agent\Temp a potom zkontrolujte, jestli v protokolech není záznam. Provider:: EndData. Tato položka znamená, že se dokončil přenos dat a operace katalogu. Neruší úlohy zálohování. Místo toho počkejte trochu déle, než se operace katalogu dokončí. Pokud se problém opakuje, obraťte se na [podporu Azure](https://portal.azure.com/#create/Microsoft.Support).
