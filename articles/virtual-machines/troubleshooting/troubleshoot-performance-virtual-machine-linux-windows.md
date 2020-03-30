---
title: Poradce při potížích s výkonem virtuálních strojů Azure v Linuxu nebo Windows
description: Tento článek popisuje řešení potíží s obecným výkonem virtuálního počítače (VM) prostřednictvím monitorování a sledování kritických míst a poskytuje možné nápravu problémů, které mohou nastat.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772614"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Poradce při potížích s výkonem virtuálních strojů Azure v Linuxu nebo Windows

Tento článek popisuje řešení potíží s obecným výkonem virtuálního počítače (VM) prostřednictvím monitorování a sledování kritických míst a poskytuje možné nápravu problémů, které mohou nastat. Kromě monitorování můžete také použít Perfinsights, který může poskytnout zprávu s doporučeními osvědčených postupů a klíčovými kritickými body kolem IO/CPU/Memory. Perfinsights je k dispozici pro virtuální počítače [s Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) i [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) v Azure.

Tento článek bude procházet pomocí monitorování diagnostikovat problémová místa výkonu.

## <a name="enabling-monitoring"></a>Povolení monitorování

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorování virtuálních strojů Azure IAAS

Ke sledování virtuálního počítače hosta použijte monitorování virtuálních počítačích Azure, které vás upozorní na určité podmínky prostředků vysoké úrovně. Pokud chcete zkontrolovat, jestli máte povolenou diagnostiku virtuálních počítače, přečtěte si informace o tom, jestli máte povolené [protokoly prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs). Pokud se zobrazí následující, pak s největší pravděpodobností nemáte povolenou diagnostiku:

![Monitorování není povoleno.](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Povolení diagnostiky virtuálních počítačů prostřednictvím portálu Microsoft Azure

Povolení diagnostiky virtuálních mandorů:

1. Přejít na virtuální ms
2. Klikněte na **Nastavení diagnostiky.**
3. Vyberte účet úložiště a klepněte na **tlačítko Povolit monitorování na úrovni hosta**.

   ![Klikněte na Nastavení a potom na Diagnostika.](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Účet úložiště použitý pro nastavení diagnostiky můžete zkontrolovat na kartě **Agent** v části **Nastavení diagnostiky**.

![Zkontrolovat účet úložiště](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Povolení diagnostiky účtu úložiště prostřednictvím portálu Azure

Úložiště je velmi důležitá úroveň, když máme v úmyslu analyzovat výkon vstupně-věcí pro virtuální počítač v Azure. Pro metriky související s úložištěm potřebujeme povolit diagnostiku jako další krok. To by také mohlo být povoleno, pokud chceme analyzovat pouze čítače související s úložištěm.

1. Zjistěte, který účet úložiště (nebo účty) váš virtuální počítač používá výběrem virtuálního počítače. Klepněte na tlačítko **Nastavení**a potom na **položku Disky**:

   ![Klikněte na Nastavení a potom na Disky.](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. Na portálu přejděte na účet úložiště (nebo účty) pro virtuální hod a postupujte podle následujících kroků:

   1. Klikněte na přehled účtu úložiště, který jste našli s výše uvedeným krokem.
   2. Zobrazí se výchozí metriky. 

    ![Výchozí metriky](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Klikněte na některou z metrik, která se zobrazí další okno s dalšími možnostmi konfigurace a přidání metrik.

   ![Přidání metrik](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Postup konfigurace těchto možností:

1.  Vyberte **Metriky**.
2.  Vyberte **prostředek** (účet úložiště).
3.  Výběr **oboru názvů**
4.  Vyberte **metrika**.
5.  Vyberte typ **agregace**
6.  Toto zobrazení můžete připnout na řídicí panel.

## <a name="observing-bottlenecks"></a>Pozorování úzkých míst

Jakmile projdeme procesem počátečního nastavení pro potřebné metriky a zveřejníme povolení diagnostiky pro virtuální počítač a související účet úložiště, můžeme přejít na fázi analýzy.

### <a name="accessing-the-monitoring"></a>Přístup k monitorování

Vyberte virtuální počítač Azure, který chcete prozkoumat, a vyberte **Sledování**.

![Vybrat monitorování](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Lhůty pro pozorování

Chcete-li zjistit, zda máte problémová místa prostředků, zkontrolujte data. Pokud zjistíte, že váš počítač běží v pořádku, ale bylo hlášeno, že výkon nedávno snížil, zkontrolujte časový rozsah dat, která zahrnuje data metriky výkonu před nahlášenými změnami, během a po problému.

### <a name="check-for-cpu-bottleneck"></a>Kontrola kritického místa procesoru

![Kontrola kritického místa procesoru](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Upravte graf.
2. Nastavte časový rozsah.
3. Poté je třeba přidat do čítače: CPU Procento Guest OS
4. Uložte.

### <a name="cpu-observe-trends"></a>Sledování trendů v cpu

Při pohledu na problémy s výkonem si uvědomte trendy a pochopte, zda se vás týkají. V dalších částech použijeme grafy monitorování z portálu k zobrazení trendů. Mohou být také užitečné pro křížové odkazování na chování prostředků rozdíl ve stejném časovém období. Pokud chcete grafy přizpůsobit, klikněte na [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)

Spiking – Spiking může souviset s naplánovanou úlohou/známou událostí. Pokud můžete identifikovat úlohu, zjistěte, zda je úloha spuštěna na požadované úrovni výkonu. Pokud je výkon přijatelný, nemusí být nutné navýšit prostředky.

Spike up a Constant – často označuje nové zatížení. Pokud to není rozpoznané zatížení, povolte monitorování ve virtuálním počítače zjistit, jaký proces (nebo procesy) způsobuje chování. Jakmile je proces rozpoznán, zjistěte, zda je zvýšená spotřeba způsobena neefektivním kódem nebo normální spotřebou. Pokud je normální spotřeba, rozhodněte se, zda proces pracuje na požadované úrovni výkonu.

Konstanta – zjistěte, zda váš virtuální počítač vždy běží na této úrovni, nebo pokud byl spuštěn pouze na této úrovni, protože diagnostika byla povolena. Pokud ano, identifikujte proces (nebo procesy), které způsobují problém, a zvažte přidání dalších prostředků.

Neustále rostoucí – konstantní nárůst spotřeby je často buď neefektivní kód nebo proces s více zatížení uživatelů.

### <a name="high-cpu-utilization-remediation"></a>Náprava s vysokým využitím procesoru

Pokud vaše aplikace nebo proces není spuštěn na správné úrovni výkonu a vidíte 95 % + konstanta využití procesoru, můžete provést některou z následujících úloh:

* Pro okamžitou úlevu – zvětšení velikosti virtuálního počítače na velikost s více jádry
* Seznamte se s problémem – vyhledejte aplikaci/proces a odpovídajícím způsobem vyřešte potíže.

Pokud jste zvýšili virtuální ho a procesor je stále spuštěn 95 %, zjistěte, zda toto nastavení nabízí lepší výkon nebo vyšší propustnost aplikace na přijatelnou úroveň. Pokud ne, vyřešte tuto jednotlivou aplikaci\zpracujte.

Perfinsights pro [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) můžete použít k analýze, který proces je hnací silou spotřeby procesoru. 

## <a name="check-for-memory-bottleneck"></a>Kontrola kritického místa paměti

Zobrazení metrik:

1. Přidejte oddíl.
2. Přidejte dlaždici.
3. Otevřete galerii.
4. Vyberte využití paměti a táhněte. Když je dlaždice ukotvena, klepněte pravým tlačítkem myši a vyberte **možnost 6x4**.

### <a name="memory-observe-trends"></a>Sledování trendů paměti

Využití paměti ukazuje, kolik paměti se spotřebovává s virtuálním počítačem. Seznamte se s trendem a tím, zda se mapuje na dobu, kdy se problémy zobrazují. Vždy byste měli mít více než 100 MB dostupné paměti.

Špička a konstantní/konstantní stabilní spotřeba – vysoké využití paměti nemusí být příčinou špatný výkon, protože některé aplikace, jako jsou relační databázové stroje přidělit velké množství paměti a toto využití nemusí být významné. Pokud však existuje více aplikací s nárokem na paměť, může se zobrazit nízký výkon z tvrzení paměti způsobuje oříznutí a stránkování/prohození na disk. Tento nízký výkon je často znatelnou příčinou dopadu na výkon aplikace.

Neustále rostoucí spotřeba - možné aplikace 'zahřívání', tato spotřeba je běžné u databázových strojů spouštění. Ale může jít také o signál nevrácení paměti v aplikaci. Identifikujte aplikaci a zjistěte, zda je očekávané chování.

Využití stránky nebo odkládacího souboru – Zkontrolujte, zda\) používáte soubor Stránkování Systému Windows (umístěný na D: nebo linuxový odkládací soubor (umístěný na) `/dev/sdb`jsou silně používány. Pokud na těchto svazcích nemáte nic kromě těchto souborů, zkontrolujte vysoké čtení a zápisy na těchto discích. Tento problém svědčí o nedostatku paměti podmínky.

### <a name="high-memory-utilization-remediation"></a>Náprava využití paměti s vysokým využitím paměti

Chcete-li vyřešit vysoké využití paměti, proveďte některou z následujících úloh:

* Pro okamžitou úlevu nebo využití stránkovacího nebo odkládacího souboru – zvyšte velikost virtuálního počítače na jeden s větší pamětí a pak monitorujte.
* Pochopit problém – vyhledejte aplikace/proces a řešení potíží pro identifikaci aplikací s vysokou spotřebou paměti.
* Pokud znáte aplikaci, zjistěte, zda přidělení paměti může být omezeno.

Pokud po upgradu na větší virtuální ms zjistíte, že stále máte konstantní stálý nárůst až do 100 %, identifikujte aplikaci/proces a řešení potíží.

Perfinsights pro [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) můžete použít k analýze, který proces je hnací silou spotřeby paměti. 

## <a name="check-for-disk-bottleneck"></a>Kontrola kritického místa disku

Chcete-li zkontrolovat podsystém úložiště pro virtuální počítač, zkontrolujte diagnostiku na úrovni virtuálního počítače Azure pomocí čítačů v diagnostice virtuálních počítače a také diagnostiky účtu úložiště.

Pro řešení potíží specifické pro virtuální počítač, můžete použít Perfinsights pro [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), které by mohly pomoci analyzovat, který proces je hnací silou IO. 

Všimněte si, že nemáme čítače pro zónové redundantní a premium storage účty. V případě problémů souvisejících s těmito přepážky nastořte případ podpory.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Zobrazení diagnostiky účtu úložiště při monitorování

Pokud chcete pracovat na níže uvedených položkách, přejděte do účtu úložiště pro virtuální počítač na portálu:

![Zobrazení diagnostiky účtu úložiště v monitorování](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Upravte graf monitorování.
2. Nastavte časový rozsah.
3. Přidejte čítače popsané v následujících krocích.
4. Uložte změny.

### <a name="disk-observe-trends-standard-storage-only"></a>Sledování trendů disku (pouze standardní úložiště)

Pokud chcete identifikovat problémy s úložištěm, podívejte se na metriky výkonu z diagnostiky účtu úložiště a diagnostiky virtuálních zařízení.

Pro každou kontrolu níže vyhledejte klíčové trendy, když k problémům dochází v časovém rozsahu problému.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Kontrola dostupnosti úložiště Azure – přidejte metriku účtu úložiště: dostupnost

Pokud se zobrazí pokles dostupnosti, může být problém s platformou, zkontrolujte [stav Azure](https://azure.microsoft.com/status/). Pokud se zde nezobrazuje žádný problém, vznesete novou žádost o podporu.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Zkontrolujte časový čas úložiště Azure – přidejte metriky účtu úložiště:

* Chyba clientTimeOut
* Chyba_časového_outu serveru
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Hodnoty v metrikách *TimeOutError označují, že operace vi trvalo příliš dlouho a časový čas. Práce na dalších krocích pomůže identifikovat potenciální příčiny.

AverageServerLatency zvyšuje současně na TimeOutErrors může být problém platformy. V této situaci vyvoláte novou žádost o podporu.

AverageE2ELatency představuje latenci klienta. Ověřte, jak jsou iOPS prováděny aplikací. Vyhledejte metriku zvýšení nebo neustále vysoké metriky TotalRequests. Tato metrika představuje iOPS. Pokud začínáte narazit na limity účtu úložiště nebo jednoho virtuálního pevného disku, latence může souviset s omezením.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Kontrola omezení úložiště Azure – přidejte metriky účtu úložiště: Omezení Error

Hodnoty pro omezení označuje, že jste omezeni na úrovni účtu úložiště, což znamená, že vaše dosažení limitu VOPS účtu. Můžete určit, zda jste dosažení prahové hodnoty iops kontrolou **metriky TotalRequests**.

Všimněte si, že každý virtuální pevný disk má limit 500 VOPS nebo 60 MBits, ale je vázán kumulativní limit 20000 VOPS na účet úložiště.

Pomocí této metriky nelze zjistit, který objekt blob způsobuje omezení a které jsou ovlivněny. Však jsou však buď přístupů vstupně-výstupní chod nebo odchozí omezení účtu úložiště.

Chcete-li zjistit, zda jste dosáhli limitu VOPS, přejděte do diagnostiky účtu úložiště a zkontrolujte TotalRequests, chcete-li zjistit, zda se blížíte 20 tisíc TotalRequests. Určete buď změnu vzoru, zda se vám limit zobrazuje poprvé, nebo zda k tomuto limitu dojde v určitou dobu.

S novými nabídkami disků v rámci standardního úložiště se limity VOPS a propustnost mohou lišit, ale kumulativní limit účtu standardstorage je 20000 IOPS (úložiště Premium má různé limity na úrovni účtu nebo disku). Přečtěte si další informace o různých standardních nabídkách disků úložiště a omezeních na disk:

* [Škálovatelnost a výkon cíle pro disky virtuálních počítačů v systému Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

#### <a name="references"></a>Odkazy

* [Škálovatelnost a výkonnostní cíle pro účty úložiště objektů blob stránky premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Šířka pásma účtu úložiště se měří metriky účtu úložiště: TotalIngress a TotalEgress. Máte různé prahové hodnoty pro šířku pásma v závislosti na typu redundance a oblastech.

* [Škálovatelnost a výkonnostní cíle pro účty standardního úložiště](../../storage/common/scalability-targets-standard-account.md)

Zkontrolujte TotalIngress a TotalEgress proti omezení příchozího přenosu dat a odchozího přenosu dat pro typ redundance účtu úložiště a oblast.

Zkontrolujte limity propustnost virtuálních disponiál připojených k virtuálnímu virtuálnímu virtuálnímu připojení. Přidejte disk čtení a zápis metrik virtuálních počítačů.

Nové nabídky disků v rámci standardního úložiště mají různé vipony a limity propustnost (vi.služby nejsou vystaveny na VHD). Podívejte se na data a zjistěte, jestli se dostanete na limity kombinované propustnostMB virtuálních pevných disků na úrovni virtuálních počítačů pomocí čtení a zápisu na disku, a pak optimalizujte konfiguraci úložiště virtuálních počítačů tak, aby se škálovala za limity jednoho virtuálního pevného disku. Přečtěte si další informace o různých standardních nabídkách disků úložiště a omezeních na disk:

* [Škálovatelnost a výkon cíle pro disky virtuálních počítačů v systému Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

### <a name="high-disk-utilizationlatency-remediation"></a>Vysoká obnova disku/latence

Snížení latence klienta a optimalizace iO virtuálních počítači pro škálování za limity virtuálního pevného disku

* [Optimalizace iO pro Windows v Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Optimalizace IO pro Linux v Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Snížení omezení

Pokud se dosáhne horní limity účtů úložiště, znovu vyvažte virtuální disky mezi účty úložiště. Podívejte se na [cíle škálovatelnosti úložiště Azure a výkonu](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Zvýšení propustnosti a snížení latence

Pokud máte aplikaci citlivou na latenci a vyžadujete vysokou propustnost, migrujte virtuální počítače do úložiště Azure Premium pomocí virtuálního počítače řady DS a GS.

Tyto články popisují konkrétní scénáře:

* [Migrace na Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Použití úložiště Azure Premium se serverem SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/).

Případně můžete podat incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
