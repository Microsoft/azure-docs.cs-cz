---
title: Průvodce řešením potíží se službou Azure File Shares Performance
description: Řešení známých problémů s výkonem u sdílených složek Azure. Zjistěte možné příčiny a související alternativní řešení, když dojde k těmto potížím.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 729c3e46cf329c525ce9204b26d4c6aefa04c89d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632491"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Řešení potíží s výkonem Azure File shares

V tomto článku jsou uvedené některé běžné problémy související se sdílenými složkami Azure. V případě, že dojde k těmto potížím, poskytuje možné příčiny a alternativní řešení.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Vysoká latence, nízká propustnost a obecné problémy s výkonem

### <a name="cause-1-share-was-throttled"></a>Příčina 1: sdílená složka byla omezená.

Požadavky jsou omezeny při dosažení vstupně-výstupních operací za sekundu (IOPS), příchozího přenosu dat nebo odchozích přenosů souborů pro sdílenou složku. Pokud chcete pochopit omezení pro sdílené složky Standard a Premium, přečtěte si téma [sdílení souborů a cíle škálování souborů](./storage-files-scale-targets.md#file-share-and-file-scale-targets).

Pokud chcete ověřit, jestli se vaše sdílená složka omezuje, můžete na portálu získat přístup a používat metriky Azure.

1. V Azure Portal přejít na účet úložiště.

1. V levém podokně v části **monitorování** vyberte **metriky**.

1. Jako obor názvů metriky pro rozsah svého účtu úložiště vyberte **soubor** .

1. Jako metriku vyberte **transakce** .

1. Přidejte filtr pro **typ odpovědi** a potom zkontrolujte, zda nějaké požadavky obsahují některý z následujících kódů odpovědí:
   * **SuccessWithThrottling**: pro protokol SMB (Server Message Block)
   * **ClientThrottlingError**: pro REST

   ![Snímek obrazovky s možnostmi metriky pro sdílené složky úrovně Premium, která zobrazuje filtr vlastností "typ odpovědi".](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > Chcete-li zobrazit výstrahu, přečtěte si část [jak vytvořit výstrahu v případě omezení sdílené složky](#how-to-create-an-alert-if-a-file-share-is-throttled) dále v tomto článku.

### <a name="solution"></a>Řešení

- Pokud používáte standardní sdílení souborů, povolte ve svém účtu úložiště [velké sdílené složky](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) . Velké sdílené složky podporují až 10 000 vstupně-výstupních operací na sdílenou složku.
- Pokud používáte prémiovou sdílenou složku, zvyšte velikost zřízené sdílené složky a zvyšte limit IOPS. Další informace najdete v tématu [Principy zřizování pro sdílené složky Premium](./understanding-billing.md#provisioned-model).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Příčina 2: silná úloha pro metadata nebo obor názvů

Pokud je většina vašich požadavků orientovaných na metadata (například CreateFile, OpenFile, CloseFile, QueryInfo nebo querydirectory), latence bude horší než operace čtení a zápisu.

Pokud chcete zjistit, jestli je většina vašich požadavků orientovaných na metadata, začněte podle kroků 1-4, jak už bylo uvedeno v příčině 1. Pro krok 5 místo přidání filtru pro **typ odpovědi** přidejte filtr vlastností pro **název rozhraní API**.

![Snímek obrazovky s možnostmi metriky pro sdílené složky Premium se zobrazeným filtrem vlastností název rozhraní API](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Alternativní řešení

- Zkontrolujte, zda lze aplikaci upravit a snížit tak počet operací s metadaty.
- Přidejte virtuální pevný disk (VHD) do sdílené složky a připojte VHD přes SMB od klienta k provádění operací se soubory na těchto datech. Tento přístup funguje pro scénáře s jedním zapisovačem a několika čtecími zařízení a umožňuje místní operace s metadaty. Nastavení nabízí výkon podobný místnímu přímo připojenému úložišti.

### <a name="cause-3-single-threaded-application"></a>Příčina 3: aplikace s jedním vláknem

Pokud je aplikace, kterou používáte, jediným vláknem, může tato instalace výrazně snížit propustnost IOPS, než je maximální možná propustnost, a to v závislosti na velikosti zřízené sdílené složky.

### <a name="solution"></a>Řešení

- Zvýšení paralelismu aplikace zvýšením počtu vláken.
- Přepněte na aplikace, kde je možné paralelismus. Například pro operace kopírování můžete použít AzCopy nebo Robocopy z klientů systému Windows nebo **paralelní** příkaz z klientů se systémem Linux.

## <a name="very-high-latency-for-requests"></a>Velmi vysoká latence pro žádosti

### <a name="cause"></a>Příčina

Klientský virtuální počítač (VM) se může nacházet v jiné oblasti než sdílení souborů. Jiným důvodem pro vysokou latenci může být latence v důsledku latence klienta nebo sítě.

### <a name="solution"></a>Řešení

- Spusťte aplikaci z virtuálního počítače, který se nachází ve stejné oblasti jako sdílená složka.
- V případě svého účtu úložiště zkontrolujte metriky transakcí **SuccessE2ELatency** a  **SuccessServerLatency** prostřednictvím **Azure monitor** v Azure Portal. Vysoký rozdíl mezi hodnotami metrik SuccessE2ELatency a SuccessServerLatency je indikací latence, která je pravděpodobně způsobena sítí nebo klientem. Viz [metriky transakcí](storage-files-monitoring-reference.md#transaction-metrics) v referenčních informacích k datům monitorování Azure Files.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klientovi se nepovedlo dosáhnout maximální propustnosti, kterou síť podporuje.

### <a name="cause"></a>Příčina
Jednou z možných příčin je nedostatečná podpora více kanálů pro standardní sdílení souborů v protokolu SMB. Soubory Azure v současné době podporují jenom jeden kanál, takže existuje jenom jedno připojení z virtuálního počítače klienta k serveru. Toto jediné připojení je v klientském počítači klienta připojeno k jednomu jádru, takže maximální propustnost, kterou je možné z virtuálního počítače dosáhnout, je svázána s jedním jádrem.

### <a name="workaround"></a>Alternativní řešení

- V případě sdílených složek úrovně Premium [Povolte v účtu úložiště souborů možnost pro vícekanálový protokol SMB](storage-files-enable-smb-multichannel.md).
- Získání virtuálního počítače s větším jádrem může pomoci zlepšit propustnost.
- Při spuštění klientské aplikace z více virtuálních počítačů se zvýší propustnost.
- Pokud je to možné, použijte rozhraní REST API.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Propustnost u klientů se systémem Linux je výrazně nižší než u klientů se systémem Windows.

### <a name="cause"></a>Příčina

Jedná se o známý problém s implementací klienta SMB v systému Linux.

### <a name="workaround"></a>Alternativní řešení

- Rozprostře zatížení mezi více virtuálních počítačů.
- Ve stejném virtuálním počítači použijte více přípojných bodů s možností **nosharesock** a rozprostřete zatížení mezi tyto přípojné body.
- V systému Linux se pokuste připojit s možností **nostrictsync** , abyste se vyhnuli vynucení vyprázdnění protokolu SMB u každého volání **Fsync** . U souborů Azure Tato možnost nekoliduje s konzistencí dat, ale může to mít za následek zastaralá metadata souborů ve výpisech adresářů (příkaz **ls-l** ). Přímé dotazování na metadata souborů pomocí příkazu **stat** vrátí nejaktuálnější metadata souborů.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Vysoká latence pro úlohy náročné na metadata zahrnující rozsáhlé operace otevření/zavření

### <a name="cause"></a>Příčina

Nedostatečná podpora pro zapůjčení adresáře.

### <a name="workaround"></a>Alternativní řešení

- Pokud je to možné, vyhněte se nadměrnému počátečnímu nebo uzavíracímu popisovači ve stejném adresáři v krátké době.
- U virtuálních počítačů se systémem Linux zvyšte časový limit mezipaměti položky adresáře zadáním **actimeo = \<sec>** as a Mount. Ve výchozím nastavení je časový limit 1 sekunda, takže větší hodnota, například 3 nebo 5 sekund, může pomáhat.
- Pro virtuální počítače se systémem CentOS Linux nebo Red Hat Enterprise Linux (RHEL) upgradujte systém na CentOS Linux 8,2 nebo RHEL 8,2. Pro jiné virtuální počítače se systémem Linux upgradujte jádro na 5,0 nebo novější.

## <a name="low-iops-on-centos-linux-or-rhel"></a>Nízká IOPS v systému CentOS Linux nebo RHEL

### <a name="cause"></a>Příčina

V systému CentOS Linux ani RHEL není podporována hloubka I/O větší než 1.

### <a name="workaround"></a>Alternativní řešení

- Upgradujte na CentOS Linux 8 nebo RHEL 8.
- Přejděte na Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Pomalé kopírování souborů do a ze sdílených složek Azure v systému Linux

Pokud máte pomalé kopírování souborů, přečtěte si část "pomalé kopírování souborů do a ze sdílených složek Azure v systému Linux" v [Průvodci odstraňováním potíží se systémem Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Vzorek kolísání nebo vede pilovému efektu pro IOPS

### <a name="cause"></a>Příčina

Klientská aplikace konzistentně překračuje základní IOPS. V současné době není k dispozici žádná nevyhlazení zátěže na straně služby. Pokud klient překročí směrného plánu IOPS, bude ho služba omezovat. Omezení může mít za následek, že u klienta dochází ke kolísání nebo vede pilovému efektu IOPS. V tomto případě může být průměrná hodnota IOPS dosažená klientem nižší než hodnota IOPS.

### <a name="workaround"></a>Alternativní řešení
- Snižte zatížení žádosti z klientské aplikace, aby sdílená složka nezískala omezení.
- Zvyšte kvótu sdílené složky tak, aby sdílená složka nezískala omezení.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Nadměrné DirectoryOpen/DirectoryClose volání

### <a name="cause"></a>Příčina

Pokud je počet volání **DirectoryOpen/DirectoryClose** mezi horními VOLÁNÍMI rozhraní API a neočekáváte, že klient provede toto mnoho volání, může to být způsobeno antivirovým softwarem, který je nainstalovaný na virtuálním počítači klienta Azure.

### <a name="workaround"></a>Alternativní řešení

- Oprava tohoto problému je k dispozici v [aktualizaci platformy duben pro Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Vytvoření souboru je pomalejší, než se očekávalo.

### <a name="cause"></a>Příčina

Úlohy, které spoléhají na vytvoření velkého počtu souborů, nebudou mít podstatný rozdíl ve výkonu mezi sdílenými složkami souborů Premium a standardními sdílenými složkami.

### <a name="workaround"></a>Alternativní řešení

- Žádné

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Pomalý výkon od Windows 8.1 nebo serveru 2012 R2

### <a name="cause"></a>Příčina

Vyšší než očekávaná latence přístupu ke sdíleným složkám Azure pro úlohy náročné na vstupně-výstupní operace.

### <a name="workaround"></a>Alternativní řešení

- Nainstalujte dostupnou [opravu hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>Vícekanálový parametr SMB není zobrazený v nastavení sdílené složky. 

### <a name="cause"></a>Příčina

Buď není předplatné u této funkce zaregistrované, nebo oblast a typ účtu se nepodporují.

### <a name="solution"></a>Řešení

Ujistěte se, že vaše předplatné je zaregistrované ve službě SMB vícekanálových funkcí. V tématu [Začínáme](storage-files-enable-smb-multichannel.md#getting-started) se ujistěte, že druh účtu je Storage (Premium File Account) na stránce Přehled účtu. 

## <a name="smb-multichannel-is-not-being-triggered"></a>Neaktivuje se funkce SMB vícekanálový.

### <a name="cause"></a>Příčina

Nedávné změny v nastaveních vícekanálové konfigurace protokolu SMB bez opětovného připojení.

### <a name="solution"></a>Řešení
 
-   Po jakýchkoli změnách nastavení konfigurace vícekanálového protokolu SMB klienta nebo účtu Windows SMB musíte odpojit sdílenou složku, počkat na 60 sekund a znovu připojit sdílenou složku, aby se spustila vícekanálový.
-   Pro klientské operační systémy Windows vygenerujte vstupně-výstupní operace s vysokou hloubkou fronty vyslovením hloubka fronty = 8, například zkopírováním souboru můžete aktivovat protokol SMB vícekanálový.  U serverového operačního systému se s protokolem SMB vícekanálový spouští hloubka fronty = 1, což znamená, že se po spuštění jakékoli vstupně-výstupní operace ke sdílené položce spustí.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Vysoká latence u webů hostovaných ve sdílených složkách 

### <a name="cause"></a>Příčina  

Vysoké číslo oznámení změny souborů u sdílených složek může způsobit výrazné vysoké latence. K tomu obvykle dochází u webů hostovaných ve sdílených složkách s hlubokou vnořenou strukturou adresářů. Typickým scénářem je webová aplikace hostovaná službou IIS, kde je pro každý adresář ve výchozí konfiguraci nastaveno oznámení o změně souboru. Každá změna ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) ve sdílené složce, která je klientem SMB registrována pro vložení oznámení o změně ze souborové služby do klienta, který přebírá systémové prostředky, a vystavuje zhoršený počet změn. To může způsobit omezení sdílení a proto má za následek vyšší latenci na straně klienta. 

K potvrzení můžete použít metriky Azure na portálu – 

1. V Azure Portal přejít na účet úložiště. 
1. V nabídce vlevo v části monitorování vyberte metriky. 
1. Jako obor názvů metriky pro rozsah svého účtu úložiště vyberte soubor. 
1. Jako metriku vyberte transakce. 
1. Přidejte filtr pro ResponseType a zkontrolujte, zda nějaké požadavky obsahují kód odpovědi SuccessWithThrottling (pro protokol SMB) nebo ClientThrottlingError (pro REST).

### <a name="solution"></a>Řešení 

- Pokud není použito oznámení o změně souboru, zakažte oznámení o změně souboru (upřednostňovaná).
    - [Zakažte upozornění na změnu souboru](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) aktualizací FCNMode. 
    - Aktualizujte interval dotazování pracovního procesu služby IIS (W3WP) na hodnotu 0 nastavením `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` v registru a restartujte proces W3wp. Další informace o tomto nastavení najdete v tématu [běžné klíče registru, které používá mnoho částí služby IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Zvýšit frekvenci intervalu dotazování na změnu souboru pro snížení objemu.
    - Aktualizujte interval dotazování pracovního procesu W3WP na vyšší hodnotu (např. 10mins nebo 30mins) podle vašeho požadavku. Nastavte `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [v registru](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) a restartujte proces W3wp.
- Pokud má váš web namapovaný fyzický adresář adresářové struktury, můžete zkusit omezit rozsah oznámení o změně souboru, aby se snížil objem oznámení. Služba IIS ve výchozím nastavení používá konfiguraci z Web.config souborů ve fyzickém adresáři, ke kterému je virtuální adresář namapován, a také v všech podřízených adresářích v tomto fyzickém adresáři. Pokud nechcete používat Web.config soubory v podřízených adresářích, zadejte pro atribut nastaveno allowsubdirconfig ve virtuálním adresáři hodnotu false. Další podrobnosti najdete [tady](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 
    - Nastavením možnosti virtuální adresář služby IIS "nastaveno allowsubdirconfig" v Web.Config na *hodnotu false* pro vyloučení mapovaných fyzických podřízených adresářů z oboru.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Postup vytvoření výstrahy, pokud je sdílená složka omezená

1. V Azure Portal přejít na účet úložiště.
1. V části **monitorování** vyberte **výstrahy** a pak vyberte **nové pravidlo výstrahy**.
1. Vyberte **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a potom vyberte **Hotovo**. Pokud je třeba název účtu úložiště *Contoso*, vyberte prostředek contoso/File.
1. Vyberte **možnost vybrat podmínku** a přidejte podmínku.
1. V seznamu signálů, které jsou podporované pro účet úložiště, vyberte metriku **transakcí** .
1. V podokně **Konfigurovat logiku signálu** v rozevíracím seznamu **název dimenze** vyberte **typ odpovědi**.
1. V rozevíracím seznamu **hodnoty dimenze** vyberte **SUCCESSWITHTHROTTLING** (pro SMB) nebo **ClientThrottlingError** (pro REST).

   > [!NOTE]
   > Pokud není uvedena hodnota dimenze **SuccessWithThrottling** ani **ClientThrottlingError** , znamená to, že prostředek nebyl omezen. Chcete-li přidat hodnotu dimenze, klikněte vedle rozevíracího seznamu **hodnoty dimenze** na **Přidat vlastní hodnotu**, zadejte **SuccessWithThrottling** nebo **ClientThrottlingError**, vyberte **OK** a pak opakujte krok 7.

1. V rozevíracím seznamu **název dimenze** vyberte **sdílení souborů**.
1. V rozevíracím seznamu **hodnoty dimenze** vyberte sdílenou složku nebo sdílené složky, na kterých chcete upozornit.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílená složka, vyberte **všechny aktuální a budoucí hodnoty**. V rozevíracím seznamu hodnoty dimenze není uveden seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici metriky vázané na sdílení. Výstrahy omezování pro standardní sdílené složky se aktivují, pokud je omezená jakákoli sdílená složka v rámci účtu úložiště a výstraha neidentifikuje, která sdílená složka byla omezená. Vzhledem k tomu, že pro standardní sdílené složky nejsou dostupné metriky vázané na sdílení, doporučujeme pro každý účet úložiště použít jednu sdílenou složku.

1. Parametry výstrahy definujte zadáním **prahové hodnoty**, **operátoru**, **členitosti agregace** a **frekvence vyhodnocení** a potom vyberte **Hotovo**.

    > [!TIP]
    > Pokud používáte statickou prahovou hodnotu, graf metriky vám pomůže určit rozumnou prahovou hodnotu, pokud je sdílená složka momentálně omezená. Pokud používáte dynamickou prahovou hodnotu, graf metriky zobrazuje vypočtené prahové hodnoty na základě nejnovějších dat.

1. Vyberte **možnost vybrat skupinu akcí** a potom do výstrahy přidejte skupinu akcí (například E-mail nebo SMS), a to buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
1. Zadejte podrobnosti výstrahy, jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
1. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Jak vytvářet výstrahy, pokud se v rámci omezení podílu souborů úrovně Premium blíží omezení

1. V Azure Portal přejít na účet úložiště.
1. V části **monitorování** vyberte **výstrahy** a pak vyberte **nové pravidlo výstrahy**.
1. Vyberte **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a potom vyberte **Hotovo**. Pokud je třeba název účtu úložiště *Contoso*, vyberte prostředek contoso/File.
1. Vyberte **možnost vybrat podmínku** a přidejte podmínku.
1. V seznamu signálů, které jsou podporované pro účet úložiště, vyberte metriku **odchozího** přenosu dat.

   > [!NOTE]
   > Je nutné vytvořit tři samostatné výstrahy, které budou upozorňovány, pokud hodnoty příchozího, výstupního nebo transakčního překročí prahové hodnoty, které jste nastavili. Důvodem je to, že výstraha se aktivuje jenom v případě, že jsou splněné všechny podmínky. Pokud například umístíte všechny podmínky do jedné výstrahy, budete upozorněni pouze v případě, že příchozí a odchozí přenos dat a transakce překročí jejich prahové hodnoty.

1. Posuňte se dolů. V rozevíracím seznamu **název dimenze** vyberte **sdílení souborů**.
1. V rozevíracím seznamu **hodnoty dimenze** vyberte sdílenou složku nebo sdílené složky, na kterých chcete upozornit.
1. Parametry výstrahy definujte tak, že vyberete hodnoty v rozevíracích seznamech **operátor**, **prahová hodnota**, **členitost agregace** a **frekvence vyhodnocování** a pak vyberete **Hotovo**.

   Metriky odchozího přenosu dat, příchozího přenosu dat a transakcí se vyjadřují za minutu, i když jste zřídili odchozí, příchozí a vstupně-výstupní operace za sekundu. Pokud máte například zřízený výstup 90 &nbsp; mebibytes za sekundu (MIB/s) a chcete, aby byla prahová hodnota 80 &nbsp; procent zřízeného odchozího přenosu, vyberte následující parametry výstrahy: 
   - Pro **mezní hodnotu**: *75497472* 
   - For – operátor *větší než nebo rovno*
   - Pro **typ agregace**: *průměr*
   
   V závislosti na tom, jak chcete, aby vaše výstraha byla, můžete také vybrat hodnoty pro **členitost agregace** a **četnost vyhodnocení**. Pokud třeba chcete, aby se vaše výstraha zobrazila na průměrném počtu příchozích dat za dobu 1 hodiny, a chcete, aby se pravidlo upozornění spouštělo každou hodinu, vyberte následující:
   - Pro **členitost agregace**: *1 hodina*
   - **Frekvence hodnocení**: *1 hodina*

1. Vyberte **Vybrat skupinu akcí** a potom do výstrahy přidejte skupinu akcí (například E-mail nebo SMS), a to tak, že vyberete existující skupinu akcí nebo vytvoříte novou.
1. Zadejte podrobnosti výstrahy, jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
1. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    > [!NOTE]
    > - Abyste byli informováni o tom, že se vaše rezerva vašich souborů na úrovni Premium blíží omezení *z důvodu zřízeného* příchozího přenosu dat, postupujte podle předchozích pokynů, ale s následující změnou:
    >    - V kroku 5 **Vyberte metriku** příchozího přenosu dat místo **odchozího** přenosu dat.
    >
    > - Abyste byli informováni o tom, že se vaše prémiové sdílení souborů blíží omezení *vzhledem k zřízeným IOPS*, postupujte podle předchozích pokynů, ale s následujícími změnami:
    >    - V kroku 5 vyberte metriky **transakcí** místo **odchozího** přenosu dat.
    >    - V kroku 10 je jedinou možností pro **typ agregace** *Celková hodnota*. Proto prahová hodnota závisí na zvolené členitosti agregace. Například pokud chcete, aby byla prahová hodnota 80 &nbsp; procent zřízeného směrného plánu IOPS a pro **členitost** jste vybrali *1 hodinu* , bude **prahová hodnota** vaše základní iops (v bajtech) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Viz také
- [Řešení potíží se soubory Azure v systému Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Řešení potíží se soubory Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Nejčastější dotazy ke službě Azure Files](storage-files-faq.md)
