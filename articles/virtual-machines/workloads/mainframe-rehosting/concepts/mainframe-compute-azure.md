---
title: Přesunout výpočetní sálové prostředí do Azure Virtual Machines
description: Výpočetní prostředky Azure porovnávají favorably s kapacitou sálového počítače, abyste mohli migrovat a modernizovat aplikace IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9c5941ec88cd793961ad66245d0dc0b5e0d7772f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998931"
---
# <a name="move-mainframe-compute-to-azure"></a>Přesunout do Azure výpočetní sálové počítače

Sálové počítače mají reputaci s vysokou spolehlivostí a dostupností a nadále se jedná o důvěryhodnou páteřní síť mnoha podniků. Často se jedná o skoro neomezenou škálovatelnost a výpočetní výkon. Některé podniky však vypěstují možnost největších dostupných sálových počítačů. Pokud se vám takové zvuky líbí, Azure nabízí flexibilitu, dosahování a úspory infrastruktury.

Pokud chcete provozovat sálové úlohy na Microsoft Azure, musíte znát, jak se výpočetní funkce vašeho sálového počítače porovnají s Azure. V tomto článku se dozvíte, jak získat srovnatelné výsledky v Azure na základě sálového počítače IBM Z14 (nejaktuálnějšího modelu v tomto zápisu).

Pokud chcete začít, zvažte prostředí vedle sebe. Následující obrázek porovnává sálové prostředí pro spouštění aplikací do hostitelského prostředí Azure.

![Prostředí služeb a emulace Azure nabízejí srovnatelnou podporu a zjednodušují migraci.](media/mainframe-compute-azure.png)

Výkon sálových počítačů se často používá pro systémy OLTP (online Transaction Processing), které zpracovávají miliony aktualizací pro tisíce uživatelů. Tyto aplikace často používají software pro zpracování transakcí, zpracování obrazovky a položku formuláře. Můžou používat systém CICS (Customer Information Control System), systém pro správu informací (IMS) nebo balíček rozhraní pro transakce (TIP).

Jak ukazuje obrázek, emulátor čipu TPM v Azure může zpracovávat úlohy CICS a IMS. Emulátor systému služby Batch v Azure provádí roli jazyka řízení úloh (JCL). Data z sálového počítače se migrují do databází Azure, například Azure SQL Database. Služby Azure nebo jiný software hostovaný ve službě Azure Virtual Machines lze použít pro správu systému.

## <a name="mainframe-compute-at-a-glance"></a>Na první pohled výpočetní sálový

V rámci sálového počítače Z14 jsou procesory uspořádány až do čtyř *zásuvk*. *Zásuvkou* je jenom cluster procesorů a chipsetů. Každý zásuvka může obsahovat šest čipy aktivního ústředního procesoru (CP) a každá z nich má 10 čipy systému (SC). V terminologii Intel x86 je k dispozici šest soketů na zásuvku, 10 jader na soket a čtyři zásuvky. Tato architektura poskytuje hrubý ekvivalent 24 soketů a 240 jader, maximálně pro Z14.

Rychlá Z14 CP má rychlost 5,2 GHz. Z14 se obvykle dodává se všemi CPs v poli. Ty se aktivují podle potřeby. Zákazníkovi se obvykle účtuje alespoň čtyři hodiny výpočetního času za měsíc navzdory skutečnému využití.

Sálový procesor lze nakonfigurovat jako jeden z následujících typů:

- Procesor Pro obecné účely (GP)
- Systém z integrovaného informačního procesoru (zIIP)
- IFL (Integrated Facility) pro procesory Linux
- Procesor (SAP) pro System Assist
- Procesor brány firewall pro integrované zařízení

## <a name="scaling-mainframe-compute-up-and-out"></a>Škálování a vyzkoušení sálového počítače

Sálové počítače IBM nabízejí možnost horizontálního navýšení kapacity až 240 jader (aktuální velikost Z14 pro jeden systém). Kromě toho je možné rozšířit sálové počítače IBM prostřednictvím funkce označované jako spojovací zařízení (CF). CF umožňuje více sálovým systémům současně přistupovat ke stejným datům. Pomocí CF, sálové sálové Sysplex technologie seskupují sálové procesory v clusterech. Po napsání této příručky funkce Parallel Sysplex podporovala 32 seskupení procesorů 64. Až 2 048 procesorů je možné seskupit tímto způsobem, abyste mohli škálovat výpočetní kapacitu.

