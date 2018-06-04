---
title: Azure Site Recovery Deployment Planner pro nasazení VMware do Azure | Microsoft Docs
description: Tento článek popisuje analýzu vygenerované sestavy Plánovače nasazení služby Azure Site Recovery pro scénář nasazení VMware do Azure.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: garavd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: 2d44d8410734193ba8b7fdda3134727d9064340c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195494"
---
# <a name="azure-site-recovery-deployment-planner-report"></a>Sestava Plánovače nasazení služby Azure Site Recovery
Vygenerovaná sestava aplikace Microsoft Excel obsahuje následující listy:
## <a name="on-premises-summary"></a>On-premises summary (Přehled místního prostředí)
List On-premises summary (Přehled místního prostředí) poskytuje přehled profilovaného prostředí VMware.

![On-premises summary (Přehled místního prostředí) pro prostředí VMware](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Start Date** a **End Date**: Počáteční a koncové datum dat profilace zahrnutých do generování sestavy. Ve výchozím nastavení je počátečním datem datum zahájení profilace a koncovým datem je datum zastavení profilace. Můžou to být hodnoty StartDate a EndDate, pokud se sestava generuje s použitím těchto parametrů.

**Total number of profiling days:** Celkový počet dnů profilace mezi počátečním a koncovým datem, pro které se generuje sestava.

**Number of compatible virtual machines:** Celkový počet kompatibilních virtuálních počítačů, pro které se počítá požadovaná šířka pásma sítě, požadovaný počet účtů úložiště, počet jader Microsoft Azure a konfiguračních serverů a dalších procesových serverů.

**Total number of disks across all compatible virtual machines** (Celkový počet disků ve všech kompatibilních virtuálních počítačích): Toto číslo se používá jako jeden ze vstupů pro určení počtu konfiguračních serverů a dalších procesových serverů, které se mají v nasazení použít.

**Average number of disks per compatible virtual machine:** Průměrný počet disků ve všech kompatibilních virtuálních počítačích.

**Average disk size (GB):** Průměrná velikost disků ve všech kompatibilních virtuálních počítačích.

**Desired RPO (minutes):** Buď výchozí cíl bodu obnovení, nebo hodnota předaná v parametru DesiredRPO při generování sestavy, sloužící k odhadu požadované šířky pásma.

**Desired bandwidth (Mbps):** Hodnota, kterou jste předali v parametru Bandwidth při generování sestavy, sloužící k odhadu dosažitelného cíle bodu obnovení.

**Observed typical data churn per day (GB):** Průměrná denní četnost změn dat vypozorovaná během všech dnů profilace. Toto číslo se používá jako jeden ze vstupů pro určení počtu konfiguračních serverů a dalších procesových serverů, které se mají v nasazení použít.

## <a name="recommendations"></a>Doporučení

List sestavy nasazení VMware do Azure s doporučeními obsahuje následující podrobné údaje pro vybraný požadovaný cíl bodu obnovení:

![Sestava doporučení pro nasazení VMware do Azure](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profilovaná data
![Zobrazení profilovaných dat v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Profiled data period:** Doba, po kterou byla profilace spuštěná. Nástroj ve výchozím nastavení do výpočtu zahrnuje všechna profilovaná data, pokud sestavu negeneruje pro konkrétní období díky použití možností StartDate a EndDate během generování sestavy.

**Server Name:** Název nebo IP adresa hostitele ESXi nebo VMware vCenter, pro jehož virtuální počítače se sestava generuje.

**Desired RPO:** Požadovaný cíl bodu obnovení pro vaše nasazení. Ve výchozím nastavení se požadovaná šířka pásma sítě počítá pro hodnoty cíle bodu obnovení 15, 30 a 60 minut. V závislosti na výběru se na listu aktualizují ovlivněné hodnoty. Pokud jste při generování sestavy použili parametr *DesiredRPOinMin*, jako výsledný požadovaný cíl bodu obnovení se zobrazí použitá hodnota.

### <a name="profiling-overview"></a>Přehled profilace

![Výsledky profilace v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total Profiled Virtual Machines:** Celkový počet virtuálních počítačů, jejichž profilovaná data jsou k dispozici. Pokud soubor VMListFile obsahuje názvy virtuálních počítačů, které nebyly profilované, nebude se na takové virtuální počítače brát ohled při generování sestav a nezahrnou se do celkového počtu profilovaných virtuálních počítačů.

**Compatible Virtual Machines:** Počet virtuálních počítačů, které lze chránit v Azure pomocí Site Recovery. Je to celkový počet kompatibilních virtuálních počítačů, pro které se počítá požadovaná šířka pásma sítě, požadovaný počet účtů úložiště, počet jader Azure a počet konfiguračních serverů a dalších procesových serverů. Podrobnosti o každém kompatibilním virtuálním počítači jsou dostupné v části Compatible VMs.

**Incompatible Virtual Machines:** Počet profilovaných virtuálních počítačů, které jsou nekompatibilní s ochranou pomocí Site Recovery. Důvody nekompatibility jsou uvedené v části Incompatible VMs. Pokud soubor VMListFile obsahuje názvy virtuálních počítačů, které nebyly profilované, nezahrnou se do celkového počtu nekompatibilních virtuálních počítačů. Takové virtuální počítače jsou uvedené jako „Data not found“ (Data nenalezena) na konci části Incompatible VMs.

**Desired RPO:** Požadovaný cíl bodu obnovení v minutách. Sestava se generuje pro tři hodnoty cíle bodu obnovení: 15 (výchozí), 30 a 60 minut. Doporučení šířky pásma v sestavě se mění v závislosti na výběru možnosti v rozevíracím seznamu Desired RPO v pravé horní části listu. Pokud jste sestavu vygenerovali s použitím parametru *-DesiredRPO* s vlastní hodnotou, v rozevíracím seznamu Desired RPO se tato vlastní hodnota zobrazí jako výchozí možnost.

### <a name="required-network-bandwidth-mbps"></a>Požadovaná šířka pásma sítě (Mb/s)

![Požadovaná šířka pásma sítě v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**To meet RPO 100 percent of the time** (Pro splnění cíle bodu obnovení 100 % času): Doporučená šířka pásma v Mb/s, kterou je potřeba přidělit pro splnění požadovaného cíle bodu obnovení 100 % času. Šířka pásma musí být vyhrazená pro zajištění stálé rozdílové replikace všech kompatibilních virtuálních počítačů, aby se předešlo jakémukoli narušení cíle bodu obnovení.

**To meet RPO 90 percent of the time** (Pro splnění cíle bodu obnovení 90 % času): Pokud kvůli cenám širokopásmového připojení nebo z jiného důvodu nemůžete nastavit šířku pásma potřebnou ke splnění požadovaného cíle bodu obnovení 100 % času, můžete se rozhodnout pro nastavení menší šířky pásma, která může splňovat požadovaný cíl bodu obnovení 90 % času. Abyste porozuměli dopadům nastavení menší šířky pásma, sestava poskytuje analýzu „co kdyby“ očekávaného počtu a trvání narušení cíle bodu obnovení.

**Achieved Throughput** (Dosažená propustnost): Propustnost ze serveru, na kterém jste spustili příkaz GetThroughput, do oblasti Microsoft Azure, ve které je umístěný účet úložiště. Tato hodnota propustnosti označuje odhadovanou úroveň propustnosti, které můžete dosáhnout při ochraně kompatibilních virtuálních počítačů pomocí Site Recovery za předpokladu, že charakteristiky sítě a úložiště konfiguračního serveru nebo procesového serveru budou stejné jako u serveru, ze kterého jste nástroj spustili.

Pro účely replikace byste měli nastavit šířku pásma doporučenou pro splnění cíle bodu obnovení 100 % času. Pokud se po nastavení šířky pásma nezvýší dosažená propustnost, kterou nástroj hlásí, proveďte následující:

1. Zkontrolujte, jestli v síti není nastavená technologie QoS (Quality of Service), která by omezovala propustnost Site Recovery.

2. Zkontrolujte, jestli je váš trezor Site Recovery v nejbližší podporované fyzické oblasti Microsoft Azure kvůli minimalizaci latence sítě.

3. Zkontrolujte charakteristiky vašeho místního úložiště a zjistěte, jestli můžete vylepšit hardware (například použít jednotky SSD místo HDD).

4. Změňte nastavení Site Recovery na procesovém serveru a [zvětšete šířku pásma sítě používanou k replikaci](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Pokud nástroj spouštíte na konfiguračním serveru nebo procesovém serveru, na kterém již jsou chráněné virtuální počítače, spusťte nástroj několikrát. Hodnota dosažené propustnosti se mění v závislosti na četnosti změn, které se v daném okamžiku zpracovávají.

Pro všechna podniková nasazení Site Recovery doporučujeme použít [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Požadované účty úložiště
Následující graf znázorňuje celkový počet účtů služby Storage (úrovně Standard a Premium) požadovaných k ochraně všech kompatibilních virtuálních počítačů. Informace o tom, jaký účet úložiště použít pro jednotlivé virtuální počítače, najdete v části VM-storage placement.

![Požadované účty úložiště v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Požadovaný počet jader Azure
Tento výsledek je celkový počet jader, která se mají nastavit před převzetím služeb při selhání nebo testovacím převzetím služeb při selhání všech kompatibilních virtuálních počítačů. Pokud v rámci předplatného není k dispozici dostatečný počet jader, Site Recovery během testovacího převzetí služeb při selhání nebo převzetí služeb při selhání nebude moci vytvořit virtuální počítače.

![Požadovaný počet jader Azure v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Požadovaná místní infrastruktura
Toto číslo je celkový počet konfiguračních serverů a dalších procesových serverů, které se mají nakonfigurovat a které budou stačit k zajištění ochrany všech kompatibilních virtuálních počítačů. V závislosti na podporované [doporučené velikosti pro konfigurační server](https://aka.ms/asr-v2a-on-prem-components) může nástroj doporučit přidání dalších serverů. Doporučení je založeno na větší z hodnot denní četnosti změn a maximálního počtu chráněných virtuálních počítačů (za předpokladu, že každý z nich obsahuje průměrně tři disky), podle toho, čeho konfigurační server nebo další procesový server dosáhne dříve. Podrobnosti o celkové denní četnosti změn a celkovém počtu chráněných disků najdete v části On-premises summary (Přehled místního prostředí).

![Požadovaná místní infrastruktura v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Analýza „co kdyby“
Tato analýza ukazuje, ke kolika narušením by mohlo dojít během období profilace, pokud nastavíte menší šířku pásma pro splnění požadovaného cíle bodu obnovení pouze 90 % času. Každý den může dojít k jednomu nebo několika narušením cíle bodu obnovení. Graf ukazuje pro každý den špičku cíle bodu obnovení.
Na základě této analýzy se můžete rozhodnout, jestli je se zadanou menší šířkou pásma přijatelný počet narušení cíle bodu obnovení ve všech dnech a denní dosažená špička cíle bodu obnovení. Pokud je to přijatelné, můžete pro replikaci přidělit menší šířku pásma, jinak podle návrhu přidělte větší šířku pásma pro splnění požadovaného cíle bodu obnovení 100 % času.

![Analýza „co kdyby“ v Deployment Planneru](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Doporučená velikost dávky virtuálních počítačů pro prvotní replikaci
V této části najdete doporučený počet virtuálních počítačů, které lze paralelně chránit, pro dokončení prvotní replikace během 72 hodin, a navrhovanou šířku pásma pro splnění požadovaného cíle bodu obnovení 100 % času. Tato hodnota je konfigurovatelná. Můžete ji změnit při generování sestavy pomocí parametru *GoalToCompleteIR*.

Zde uvedený graf na základě průměrné zjištěné velikosti kompatibilních virtuálních počítačů ukazuje rozsah hodnot šířky pásma a vypočítaného počtu virtuálních počítačů v dávce pro dokončení prvotní replikace během 72 hodin.

Ve verzi Public Preview sestava neudává, které virtuální počítače by měly být zahrnuté v dávce. Virtuální počítače pro dávku můžete vybrat na základě známých charakteristik úloh nebo podle velikosti jednotlivých virtuálních počítačů – tu můžete zjistit pomocí velikostí disků zobrazených v části Compatible VMs. Čas dokončení prvotní replikace se úměrně mění v závislosti na skutečné velikosti disku virtuálního počítače, využitém místu na disku a dostupné propustnosti sítě.

![Doporučená velikost dávky virtuálních počítačů](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Odhad nákladů
Graf zobrazuje přehled odhadovaných celkových nákladů na zotavení po havárii do Azure ve zvolené cílové oblasti a měnu, kterou jste zadali pro generování sestav.

![Souhrn odhadu nákladů](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Tento souhrn pomáhá porozumět nákladům, které budete muset platit za úložiště, výpočetní funkce, sítě a licence při ochraně všech kompatibilní virtuálních počítačů v Azure pomocí Azure Site Recovery. Náklady se počítají pro kompatibilní virtuální počítače, a ne pro všechny profilované virtuální počítače.  

Náklady můžete zobrazit po měsících nebo letech. Další informace o [podporovaných cílových oblastech](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) a [podporovaných měnách](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)

**Náklady podle komponent** Celkové náklady na zotavení po havárii se dělí do čtyř komponent: výpočetní služby, úložiště, síť a licenční náklady na Azure Site Recovery. Náklady se počítají na základě spotřeby, ke které dojde během replikace a v průběhu nácviku zotavení po havárii pro výpočetní funkce, úložiště (Premium a Standard), síť ExpressRoute nebo VPN, která je nakonfigurovaná mezi místní lokalitou a Azure, a licence Azure Site Recovery.

**Náklady podle stavu** Celkové náklady na zotavení po havárii jsou rozdělené do kategorií na základě dvou různých stavů – replikace a nácvik zotavení po havárii.

**Náklady na replikaci:** Náklady, které vzniknou během replikace. Zahrnují náklady na úložiště, síť a licence Azure Site Recovery.

**Náklady na nácvik zotavení po havárii:** Náklady, které vzniknou během testovacího převzetí služeb při selhání. Během testovacího převzetí služeb při selhání Azure Site Recovery rozjede virtuální počítače. Náklady na nácvik zotavení po havárii zahrnují náklady na výpočetní funkce a úložiště spuštěných virtuálních počítačů.

**Náklady na úložiště Azure za měsíc a rok:** Celkové náklady na úložiště, které vzniknou pro úložiště úrovně Standard a Premium při replikaci a nácviku zotavení po havárii.
Podrobnou analýzu nákladů na virtuální počítač najdete na listu s [odhadem nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Použitý faktor růstu a hodnoty percentilu
Tato oblast v dolní části listu ukazuje hodnotu percentilu použitou pro všechny čítače výkonu profilovaných virtuálních počítačů (výchozí je 95. percentil) a faktor růstu (výchozí hodnota je 30 %), který se používá ve všech výpočtech.

![Použitý faktor růstu a hodnoty percentilu](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Doporučení s možností zadat dostupnou šířku pásma jako vstup

![Doporučení s možností zadat dostupnou šířku pásma jako vstup](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Může nastat situace, kdy víte, že pro účely replikace Site Recovery nemůžete nastavit šířku pásma větší než x Mb/s. Tento nástroj umožňuje zadat dostupnou šířku pásma (pomocí parametru -Bandwidth během generování sestavy) a zjistit dosažitelný cíl bodu obnovení v minutách. Na základě této hodnoty dosažitelného cíle bodu obnovení se můžete rozhodnout, jestli potřebujete nastavit větší šířku pásma, nebo vám vyhovuje řešení zotavení po havárii s tímto cílem bodu obnovení.

![Dosažitelný cíl bodu obnovení pro šířku pásma 500 Mb/s](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Umístění virtuálních počítačů v účtech úložiště

![Umístění virtuálních počítačů v účtech úložiště](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)


  **Disk Storage Type:** Účet služby Storage úrovně Standard nebo Premium, který slouží k replikaci všech odpovídajících virtuálních počítačů uvedených ve sloupci **VMs to Place** (Virtuální počítače k umístění).

**Suggested Prefix:** Navrhovaná tříznaková předpona, kterou můžete použít k pojmenování účtu úložiště. Můžete použít vlastní předponu, ale návrh nástroje se řídí [zásadami vytváření názvů pro oddíly účtů úložiště](https://aka.ms/storage-performance-checklist).

**Suggested Account Name:** Název účtu úložiště po zahrnutí navrhované předpony. Název v ostrých závorkách (< a >) nahraďte vlastním názvem.

**Log Storage Account:** Všechny protokoly replikace se ukládají v účtu služby Storage úrovně Standard. Pro virtuální počítače, které se replikují do účtu služby Premium Storage, nastavte další účet služby Storage úrovně Standard pro ukládání protokolů. Jeden účet úložiště protokolů úrovně Standard může využívat více účtů úložiště replikace úrovně Premium. Virtuální počítače replikované do účtů úložiště úrovně Standard používají stejný účet i k ukládání protokolů.

**Suggested Log Account Name:** Název účtu úložiště protokolů po zahrnutí navrhované předpony. Název v ostrých závorkách (< a >) nahraďte vlastním názvem.

**Placement Summary:** Souhrn celkové zátěže virtuálních počítačů na účet úložiště v době replikace a testovacího převzetí služeb při selhání nebo převzetí služeb při selhání. Zahrnuje celkový počet virtuálních počítačů namapovaných na účet úložiště, celkový počet R/W IOPS ve všech virtuálních počítačích umístěných v tomto účtu úložiště, celkový počet vstupně-výstupních operací zápisu (replikace) za sekundu, celkovou nastavenou velikost všech disků a celkový počet disků.

**Virtual Machines to Place:** Seznam všech virtuálních počítačů, které by se měly umístit do daného účtu úložiště pro zajištění optimálního výkonu a využití.

## <a name="compatible-vms"></a>Kompatibilní virtuální počítače
![Tabulka aplikace Excel s kompatibilními virtuálními počítači](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM Name:** Název nebo IP adresa virtuálního počítače, které se použily v souboru VMListFile při generování sestavy. V tomto sloupci jsou uvedeny také disky (VMDK) připojené k virtuálním počítačům. Aby se rozlišily virtuální počítače vCenter s duplicitními názvy nebo IP adresami, je součástí názvů i název hostitele ESXi. Uvedený hostitel ESXi je ten, na kterém byl virtuální počítač umístěn při zjištění nástrojem během období profilace.

**VM Compatibility** (Kompatibilita virtuálního počítače): Hodnoty jsou **Yes** (Ano) a **Yes**\* (Ano). **Yes**\* je pro situace, kdy je virtuální počítač vhodný pro službu [Azure Premium Storage](https://aka.ms/premium-storage-workload). V takovém případě profilovaný disk s vysokou četností změn nebo vysokým počtem IOPS spadá do kategorie P20 nebo P30, ale kvůli velikosti se disk mapuje na nižší kategorii P10 nebo P20. Účet úložiště určuje, na jaký disk služby Premium Storage se disk bude mapovat, na základě jeho velikosti. Příklad:
* Menší než 128 GB je P10.
* 128 GB až 256 GB je P15.
* 256 GB až 512 GB je P20.
* 512 GB až 1 024 GB je P30.
* 1 025 GB až 2 048 GB je P40.
* 2 049 GB až 4 095 GB je P50.

Pokud se například díky charakteristikám úloh disk umístil do kategorie P20 nebo P30, ale kvůli velikosti je mapován na nižší typ disku služby Premium Storage, nástroj označí tento virtuální počítač jako **Yes**\*. Nástroj také doporučí změnit velikost zdrojového disku tak, aby se vešel do doporučeného typu disku služby Premium Storage, nebo po převzetí služeb při selhání změnit typ cílového disku.

**Storage Type** (Typ služby Storage): Standard nebo Premium.

**Suggested Prefix:** Tříznaková předpona účtu úložiště.

**Storage Account** (Účet služby Storage): Název s použitím navrhované předpony účtu úložiště.

**Peak R/W IOPS (with Growth Factor)**: Počet vstupně-výstupních operací čtení a zápisu na disku ve špičce (výchozí 95. percentil), včetně faktoru budoucího růstu (výchozí hodnota 30 %). Všimněte si, že celkový počet R/W IOPS virtuálního počítače nebude vždy odpovídat součtu R/W IOPS jednotlivých disků virtuálního počítače, protože počet R/W IOPS virtuálního počítače ve špičce je maximální hodnota součtu R/W IOPS jeho jednotlivých disků v každé minutě období profilace.

**Peak Data Churn in Mbps (with Growth Factor):** Četnost změn dat na disku ve špičce (výchozí 95. percentil), včetně faktoru budoucího růstu (výchozí hodnota 30 %). Všimněte si, že celková četnost změn dat virtuálního počítače nebude vždy odpovídat součtu četností změn dat jednotlivých disků virtuálního počítače, protože četnost změn dat virtuálního počítače ve špičce je maximální hodnota součtu četností změn jeho jednotlivých disků v každé minutě období profilace.

**Azure VM Size:** Ideální velikost mapovaného virtuálního počítače Azure Cloud Services pro tento místní virtuální počítač. Mapování vychází z velikosti paměti, počtu disků, jader nebo síťových adaptérů a počtu R/W IOPS místního virtuálního počítače. Doporučení vždy představuje nejmenší velikost virtuálního počítače Azure, která odpovídá všem charakteristikám místního virtuálního počítače.

**Number of Disks:** Celkový počet disků (VMDK) ve virtuálním počítači.

**Disk size (GB):** Celková nastavená velikost všech disků virtuálního počítače. Nástroj ukazuje také velikosti jednotlivých disků ve virtuálním počítači.

**Cores:** Počet procesorových jader ve virtuálním počítači.

**Memory (MB):** Velikost paměti RAM ve virtuálním počítači.

**NICs:** Počet síťových adaptérů ve virtuálním počítači.

**Boot Type:** Typ spuštění virtuálního počítače. Může to být buď BIOS, nebo EFI.  Azure Site Recovery v současnosti podporuje virtuální počítače EFI s Windows Serverem (Windows Server 2012, 2012 R2 a 2016), za předpokladu, že počet oddílů spouštěcího disku je menší než 4 a velikost spouštěcího sektoru je 512 bajtů. Pro zajištění ochrany virtuálních počítačů EFI musí být služba mobility Azure Site Recovery ve verzi 9.13 nebo vyšší. Pro virtuální počítače EFI se podporuje jenom převzetí služeb při selhání. Navrácení služeb po obnovení se nepodporuje.  

**OS Type:** Jde o typ operačního systému virtuálního počítače. Může to být Windows, Linux nebo jiný systém, v závislosti na šabloně zvolené ve VMware vSphere při vytváření virtuálního počítače.  

## <a name="incompatible-vms"></a>Nekompatibilní virtuální počítače

![Tabulka aplikace Excel s nekompatibilními virtuálními počítači
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM Name:** Název nebo IP adresa virtuálního počítače, které se použily v souboru VMListFile při generování sestavy. V tomto sloupci jsou uvedeny také disky VMDK připojené k virtuálním počítačům. Aby se rozlišily virtuální počítače vCenter s duplicitními názvy nebo IP adresami, je součástí názvů i název hostitele ESXi. Uvedený hostitel ESXi je ten, na kterém byl virtuální počítač umístěn při zjištění nástrojem během období profilace.

**VM Compatibility:** Označujte, proč je daný virtuální počítač nekompatibilní se Site Recovery. Pro každý nekompatibilní disk virtuálního počítače jsou popsané důvody. V závislosti na publikovaných [omezeních úložiště](https://aka.ms/azure-storage-scalbility-performance) může důvodem být některá z následujících možností:

* Disk je větší než 4 095 GB. Azure Storage v současné době nepodporuje disky větší než 4 095 GB.

* Disk s operačním systémem je větší než 2 048 GB. Azure Storage v současné době nepodporuje disky s operačním systémem větší než 2 048 GB.

* Total VM size (replication + TFO) exceeds the supported storage-account size limit (35 TB) (Celková velikost virtuálního počítače (replikace + testovací převzetí služeb při selhání) překračuje omezení podporované velikosti účtu úložiště (35 TB)). K této nekompatibilitě obvykle dochází, když je ve virtuálním počítači jeden disk, jehož některé charakteristiky výkonu překračují maximální podporovaná omezení systému Azure nebo Site Recovery pro účet služby Storage úrovně Standard. V takové situaci se virtuální počítač dostává do zóny účtu služby Premium Storage. Nicméně maximální podporovaná velikost účtu služby Premium Storage je 35 TB a jeden virtuální počítač nemůže být chráněn v rámci několika účtů úložiště. Všimněte si také, že testovací převzetí služeb při selhání spuštěné na chráněném virtuálním počítači poběží v rámci stejného účtu úložiště, ve kterém probíhá replikace. V takovém případě nastavte dvojnásobnou velikost disku, aby paralelně mohla probíhat replikace a úspěšné testovací převzetí služeb při selhání.

* Source IOPS exceeds supported storage IOPS limit of 7500 per disk (Počet zdrojových IOPS překračuje podporované omezení úložiště – 7 500 IOPS na disk).

* Source IOPS exceeds supported storage IOPS limit of 80,000 per VM (Počet zdrojových IOPS překračuje podporované omezení úložiště – 80 000 IOPS na virtuální počítač).

* Average data churn exceeds supported Site Recovery data churn limit of 10 MB/s for average I/O size for the disk (Průměrná četnost změn dat překračuje podporované omezení Site Recovery pro četnost změn dat – průměrná velikost vstupně-výstupních operací disku 10 MB/s).

* Průměrná četnost změn dat překračuje podporované omezení Site Recovery pro četnost změn dat, které je 25 MB/s pro průměrnou velikost vstupně-výstupních operací na virtuální počítač (součet četností všech disků).

* Četnost změn dat ve špičce na všech discích virtuálního počítače překračuje maximální podporované omezení Site Recovery pro četnost změn dat, které je 54 MB/s na virtuální počítač.

* Average effective write IOPS exceeds the supported Site Recovery IOPS limit of 840 for disk (Průměrný počet efektivních vstupně-výstupních operací zápisu za sekundu překračuje podporované omezení Site Recovery pro počet IOPS – 840 na disk).

* Calculated snapshot storage exceeds the supported snapshot storage limit of 10 TB (Vypočtená velikost úložiště snímků překračuje podporované omezení velikosti úložiště snímků – 10 TB).

* Celková četnost změn dat překračuje podporovaný limit četnosti změn za den, který je 2 TB na procesový server.


**Peak R/W IOPS (with Growth Factor):** Počet IOPS na disku ve špičce (výchozí 95. percentil), včetně faktoru budoucího růstu (výchozí hodnota 30 %). Všimněte si, že celkový počet R/W IOPS virtuálního počítače nebude vždy odpovídat součtu R/W IOPS jednotlivých disků virtuálního počítače, protože počet R/W IOPS virtuálního počítače ve špičce je maximální hodnota součtu R/W IOPS jeho jednotlivých disků v každé minutě období profilace.

**Peak Data Churn in Mbps (with Growth Factor):** Četnost změn dat na disku ve špičce (výchozí 95. percentil), včetně faktoru budoucího růstu (výchozí hodnota 30 %). Všimněte si, že celková četnost změn dat virtuálního počítače nebude vždy odpovídat součtu četností změn dat jednotlivých disků virtuálního počítače, protože četnost změn dat virtuálního počítače ve špičce je maximální hodnota součtu četností změn jeho jednotlivých disků v každé minutě období profilace.

**Number of Disks:** Celkový počet disků VMDK ve virtuálním počítači.

**Disk size (GB):** Celková nastavená velikost všech disků virtuálního počítače. Nástroj ukazuje také velikosti jednotlivých disků ve virtuálním počítači.

**Cores:** Počet procesorových jader ve virtuálním počítači.

**Memory (MB):** Velikost paměti RAM ve virtuálním počítači.

**NICs:** Počet síťových adaptérů ve virtuálním počítači.

**Boot Type:** Typ spuštění virtuálního počítače. Může to být buď BIOS, nebo EFI.  Azure Site Recovery v současnosti podporuje virtuální počítače EFI s Windows Serverem (Windows Server 2012, 2012 R2 a 2016), za předpokladu, že počet oddílů spouštěcího disku je menší než 4 a velikost spouštěcího sektoru je 512 bajtů. Pro zajištění ochrany virtuálních počítačů EFI musí být služba mobility Azure Site Recovery ve verzi 9.13 nebo vyšší. Pro virtuální počítače EFI se podporuje jenom převzetí služeb při selhání. Navrácení služeb po obnovení se nepodporuje.

**OS Type:** Jde o typ operačního systému virtuálního počítače. Může to být Windows, Linux nebo jiný systém, v závislosti na šabloně zvolené ve VMware vSphere při vytváření virtuálního počítače.

## <a name="azure-site-recovery-limits"></a>Omezení Azure Site Recovery
Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení se zakládají na našich testováních, nemůžou však pokrýt všechny možné kombinace vstupně-výstupních operací aplikace. Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace. Pro dosažení co nejlepších výsledků, a to i po naplánování nasazení, vždy doporučujeme provádět rozsáhlé testování aplikace pomocí testovacího převzetí služeb při selhání, abyste získali skutečnou představu o výkonu aplikace.

**Cíl ukládání replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |10 MB/s | 842 GB na disk

**Četnost změn dat zdroje** | **Maximální limit**
---|---
Průměrná četnost změn dat na virtuální počítač| 25 MB/s
Četnost změn dat ve špičce napříč všemi disky virtuálního počítače | 54 MB/s
Maximální četnost změn dat za den s podporou procesového serveru | 2 TB

Toto jsou průměrné hodnoty za předpokladu, že se vstupně-výstupní operace z 30 % překrývají. Služba Site Recovery je schopna zpracovávat větší propustnost v závislosti na poměru překrývání, větší velikosti zápisů a skutečného chování vstupně-výstupních operací úloh. Předchozí čísla předpokládají typický backlog přibližně 5 minut. To znamená, že zpracování nahrávaných dat a vytvoření bodu obnovení proběhne do pěti minut od nahrání.


## <a name="cost-estimation"></a>Odhad nákladů
Další informace o [odhadu nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md)


## <a name="next-steps"></a>Další kroky
Další informace o [odhadu nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md)
