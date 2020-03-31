---
title: Poradce při potížích s pomalým zálohováním souborů a složek
description: Obsahuje pokyny pro řešení potíží, které vám pomohou diagnostikovat příčinu problémů s výkonem služby Azure Backup.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408687"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup

Tento článek obsahuje pokyny pro řešení potíží, které vám pomohou diagnostikovat příčinu pomalého výkonu zálohování souborů a složek při používání služby Azure Backup. Při použití agenta Azure Backup k zálohování souborů, proces zálohování může trvat déle, než bylo očekáváno. Toto zpoždění může být způsobeno jedním nebo více z následujících:

* [V počítači, který je zálohován, jsou problémová místa výkonu.](#cause1)
* [Jiný proces nebo antivirový software narušuje proces zálohování Azure.](#cause2)
* [Agent zálohování běží na virtuálním počítači Azure (VM).](#cause3)  
* [Zálohujete velké množství (miliony) souborů.](#cause4)

Než začnete řešit problémy, doporučujeme stáhnout a nainstalovat [nejnovějšího agenta Azure Backup](https://aka.ms/azurebackup_agent). Agenta zálohování často aktualizujeme, abychom opravili různé problémy, přidali funkce a zvýšili výkon.

Důrazně doporučujeme, abyste si prověřili [nejčastější dotazy ke službě Azure Backup](backup-azure-backup-faq.md) a ujistili se, že nedochází k žádným běžným problémům s konfigurací.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Příčina: Úloha zálohování spuštěná v neoptimalizovaném režimu

* Agent MARS může spustit úlohu zálohování v **optimalizovaném režimu** pomocí deníku změn USN (pořadové číslo aktualizace) nebo **neoptimalizovaného režimu** kontrolou změn v adresářích nebo souborech skenováním celého svazku.
* Neoptimalizovaný režim je pomalý, protože agent musí prohledat každý soubor na svazku a porovnat s metadaty k určení změněných souborů.
* Chcete-li to ověřit, **otevřete podrobnosti úlohy** z konzoly agenta MARS a zkontrolujte stav, abyste zjistili, zda je uvedeno **Přenos dat (neoptimalizovaný, může trvat déle),** jak je znázorněno níže:

    ![Běh v neoptimalizovaném režimu](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Následující podmínky mohou způsobit spuštění úlohy zálohování v neoptimalizovaném režimu:
  * První záloha (známá také jako počáteční replikace) bude vždy spuštěna v neoptimalizovaném režimu
  * Pokud předchozí úloha zálohování selže, bude další naplánovaná úloha zálohování spuštěna jako neoptimalizovaná.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: Problémová místa výkonu v počítači

Kritická místa v počítači, který je zálohována může způsobit zpoždění. Například schopnost počítače číst nebo zapisovat na disk nebo dostupná šířka pásma pro odesílání dat po síti může způsobit problémová místa.

Systém Windows poskytuje integrovaný nástroj s názvem [Sledování výkonu](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) pro detekci těchto kritických míst.

Zde jsou některé čítače výkonu a rozsahy, které mohou být užitečné při diagnostice kritických míst pro optimální zálohování.

| Čítač | Status |
| --- | --- |
| Logický disk (fyzický disk)--%nečinnosti |* 100% nečinnosti až 50% nečinnosti = zdravé</br>* 49% nečinnosti až 20% nečinnosti = Varování nebo Monitor</br>* 19% nečinnosti na 0% nečinnosti = kritické nebo mimo spec |
| Logický disk (fyzický disk)--%Avg. Disk Sec čtení nebo zápis |* 0,001 ms až 0,015 ms = zdravé</br>* 0,015 ms až 0,025 ms = Varování nebo Monitor</br>* 0,026 ms nebo delší = kritické nebo mimo spec |
| Logický disk (fyzický disk)--Aktuální délka fronty disku (pro všechny instance) |80 žádostí po dobu delší než 6 minut |
| Paměť – nestránkované bajty fondu |* Méně než 60% spotřebovaného bazénu = Zdravé<br>* 61% až 80% spotřebovaného fondu = Varování nebo Monitor</br>* Větší než 80% spotřebovaného fondu = kritické nebo mimo spec |
| Paměť – bajty stránkovaných bazénem |* Méně než 60% spotřebovaného bazénu = Zdravé</br>* 61% až 80% spotřebovaného fondu = Varování nebo Monitor</br>* Větší než 80% spotřebovaného fondu = kritické nebo mimo spec |
| Paměť – dostupné megabajty |* 50% volné paměti k dispozici nebo více = Zdravé</br>* 25% volné paměti k dispozici = Monitor</br>* 10% volné paměti k dispozici = Varování</br>* Méně než 100 MB nebo 5% volné paměti k dispozici = Kritické nebo Mimo Spec |
| Procesor\%- Čas procesoru (všechny instance) |* Méně než 60% spotřebované = Zdravé</br>* 61% až 90% spotřebované = Monitor nebo Pozor</br>* 91% až 100% spotřebované = Kritické |

> [!NOTE]
> Pokud zjistíte, že infrastruktura je viníkem, doporučujeme defragmentovat disky pravidelně pro lepší výkon.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Příčina: Jiný proces nebo antivirový software, který narušuje Azure Backup

Viděli jsme několik instancí, kdy ostatní procesy v systému Windows negativně ovlivnily výkon procesu agenta Azure Backup. Například pokud používáte agenta Azure Backup a jiný program pro zálohování dat, nebo pokud antivirový software je spuštěn a má zámek na soubory, které mají být zálohovány, může způsobit více uzamčení na soubory může způsobit konflikty. V takovém případě může selhat záloha nebo úloha může trvat déle, než bylo očekáváno.

Nejlepší doporučení v tomto scénáři je vypnout jiný zálohovací program a zjistit, zda se změní doba zálohování pro agenta Azure Backup. Obvykle se ujistěte, že více úloh zálohování nejsou spuštěny ve stejnou dobu je dostatečná, aby se zabránilo jejich vzájemné ovlivnění.

U antivirových programů doporučujeme vyloučit následující soubory a umístění:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* Složky C:\Program Files\Microsoft Azure Recovery Services Agent\
* Odškrtnout místo (pokud nepoužíváte standardní umístění)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Agent zálohování spuštěný na virtuálním počítači Azure

Pokud používáte agenta zálohování na virtuálním počítači, výkon bude pomalejší než při spuštění na fyzickém počítači. To se očekává z důvodu omezení viops.  Výkon však můžete optimalizovat přepnutím datových jednotek, které jsou zálohovány do úložiště Azure Premium. Pracujeme na vyřešení tohoto problému a oprava bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: Zálohování velkého počtu (milionů) souborů

Přesunutí velkého objemu dat bude trvat déle než přesunutí menšího objemu dat. V některých případech se doba zálohování vztahuje nejen na velikost dat, ale také na počet souborů nebo složek. To platí zejména v případě, že jsou zálohovány miliony malých souborů (několik bajtů na několik kilobajtů).

K tomuto chování dochází, protože při zálohování dat a jejich přesunutí do Azure Azure, Azure je současně katalogizuje vaše soubory. V některých výjimečných případech operace katalogu může trvat déle, než bylo očekáváno.

Následující ukazatele vám mohou pomoci pochopit problémovou hrdla a podle toho pracovat na dalších krocích:

* **UI ukazuje průběh přenosu dat**. Data jsou stále přenášena. Šířka pásma sítě nebo velikost dat mohou způsobovat zpoždění.
* **UI nezobrazuje průběh přenosu dat**. Otevřete protokoly umístěné na adrese C:\Program Files\Microsoft Azure Recovery Services Agent\Temp a pak zkontrolujte položku FileProvider::EndData v protokolech. Tato položka znamená, že přenos dat byl dokončen a operace katalogu probíhá. Neznevažujte úlohy zálohování. Místo toho počkejte trochu déle pro dokončení operace katalogu. Pokud problém přetrvává, obraťte se na [podporu Azure](https://portal.azure.com/#create/Microsoft.Support).

## <a name="next-steps"></a>Další kroky

* [Běžné otázky týkající se zálohování souborů a složek](backup-azure-file-folder-backup-faq.md)