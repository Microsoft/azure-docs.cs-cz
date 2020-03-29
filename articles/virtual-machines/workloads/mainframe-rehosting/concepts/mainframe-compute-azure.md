---
title: Přesunutí výpočetních prostředků sálového počítače do virtuálních počítačů Azure
description: Výpočetní prostředky Azure se příznivě porovnávají s kapacitou sálových počítačů, takže můžete migrovat a modernizovat aplikace IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288927"
---
# <a name="move-mainframe-compute-to-azure"></a>Přesunutí výpočetních prostředků sálového počítače do Azure

Sálové počítače mají pověst vysoké spolehlivosti a dostupnosti a jsou i nadále důvěryhodnou páteří mnoha podniků. Často se předpokládá, že mají téměř neomezenou škálovatelnost a výpočetní výkon. Některé podniky však přerostly schopnosti největších dostupných sálových počítačů. Pokud to zní jako vy, Azure nabízí flexibilitu, dosah a úspory infrastruktury.

Chcete-li spustit úlohy sálových počítačů v Microsoft Azure, musíte vědět, jak se výpočetní možnosti vašeho sálového počítače srovnávají s Azure. Na základě sálového počítače IBM z14 (nejnovější model k tomuto psaní) tento článek vám řekne, jak získat srovnatelné výsledky v Azure.

Chcete-li začít, zvažte prostředí vedle sebe. Následující obrázek porovnává prostředí sálových počítačů pro spouštění aplikací s hostitelským prostředím Azure.

![Prostředí služeb Azure a emulace nabízejí srovnatelnou podporu a zjednodušují migraci](media/mainframe-compute-azure.png)

Výkon sálových počítačů se často používá pro online zpracování transakcí (OLTP) systémy, které zpracovávají miliony aktualizací pro tisíce uživatelů. Tyto aplikace často používají software pro zpracování transakcí, zpracování obrazovky a zadávání formulářů. Mohou používat systém řízení informací o zákaznících (CICS), systém správy informací (IMS) nebo balíček transakčního rozhraní (TIP).

Jak ukazuje obrázek, emulátor Čipu TPM v Azure zvládne úlohy CICS a IMS. Emulátor dávkového systému v Azure plní roli jazyka řízení úloh (JCL). Data sálových počítačů se migrují do databází Azure, jako je Azure SQL Database. Služby Azure nebo jiný software hostovaný ve virtuálních počítačích Azure se dá použít pro správu systému.

## <a name="mainframe-compute-at-a-glance"></a>Výpočet sálového počítače na první pohled

V sálovém počítači z14 jsou procesory uspořádány až do čtyř *zásuvek*. *Zásuvka* je prostě cluster procesorů a čipových sad. Každá zásuvka může mít šest aktivních čipů centrálního procesoru (CP) a každá CP má 10 čipů systémového řadiče (SC). V terminologii Intel x86 je šest zásuvek na zásuvku, 10 jader na zásuvku a čtyři zásuvky. Tato architektura poskytuje hrubý ekvivalent 24 soketů a maximálně 240 jader pro z14.

Rychlý z14 CP má taktovací rychlost 5,2 GHz. Obvykle je z14 dodáván se všemi CPs v krabici. Jsou aktivovány podle potřeby. Zákazníkovi se běžně účtují alespoň čtyři hodiny výpočetního času za měsíc navzdory skutečnému využití.

Procesor sálového počítače lze nakonfigurovat jako jeden z následujících typů:

