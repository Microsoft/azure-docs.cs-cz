---
title: Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
description: Poskytuje pokyny k odstraňování problémů, které vám pomohou diagnostikovat příčiny problémů s výkonem Azure Backup
services: backup
author: genlin
manager: cshepard
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f24a60ab9bdcf1231085de4edeeb89ce1edf4e80
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248465"
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

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: Kritické body výkonu v počítači
Kritická místa v počítači, který je právě zálohován může to způsobit prodlevy. Počítači umožňuje čtení nebo zápisu na disk nebo dostupnou šířku pásma pro odesílání dat přes síť, například může způsobit kritické body.

Windows poskytuje integrované nástroje, která je volána [sledování výkonu](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) ke zjištění těchto kritické body.

Tady jsou některé čítače výkonu a rozsahy, které může být užitečné při diagnostice problémová místa pro optimální zálohování.

| Čítač | Status |
| --- | --- |
| Logický Disk (fyzického disku)--% nečinnosti |• 100 % nečinnosti na 50 % nečinný = v pořádku</br>• 49 % nečinnosti 20 % nečinnosti = upozornění nebo monitorování</br>• 19 % nečinnosti na 0 % nečinný = důležité aktualizace nebo z specifikace |
| Logický Disk (fyzického disku)--% střední Disku čtení nebo zápisu |• 0,001 ms pro 0,015 ms = v pořádku</br>• 0,015 ms pro 0,025 ms = upozornění nebo monitorování</br>• 0.026 ms nebo už = důležité aktualizace nebo z specifikace |
| Logický Disk (fyzického disku) – aktuální délka fronty disku (pro všechny instance) |80 požadavků po dobu více než 6 minut |
| Paměť – bajty stránkovaného fondu bez |• Méně než 60 % fondu spotřebované = v pořádku<br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = důležité aktualizace nebo z specifikace |
| Paměť – bajty stránkovaného fondu |• Méně než 60 % fondu spotřebované = v pořádku</br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = důležité aktualizace nebo z specifikace |
| Paměť – počtu dostupných megabajtů |• 50 % podíl volné paměti, které jsou k dispozici nebo více = v pořádku</br>• 25 % podíl volné paměti, které jsou k dispozici = monitorování</br>• 10 % podíl volné paměti, které jsou k dispozici = upozornění</br>• Méně než 100 MB nebo 5 % volné paměti k dispozici = důležité aktualizace nebo z specifikace |
| Procesor –\%času procesoru (všechny instance) |• Méně než 60 % využité = v pořádku</br>• 61 % až 90 % využité = monitorování nebo upozornění</br>• 91 až 100 % využité = kritický |

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

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Zálohování agenta spuštěného na virtuálním počítači Azure
Pokud používáte agenta zálohování na virtuálním počítači, sníží se výkon než při spuštění na fyzickém počítači. Očekává se z důvodu omezení vstupně-výstupních operací.  Můžete však optimalizovat výkon díky přepínání datových jednotek, které jsou zálohovány do služby Azure Premium Storage. Pracujeme na opravě tohoto problému a oprava bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: Zálohování velké množství souborů (miliony)
Přesouvání velkého objemu dat bude trvat déle než přesouvání menších objemech dat. V některých případech se čas zálohování se týká nejen velikost dat, ale i počet souborů nebo složek. To platí zejména při miliony malých souborů (několik bajtů do několika kilobajtů) jsou zálohované.

K tomuto chování dochází, protože při zálohování dat a jeho přesun do Azure, Azure je současně do katalogu souborů. V některých výjimečných případech katalogu operace může trvat déle než obvykle.

Následující ukazatele vám může pomoct pochopit, kritický bod a odpovídajícím způsobem pracovat na další kroky:

* **Uživatelské rozhraní se zobrazuje průběh za přenos dat**. Data se stále se přenesou. Šířka pásma sítě nebo velikosti dat může způsobovat zpoždění.
* **Uživatelské rozhraní se za přenos dat nezobrazuje průběh**. Otevřete umístění C:\Program Files\Microsoft Azure Recovery Services Agent\Temp protokoly a zkontrolujte položku FileProvider::EndData v protokolech. Tato položka označuje, že dokončení přenosu dat a operace katalogu se děje. Nepřerušujte úlohy zálohování. Místo toho počkejte trochu déle na dokončení operace katalogu. Pokud se problém nevyřeší, obraťte se na [podpory Azure](https://portal.azure.com/#create/Microsoft.Support).
