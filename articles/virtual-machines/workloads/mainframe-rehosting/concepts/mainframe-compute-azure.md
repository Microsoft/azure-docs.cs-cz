---
title: Přesunout výpočetní mainframových Azure Virtual Machines
description: Porovnání prostředky příznivě ke kapacitě mainframových výpočtů Azure vám umožní migrovat a modernizujte aplikace z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485475"
---
# <a name="move-mainframe-compute-to-azure"></a>Přesunout výpočetní sálové počítače do Azure

Sálové počítače mají mají vynikající pověst vysokou spolehlivostí a dostupností a nadále důvěryhodné páteřní mnoho podniků. Už se často představit společnost využívat skoro neomezenou škálovatelnost a výpočetní výkon i. Některé podniky však mít outgrown funkce největší sálové počítače k dispozici. Pokud to podle názvu dalo čekat jako jste vy, Azure nabízí flexibilitu, dosahu a infrastruktury úspory.

Spouštění úloh sálové v Microsoft Azure, je potřeba vědět, jak vaše mainframových výpočetní funkce porovnání do Azure. Podle mainframových z14 IBM (aktuální model v době psaní tohoto textu), tento článek vysvětluje, jak získat srovnatelné výsledků v Azure.

Chcete-li začít, zvažte prostředí vedle sebe. Následující obrázek porovnává mainframových prostředí pro spouštění aplikací Azure hostitelského prostředí.

![Služby Azure a nabídky prostředí emulace srovnatelné podporu a zjednodušuje migraci](media/mainframe-compute-azure.png)

Napájení sálové počítače se často používá pro online transakcí (OLTP) systémy, které zpracovávají miliony aktualizace pro tisíce uživatelů pro zpracování. Tyto aplikace často používat software pro zpracování transakcí, zpracování obrazovky a položka formuláře. Používají může zákazník informace ovládacího prvku systému (CICS), systém správy informací (IMS) nebo rozhraní balíčku TIP (Transaction).

Jak ukazuje obrázek emulátoru TPM v Azure dokáže zpracovat CICS a IMS úlohy. Emulátor systému služby batch v Azure hraje roli úlohy ovládací prvek jazyka (JCL). Mainframových data jsou migrována do databáze Azure, jako je Azure SQL Database. Služby Azure nebo jiný software hostované ve službě Azure Virtual Machines je možné pro správu systému.

## <a name="mainframe-compute-at-a-glance"></a>Výpočetní mainframových na první pohled

V mainframových z14 procesory jsou uspořádány do až čtyři *zásobníky*. A *zásobník* je jednoduše cluster procesory a čipovými sadami. Každý zásobník může mít šest čipy aktivní Centrální procesor (CP) a každý CP má 10 čipy kontroler (SC) systému. V terminologii Intel x86 se šesti sockets na zásobník, 10 jader na soket a čtyři zásobníky. Tato architektura poskytuje hrubý ekvivalent 24 soketů a 240 jádra, maximum, z14.

Rychlý z14 CP má rychlost 5.2 GHz hodinami. Obvykle se dodává z14 se prohlášení CPs v poli. Jsou aktivovány podle potřeby. Zákazník je běžně účtovat minimálně čtyři hodiny za měsíc bez ohledu na skutečné využití výpočetního času.

Procesor sálové počítače se dají konfigurovat jako jednu z následujících typů:

- Obecné účely (zásady skupiny) procesoru
- Systém z integrované informace o procesoru (zIIP)
- Integrované zařízení pro Linux (IFL) procesor
- Procesor Assist systému (SAP)
- Integrované procesoru párování zařízení (BRÁNU)

## <a name="scaling-mainframe-compute-up-and-out"></a>Škálování výpočetního výkonu mainframových i horizontální navyšování kapacity

Sálové počítače IBM nabízejí možnost škálovat až 240 jader (aktuální velikost z14 jednoho systému). Kromě toho sálové počítače IBM můžete horizontálně navýšit kapacitu prostřednictvím funkci s názvem zařízení párování (CR). CF umožňuje více sálových počítačích pro přístup ke stejným datům současně. Pomocí CF mainframových paralelní Sysplex technologie skupiny mainframových procesorů v clusterech. Při této příručce byla zapsána, paralelní Sysplex funkce podporována 32 seskupení 64 procesorů. Až 2 048 procesory mohou být seskupeny tímto způsobem pro horizontální navýšení kapacity výpočetní kapacitu.