CF umožňuje výpočetním clusterům sdílet data s přímým přístupem. Používá se pro uzamykání informací, informace o mezipaměti a seznam sdílených datových prostředků. Paralelní Sysplex s jedním nebo více CFs se dá představit jako sdílený cluster se škálováním na více instancí. Další informace o těchto funkcích naleznete v tématu [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) na webu IBM.

Aplikace můžou tyto funkce využívat k poskytování výkonu i vysoké dostupnosti. Informace o tom, jak může CICS použít paralelní Sysplex s CF, stáhnout [IBM CICS a zařízení pro spojení: mimo základy](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) standardu RedBook.

## <a name="azure-compute-at-a-glance"></a>Na první pohled na Azure COMPUTE

Někteří lidé omylem pomysleli, že servery se systémem Intel nejsou tak výkonné jako sálové počítače. Nové základní-husté systémy založené na platformě Intel ale mají tolik výpočetní kapacity jako sálové počítače. Tato část popisuje možnosti infrastruktury Azure jako služby (IaaS) pro výpočetní prostředí a úložiště. Azure poskytuje i možnosti typu platforma jako služba (PaaS), ale tento článek se zaměřuje na možnosti IaaS, které poskytují srovnatelnou kapacitu sálového počítače.

Azure Virtual Machines poskytuje výpočetní výkon v různých velikostech a typech. V Azure je virtuální procesor (vCPU) zhruba stejný jako jádro v rámci sálového počítače.

V současné době nabízí rozsah velikostí virtuálních počítačů Azure od 1 do 128 vCPU. Typy virtuálních počítačů jsou optimalizované pro konkrétní úlohy. Například v následujícím seznamu jsou uvedeny typy virtuálních počítačů (aktuální při psaní) a jejich Doporučené použití:

| Velikost     | Typ a popis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Řady D-Series | Obecné účely s 64 vCPU a taktem až 3,5 GHz                           |
| Řady E | Paměť optimalizovaná s až 64 vCPU                                                 |
| Řada F-Series | COMPUTE optimalizované s až 64 vCPU a 3 GHz... 7 GHz                       |
| Řada H-Series | Optimalizováno pro vysoce výkonné výpočetní aplikace (HPC)                          |
| Řady L | Úložiště optimalizované pro aplikace s vysokou propustností, které jsou založené na databázích, jako je NoSQL |
| Řada M | Největší výpočetní a paměťově optimalizované virtuální počítače s až 128 vCPU                        |

