---
title: Průvodce řešením potíží s výkonem služby soubory Azure
description: Známé problémy s výkonem se sdílenými složkami Azure a souvisejícími alternativními řešeními.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6b28d004ceacda3cec13e96ceae84d5d1ff1a2e5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699176"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Řešení potíží s výkonem souborů Azure

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami Azure. Poskytuje možné příčiny a alternativní řešení, když dojde k těmto potížím.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, nízká propustnost a obecné problémy s výkonem

### <a name="cause-1-share-experiencing-throttling"></a>Příčina 1: Sdílení, u kterého dochází k omezování

Výchozí kvótou pro sdílenou složku Premium je 100 GiB, která poskytuje 100 standardních IOPS (s potenciálem pro nárůst až 300 po hodinu). Další informace o zřizování a jeho vztahu k IOPS najdete v části [zřízené sdílené složky](storage-files-planning.md#provisioned-shares) v příručce pro plánování.

Pokud chcete ověřit, jestli se vaše sdílená složka omezuje, můžete využít metriky Azure na portálu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** a pak vyhledejte **metriky**.

1. Vyberte **Metriky**.

1. Jako prostředek vyberte svůj účet úložiště.

1. Jako obor názvů metriky vyberte **soubor** .

1. Jako metriku vyberte **transakce** .

1. Přidejte filtr pro **ResponseType** a zkontrolujte, zda nějaké požadavky obsahují kód odpovědi **SuccessWithThrottling** (pro protokol SMB) nebo **ClientThrottlingError** (pro REST).

![Možnosti metrik pro sdílené složky Premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Řešení

- Zvyšte kapacitu zřízenou sdílenou složkou tím, že zadáte vyšší kvótu pro sdílenou složku.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Příčina 2: Silná úloha metadata/obor názvů

Pokud jsou většina vašich požadavků na střed (například CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory), bude latence ve srovnání s operacemi čtení/zápisu horší.

Pokud chcete potvrdit, jestli je většina vašich požadavků na střed, můžete použít stejné kroky jako v předchozím kroku. S výjimkou přidání filtru pro **ResponseType**přidejte filtr pro **název rozhraní API**.

![Filtrování názvu rozhraní API v metrikách](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Alternativní řešení

- Ověřte, zda lze aplikaci upravovat a snížit tak počet operací s metadaty.

### <a name="cause-3-single-threaded-application"></a>Příčina 3: Aplikace s jedním vláknem

Pokud je aplikace, kterou zákazník používá, jediným vláknem, může to vést k výraznému snížení počtu IOPS/propustnosti, než je maximální možná velikost na základě zřízené velikosti vaší sdílené složky.

### <a name="solution"></a>Řešení

- Zvýšení paralelismu aplikace zvýšením počtu vláken.
- Přepněte na aplikace, kde je možné paralelismus. Například pro operace kopírování mohou zákazníci používat AzCopy nebo Robocopy z klientů systému Windows nebo **paralelní** příkaz v klientech se systémem Linux.

## <a name="very-high-latency-for-requests"></a>Velmi vysoká latence pro žádosti

### <a name="cause"></a>Příčina

Klientský virtuální počítač se může nacházet v jiné oblasti než sdílená složka.

### <a name="solution"></a>Řešení

- Spusťte aplikaci z virtuálního počítače, který je umístěný ve stejné oblasti jako sdílená složka.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klientovi se nepovedlo dosáhnout maximální propustnosti, kterou síť podporuje.

Jednou z možných příčin tohoto je nedostatečná podpora více kanálů SMB. Sdílené složky Azure v současné době podporují jenom jeden kanál, takže existuje jenom jedno připojení z virtuálního počítače klienta k serveru. Toto jediné připojení je v klientském počítači klienta připojeno k jednomu jádru, takže maximální propustnost, kterou je možné z virtuálního počítače dosáhnout, je svázána s jedním jádrem.

### <a name="workaround"></a>Alternativní řešení

- Získání virtuálního počítače s větším jádrem vám může pomoci zlepšit propustnost.
- Při spuštění klientské aplikace z více virtuálních počítačů se zvýší propustnost.
- Pokud je to možné, použijte rozhraní REST API.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Při porovnání s klienty se systémem Windows výrazně snižuje propustnost klientů se systémem Linux.

### <a name="cause"></a>Příčina

Jedná se o známý problém s implementací klienta SMB v systému Linux.

### <a name="workaround"></a>Alternativní řešení

- Rozprostře zatížení mezi více virtuálních počítačů.
- Ve stejném virtuálním počítači použijte více přípojných bodů s možností **nosharesock** a rozprostřete zatížení mezi tyto přípojné body.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Vysoká latence pro silná zatížení metadat, která zahrnují rozsáhlé operace otevření/zavření.

### <a name="cause"></a>Příčina

Nedostatečná podpora pro zapůjčení adresáře.

### <a name="workaround"></a>Alternativní řešení

- Pokud je to možné, vyhněte se nadměrnému otevírání a zavírání popisovačům ve stejném adresáři v krátké době.
- U virtuálních počítačů se systémem Linux zvyšte časový limit mezipaměti položky adresáře zadáním **actimeo =\<sec >** jako možnosti připojení. Ve výchozím nastavení je to jedna sekunda, takže může pomáhat větší hodnota, například tři nebo pět.
- Pro virtuální počítače se systémem Linux upgradujte jádro na 4,20 nebo vyšší.

## <a name="low-iops-on-centosrhel"></a>Nízká IOPS v CentOS/RHEL

### <a name="cause"></a>Příčina

V CentOS/RHEL není podporována Hloubka v/v s větší než jednou.

### <a name="workaround"></a>Alternativní řešení

- Upgradujte na CentOS 8/RHEL 8.
- Přejděte na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Pomalé kopírování souborů do a ze souborů Azure v systému Linux

Pokud máte pomalé kopírování souborů do a ze souborů Azure, podívejte se do části [pomalé kopírování souborů do a ze souborů Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) v Průvodci odstraňováním potíží se systémem Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Model kolísání/pily-tooth pro IOPS

### <a name="cause"></a>Příčina

Klientská aplikace konzistentně překračuje základní IOPS. V současné době není možné vyhladit zatížení žádosti na straně služby, takže pokud klient překročí směrného plánu IOPS, bude služba omezena. Toto omezování může mít za následek, že u klienta dochází ke kolísání nebo navýšení IOPS – tooth. V tomto případě může být průměrná hodnota IOPS dosažená klientem nižší než hodnota IOPS.

### <a name="workaround"></a>Alternativní řešení

- Snižte zatížení žádosti z klientské aplikace tak, aby se sdílená složka neomezila.
- Zvyšte kvótu sdílené složky tak, aby sdílená složka nezískala omezení.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadměrné DirectoryOpen/DirectoryClose volání

### <a name="cause"></a>Příčina

Pokud je počet volání DirectoryOpen/DirectoryClose mezi horními voláními rozhraní API a neočekáváte, že klient bude provádět mnoho volání, může se jednat o problém s antivirovým programem nainstalovaným na virtuálním počítači klienta Azure.

### <a name="workaround"></a>Alternativní řešení

- Oprava tohoto problému je k dispozici v [aktualizaci platformy duben pro Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Vytvoření souboru je pomalejší, než se očekávalo.

### <a name="cause"></a>Příčina

Úlohy, které spoléhají na vytvoření velkého počtu souborů, nebudou mít podstatný rozdíl mezi výkonem a standardními sdílenými složkami souborů Premium.

### <a name="workaround"></a>Alternativní řešení

- Žádné.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Pomalý výkon od Windows 8.1 nebo serveru 2012 R2

### <a name="cause"></a>Příčina

Vyšší než očekávaná latence při přístupu k souborům Azure pro úlohy náročné na vstupně-výstupní operace.

### <a name="workaround"></a>Alternativní řešení

- Nainstalujte dostupnou [opravu hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).
