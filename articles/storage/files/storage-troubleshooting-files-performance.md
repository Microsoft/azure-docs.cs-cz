---
title: Průvodce řešením potíží s výkonem služby soubory Azure
description: Řešení známých problémů s výkonem u sdílených složek Azure. Zjistěte možné příčiny a související alternativní řešení, když dojde k těmto potížím.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: cffac114cacd05e04e149af96d1678b536db7fec
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564232"
---
# <a name="troubleshoot-azure-files-performance-issues-smb"></a>Řešení potíží s výkonem Azure Files (SMB)

> [!IMPORTANT]
> Obsah tohoto článku platí pouze pro sdílené složky SMB.

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami Azure. Poskytuje možné příčiny a alternativní řešení, když dojde k těmto potížím.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, nízká propustnost a obecné problémy s výkonem

### <a name="cause-1-share-was-throttled"></a>Příčina 1: sdílená složka byla omezená.

Požadavky jsou omezeny, když jsou dosaženy limity IOPS, příchozí nebo odchozí připojení ke sdílené složce. Pokud chcete pochopit omezení pro sdílené složky Standard a Premium, přečtěte si téma [sdílení souborů a cíle škálování souborů](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets).

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

- Pokud používáte standardní sdílení souborů, povolte ve svém účtu úložiště [velké sdílené složky](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) . Velké sdílené složky podporují až 10 000 vstupně-výstupních operací na sdílenou složku.
- Pokud používáte prémiovou sdílenou složku, zvyšte velikost zřízené sdílené složky a zvyšte limit IOPS. Další informace najdete v části [Principy zřizování pro služby Premium pro sdílení](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) souborů v příručce pro plánování souborů Azure.

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

1. V **Azure Portal**přejít na svůj **účet úložiště** .
2. V části monitorování klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště contoso, vyberte prostředek contoso/File.
4. Kliknutím na **vybrat podmínku** přidáte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, vyberte metriku **transakcí** .
6. V okně **Konfigurovat logiku signálu** klikněte na rozevírací seznam **název dimenze** a vyberte **typ odpovědi**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte **SUCCESSWITHTHROTTLING** (pro SMB) nebo **ClientThrottlingError** (pro REST).

  > [!NOTE]
  > Pokud není uvedená hodnota dimenze SuccessWithThrottling nebo ClientThrottlingError, znamená to, že prostředek nebyl omezen. Chcete-li přidat hodnotu dimenze, klikněte na tlačítko **Přidat vlastní hodnotu** vedle rozevíracího seznamu **hodnoty dimenzí** , zadejte **SuccessWithThrottling** nebo **ClientThrottlingError**, klikněte na tlačítko **OK** a poté opakujte krok #7.

8. Klikněte na rozevírací seznam **název dimenze** a vyberte **sdílení souborů**.
9. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.

  > [!NOTE]
  > Pokud je sdílená složka standardní sdílená složka, vyberte **všechny aktuální a budoucí hodnoty**. Rozevírací seznam hodnoty dimenze nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici metriky pro jednotlivé sdílené složky. Výstrahy omezování pro standardní sdílené složky se aktivují, pokud je omezená jakákoli sdílená složka v rámci účtu úložiště a výstraha neurčí, která sdílená složka byla omezená. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

10. Definujte **Parametry výstrahy** (prahová hodnota, operátor, členitost agregace a frekvence vyhodnocení) a klikněte na **Hotovo**.

  > [!TIP]
  > Pokud používáte statickou prahovou hodnotu, graf metriky může pomoci určit rozumnou prahovou hodnotu, pokud je sdílená složka momentálně omezená. Pokud používáte dynamickou prahovou hodnotu, v grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

11. Kliknutím na **Vybrat skupinu akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
12. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
13. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-towards-being-throttled"></a>Jak vytvořit výstrahy, pokud se v rámci omezení podílu souborů úrovně Premium dospěje k omezení

1. V **Azure Portal**přejít na svůj **účet úložiště** .
2. V části monitorování klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště contoso, vyberte prostředek contoso/File.
4. Kliknutím na **vybrat podmínku** přidáte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, a vyberte **výstupní** metriku.

  > [!NOTE]
  > Je nutné vytvořit 3 samostatné výstrahy, které budou upozorňovány, pokud příchozí, odchozí nebo transakční transakce překročí nastavenou prahovou hodnotu. Důvodem je to, že výstraha se aktivuje jenom v případě, že jsou splněné všechny podmínky. Takže pokud zadáte všechny podmínky do jedné výstrahy, budete upozorněni jenom v případě, že příchozí nebo odchozí přenos dat a transakce překročily prahové hodnoty.

6. Posuňte se dolů. Klikněte na rozevírací seznam **název dimenze** a vyberte **sdílení souborů**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.
8. Definujte **Parametry výstrahy** (prahová hodnota, operátor, členitost agregace a frekvence vyhodnocení) a klikněte na **Hotovo**.

  > [!NOTE]
  > Metriky odchozího, příchozího a transakčního vysílání jsou za minutu, i když jste zřídili odchozí, příchozí a vstupně-výstupní operace za sekundu. (pohovořit o členitosti agregace – > za minutu = větší úroveň šumu, takže vyberte rozdílové) Pokud máte například zřízený výstup 90 MiB/s a chcete, aby byla prahová hodnota 80% zřízeného odchozího přenosu, měli byste vybrat následující parametry výstrahy: 75497472 pro **mezní hodnotu**, větší nebo rovnou **operátoru**for a průměr pro **typ agregace**. V závislosti na tom, jak chcete, aby vaše výstraha byla, můžete zvolit, které hodnoty se mají vybrat pro členitost agregace a četnost vyhodnocení. Například pokud chci, aby moje výstraha vypadala na základě průměrného počtu příchozích dat za hodinu a chtěla se spustit pravidlo upozornění každou hodinu, vybrali jste 1 hodinu pro **členitost agregace** a 1 hodinu pro **frekvenci vyhodnocení**.

9. Kliknutím na **Vybrat skupinu akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
10. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
11. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

  > [!NOTE]
  > Chcete-li být upozorněni na blížící se omezení vaší sdílené složky v důsledku zřízení příchozího přenosu dat, postupujte prosím podle stejných kroků, s výjimkou kroku 5 vyberte **metriku** příchozího přenosu dat.

  > [!NOTE]
  > Abyste byli informováni o tom, jestli se nadlimitní sdílení souborů blíží omezení z důvodu zřízeného IOPS, budete muset provést několik změn. V kroku 5 vyberte místo toho metriku **transakcí** . V kroku 10 je také jediná možnost pro **typ agregace** . Proto by prahová hodnota byla závislá na zvolené členitosti agregace. Pokud jste například chtěli, aby byla prahová hodnota 80% zřízeného směrného plánu IOPS a vybrali jste 1 hodinu pro **členitost agregace**, bude **prahová hodnota** vaše základní IOPS (v bajtech) × 0,8 × 3600. Kromě těchto změn použijte stejný postup, který je uvedený výše. 

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Viz také
* [Řešení potíží se soubory Azure v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží se soubory Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Nejčastější dotazy ke službě Azure Files](storage-files-faq.md)