Podrobnosti o dostupných virtuálních počítačích najdete v tématu [řady virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Z14 sálové počítače může mít až 240 jader. Ale Z14 sálové počítače téměř nikdy nepoužívají všechny jádra pro jednu aplikaci nebo úlohu. Místo toho sálové počítače odděluje úlohy do logických oddílů (LPARs) a LPARs mají hodnocení – MIPS (miliony instrukcí za sekundu) nebo MSU (milion jednotek služby). Při určování srovnatelné velikosti virtuálních počítačů potřebných ke spuštění úlohy sálového počítače v Azure, faktoru v hodnocení MIPS (nebo MSU).

Níže jsou uvedené obecné odhady:

-   150 MIPS na vCPU

-   1 000 MIPS na procesor

K určení správné velikosti virtuálních počítačů pro danou úlohu ve LPAR je třeba nejprve optimalizovat virtuální počítač pro úlohu. Pak určete potřebný počet vCPU. Konzervativní odhad je 150 MIPS na vCPU. Na základě tohoto odhadu může například virtuální počítač řady F-Series s 16 vCPU snadno podporovat úlohy IBM Db2 přicházející z LPAR s 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Škálování Azure COMPUTE – nahoru

Virtuální počítače řady M-Series lze škálovat až na 128 vCPU (v době, kdy byl tento článek napsán). Při použití konzervativního odhadu 150 MIPS na vCPU se virtuální počítač řady M-Series rovná asi 19 000 MIPS. Obecné pravidlo pro odhad MIPS pro sálový disk je 1 000 MIPS na procesor. Z14 sálový počítač může mít až 24 procesorů a poskytuje asi 24 000 MIPS pro jeden sálový systém.

Největší jeden Z14 sálový počítač má přibližně 5 000 MIPS více než největší virtuální počítač dostupný v Azure. Je ale důležité porovnat způsob nasazení úloh. Pokud má sálový systém jak aplikaci, tak relační databázi, obvykle se nasazují na stejný fyzický sálový počítač – každý vlastní LPAR. Stejné řešení v Azure se často nasazuje pomocí jednoho virtuálního počítače pro aplikaci a samostatného virtuálního počítače s vhodnou velikostí pro databázi.

Například pokud systém M64 vCPU podporuje aplikaci a pro databázi se používá M96 vCPU, je třeba zadat přibližně 150 vCPU – nebo přibližně 24 000 MIPS, jak ukazuje následující obrázek.

![Porovnání nasazení úloh 24 000 MIPS](media/mainframe-compute-mips.png)

Přístup je k migraci LPARs na jednotlivé virtuální počítače. Azure se pak snadno škáluje až na velikost potřebnou pro většinu aplikací, které jsou nasazené v jednom sálovém systému.

## <a name="azure-compute-scale-out"></a>Azure COMPUTE Scale-out

Jednou z výhod řešení založeného na Azure je schopnost horizontálního navýšení kapacity. Škálování zpřístupňuje téměř neomezenou dostupnou výpočetní kapacitu pro aplikaci. Azure podporuje více metod pro horizontální navýšení výpočetní výkon:

- **Vyrovnávání zatížení napříč clusterem.** V tomto scénáři může aplikace pomocí nástroje [pro vyrovnávání zatížení](../../../../load-balancer/load-balancer-overview.md) nebo správce prostředků rozložit zatížení mezi několika virtuálními počítači v clusteru. Pokud potřebujete větší výpočetní kapacitu, přidají se do clusteru další virtuální počítače.

- **Sady škálování virtuálních počítačů.** V tomto scénáři shluku se aplikace může škálovat na další [výpočetní prostředky](../../../../virtual-machine-scale-sets/overview.md) na základě využití virtuálních počítačů. Pokud se poptávka napadne, může se také snížit počet virtuálních počítačů v sadě škálování, což zajistí efektivní využití výpočetního výkonu.

- **PaaS škálování.** Nabídky Azure PaaS škálují výpočetní prostředky. Například [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) přiděluje výpočetní prostředky pro splnění zvýšení objemu požadavků.

- **Clustery Kubernetes.** Aplikace v Azure můžou používat [clustery Kubernetes](../../../../aks/concepts-clusters-workloads.md) pro výpočetní služby pro zadané prostředky. Služba Azure Kubernetes Service (AKS) je spravovaná služba, která orchestruje uzly Kubernetes, fondy a clustery v Azure.

Pokud chcete zvolit správnou metodu pro škálování výpočetních prostředků, je důležité pochopit, jak se Azure a sálové počítače liší. Klíč je, jak nebo pokud – data se sdílejí pomocí výpočetních prostředků. V Azure nejsou data (ve výchozím nastavení) obvykle sdílená více virtuálními počítači. Pokud je sdílení dat vyžadováno více virtuálními počítači ve výpočetním clusteru se škálováním na více instancí, musí se sdílená data nacházet v prostředku, který tuto funkci podporuje. Sdílení dat v Azure zahrnuje úložiště, jak je popsáno v následující části.

## <a name="azure-compute-optimization"></a>Optimalizace výpočtů Azure

Jednotlivé úrovně zpracování můžete optimalizovat v architektuře Azure. Používejte nejvhodnější typ virtuálních počítačů a funkcí pro každé prostředí. Následující obrázek ukazuje jeden potenciální vzor pro nasazení virtuálních počítačů v Azure pro podporu aplikace CICS, která používá Db2. V primární lokalitě jsou nasazené produkční a testovací virtuální počítače s vysokou dostupností. Sekundární lokalita je určena pro zálohování a zotavení po havárii.

Jednotlivé úrovně můžou také poskytovat vhodné služby pro zotavení po havárii. Například provozní a databázové virtuální počítače mohou vyžadovat rychlé obnovení nebo zahřívání, zatímco virtuální počítače pro vývoj a testování podporují studenou obnovu.

![Vysoce dostupné nasazení, které podporuje zotavení po havárii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Další kroky

- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Opětovné hostování sálového počítače v Azure Virtual Machines](../overview.md)
- [Přesunout sálové úložiště do Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Prostředky IBM

- [Paralelní Sysplex v IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a spojovací zařízení: rámec základních](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytváření požadovaných uživatelů pro instalaci funkce Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – vytvoření instance – příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Řešení clusterové databáze Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government Cloud pro sálové aplikace](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Další zdroje migrace

- [Průvodce zvednutím a posunutím virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
