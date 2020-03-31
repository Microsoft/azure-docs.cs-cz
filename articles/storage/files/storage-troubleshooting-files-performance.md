---
title: Průvodce řešením potíží s výkonem souborů Azure
description: Známé problémy s výkonem sdílených složek Azure a souvisejících řešení.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598081"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Poradce při potížích s výkonem souborů Azure

Tento článek uvádí některé běžné problémy související se sdílenými složkami Azure. Poskytuje potenciální příčiny a řešení při výskytu těchto problémů.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, nízká propustnost a obecné problémy s výkonem

### <a name="cause-1-share-experiencing-throttling"></a>Příčina 1: Podíl dochází k omezení

Výchozí kvóta pro prémiovou akcii je 100 GiB, která poskytuje 100 základních IOPS (s potenciálem k prasknutí až 300 za hodinu). Další informace o zřizování a jeho vztahu k vipografií, naleznete v části [Zřízená sdílené složky](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) průvodce plánováním.

Pokud se vaše sdílená stránka omezuje, můžete využít metriky Azure na portálu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte **Všechny služby** a vyhledejte **metriky**.

1. Vyberte **Metriky**.

1. Vyberte účet úložiště jako prostředek.

1. Jako obor názvů metriky vyberte **Soubor.**

1. Jako metriku vyberte **Transakce.**

1. Přidejte filtr pro **ResponseType** a zkontrolujte, zda všechny požadavky mají kód odpovědi **SuccessWithThrottling** (pro SMB) nebo **ClientThrottlingError** (pro REST).