CF umožňuje sdílet data s přímým přístupem výpočetní clustery. Používá se pro uzamčení informace, informace o mezipaměti a seznam sdílených datových prostředků. Paralelní Sysplex pomocí jednoho nebo více CFs můžete představit jako "sdíleném vše" Škálováním výpočetního clusteru. Další informace o těchto funkcích najdete v tématu [paralelní Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) na webu IBM.

Aplikace můžete tyto funkce uvést horizontální navýšení výkonu a vysoké dostupnosti. Informace o tom, jak CICS paralelní Sysplex používat s CF, stáhněte si [IBM CICS a párování zařízení: Nad rámec Základy](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Výpočetní prostředky Azure na první pohled

Někteří lidé omylem myslíte, že nejsou servery s technologií Intel výkonné jako funkce sálové počítače. Nové bohatém na jádro, na základě Intel systémy však mít jako mnohem výpočetní kapacitu jako sálové počítače. Tato část popisuje možnosti Azure infrastruktury jako služby (IaaS) pro počítačové výpočty a úložiště. Azure nabízí platforma jako služba (PaaS) a možnosti, ale tento článek se zaměřuje na IaaS volby, které poskytují srovnatelné mainframových kapacitu.

Virtuální počítače Azure poskytují výpočetní výkon v rozsahu velikostí a typů. V Azure virtuální procesory (vCPU) přibližně odpovídá core na sálového počítače.

V současné době velikostí rozsahu z virtuálního počítače Azure poskytuje od 1 do 128 virtuálních procesorů. Typy virtuálních počítačů (VM) jsou optimalizované pro konkrétní úlohy. Například následující seznam obsahuje typy virtuálních počítačů (aktuální v době psaní tohoto textu) a jejich doporučené použití:

| Velikost     | Typ a popis                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-Series | Obecné účely s 64 virtuálních procesorů a až frekvence 3,5 GHz procesoru                           |
| E-Series | Paměťově optimalizovaná s až 64 virtuálních procesorů                                                 |
| Řada F | COMPUTE optimalizované s až 64 virtuálních procesorů a rychlost 3..7 GHz                       |
| H-Series | Optimalizováno pro aplikace vysokovýkonného výpočetního prostředí (HPC)                          |
| L-Series | Storage optimalizované pro vysoce propustné aplikace se opírá o databáze, jako jsou NoSQL |
| M Series | Největší výpočetní a paměťové prostředky optimalizace virtuálních počítačů s až 128 virtuálních procesorů                        |

Podrobnosti o dostupných virtuálních počítačů najdete v tématu [řady virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Z14 mainframových může mít až 240 jader. Ale z14 sálové počítače téměř nikdy nepoužívejte všechna jádra pro jednu aplikaci nebo úlohy. Místo toho sálového počítače odděluje úlohy do logické oddíly (LPARs) a LPARs mají hodnocení – MIPS (miliony pokyny za sekundu) nebo MSU (milion jednotek služby). Při určování srovnatelné velikosti virtuálního počítače, které jsou potřebné ke spuštění úlohy sálové počítače v Azure, hodnocení faktorem MIPS (nebo MSU).

Následují obecné odhady:

-   150 MIPS na virtuální procesor

-   1 000 MIPS na procesor

K určení správné velikosti virtuálního počítače pro konkrétní úlohu v LPAR, nejprve Optimalizujte pro pracovní vytížení virtuálního počítače. Pak zjistěte počet virtuálních procesorů, které jsou potřeba. Konzervativní odhad je 150 MIPS na virtuální procesor. Podle tohoto odhadu, třeba virtuální počítač řady F-series s 16 virtuálních procesorů může snadno podporu IBM Db2 zatížení přicházející z LPAR s 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Vertikálně navýšit výpočetní prostředky Azure

Virtuální počítače řady M-series můžete škálovat až 128 virtuálních procesorů (v době, kdy byla zapsána v tomto článku). Pomocí konzervativní odhad 150 MIPS na virtuální procesor, virtuálních počítačů řady M-series odpovídá přibližně 19,000 MIPS. Obecným pravidlem pro odhad MIPS sálového počítače je 1 000 MIPS na procesor. Z14 mainframových můžete mít až 24 procesory a poskytují přibližně 24 000 MIPS systému jeden mainframových.

Největší mainframových jeden z14 má přibližně 5 000 MIPS větší než největší virtuálních počítačů dostupných v Azure. Ještě je potřeba porovnat, jak jsou služby nasazené. Pokud systém sálového počítače aplikace a relační databáze, jsou obvykle nasazeni na stejný fyzický mainframových – každé ve vlastní LPAR. Stejné řešení v Azure se často nasazuje pomocí jednoho virtuálního počítače pro aplikaci a samostatné vhodné velikosti virtuálního počítače pro databázi.

Například pokud M64 vCPU systém podporuje aplikace a virtuální procesor M96 se používá pro databázi, přibližně 150 virtuálních procesorů nejsou potřeba – nebo přibližně 24 000 MIPS jak ukazuje následující obrázek.

![Porovnání úloh nasazení 24 000 MIPS](media/mainframe-compute-mips.png)

Tento přístup je migrace LPARs k jednotlivým virtuálním počítačům. Pak Azure snadno škáluje na velikost potřebná pro většinu aplikací, které jsou nasazené v jedné mainframových systému.

## <a name="azure-compute-scale-out"></a>Horizontální navýšení kapacity výpočetní prostředky Azure

Jednou z výhod řešení založené na Azure je možnost pro horizontální navýšení kapacity. Škálování provede společnost využívat skoro neomezenou výpočetní kapacitu, které jsou k dispozici pro aplikaci. Azure podporuje více metod pro horizontální navýšení kapacity výpočetní výkon:

- **Vyrovnávání zatížení napříč clusterem.** V tomto scénáři můžete použít aplikaci [nástroj pro vyrovnávání zatížení](/azure/load-balancer/load-balancer-overview) nebo resource Manageru pro šíření úloh mezi několik virtuálních počítačů v clusteru. Pokud další výpočetní kapacity je potřeba, další virtuální počítače se přidají do clusteru.

- **Škálovací sady virtuálních počítačů.** V tento scénář zvládání výkonových špiček aplikaci je možné škálovat na další [výpočetní prostředky](/azure/virtual-machine-scale-sets/overview) na základě využití virtuálních počítačů. Když poptávka poklesne, počet virtuálních počítačů ve škálovací sadě můžete také přejít mimo provoz, zajištění efektivního využívání výpočetního výkonu.

- **PaaS škálování.** Azure PaaS nabídky škálování výpočetních prostředků. Například [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) přiděluje výpočetní prostředky, které splňují zvýšení množství požadavků.

- **Clustery Kubernetes.** Aplikace v Azure můžete použít [clustery Kubernetes](/azure/aks/concepts-clusters-workloads) u výpočetních služeb pro zadané prostředky. Azure Kubernetes Service (AKS) je spravovaná služba, která orchestruje uzly, fondy a clustery v Azure Kubernetes.

Chcete-li zvolit správnou metodu pro horizontální navýšení kapacity výpočetních prostředků, je důležité pochopit, jak Azure a sálové počítače liší. Klíč je jak – nebo – data se sdílí výpočetní prostředky. V Azure není dat (ve výchozím nastavení) obvykle sdílí několik virtuálních počítačů. Sdílení dat vyžaduje-li několik virtuálních počítačů v příslušné škálování na výpočetním clusteru, sdílených dat se musí nacházet v prostředku, který podporuje tuto funkci. V Azure sdílení dat zahrnuje úložiště, jak se popisuje v následující části.

## <a name="azure-compute-optimization"></a>Optimalizace pro výpočetní prostředky Azure

Každá úroveň zpracování ve službě Azure architektury můžete optimalizovat. Použijte nejvhodnější typ virtuálních počítačů a funkce pro každé prostředí. Následující obrázek znázorňuje jeden potenciální vzor pro nasazování virtuálních počítačů v Azure pro podporu CICS aplikaci, která používá Db2. V primární lokalitě, produkčním prostředí se nasazují předprodukční a testovací virtuální počítače s vysokou dostupností. Sekundární lokality je zálohování a zotavení po havárii.

Každá úroveň můžete zadat taky odpovídající po havárii služby recovery services. Například může vyžadovat produkčního prostředí a virtuální počítače databáze horké, teplé nebo obnovení, zatímco vývoj a testování virtuálních počítačů podporují úplné obnovení.

![Vysoce dostupné nasazení, který podporuje zotavení po havárii](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Další postup

- [Migrace mainframů](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Změna mainframových hostování na Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Přesunout mainframových úložiště do Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM prostředky

- [Paralelní Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a párování zařízení: Nad rámec základní informace](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytváří se požadovaní uživatelé pro Db2 pureScale instalace funkce](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - vytvořit instanci příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale skupinový databázové řešení](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud pro mainframových aplikací](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Další zdroje migrace

- [Modernizace Alliance platformy: IBM Db2 v Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure virtuální datové centrum Lift and Shift Průvodce](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
