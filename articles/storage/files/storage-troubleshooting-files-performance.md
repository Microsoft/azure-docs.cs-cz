---
title: Průvodce odstraňováním potíží Azure souborů výkonu
description: Známé problémy s výkonem se sdílenými složkami Azure úrovně premium (preview) a přidružené alternativní řešení.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190056"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Řešení problémů s výkonem Azure Files

Tento článek uvádí některé běžné problémy související s premium sdílených složek Azure (preview). Pokud nedojde k těmto problémům poskytuje možné příčiny a řešení.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, Nízká propustnost a obecné informace o výkonu problémy

### <a name="cause-1-share-experiencing-throttling"></a>1\. příčina: K omezení sdílení

Výchozí kvóta ve sdílené složce je 100 GB, která poskytuje 100 standardní hodnoty vstupně-výstupních operací (s potenciálně burst 300 až hodinu). Další informace o zřizování a jeho vztah k vstupně-výstupních operací, najdete v článku [zřízené sdílené složky](storage-files-planning.md#provisioned-shares) části příručky plánování.

Pokud chcete potvrdit, pokud je omezovaná svou sdílenou složku, můžete využít metriky Azure na portálu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** a vyhledejte **metriky**.

1. Vyberte **Metriky**.

1. Vyberte svůj účet úložiště jako prostředek.

1. Vyberte **souboru** jako metriky oboru názvů.

1. Vyberte **transakce** jako metriku.

1. Přidat filtr pro **hodnotu ResponseType** a zkontrolujte, zda mají všechny žádosti kód odpovědi **SuccessWithThrottling**.

![Možnosti metriky pro premium fileshares](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Řešení

- Zvýšení sdílet zřízená kapacita vytvořením vyšší kvótu pro svou sdílenou složku.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>2\. příčina: Úlohy heavy metadat nebo obor názvů

Pokud se většina žádostí o metadata na střed (například createfile/openfile/closefile/informací dotazu/querydirectory) latence bude horší ve srovnání s operací čtení a zápis.

Pokud chcete potvrdit, pokud se většina žádostí o metadata zaměřenou na, můžete použít stejný postup jako výše. S výjimkou nepřidávat filtr pro **hodnotu ResponseType**, přidejte filtr pro **název rozhraní API**.

![Filtr pro název rozhraní API v metriky](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Alternativní řešení:

- Zaškrtněte, pokud aplikace můžete upravit tak, aby snížil počet operací s metadaty.

### <a name="cause-3-single-threaded-application"></a>3\. důvod: Aplikace s jedním vláknem

Pokud aplikace používá zákazník s jedním vláknem, výsledkem může výrazně nižší IOPS a propustnosti překračuje maximální povolenou na základě velikosti zřízené sdílené složky.

### <a name="solution"></a>Řešení

- Zvýšení počtu vláken zvýšit aplikace paralelismu.
- Přepněte do aplikace, kde je možné paralelismu. Například pro operace kopírování zákazníkům může použít AzCopy nebo RoboCopy klientů Windows nebo **paralelní** příkaz na klientech systému Linux.

## <a name="very-high-latency-for-requests"></a>Velmi vysokou latencí pro požadavky

### <a name="cause"></a>Příčina

Klientského virtuálního počítače může nacházet v jiné oblasti než premium sdílené složky.

### <a name="solution"></a>Řešení

- Spusťte aplikaci z virtuálního počítače, který je umístěný ve stejné oblasti jako sdílené složky premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klientovi se nepodařilo dosáhnout maximální propustnost, které jsou podporovány v síti

Jedna z možných příčin této je nedostatek fo SMB podpora vícekanálové. Aktuálně sdílené složky Azure podporují pouze jeden kanál, tedy pouze jedno připojení z klientského virtuálního počítače k serveru. Toto jediné připojení je propojen s jedním jádrem na straně klienta virtuálního počítače, tak maximální propustnost dosažitelný z virtuálního počítače je svázaná s jedním jádrem.

### <a name="workaround"></a>Alternativní řešení:

- Získání virtuálního počítače s větší core může zvýšit propustnost.
- Spuštění klientské aplikace z několika virtuálních počítačů se zvýší propustnost.
- Použití rozhraní REST API, kde je to možné.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Propustnost na klienty Linux je významně nižší ve srovnání s klienty Windows.

### <a name="cause"></a>Příčina

Jde o známý problém s implementací klienta protokolu SMB v systému Linux.

### <a name="workaround"></a>Alternativní řešení:

- Rozložit zatížení mezi několik virtuálních počítačů
- Na jednom virtuálním počítači, použijte více přípojných bodů s **nosharesock** možnost a rozložení zátěže mezi nimi přípojné body.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Vysokou latencí pro náročné úlohy metadat zahrnující rozsáhlé operace otevřít nebo zavřít.

### <a name="cause"></a>Příčina

Chybějící podpora jazyků adresářů.

### <a name="workaround"></a>Alternativní řešení:

- Pokud je to možné Vyhněte se nadměrné levé nebo pravé popisovač ve stejném adresáři v krátké době.
- U virtuálních počítačů s Linuxem zvýšit časový limit mezipaměti položka adresáře tak, že zadáte **actimeo =<sec>**  jako možnost připojení. Ve výchozím nastavení je jedna sekunda, může pomoct větší hodnotu jako tři nebo pět.
- Pro virtuální počítače s Linuxem upgradujte jádra 4.20 nebo vyšší.

## <a name="low-iops-on-centosrhel"></a>Nízký počet IOPS na CentOS/RHEL

### <a name="cause"></a>Příčina

Hloubka vstupně-výstupní operace větší než jedna nepodporuje CentOS/RHEL.

### <a name="workaround"></a>Alternativní řešení:

- Upgrade na CentOS 8 / RHEL 8.
- Změnit až po Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Vzor nestabilní/saw-tooth pro vstupně-výstupních operací

### <a name="cause"></a>Příčina

Klientská aplikace trvale překračuje základní vstupně-výstupních operací. V současné době neexistuje žádný na straně služby vyhlazování zatížení, takže pokud klient překročí standardní hodnoty vstupně-výstupních operací, dojde k jeho omezení službou. V klientovi dochází k vstupně-výstupních operací vzor nestabilní/saw-tooth omezování může způsobit, že. V tomto případě průměrné vstupně-výstupních operací dosahuje klienta může být nižší než standardní hodnoty vstupně-výstupních operací.

### <a name="workaround"></a>Alternativní řešení:

- Snížit zátěž tvořenou klientská aplikace tak, aby sdílená složka získat není omezený.
- Navyšte kvótu sdílené složky tak, aby sdílená složka získat není omezený.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadbytečná volání DirectoryOpen/DirectoryClose

### <a name="cause"></a>Příčina

Pokud je počet volání DirectoryOpen/DirectoryClose mezi horní volání rozhraní API a nečekáte klientovi provádět, že mnoho volání, může být problém s antivirové ochrany nainstalovaný na Azure klientského virtuálního počítače.

### <a name="workaround"></a>Alternativní řešení:

- Oprava tohoto problému je k dispozici v [dubna Platform Update pro Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Vytvoření souboru je pomalejší, než se čekalo

### <a name="cause"></a>Příčina

Úlohy, které spoléhají na vytváření velký počet souborů nezobrazí podstatný rozdíl mezi výkonem sdílené složky premium a standard sdílené složky.

### <a name="workaround"></a>Alternativní řešení:

- Žádné

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Pomalý výkon z Windows 8.1 nebo Server 2012 R2

### <a name="cause"></a>Příčina

Vyšší než očekávané latence přístupu k Azure Files pro úlohy náročné na vstupně-výstupních operací.

### <a name="workaround"></a>Alternativní řešení:

- Instalace dostupných [opravu hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).