---
title: Řešení potíží s pomalým zálohováním souborů a složek
description: Poskytuje pokyny k odstraňování potíží, které vám pomůžou diagnostikovat příčinu potíží s výkonem Azure Backup.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518520"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup

Tento článek poskytuje pokyny k odstraňování potíží, které vám pomůžou diagnostikovat příčinu pomalého výkonu zálohování souborů a složek při použití Azure Backup. Když použijete agenta Azure Backup k zálohování souborů, může proces zálohování trvat déle, než se čekalo. Tato prodleva může být způsobena jedním nebo více následujícími akcemi:

* [U počítače, který se zálohuje, dochází k kritickým bodům výkonu.](#cause1)
* [Jiný proces nebo antivirový software brání procesu Azure Backup.](#cause2)
* [Agent zálohování je spuštěný na virtuálním počítači Azure (VM).](#cause3)  
* [Zálohujete velký počet (milionů) souborů.](#cause4)

Než začnete řešit problémy, doporučujeme si stáhnout a nainstalovat [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent). Častým aktualizacím agenta zálohování je řešení různých problémů, přidávání funkcí a zlepšení výkonu.

Důrazně doporučujeme, abyste si přesvědčili, že [služba Azure Backup Service na nejčastější dotazy](backup-azure-backup-faq.yml) , abyste se ujistili, že nedošlo k žádným běžným problémům s konfigurací.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Příčina: úloha zálohování běží v neoptimalizovaném režimu

* Agent MARS může spustit úlohu zálohování v **optimalizovaném režimu** pomocí deníku změn (číslo sekvence aktualizace) nebo **neoptimalizovaného režimu** tak, že zkontroluje celý svazek pomocí hledání změn v adresářích nebo souborech.
* Neoptimalizovaný režim je pomalý, protože agent musí naskenovat každý soubor na svazku a porovnat s metadaty a určit změněné soubory.
* Pokud to chcete ověřit, otevřete **Podrobnosti úlohy** z konzoly agenta Mars a zkontrolujte stav, abyste viděli, jestli říká **přenos dat (neoptimalizované, může trvat delší dobu)** , jak je znázorněno níže:

    ![Spuštění v neoptimalizovaném režimu](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Následující podmínky můžou způsobit, že se úloha zálohování spustí v neoptimalizovaném režimu:
  * První zálohování (označované taky jako počáteční replikace) se vždycky spustí v neoptimalizovaném režimu.
  * Pokud předchozí úloha zálohování selže, spustí se další naplánovaná úloha zálohování jako neoptimalizovaná.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: kritické body výkonu počítače

Kritická místa v zálohovaném počítači můžou způsobit zpoždění. Například možnost počítače ke čtení nebo zápisu na disk nebo k dostupné šířce pásma pro posílání dat přes síť může způsobit problémová místa.

Systém Windows poskytuje integrovaný nástroj s názvem [sledování výkonu](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (PerfMon) k detekci těchto kritických bodů.

Tady jsou některé čítače a rozsahy výkonu, které mohou být užitečné při diagnostice kritických bodů pro optimální zálohování.

| Čítač | Status |
| --- | --- |
| Logický disk (fyzický disk) –% nečinný |<li> 100% nečinných až 50% nečinných = v pořádku</br><li> 49% nečinných až 20% nečinných = Warning nebo monitor</br><li> 19% nečinných po 0% nečinných = kritická nebo mimo specifikaci |
| Logický disk (fyzický disk) –% střední doba disku pro čtení nebo zápis |<li> 0,001 MS až 0,015 MS = v pořádku</br><li> 0,015 ms až 0,025 MS = Warning nebo monitor</br><li> 0,026 MS nebo delší = kritická nebo mimo specifikaci |
| Logický disk (fyzický disk) – aktuální délka fronty disku (pro všechny instance) |80 požadavků po dobu více než 6 minut |
| Paměť – fond bez stránkovaného fondu |<li> Méně než 60% spotřebovaného fondu = v pořádku<br><li> 61% až 80% spotřebovaného fondu = upozornění nebo monitorování</br><li> Je větší než 80% spotřebovaného fondu = kritické nebo mimo specifikaci. |
| Paměť – bajty stránkovaného fondu |<li> Méně než 60% spotřebovaného fondu = v pořádku</br><li> 61% až 80% spotřebovaného fondu = upozornění nebo monitorování</br><li> Je větší než 80% spotřebovaného fondu = kritické nebo mimo specifikaci. |
| Paměť – dostupné megabajty |<li> 50% volné paměti je k dispozici nebo je více = v pořádku</br><li> k dispozici je 25% volné paměti = monitor</br><li>10% dostupné volné paměti = upozornění</br><li> Je k dispozici méně než 100 MB nebo 5% volné paměti = kritické nebo mimo specifikaci. |
| Procesor – \% čas procesoru (všechny instance) |<li> Méně než 60% spotřebované = v pořádku</br><li> 61% až 90% spotřebované = monitor nebo upozornění</br><li> 91% až 100% spotřebované = kritické |

> [!NOTE]
> Pokud zjistíte, že infrastruktura je příčinou, doporučujeme pravidelně defragmentovat disky pro lepší výkon.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Příčina: jiný proces nebo antivirový software narušuje Azure Backup

Zjistili jsme několik instancí, kdy jiné procesy v systému Windows mají negativně vliv na výkon procesu Azure Backup agenta. Pokud například použijete agenta Azure Backup i jiný program k zálohování dat nebo pokud je antivirový software spuštěný a má zámek na zálohovaných souborech, může více zámků souborů způsobit kolizi. V takovém případě může zálohování selhat nebo může trvat déle, než se čekalo.

Nejlepším doporučením v tomto scénáři je vypnout jiný zálohovací program a zjistit, zda se změní čas zálohování agenta Azure Backup. Obvykle je potřeba zajistit, aby více úloh zálohování neběžely současně, aby nedošlo k jejich vzájemnému ovlivňování.

Pro antivirové programy doporučujeme, abyste vyloučili následující soubory a umístění:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services agent \ Folders
* Pomocné umístění (Pokud nepoužíváte standardní umístění)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Agent zálohování běžící na virtuálním počítači Azure

Pokud používáte agenta Zálohování na VIRTUÁLNÍm počítači, bude výkon pomalejší než při jeho spuštění na fyzickém počítači. Očekává se kvůli omezením IOPS.  Výkon ale můžete optimalizovat tak, že přepnete datové jednotky, které se zálohují do Azure Premium Storage. Pracujeme na vyřešení tohoto problému a oprava bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: zálohování velkého počtu souborů (miliony)

Přesun velkého objemu dat bude trvat déle, než přesunete menší objem dat. V některých případech se čas zálohování vztahuje nejen na velikost dat, ale také na počtu souborů nebo složek. To platí hlavně v případě, že jsou zálohovány miliony malých souborů (několik bajtů až po několik kilobajtů).

K tomuto chování dochází, protože když zálohujete data a přesouváte je do Azure, Azure současně ukládá do katalogu soubory. V některých výjimečných scénářích může Katalogová operace trvat déle, než se čekalo.

Následující indikátory vám pomohou pochopit kritické body a odpovídajícím způsobem pracovat s dalšími kroky:

* **Uživatelské rozhraní zobrazuje průběh přenosu dat**. Data se pořád přenáší. Šířka pásma sítě nebo velikost dat můžou způsobovat zpoždění.
* **Uživatelské rozhraní nezobrazuje průběh přenosu dat**. Otevřete protokoly nacházející se v umístění C:\Program Files\Microsoft Azure Recovery Services Agent\Temp a potom zkontrolujte, jestli v protokolech není záznam. Provider:: EndData. Tato položka znamená, že se dokončil přenos dat a operace katalogu. Neruší úlohy zálohování. Místo toho počkejte trochu déle, než se operace katalogu dokončí. Pokud se problém opakuje, obraťte se na [podporu Azure](https://portal.azure.com/#create/Microsoft.Support).

Při pokusu o zálohování velkých disků se doporučuje použít [Azure Data box](./offline-backup-azure-data-box.md) pro první zálohování (počáteční replikace).  Pokud nemůžete použít Data Box, pak jakékoli přechodné problémy se sítí, které ve vašem prostředí probíhají během dlouhých přenosů dat přes síť, můžou způsobit selhání zálohování.  K ochraně proti těmto chybám můžete do prvotního zálohování přidat několik složek a průběžně přidávat další složky, dokud se všechny složky úspěšně nezálohují do Azure.  Následné přírůstkové zálohování bude poměrně rychlejší.

## <a name="next-steps"></a>Další kroky

* [Běžné dotazy týkající se zálohování souborů a složek](backup-azure-file-folder-backup-faq.yml)