![Možnosti metrik pro prémiové sdílené složky](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Pokud chcete dostávat upozornění, pokud je sdílená složka omezená, přečtěte si část [Jak vytvořit výstrahu, pokud je sdílená složka omezena](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Řešení

- Zvyšte kapacitu zřízeného podílem zadáním vyšší kvóty pro váš podíl.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Příčina 2: Velké zatížení metadat/oboru názvů

Pokud většina vašich požadavků jsou zaměřeny na metadata (například createfile/openfile/closefile/queryinfo/querydirectory), pak latence bude horší ve srovnání s operace mise pro čtení a zápis.

Chcete-li potvrdit, zda většina vašich požadavků je zaměřena na metadata, můžete použít stejné kroky jako výše. Kromě přidání filtru pro **ResponseType**přidejte filtr pro **název rozhraní API**.

![Filtrování názvu rozhraní API v metrikách](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Alternativní řešení

- Zkontrolujte, zda lze aplikaci upravit, aby se snížil počet operací metadat.
- Přidejte virtuální pevný disk do sdílené složky a připojte virtuální pevný disk přes SMB z klienta, abyste provedli operace se soubory proti datům. Tento přístup funguje pro scénáře jednoho zapisovače a více čtenářů a umožňuje operace metadat být místní, nabízí výkon podobný místní přímo připojené úložiště.

### <a name="cause-3-single-threaded-application"></a>Příčina 3: Aplikace s jedním závitem

Pokud je aplikace používaná zákazníkem jednovláknová, může to mít za následek výrazně nižší iOPS/propustnost, než je maximální možná na základě vaší zřízené velikosti sdílené složky.

### <a name="solution"></a>Řešení

- Zvyšte paralelismus aplikace zvýšením počtu podprocesů.
- Přepněte do aplikací, kde je možný paralelismus. Například pro operace kopírování mohou zákazníci používat AzCopy nebo RoboCopy z klientů systému Windows nebo **paralelní** příkaz v klientech Linuxu.

## <a name="very-high-latency-for-requests"></a>Velmi vysoká latence pro požadavky

### <a name="cause"></a>Příčina

Virtuální virtuální město klienta může být umístěn v jiné oblasti než sdílené složky.

### <a name="solution"></a>Řešení

- Spusťte aplikaci z virtuálního virtuálního zařízení, který se nachází ve stejné oblasti jako sdílená složka.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klient nemůže dosáhnout maximální propustnosti podporované sítí

Jednou z možných příčin je nedostatek vícekanálové podpory Pro SMB. V současné době sdílené složky Azure podporují jenom jeden kanál, takže existuje jenom jedno připojení z klientského virtuálního počítače k serveru. Toto jediné připojení je zavěšeno na jedno jádro na klientském virtuálním počítači, takže maximální propustnost dosažitelná z virtuálního počítači je vázána jedním jádrem.

### <a name="workaround"></a>Alternativní řešení

- Získání virtuálního virtuálního virtuálního min s větším jádrem může pomoci zlepšit propustnost.
- Spuštění klientské aplikace z více virtuálních počítačů zvýší propustnost.

- Pokud je to možné, použijte rest api.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Propustnost klientů s Linuxem je výrazně nižší ve srovnání s klienty windows.

### <a name="cause"></a>Příčina

Jedná se o známý problém s implementací klienta SMB na Linuxu.

### <a name="workaround"></a>Alternativní řešení

- Rozprostřete zatížení mezi více virtuálních počítačích.
- Na stejném virtuálním počítači použijte více přípojné body s možností **nosharesock** a rozprostřete zatížení mezi tyto přípojné body.
- V systému Linux zkuste montáž s možností **nostrictsync,** abyste se vyhnuli vynucení vyprázdnění smb při každém volání **fsync.** U souborů Azure tato možnost nenarušuje konzistenci dat, ale může mít za následek zastaralá metadata souboru na výpisu adresáře ( příkaz**ls -l).** Přímé dotazování metadat souboru **(příkaz stat)** vrátí nejaktuálnější metadata souboru.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Vysoká latence pro úlohy s vysokou citlivostí metadat, které zahrnují rozsáhlé operace otevření a zavření.

### <a name="cause"></a>Příčina

Nedostatečná podpora pro adresář e-leasingu.

### <a name="workaround"></a>Alternativní řešení

- Pokud je to možné, vyhněte se nadměrnému otevírání a zavírání ve stejném adresáři v krátkém časovém období.
- Pro virtuální počítače s Linuxem zvyšte časový režim mezipaměti pro zadávání adresářů zadáním **actimeo\<= sec>** jako možnost připojení. Ve výchozím nastavení je to jedna sekunda, takže větší hodnota jako tři nebo pět může pomoci.
- U virtuálních počítačů s Linuxem upgradujte jádro na 4.20 nebo vyšší.

## <a name="low-iops-on-centosrhel"></a>Nízké IOPS na CentOS/RHEL

### <a name="cause"></a>Příčina

Hloubka vstupně-i větší než jedna není podporována na CentOS/RHEL.

### <a name="workaround"></a>Alternativní řešení

- Upgrade na CentOS 8 / RHEL 8.
- Změna na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Pomalé kopírování souborů do a z Azure Files v Linuxu

Pokud dochází k pomalékopírování souborů do a z Azure Files, podívejte se na [pomalé kopírování souborů do a z Azure Soubory v Linuxu](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) části v průvodci řešení problémů Linuxu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Kyttery/pilový zub pro IOPS

### <a name="cause"></a>Příčina

Klientská aplikace konzistentně překračuje základní vipony. V současné době neexistuje žádné straně služby vyhlazení zatížení požadavku, takže pokud klient překročí základní VOPS, bude omezena službou. Toto omezení může mít za následek, že klient zažívá nervózní/pilovitý zub VOPS vzor. V tomto případě může být průměrné vipos dosažené klientem nižší než výchozí vipony.

### <a name="workaround"></a>Alternativní řešení

- Snižte zatížení požadavku z klientské aplikace, aby se sdílená složky neomezily.
- Zvyšte kvótu sdílené složky tak, aby se sdílená složky neomezily.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadměrná volání DirectoryOpen/DirectoryClose

### <a name="cause"></a>Příčina

Pokud je počet volání DirectoryOpen/DirectoryClose mezi voláním hlavního rozhraní API a neočekáváte, že klient bude uskutečňovat tolik volání, může to být problém s antivirovým programem nainstalovaným ve virtuálním počítači klienta Azure.

### <a name="workaround"></a>Alternativní řešení

- Oprava tohoto problému je k dispozici v [dubnové aktualizaci platformy pro systém Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Vytvoření souboru je pomalejší, než bylo očekáváno.

### <a name="cause"></a>Příčina

Úlohy, které jsou závislé na vytvoření velkého počtu souborů, neuvidí podstatný rozdíl mezi výkonem sdílených složek premium a standardními sdílenými složkami.

### <a name="workaround"></a>Alternativní řešení

- Žádné.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Pomalý výkon ze systému Windows 8.1 nebo Server 2012 R2

### <a name="cause"></a>Příčina

Vyšší než očekávaná latence přístup k souborům Azure pro úlohy náročné na vi.

### <a name="workaround"></a>Alternativní řešení

- Nainstalujte dostupnou [opravu hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak vytvořit výstrahu, pokud je omezena sdílená složka

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na **Monitor**. 

2. Klepněte na tlačítko **Výstrahy** a potom klepněte na tlačítko **+ Nové pravidlo výstrah**.

3. Klepnutím na **tlačítko Vybrat** vyberte účet úložiště nebo prostředek **souboru** obsahující sdílenou složku, na kterou chcete upozornit, a klepněte na tlačítko **Hotovo**. Pokud je například název účtu úložiště contoso, vyberte prostředek contoso/file.

4. Kliknutím na **Přidat** přidejte podmínku.

5. Zobrazí se seznam signálů podporovaných pro účet úložiště, vyberte metriku **Transakce.**

6. V okně **Konfigurovat logiku signálu** přejděte do dimenze **typu odpověď,** klepněte na rozevírací seznam **Hodnoty dimenze** a vyberte možnost **SuccessWithThrottling** (pro SMB) nebo **ClientThrottlingError** (pro REST). 

  > [!NOTE]
  > Pokud hodnota dimenze SuccessWithThrottling nebo ClientThrottlingError není uvedena, znamená to, že prostředek nebyl omezen.  Chcete-li přidat hodnotu **+** dimenze, klepněte na tlačítko vedle rozevíracího seznamu **Hodnoty dimenze,** zadejte **příkaz SuccessWithThrottling** nebo **ClientThrottlingError**, klepněte na **tlačítko OK** a opakujte krok #6.

7. Přejděte do dimenze **Sdílení souborů,** klikněte na rozevírací rozevírací **položky Hodnoty dimenze** a vyberte sdílenou složku, na kterou chcete upozornit. 

  > [!NOTE]
  > Pokud je sdílená složka standardní sdílenou složkou, rozevírací rozevírací soubor s hodnotami dimenzí bude prázdný, protože metriky na sdílenou složku nejsou k dispozici pro standardní sdílené složky. Omezení výstrahy pro standardní sdílené složky se spustí, pokud je omezena jakákoli sdílená složka v rámci účtu úložiště a výstraha neurčí, která sdílená složka byla omezena. Vzhledem k tomu, že metriky na sdílenou složku nejsou k dispozici pro standardní sdílené složky, doporučujeme mít jednu sdílenou složku na účet úložiště. 

8. Definujte **parametry výstrahy** (prahová hodnota, operátor, rozlišovací schopnost a četnost agregace), které se používají k vyhodnocení pravidla upozornění metriky a klepněte na tlačítko **Hotovo**.

  > [!TIP]
  > Pokud používáte statickou prahovou hodnotu, může graf metriky pomoci určit přiměřenou prahovou hodnotu, pokud je sdílená složka aktuálně omezena. Pokud používáte dynamickou prahovou hodnotu, zobrazí se v grafu metriky vypočtené prahové hodnoty založené na posledních datech.

9. Přidejte skupinu **akcí** (e-mail, SMS atd.) do výstrahy výběrem existující skupiny akcí nebo vytvořením nové skupiny akcí.

10. Vyplňte **podrobnosti výstrahy,** jako **je název pravidla výstrahy**, **Popis** a **Závažnost**.

11. Chcete-li **výstrahu** vytvořit, klepněte na tlačítko Vytvořit pravidlo výstrahy.

Další informace o konfiguraci výstrah v Azure Monitoru najdete v [tématu Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