- Procesor pro všeobecné účely (GP)
- Systém z Integrovaný informační procesor (zIIP)
- Integrovaný procesor Facility for Linux (IFL)
- Procesor systémového asistenta (SAP)
- Procesor integrovaného spojovacího zařízení (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Změna velikosti výpočtu sálového počítače nahoru a ven

Sálové počítače IBM nabízejí možnost škálování až na 240 jader (současná velikost z14 pro jeden systém). Kromě toho mohou mainframy IBM horizontální navýšení kapacity prostřednictvím funkce nazývané spojovací zařízení (CF). CF umožňuje více sálových systémů přístup ke stejným datům současně. Pomocí cf, mainframe Paralelní Sysplex technologie skupiny sálové procesory v clusterech. Když byla tato příručka napsána, funkce Parallel Sysplex podporovala 32 seskupení po 64 procesorech. Tímto způsobem lze seskupit až 2 048 procesorů, aby se zvýšila výpočetní kapacita.

Cf umožňuje výpočetní clustery sdílet data s přímým přístupem. Používá se pro zamykání informací, informací o mezipaměti a seznamu sdílených datových prostředků. Paralelní Sysplex pomocí jednoho nebo více CFs lze považovat za "sdílené vše" horizontální navýšení kapacity výpočetní cluster. Další informace o těchto funkcích naleznete [v tématu Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) na webu IBM.

Aplikace mohou tyto funkce používat k poskytování horizontálního navýšení kapacity výkonu a vysoké dostupnosti. Informace o tom, jak může CICS používat Parallel Sysplex s CF, si stáhněte [IBM CICS a Spojovací zařízení: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Azure počítá na první pohled

Někteří lidé si mylně myslí, že servery založené na společnosti Intel nejsou tak výkonné jako sálové počítače. Nové systémy založené na jádru, založené na procesoru Intel, však mají stejnou výpočetní kapacitu jako sálové počítače. Tato část popisuje možnosti infrastruktury Azure jako služby (IaaS) pro výpočetní techniku a úložiště. Azure poskytuje možnosti platformy jako služby (PaaS), ale tento článek se zaměřuje na možnosti IaaS, které poskytují srovnatelnou kapacitu sálových počítačů.

Virtuální počítače Azure poskytují výpočetní výkon v řadě velikostí a typů. V Azure virtuální procesor (vCPU) zhruba rovná jádro na sálovém počítači.

V současné době rozsah velikosti virtuálních strojů Azure poskytuje od 1 do 128 virtuálních procesorů. Typy virtuálních počítačů jsou optimalizované pro konkrétní úlohy. Například v následujícím seznamu jsou uvedeny typy virtuálních počítačů (aktuální od tohoto zápisu) a jejich doporučená použití:

| Velikost     | Typ a popis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Řada D | Obecné použití s 64 virtuálními procesory a až 3,5 GHz taktovací frekvencí                           |
| Řada E | Paměť optimalizovaná až 64 virtuálními procesory                                                 |
| Řada F | Výpočetní optimalizované s až 64 virtuálními procesory a taktovací frekvencí 3,7 GHz                       |
| Řada H | Optimalizováno pro vysoce výkonné výpočetní aplikace (HPC)                          |
| Řada L | Úložiště optimalizované pro aplikace s vysokou propustností podporované databázemi, jako je NoSQL |
| Řada M | Největší virtuální počítače optimalizované pro výpočetní výkony a paměť s až 128 virtuálními procesory                        |

Podrobnosti o dostupných virtuálních počítačích najdete [v tématu řady virtuálních strojů](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Sálový počítač z14 může mít až 240 jader. Nicméně z14 sálové počítače téměř nikdy používat všechna jádra pro jednu aplikaci nebo zatížení. Místo toho sálový počítač odděluje úlohy do logických oddílů (LPARs) a LPARs mají hodnocení – MIPS (Miliony instrukcí za sekundu) nebo MSU (Milion service unit). Při určování srovnatelné velikosti virtuálního počítače potřebné ke spuštění úlohy sálového počítače v Azure, faktor v hodnocení MIPS (nebo MSU).

Níže jsou uvedeny obecné odhady:

-   150 MIPS na virtuální procesor

-   1 000 MIPS na procesor

Chcete-li zjistit správnou velikost virtuálního počítače pro danou úlohu v LPAR, nejprve optimalizovat virtuální počítač pro úlohy. Pak určete počet potřebných virtuálních procesorů. Konzervativní odhad je 150 MIPS na virtuální procesor. Na základě tohoto odhadu může například virtuální počítač řady F s 16 virtuálními procesory snadno podporovat úlohu IBM Db2 pocházející z LPAR s 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Škálování výpočetních prostředků Azure

Virtuální servery řady M mohou škálovat až na 128 virtuálních procesorů (v době, kdy byl tento článek napsán). Pomocí konzervativní odhad 150 MIPS na virtuální procesor, M-series Virtuální počítače se rovná asi 19 000 MIPS. Obecné pravidlo pro odhad MIPS pro sálový počítač je 1 000 MIPS na procesor. Z14 mainframe může mít až 24 procesorů a poskytují asi 24.000 MIPS pro jeden sálový systém.

Největší jednotlivý mainfram z14 má přibližně o 5 000 MIPS více než největší virtuální počítač dostupný v Azure. Přesto je důležité porovnat, jak se nasazují úlohy. Pokud sálový počítač má aplikaci i relační databázi, jsou obvykle nasazeny na stejném fyzickém sálovém počítači – každý ve vlastním LPAR. Stejné řešení v Azure se často nasazuje pomocí jednoho virtuálního počítače pro aplikaci a samostatného virtuálního počítače s vhodně vhodně velikosti pro databázi.

Například pokud m64 virtuální procesor systém podporuje aplikaci a M96 virtuální procesor se používá pro databázi, jsou potřeba přibližně 150 virtuálních procesorů – nebo asi 24 000 MIPS, jak ukazuje následující obrázek.

![Porovnání nasazení pracovního vytížení 24 000 MIPS](media/mainframe-compute-mips.png)

Přístup je migrace LPARs do jednotlivých virtuálních her. Pak Azure snadno škáluje až do velikosti potřebné pro většinu aplikací, které jsou nasazené v jednom sálovém systému.

## <a name="azure-compute-scale-out"></a>Horizontální navýšení kapacity azure

Jednou z výhod řešení založeného na Azure je možnost horizontálnínavýšení kapacity. Škálování umožňuje téměř neomezené výpočetní kapacity k dispozici pro aplikaci. Azure podporuje několik metod pro horizontální navýšení výpočetního výkonu:

- **Vyrovnávání zatížení v clusteru.** V tomto scénáři aplikace můžete použít [nástroje pro vyrovnávání zatížení](/azure/load-balancer/load-balancer-overview) nebo správce prostředků k rozložení zatížení mezi více virtuálních počítačů v clusteru. Pokud je potřeba další výpočetní kapacita, přidají se do clusteru další virtuální počítače.

- **Škálovací sady virtuálních strojů.** V tomto scénáři burst aplikace můžete škálovat na další [výpočetní prostředky](/azure/virtual-machine-scale-sets/overview) na základě využití virtuálního počítače. Když klesne poptávka, počet virtuálních počítačů ve škálovací sadě může také snížit, což zajišťuje efektivní využití výpočetního výkonu.

- **Změna měřítka PaaS.** Nabídky Azure PaaS škálují výpočetní prostředky. [Například Azure Service Fabric](/azure/service-fabric/service-fabric-overview) přiděluje výpočetní prostředky ke splnění zvýšení objemu požadavků.

- **Kubernetes ovy shluky.** Aplikace v Azure můžou pro určené prostředky používat [clustery Kubernetes](/azure/aks/concepts-clusters-workloads) pro výpočetní služby. Azure Kubernetes Service (AKS) je spravovaná služba, která organizuje uzly, fondy a clustery Kubernetes v Azure.

Chcete-li zvolit správnou metodu pro škálování výpočetních prostředků, je důležité pochopit, jak se liší Azure a sálové počítače. Klíčem je, jak – nebo pokud – data jsou sdílena výpočetními prostředky. V Azure se data (ve výchozím nastavení) obvykle nesdílí více virtuálními počítači. Pokud sdílení dat vyžaduje více virtuálních počítačů ve výpočetním clusteru s horizontálním navýšením kapacity, sdílená data musí být umístěna v prostředku, který podporuje tuto funkci. V Azure zahrnuje sdílení dat úložiště, jak popisuje následující část.

## <a name="azure-compute-optimization"></a>Optimalizace výpočetních prostředků Azure

Každou úroveň zpracování můžete optimalizovat v architektuře Azure. Použijte nejvhodnější typ virtuálních discích a funkcí pro každé prostředí. Následující obrázek znázorňuje jeden potenciální vzor pro nasazení virtuálních počítačů v Azure pro podporu aplikace CICS, která používá Db2. V primární lokalitě se produkční, předprodukční a testovací virtuální virtuální servery nasazují s vysokou dostupností. Sekundární lokalita je pro zálohování a zotavení po havárii.

Každá vrstva může také poskytovat příslušné služby zotavení po havárii. Například produkční a databázové virtuální chody může vyžadovat horké nebo teplé obnovení, zatímco vývoj a testování virtuálních aplikací podporují obnovení za studena.

![Vysoce dostupné nasazení, které podporuje zotavení po havárii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Další kroky

- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosting sálového počítače na virtuálních počítačích Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Přesunutí úložiště sálových počítačů do Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Zdroje pro IBM

- [Paralelní Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a spojovací zařízení: Nad rámec základů](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytvoření požadovaných uživatelů pro instalaci funkce Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - příkaz Vytvořit instanci](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Řešení clusterované databáze Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud Microsoft Azure Government pro sálové aplikace](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Více zdrojů pro migraci

- [Průvodce výtahem a směnami virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
