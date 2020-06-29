---
title: Průvodce řešením potíží s výkonem služby soubory Azure
description: Známé problémy s výkonem se sdílenými složkami Azure a souvisejícími alternativními řešeními.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 64c7e56f14fb06e7b211954eb93e4858563a8f08
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85511949"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Řešení potíží s výkonem souborů Azure

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami Azure. Poskytuje možné příčiny a alternativní řešení, když dojde k těmto potížím.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, nízká propustnost a obecné problémy s výkonem

### <a name="cause-1-share-experiencing-throttling"></a>Příčina 1: sdílená omezení

Výchozí kvótou pro sdílenou složku Premium je 100 GiB, která poskytuje 100 standardních IOPS (s potenciálem pro nárůst až 300 po hodinu). Další informace o zřizování a jeho vztahu k IOPS najdete v části [zřízené sdílené složky](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) v příručce pro plánování.

Pokud chcete ověřit, jestli se vaše sdílená složka omezuje, můžete využít metriky Azure na portálu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** a pak vyhledejte **metriky**.

1. Vyberte **Metriky**.

1. Jako prostředek vyberte svůj účet úložiště.

1. Jako obor názvů metriky vyberte **soubor** .

1. Jako metriku vyberte **transakce** .

1. Přidejte filtr pro **ResponseType** a zkontrolujte, zda nějaké požadavky obsahují kód odpovědi **SuccessWithThrottling** (pro protokol SMB) nebo **ClientThrottlingError** (pro REST).

![Možnosti metrik pro sdílené složky Premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Pokud chcete dostávat upozornění, pokud je sdílená složka omezená, přečtěte si téma [Postup vytvoření výstrahy, pokud je sdílená složka omezená](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Řešení

- Zvyšte kapacitu zřízenou sdílenou složkou tím, že zadáte vyšší kvótu pro sdílenou složku.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Příčina 2: silná úloha metadat/obor názvů

Pokud jsou většina vašich požadavků na střed (například CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory), bude latence ve srovnání s operacemi čtení/zápisu horší.

Pokud chcete potvrdit, jestli je většina vašich požadavků na střed, můžete použít stejné kroky jako v předchozím kroku. S výjimkou přidání filtru pro **ResponseType**přidejte filtr pro **název rozhraní API**.

![Filtrování názvu rozhraní API v metrikách](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Alternativní řešení

- Ověřte, zda lze aplikaci upravovat a snížit tak počet operací s metadaty.
- Přidejte virtuální pevný disk do sdílené složky a připojte VHD přes SMB od klienta k provádění operací se soubory s daty. Tento přístup funguje pro scénáře s jedním zapisovačem a více čtenářů a umožňuje místní operace s metadaty, což nabízí podobný výkon jako místní úložiště připojené přímo.

### <a name="cause-3-single-threaded-application"></a>Příčina 3: aplikace s jedním vláknem

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
- V systému Linux se pokuste připojit pomocí možnosti **nostrictsync** , abyste se vyhnuli vynucení vyprázdnění protokolu SMB u každého volání **Fsync** . U souborů Azure Tato možnost neovlivňuje konzistenci dat, ale může mít za následek zastaralá metadata souborů pro výpis adresáře (příkaz**ls-l** ). Přímo se dotazování na metadata souboru (**stat** Command) vrátí nejaktuálnější metadata souborů.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Vysoká latence pro silná zatížení metadat, která zahrnují rozsáhlé operace otevření/zavření.

### <a name="cause"></a>Příčina

Nedostatečná podpora pro zapůjčení adresáře.

### <a name="workaround"></a>Alternativní řešení

- Pokud je to možné, vyhněte se nadměrnému otevírání a zavírání popisovačům ve stejném adresáři v krátké době.
- U virtuálních počítačů se systémem Linux zvyšte časový limit mezipaměti položky adresáře zadáním **actimeo = \<sec> ** as a Mount. Ve výchozím nastavení je to jedna sekunda, takže může pomáhat větší hodnota, například tři nebo pět.
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

- Žádné

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Pomalý výkon od Windows 8.1 nebo serveru 2012 R2

### <a name="cause"></a>Příčina

Vyšší než očekávaná latence při přístupu k souborům Azure pro úlohy náročné na vstupně-výstupní operace.

### <a name="workaround"></a>Alternativní řešení

- Nainstalujte dostupnou [opravu hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Postup vytvoření výstrahy, pokud je sdílená složka omezená

1. V [Azure Portal](https://portal.azure.com)klikněte na **monitorování**. 

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

3. Klikněte na **Vybrat** a vyberte **účet úložiště/prostředek souboru** , který obsahuje sdílenou složku, na které chcete upozornit, a potom klikněte na **Hotovo**. Pokud je třeba název účtu úložiště contoso, vyberte prostředek contoso/File.

4. Kliknutím na **Přidat** přidejte podmínku.

5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, vyberte metriku **transakcí** .

6. V okně **Konfigurovat logiku signálu** přejděte na dimenze **typ odpovědi** , klikněte na rozevírací seznam **hodnoty dimenze** a vyberte **SuccessWithThrottling** (pro SMB) nebo **ClientThrottlingError** (pro REST). 

  > [!NOTE]
  > Pokud není uvedená hodnota dimenze SuccessWithThrottling nebo ClientThrottlingError, znamená to, že prostředek nebyl omezen.  Chcete-li přidat hodnotu dimenze, klikněte na tlačítko **+** vedle rozevíracího seznamu **hodnoty dimenze** , zadejte **SuccessWithThrottling** nebo **ClientThrottlingError**, klikněte na tlačítko **OK** a poté opakujte krok #6.

7. Přejděte do dimenze **sdílení souborů** , klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit. 

  > [!NOTE]
  > Pokud je sdílená složka standardní sdílenou složkou, budou hodnoty dimenzí prázdné, protože pro standardní sdílené složky nejsou k dispozici metriky vázané na sdílení. Výstrahy omezování pro standardní sdílené složky se aktivují, pokud je omezená jakákoli sdílená složka v rámci účtu úložiště a výstraha neurčí, která sdílená složka byla omezená. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku. 

8. Definujte **Parametry výstrahy** (prahová hodnota, operátor, členitost a frekvence), které se používají k vyhodnocení pravidla upozornění na metriky a klikněte na **Hotovo**.

  > [!TIP]
  > Pokud používáte statickou prahovou hodnotu, graf metriky může pomoci určit rozumnou prahovou hodnotu, pokud je sdílená složka momentálně omezená. Pokud používáte dynamickou prahovou hodnotu, v grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

9. Přidejte **skupinu akcí** (E-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.

10. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.

11. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
