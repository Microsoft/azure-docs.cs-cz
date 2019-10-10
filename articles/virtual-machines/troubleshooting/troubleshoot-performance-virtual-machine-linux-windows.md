---
title: Řešení obecných potíží s výkonem pro virtuální počítač Azure se systémem Linux nebo Windows
description: Tento článek popisuje obecný Poradce při potížích s výkonem virtuálních počítačů prostřednictvím monitorování a dodržování kritických bodů a poskytuje možnou nápravu problémů, ke kterým může dojít.
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
ms.openlocfilehash: fc8cc4834997033203376cd33670cc907e2911e7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170300"
---
# <a name="generic-performance-troubleshooting-for-azure-virtual-machine-running-linux-or-windows"></a>Řešení obecných problémů s výkonem virtuálního počítače Azure se systémem Linux nebo Windows

Tento článek popisuje obecný Poradce při potížích s výkonem virtuálních počítačů prostřednictvím monitorování a dodržování kritických bodů a poskytuje možnou nápravu problémů, ke kterým může dojít.

## <a name="enabling-monitoring"></a>Povolení monitorování

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorování virtuálních počítačů Azure IAAS

Pokud chcete monitorovat virtuální počítač hosta, použijte monitorování virtuálních počítačů Azure, které vás upozorní na určité podmínky prostředků na vysoké úrovni. Pokud chcete zjistit, jestli máte zapnutou diagnostiku virtuálních počítačů, přečtěte si téma [Přehled protokolů prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#collecting-resource-logs). Pokud se zobrazí následující informace, pravděpodobně nemáte povolenou diagnostiku:

![Monitorování není povoleno.](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Povolení diagnostiky virtuálních počítačů prostřednictvím Microsoft Azure Portal

Pokud chcete povolit diagnostiku virtuálních počítačů, přejděte na virtuální počítač, klikněte na **Nastavení**a potom klikněte na **Diagnostika**.

![Klikněte na nastavení a potom na Diagnostika.](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)
 
### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Povolit diagnostiku účtu úložiště prostřednictvím Azure Portal

Nejdřív pomocí výběru virtuálního počítače určete, který účet úložiště (nebo účty) váš virtuální počítač používá. Klikněte na **Nastavení**a potom na **disky**:

![Klikněte na nastavení a potom na disky.](media/troubleshoot-performance-virtual-machine-linux-windows/3-storage-disks-disks-selection.png)

Na portálu přejdete do účtu úložiště (nebo účtů) pro virtuální počítač a provedete následující kroky:

![Vybrat metriky objektů BLOB](media/troubleshoot-performance-virtual-machine-linux-windows/4-select-blob-metrics.png)
 
1. Vyberte **všechna nastavení**.
2. Zapnout diagnostiku.
3. Vyberte *metriky *objektů BLOB** * a nastavte uchování na **30** dní.
4. Uložte změny.

## <a name="observing-bottlenecks"></a>Pozorování kritických bodů

### <a name="accessing-the-monitoring"></a>Přístup k monitorování

Vyberte virtuální počítač Azure, který chcete prozkoumat, a vyberte **monitorování**.

![Vybrat monitorování](media/troubleshoot-performance-virtual-machine-linux-windows/5-observe-monitoring.png)
 
### <a name="timelines-of-observation"></a>Časové osy pozorování

Pokud chcete zjistit, jestli máte nějaké kritické body prostředků, Projděte si data. Pokud zjistíte, že váš počítač běží správně, ale ohlásil, že nedávno došlo ke snížení výkonu, zkontrolujte časový rozsah dat, který zahrnuje data metriky výkonu před nahlášeným stavem, během a po problému.

### <a name="check-for-cpu-bottleneck"></a>Vyhledat kritická místa procesoru

![Vyhledat kritická místa procesoru](media/troubleshoot-performance-virtual-machine-linux-windows/6-cpu-bottleneck-time-range.png)

1. Upravte graf.
2. Nastavte časový rozsah.
3. Pak je nutné přidat do čítače: procento využití CPU hostovaného operačního systému
4. Uložte.

### <a name="cpu-observe-trends"></a>Sledování trendů procesoru

Při hledání potíží s výkonem si pamatujte na trendy a zjistěte, jestli vás na ně mají vliv. V dalších částech budeme k zobrazení trendů používat grafy monitorování z portálu. Můžou to být užitečné i při křížovém odkazování na chování prostředků ve stejném časovém období. Pokud chcete grafy přizpůsobit, klikněte na [Azure monitor datovou platformu](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking – spiking může souviset s plánovanou úlohou nebo známou událostí. Pokud můžete úlohu identifikovat, zjistěte, jestli je úloha spuštěná na požadované úrovni výkonu. Pokud je výkon přijatelný, možná nebudete muset zvyšovat prostředky.

Špička nahoru a konstantní – často označuje novou úlohu. Pokud se nejedná o rozpoznanou úlohu, povolte monitorování na virtuálním počítači, abyste zjistili, jaký proces (nebo procesy) způsobuje chování. Po rozpoznání procesu Zjistěte, zda je zvýšená spotřeba způsobena neefektivním kódem nebo normální spotřebou. Pokud se jedná o normální spotřebu, rozhodněte se, jestli proces funguje na požadované úrovni výkonu.

Konstanta – určuje, jestli se váš virtuální počítač vždycky spouštěl na této úrovni, nebo jestli se spustil jenom na této úrovni, protože byla povolená Diagnostika. Pokud ano, identifikujte proces (nebo procesy), které způsobují problém, a zvažte přidání dalších prostředků.

Ustálené zvyšování – stálé zvýšení spotřeby je často buď neefektivní kód, nebo proces, který přebírá více uživatelských úloh.

### <a name="high-cpu-utilization-remediation"></a>Vysoká náprava využití procesoru

Pokud vaše aplikace nebo proces neběží na správné úrovni výkonu a vidíte 95% + konstanta využití procesoru, můžete provést jednu z následujících úloh:

* Okamžité zvýšení velikosti virtuálního počítače na velikost s více jádry
* Pochopení problému – vyhledání aplikace nebo procesu a odpovídajícím způsobem odstraňování potíží.

Pokud jste virtuální počítač zvýšili a procesor stále běží 95%, určete, zda toto nastavení nabízí vyšší výkon nebo vyšší propustnost aplikace na přijatelnou úroveň. V takovém případě řešení potíží s jednotlivými application\process.

## <a name="check-for-memory-bottleneck"></a>Vyhledat kritická místa pro paměť

Zobrazení metrik:

1. Přidejte oddíl.
2. Přidejte dlaždici.
3. Otevřete galerii.
4. Vyberte využití paměti a přetáhněte je. Když je dlaždice ukotvená, klikněte pravým tlačítkem a vyberte **6x4**.

### <a name="memory-observe-trends"></a>Trendy ve sledování paměti

Využití paměti ukazuje, kolik paměti je ve virtuálním počítači spotřebováno. Seznámení s trendem a informace o tom, jestli se namapuje na čas, ve kterém se zobrazují problémy Vždy byste měli mít více než 100 MB dostupné paměti.

Špička a konstantní/konstantní stabilní spotřeba – vysoké využití paměti nemusí způsobovat špatný výkon, protože některé aplikace, jako například relační databázové moduly, přidělují velké množství paměti a toto využití nemusí být významné. Pokud ale existuje několik aplikací náročné paměti, můžete se setkat s nízkým výkonem od kolizí paměti, které způsobuje ořezávání a stránkování na disk a jejich výměnu. Tento špatný výkon je často znatelné příčinou dopadu aplikace na výkon.

Neustálé zvyšování spotřeby – možná aplikace "zahřívání", tato spotřeba je společná mezi databázovými moduly, které se spouštějí. Může se ale také jednat o znaménko nevracení paměti v aplikaci. Identifikujte aplikaci a zjistěte, jestli je očekávané chování.

Použití stránky nebo odkládacího souboru – ověřte, zda používáte stránkovací soubor systému Windows (umístěný na D: \) nebo v systému Linux odkládací soubor (umístěný na `/dev/sdb`) jsou silně využívány. Pokud na těchto svazcích nemáte nic s výjimkou těchto souborů, vyhledejte na těchto discích vysokou čitelnost a zápis. Tento problém je informativní v podmínkách nedostatku paměti.

### <a name="high-memory-utilization-remediation"></a>Náprava využití vysoké paměti

Chcete-li vyřešit vysoké využití paměti, proveďte některou z následujících úloh:

* Pro okamžité použití nebo stránkovací nebo odkládací soubor – zvyšte velikost virtuálního počítače na jednu a pak Sledujte.
* Pochopení problému – vyhledání aplikací/procesů a řešení potíží při určování vysoce náročných paměťových aplikací.
* Pokud aplikaci znáte, přečtěte si, jestli je možné omezené přidělení paměti.

Pokud po upgradu na větší virtuální počítač, zjistíte, že stále máte konstantní stabilní zvýšení až do 100%, určete aplikaci nebo proces a odstraňte potíže.

## <a name="check-for-disk-bottleneck"></a>Vyhledat kritický bod disku

Pokud chcete kontrolovat podsystém úložiště pro virtuální počítač, podívejte se na diagnostiku na úrovni virtuálního počítače Azure pomocí čítačů v části Diagnostika virtuálních počítačů a také diagnostiky účtu úložiště.

Všimněte si, že pro účty redundantní a Premium Storage pro zónu nejsou k dispozici čítače. V případě problémů souvisejících s těmito čítači můžete vyvolat případ podpory.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Zobrazení diagnostiky účtu úložiště v monitorování

Pokud chcete pracovat s níže uvedenými položkami, přečtěte si účet úložiště pro virtuální počítač na portálu:

![Zobrazení diagnostiky účtu úložiště v monitorování](media/troubleshoot-performance-virtual-machine-linux-windows/7-virtual-machine-storage-account.png)

1. Upravte graf monitorování.
2. Nastavte časový rozsah.
3. Přidejte čítače popsané v následujících krocích.
4. Uložte změny.

### <a name="disk-observe-trends-standard-storage-only"></a>Trendy sledovat disk (jenom standardní úložiště)

Pokud chcete identifikovat problémy s úložištěm, podívejte se na metriky výkonu z diagnostiky účtu úložiště a diagnostiku virtuálních počítačů.

U každé následující kontroly hledejte klíčové trendy v době, kdy dojde k problémům v časovém rozsahu problému.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Ověření dostupnosti Azure Storage – přidání metriky účtu úložiště: dostupnost

Pokud se vám zobrazí dostupnost při odkládacím programu, může se jednat o problém s platformou. Zkontrolujte [stav Azure](https://azure.microsoft.com/status/). Pokud zde není zobrazen žádný problém, vyvolejte novou žádost o podporu.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Zjistit časový limit Azure Storage – přidejte metriky účtu úložiště:

* ClientTimeOutError
* ServerTimeOutError
* Hodnotu averagee2elatency
* Hodnotu averageserverlatency
* TotalRequests

Hodnoty v metrikách * TimeOutError označují, že vstupně-výstupní operace trvaly příliš dlouho a vypršel časový limit. Při práci prostřednictvím dalších kroků vám pomůže identifikovat možné příčiny.

Hodnotu averageserverlatency se zvyšuje ve stejnou dobu, kdy TimeOutErrors může představovat problém platformy. V této situaci vyvolat novou žádost o podporu.

Hodnotu averagee2elatency představuje latenci klienta. Ověřte, jak aplikace provádí IOPS. Vyhledejte zvýšení nebo nepřetržitou TotalRequests metriku. Tato metrika představuje IOPS. Pokud začínáte s limity účtu úložiště nebo s jedním virtuálním pevným diskem, může latence souviset s omezením.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Ověřit omezení Azure Storage – přidejte metriky účtu úložiště: ThrottlingError

Hodnoty pro omezování signalizují, že budete omezovat na úrovni účtu úložiště, což znamená, že váš účet zasáhne limit IOPS. Zaškrtnutím metriky **TotalRequests**můžete zjistit, jestli jste nedosáhli prahové hodnoty IOPS.

Všimněte si, že každý virtuální pevný disk má omezení 500 IOPS nebo 60 MBit, ale je vázaný na kumulativní limit 20000 IOPS na účet úložiště.

Pomocí této metriky nemůžete zjistit, které objekty blob způsobují omezení a které jsou ovlivněny. Buď ale zasáhnete limity IOPS nebo příchozí/odchozí přenos dat účtu úložiště.

Pokud chcete zjistit, jestli jste nedosáhli limitu IOPS, přejděte do části Diagnostika účtu úložiště a podívejte se na TotalRequests, kde se můžete podívat, jestli se k 20000 TotalRequests dostanete. Identifikujte změnu ve vzoru bez ohledu na to, jestli se limit zobrazuje poprvé, nebo jestli tento limit nastane v určitou dobu.

#### <a name="references"></a>Odkazy

* [Cíle škálovatelnosti pro disky virtuálních počítačů](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

Šířka pásma účtu úložiště se měří na základě metrik účtu úložiště: Totalbillablerequests a TotalEgress. V závislosti na typu redundance a oblastí máte jiné prahové hodnoty pro šířku pásma.

* [Cíle škálovatelnosti pro objekty blob, fronty, tabulky a soubory](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

Pro typ a oblast redundance účtu úložiště se podívejte na Totalbillablerequests a TotalEgress proti omezením příchozího a odchozího přenosu účtu úložiště.

Ověřte propustnost všech virtuálních pevných disků připojených k virtuálnímu počítači. Přidejte disk metriky virtuálního počítače čtení a zápis.

Každý virtuální pevný disk může podporovat až 60 MB/s (IOPS se nezveřejňují na virtuální pevný disk). Podívejte se na data, abyste viděli, jestli jste nedosáhli limitu celkové propustnosti disků VHD na úrovni virtuálních počítačů pomocí čtení a zápisu z disku, a pak Optimalizujte konfiguraci úložiště virtuálních počítačů tak, aby bylo možné škálovat minulé limity pro jedno virtuální pevné disky.

### <a name="high-disk-utilizationlatency-remediation"></a>Vysoké využití disku/náprava latence

Snížení latence klienta a optimalizace vstupně-výstupních operací virtuálního počítače pro škálování minulých limitů VHD

* [Optimalizace vstupně-výstupních operací pro Windows v Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Optimalizace vstupně-výstupních operací pro Linux v Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Omezení omezování

Pokud jste dosáhli horních limitů účtů úložiště, znovu vyvážit virtuální pevné disky mezi účty úložiště. Přečtěte si [Azure Storage škálovatelnost a výkonnostní cíle](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Zvýšení propustnosti a snížení latence

Pokud máte aplikaci citlivou na latenci a potřebujete vysokou propustnost, migrujte virtuální pevné disky do Azure Premium Storage pomocí virtuálního počítače DS a GS Series.

Tyto články popisují konkrétní scénáře:

* [Migrace na Azure Premium Storage](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Použití Azure Premium Storage s SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a Stack Overflow fórech](https://azure.microsoft.com/support/forums/).

Případně můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
