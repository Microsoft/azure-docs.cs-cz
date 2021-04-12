---
title: 'SAP v Azure: podporované scénáře s virtuálními počítači Azure'
description: Azure Virtual Machines podporované scénáře s úlohou SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670931"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Podporované scénáře pro úlohy SAP na virtuálních počítačích Azure
Návrh architektury SAP NetWeaver, Business One `Hybris` nebo S/4HANA Systems v Azure otevírá spoustu různých příležitostí pro různé architektury a nástroje, které slouží k získání škálovatelného, efektivního a vysoce dostupného nasazení. I když závisí na operačním systému nebo používaném systému DBMS, existují omezení. Ne všechny podporované scénáře jsou také podporovány stejným způsobem v Azure. Tento dokument vás provede podporovanými konfiguracemi bez vysoké dostupnosti a konfigurací a architekturou s vysokou dostupností a s využitím výhradně virtuálních počítačů Azure. Scénáře podporované [velkými instancemi Hana](./hana-overview-architecture.md)najdete v článku [podporované scénáře pro velké instance Hana](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>konfigurace na úrovni 2
Konfigurace SAP 2 úrovně se považuje za sestavenou ze kombinované vrstvy systémů SAP DBMS a aplikační vrstvy, která běží na stejném serveru nebo jednotce virtuálního počítače. Druhá vrstva je považována za vrstvu uživatelského rozhraní. V případě konfigurace dvou vrstev sdílí vrstva aplikace DBMS a SAP prostředky virtuálního počítače Azure. V důsledku toho je potřeba nakonfigurovat různé komponenty způsobem, který nesoutěží o prostředky. Musíte být také opatrní, abyste nemuseli odebírat prostředky virtuálního počítače. Taková konfigurace neposkytuje žádnou vysokou dostupnost, nad rámec smluv o [úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/) , které se týkají různých součástí Azure.

Grafická reprezentace takové konfigurace může vypadat takto:

![Jednoduchá konfigurace na dvě úrovně](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Tyto konfigurace se podporují s Windows, Red Hat, SUSE a Oracle Linux pro systémy DBMS SQL Server, Oracle, Db2, maxDB a SAP pomocného programu pro produkční a neprodukční případy. Pro SAP HANA jako DBMS se takový typ konfigurace podporuje jenom pro případy, které nejsou v produkčním prostředí. To zahrnuje i případ nasazení [velkých instancí Azure Hana](./hana-overview-architecture.md) .
U všech kombinací operačního systému/DBMS podporovaných v Azure se tento typ konfigurace podporuje. Je však povinný, abyste nastavili konfiguraci systému DBMS a komponent SAP způsobem, který se systémem DBMS a komponentami SAP nesoutěží na prostředky paměti a procesoru a tím se překročí množství fyzických dostupných prostředků. To je nutné provést omezením paměti, kterou může systém DBMS přidělit. Také je potřeba omezit na instance aplikace rozšířenou paměť SAP. Také je potřeba monitorovat spotřebu celého virtuálního počítače celkově, aby se zajistilo, že tyto součásti nemaximalizují prostředky procesoru. 

> [!NOTE]
> V případě produkčních systémů SAP doporučujeme další nastavení vysoké dostupnosti a případné konfigurace zotavení po havárii, jak je popsáno dále v tomto dokumentu.


## <a name="3-tier-configuration"></a>Konfigurace 3 vrstvy
V takových konfiguracích oddělíte vrstvu aplikace SAP a vrstvu DBMS do různých virtuálních počítačů. Obvykle to provedete v případě větších systémů a z důvodů, proč je flexibilnější na prostředky aplikační vrstvy SAP. V nejjednodušší instalaci neexistují žádné vysoké dostupnosti nad rámec [smluv o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/) různých součástí Azure, které se týkají. 

Grafická reprezentace vypadá takto:

![Diagram, který zobrazuje jednoduchou konfiguraci 3 vrstvy.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Tento typ konfigurace se podporuje ve Windows, Red Hat, SUSE a Oracle Linux pro systémy DBMS SQL Server, Oracle, Db2, SAP HANA, maxDB a SAP pomocného programu pro produkční a neprodukční případy. Toto je výchozí konfigurace nasazení pro [velké instance Azure Hana](./hana-overview-architecture.md). Pro zjednodušení nerozlišujeme mezi instancemi systému SAP Central Services a dialogy SAP v aplikační vrstvě SAP. V této jednoduché konfiguraci se třemi úrovněmi by neexistovala žádná ochrana s vysokou dostupností pro centrální služby SAP.

> [!NOTE]
> V případě produkčních systémů SAP doporučujeme další nastavení vysoké dostupnosti a případné konfigurace zotavení po havárii, jak je popsáno dále v tomto dokumentu.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Více instancí systému DBMS na jednotku velkých instancí virtuálních počítačů nebo HANA
V tomto typu konfigurace Hostujte víc instancí DBMS na virtuální počítač Azure nebo jednotku velké instance HANA. Motivace může mít méně operačních systémů na údržbu a s těmito sníženými náklady. Další motivace může mít větší flexibilitu a vyšší efektivitu díky sdílení prostředků větší jednotky velkých instancí virtuálních počítačů nebo HANA mezi několika instancemi systému DBMS. Zatím se tyto konfigurace zobrazovaly hlavně pro jiné než produkční systémy.

Podobná konfigurace by mohla vypadat takto:

![Více instancí systému DBMS v jedné jednotce](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Tento typ nasazení DBMS je podporovaný pro:

- SQL Server on Windows
- IBM Db2. Hledání podrobností v článku [více instancí (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Pro Oracle. Podrobnosti najdete v [poznámkách k podpoře sap #1778431](https://launchpad.support.sap.com/#/notes/1778431) a souvisejících poznámkách SAP.
- V případě SAP HANA je podporována více instancí na jednom virtuálním počítači, SAP volá tuto metodu nasazení MCOS. Podrobnosti najdete v článku SAP [více SAP HANA systémů na jednom hostiteli (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- Nebo b2751fd43bec41a9a14e01913f1edf18.html)

Spuštění více instancí databáze na jednom hostiteli je nutné zajistit, aby různé instance nekonkurují prostředkům a aby překročily omezení fyzického prostředku virtuálního počítače. To platí zejména pro paměť, kde je třeba uvolnit paměť všech instancí, které může virtuální počítač sdílet, aby mohl být přidělen. To může být také pravdivé pro prostředky procesoru, které mohou využívat různé instance databáze. Všechny zmíněné systémy DBMS mají konfigurace, které umožňují omezit přidělování paměti a prostředky procesoru na úrovni instance.
Aby bylo možné podporovat takovou konfiguraci pro virtuální počítače Azure, je třeba, aby byly disky nebo svazky používané pro soubory protokolů dat a log/znovu souborů databáze spravované různými instancemi oddělené. Nebo jinými slovy soubory protokolu nebo protokolů databáze, které spravuje jiná instance systému DBMS, nemusí sdílet stejné disky nebo svazky. 

Konfigurace disku pro velké instance HANA je dodána nakonfigurované a je podrobně popsána v [podporovaných scénářích pro velké instance Hana](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> V případě produkčních systémů SAP doporučujeme další konfigurace s vysokou dostupností a konfigurací zotavení po havárii, jak je popsáno dále v tomto dokumentu. Virtuální počítače s víc instancemi DBMS se nepodporují s konfiguracemi s vysokou dostupností, které jsou popsané dále v tomto dokumentu.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Několik instancí dialogů SAP v jednom virtuálním počítači
V mnoha případech byly na holé servery nasazené víc instancí dialogů, nebo dokonce i na virtuálních počítačích běžících v privátních cloudech. Důvodem takových konfigurací bylo přizpůsobení některých instancí dialogů SAP konkrétním úlohám, obchodním funkcím nebo typům úloh. Důvodem pro neizolaci těchto instancí na samostatné virtuální počítače bylo úsilí při údržbě a provozu operačního systému. Nebo v mnoha případech se jedná o náklady na případ, kdy hostitel nebo obsluha virtuálního počítače žádá o měsíční poplatek za každý virtuální počítač provozovaný a spravovaný. V Azure je scénář hostování více instancí dialogů SAP v jednom virtuálním počítači, který je podporovaný pro produkční a neprodukční účely v operačních systémech Windows, Red Hat, SUSE a Oracle Linux. V případě, že je na jednom virtuálním počítači spuštěno více instancí aplikačního serveru SAP, je třeba nastavit parametr jádra SAP PHYS_MEMSIZE, který je k dispozici v jádrech Windows a moderních systémech Linux. Doporučuje se také omezit rozšiřování rozšířené paměti SAP v operačních systémech, jako je například Windows, kde je implementován Automatický růst paměti rozšířené pro SAP. To se dá udělat s parametrem profilu SAP `em/max_size_MB` .

V případě konfigurace na 3 úrovni, kde se v rámci virtuálních počítačů Azure spouští víc instancí dialogů SAP, může vypadat takto:

![Diagram, který zobrazuje konfiguraci na 3 úrovni, kde se na virtuálních počítačích Azure spouští víc instancí dialogů SAP.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Pro zjednodušení nerozlišujeme mezi instancemi systému SAP Central Services a dialogy SAP v aplikační vrstvě SAP. V této jednoduché konfiguraci se třemi úrovněmi by neexistovala žádná ochrana s vysokou dostupností pro centrální služby SAP. U produkčních systémů se nedoporučuje opustit službu SAP Central Services bez ochrany. Konkrétní informace, které se označují jako konfigurace více identifikátorů SID kolem centrálních instancí SAP a vysoké dostupnosti takových konfigurací s více identifikátory SID, najdete v dalších částech tohoto dokumentu.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Ochrana při vysoké dostupnosti pro vrstvu SAP DBMS
Při hledání nasazení produkčních systémů SAP je třeba vzít v úvahu aktivní pohotovostní režim konfigurací s vysokou dostupností. Obzvláště u SAP HANA, kdy je potřeba načíst data do paměti před tím, než bude možné získat úplný výkon a škálovatelnost zpátky, služba Azure retušovací není ideální mírou pro vysokou dostupnost. 

Obecně platí, že Microsoft podporuje jenom konfigurace s vysokou dostupností a softwarové balíčky, které jsou popsané v části úlohy SAP v docs.microsoft.com. Můžete si přečíst stejný příkaz v tématu SAP Note [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Společnost Microsoft nebude poskytovat podporu pro další vysoce dostupné softwarové architektury třetích stran, které společnost Microsoft nezdokumentuje ve spojení s úlohou SAP. V takových případech je dodavatel třetí strany architektury vysoké dostupnosti podpůrnou stranou pro konfiguraci vysoké dostupnosti, kterou je třeba zavažovat za zákazníka do procesu podpory. Výjimky budou zmíněny v tomto článku. 

Obecně platí, že Microsoft podporuje omezené sady konfigurací s vysokou dostupností na virtuálních počítačích Azure nebo jednotkách velkých instancí HANA. Pro podporované scénáře velkých instancí HANA si přečtěte dokument [podporované scénáře pro velké instance Hana](./hana-supported-scenario.md).

Pro virtuální počítače Azure se na úrovni DBMS podporují tyto konfigurace s vysokou dostupností:

- SAP HANA systémovou replikaci založenou na Linux Pacemaker v SUSE a Red Hat. Přečtěte si podrobné články:
    - [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA konfigurace škálované n + m pomocí [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) na SUSE a Red Hat. Podrobnosti najdete v těchto článcích:
    - [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server cluster s podporou převzetí služeb při selhání založený na Souborové službě Windows Scale-Out. I když se v produkčních systémech doporučuje použít místo clusteringu SQL Server Always On. Služba SQL Server Always On nabízí lepší dostupnost pomocí samostatného úložiště. Podrobnosti jsou popsány v tomto článku: 
    - [Konfigurace SQL Server instance clusteru s podporou převzetí služeb při selhání na virtuálních počítačích Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On se podporuje s operačním systémem Windows pro SQL Server v Azure. Toto je výchozí doporučení pro produkční SQL Server instance v Azure. Podrobnosti jsou popsány v těchto článcích:
    - [Představujeme SQL Server skupiny dostupnosti Always On na virtuálních počítačích Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Nakonfigurujte skupinu dostupnosti Always On na virtuálních počítačích Azure v různých oblastech](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Nakonfigurujte nástroj pro vyrovnávání zatížení pro skupinu dostupnosti Always On v Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle data Guard pro Windows a Oracle Linux. Podrobnosti o Oracle Linux najdete v tomto článku:
    - [Implementace Oracle data Guard na virtuálním počítači Azure Linux](../oracle/configure-oracle-dataguard.md)
- HADR IBM Db2 v SUSE a RHEL podrobná dokumentace pro SUSE a RHEL pomocí Pacemaker je k dispozici zde:
    - [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Pacemaker](./dbms-guide-ha-ibm.md)
    - [Vysoká dostupnost IBM DB2 LUW ve virtuálních počítačích Azure na linuxovém serveru Red Hat Enterprise](./high-availability-guide-rhel-ibm-db2-luw.md)
- Konfigurace protokolu SAP pomocného mechanismu a SAP maxDB, jak je popsáno v těchto dokumentech:
    - [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](./dbms_guide_sapase.md)
    - [Nasazení SAP MaxDB, liveCache a Content serveru na virtuálních počítačích Azure](./dbms_guide_maxdb.md)
- Velké instance HANA vysoké dostupnosti jsou podrobně popsané v těchto scénářích:
    - [Podporované scénáře pro velké instance HANA – HSR s STONITH pro vysokou dostupnost](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Podporované scénáře pro velké instance HANA – automatické převzetí služeb při selhání hostitele (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Pro žádný z výše popsaných scénářů podporujeme konfigurace více instancí systému DBMS v jednom virtuálním počítači. V každém z těchto případů se dá nasadit jenom jedna instance databáze na virtuální počítač a chránit se popsanými metodami vysoké dostupnosti. Ochrana více instancí systému DBMS v rámci jednoho clusteru s podporou převzetí služeb při selhání s Windows nebo **Pacemaker není v tomto okamžiku podporována.** Také podpora Oracle data Guard je podporována pouze pro jednotlivé instance v případě nasazení virtuálních počítačů. 

Různé databázové systémy umožňují hostovat více databází v rámci jedné instance systému DBMS. Stejně jako v případě SAP HANA lze hostovat více databází v několika databázových kontejnerech (MDC). V případě, že tyto konfigurace s více databázemi fungují v rámci jednoho prostředku clusteru s podporou převzetí služeb při selhání, jsou tyto konfigurace podporovány. Nepodporované konfigurace jsou případy, kdy by se vyžadovalo více prostředků clusteru. Jako u konfigurací, kde byste definovali více SQL Server skupin dostupnosti v rámci jedné instance SQL Server.


![Konfigurace HA systému DBMS](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

V závislosti na operačním systému DBMS a/nebo se můžou součásti jako Azure Load Balancer vyžadovat nebo nemusí považovat za součást architektury řešení. 

V případě maxDB se musí konfigurace úložiště lišit. V maxDB se soubory dat a protokolů musí nacházet ve sdíleném úložišti pro konfigurace s vysokou dostupností. Pro zajištění vysoké dostupnosti se podporuje sdílené úložiště jenom v případě maxDB. Pro všechny ostatní systémy DBMS jsou samostatné paměťové zásobníky na uzel jedinou podporovanou konfigurací disků.

Existují i další architektury vysoké dostupnosti, které jsou známé pro spuštění na Microsoft Azure. Společnost Microsoft ale tyto architektury neotestovala. Pokud chcete vytvořit konfiguraci s vysokou dostupností pomocí těchto platforem, budete muset spolupracovat se zprostředkovatelem tohoto softwaru:

- Vývoj architektury nasazení
- Nasazení architektury
- Podpora architektury

> [!IMPORTANT]
> Microsoft Azure Marketplace nabízí celou řadu softwarových zařízení, která poskytují řešení úložiště nad nativním úložištěm Azure. Tato softwarová zařízení je možné použít k vytváření sdílených složek systému souborů NFS i v případě, že se v SAP HANA nasazení se škálováním na více instancí vyžaduje, aby bylo možné použít pohotovostní uzel. Z různých důvodů nejsou žádná z těchto softwarových zařízení podporovaná pro žádná z nasazení DBMS od Microsoftu a SAP v Azure. V tuto chvíli nejsou v současnosti podporovaná nasazení systému DBMS v sdílených složkách SMB. Nasazení systému DBMS ve sdílených složkách NFS je omezené na sdílené složky NFS 4,1 na [Azure NetApp Files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Vysoká dostupnost pro centrální službu SAP
Služba SAP Central Services je druhým jediným bodem selhání konfigurace SAP. V důsledku toho je nutné chránit i tyto procesy centrálních služeb. Tato nabídka je podporovaná a doložená pro úlohy SAP, jako je:

- Cluster Windows s podporou převzetí služeb při selhání pomocí souborové služby Windows se škálováním na více systémů pro sapmnt a globální transportní adresář. Podrobnosti jsou popsány v článku:
    - [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdílené složky v Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Příprava infrastruktury Azure na vysokou dostupnost pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Cluster s podporou převzetí služeb při selhání systému Windows používající sdílenou složku SMB na základě [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) pro adresář sapmnt a globální přenos. Podrobnosti jsou uvedeny v článku:
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP](./high-availability-guide-windows-netapp-files-smb.md)
- Server clusteru s podporou převzetí služeb při selhání systému Windows založený na `Datakeeper` . I když jste si ho popsali od Microsoftu, potřebujete relaci podpory s s, takže můžete při používání tohoto řešení začít s podporou. Podrobnosti jsou popsány v článku:
    - [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker v operačním systému SUSE s vytvořením vysoce dostupné sdílené složky NFS pomocí dvou virtuálních počítačů SUSE a `drdb` replikace souborů. Podrobnosti jsou popsány v článku.
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md)
    - [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Operační systém Pacemaker SUSE s využitím sdílených složek systému souborů NFS poskytovaných [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Podrobnosti jsou popsány v
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md)
- Pacemaker na operační systém Red Hat se sdílenou složkou systému souborů NFS, která je hostovaná v `glusterfs` clusteru. Podrobnosti najdete v článcích.
    - [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` na virtuálních počítačích Azure v Red Hat Enterprise Linux pro SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker na operační systém Red Hat se sdílenou složkou systému souborů NFS, která je hostována na [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) Podrobnosti jsou popsány v článku.
    - [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md)

V uvedených řešeních potřebujete relaci podpory s produktem s podporou pro podporu `Datakeeper` produktu a zapojení s s přímým výkonem v případě problémů. V závislosti na tom, jakým způsobem jste získali licenci na Windows, Red Hat a/nebo SUSE, můžete také vyžadovat, aby se svým poskytovatelem operačního systému nastavila plná podpora pro uvedené konfigurace s vysokou dostupností.

Konfigurace může být také zobrazena jako:

![Konfigurace systému DBMS a ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Na pravé straně grafiky se zobrazí vysoce dostupné centrální služby SAP. Kromě toho, že je služba SAP Central Services chráněná pomocí architektury clusteru s podporou převzetí služeb při selhání, která může při selhání převzít služby při selhání, je nutné mít vysoce dostupnou sdílenou složku nebo sdílenou složku SMB nebo sdílený disk se systémem Windows, abyste se ujistili, že je adresář sapmnt a globální přenos dostupný nezávisle na existenci jednoho virtuálního počítače. Další některá řešení, jako třeba clusterový server s podporou převzetí služeb při selhání Windows a Pacemaker, budou vyžadovat, aby nástroj pro vyrovnávání zatížení Azure nasměroval nebo přesměroval provoz na uzel v pořádku.

V zobrazeném seznamu se nezmiňuje Oracle Linux operační systém. Oracle Linux nepodporuje Pacemaker jako architekturu clusteru. Pokud chcete nasadit systém SAP na Oracle Linux a potřebujete architekturu vysoké dostupnosti pro Oracle Linux, budete muset pracovat s dodavateli třetích stran. Jeden ze dodavatelů se používá v rámci sady ochrany pro Linux, který podporuje SAP v Azure. Další informace najdete v tématu informace o podpoře SAP Note [#1662610 – podrobnosti o podpoře pro sadu s ochranou Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610) .



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Podporované úložiště s výše uvedenými scénáři služby SAP Central Services
Vzhledem k tomu, že pouze podmnožina typů úložiště Azure poskytuje vysoce dostupné sdílené složky systému souborů NFS nebo SMB, kvalita pro využití v našich clusterech služby SAP Central Services obsahuje seznam podporovaných typů úložiště.

- Cluster Windows s podporou převzetí služeb při selhání se souborovým serverem Windows se škálováním na více systémů se dá nasadit na všechny nativní typy úložiště Azure, kromě Azure NetApp Files. Doporučujeme ale využít Premium Storage v důsledku propustnosti a vstupně-výstupních smluv o úrovni služeb.
- Clusterový server s podporou převzetí služeb při selhání ve Windows s SMB v Azure NetApp Files se podporuje v Azure NetApp Files. Sdílené složky SMB ve službě Azure File **Services nejsou v tuto chvíli podporované.**
- Cluster s podporou převzetí služeb při selhání systému Windows se sdíleným diskem Windows na základě platformy je `Datakeeper` možné nasadit na všechny nativní typy úložiště Azure s výjimkou Azure NetApp Files. Doporučujeme ale využít Premium Storage v důsledku propustnosti a vstupně-výstupních smluv o úrovni služeb.
- SUSE nebo Red Hat Pacemaker, které používají sdílené složky NFS na Azure NetApp Files, jsou podporovány v Azure NetApp Files. 
- SUSE Pacemaker s použitím `drdb` konfigurace mezi dvěma virtuálními počítači se podporuje pomocí nativních typů úložiště Azure, s výjimkou Azure NetApp Files. Doporučujeme ale využít Premium Storage v důsledku propustnosti a vstupně-výstupních smluv o úrovni služeb.
- Red Hat Pacemaker využívající `glusterfs` pro poskytování sdílené složky NFS se podporuje pomocí nativních typů úložiště Azure, s výjimkou Azure NetApp Files. Doporučujeme ale využít Premium Storage v důsledku propustnosti a vstupně-výstupních smluv o úrovni služeb.

> [!IMPORTANT]
> Microsoft Azure Marketplace nabízí celou řadu softwarových zařízení, která poskytují řešení úložiště nad nativním úložištěm Azure. Tato softwarová zařízení je možné použít k vytváření sdílených složek NFS nebo SMB i v případě, že se teoreticky dají použít i v clusterovaných službách SAP pro clustery s podporou převzetí služeb při selhání. Tato řešení nejsou přímo podporována pro úlohy SAP od společnosti Microsoft. Pokud se rozhodnete toto řešení použít k vytvoření sdílené složky systému souborů NFS nebo SMB, musí být podpora pro konfiguraci služby SAP Central Service poskytnuta třetí stranou, která by mohla být součástí softwarového zařízení úložiště.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Clustery s podporou převzetí služeb při selhání pro více SID služby SAP Central
Aby se snížil počet virtuálních počítačů, které jsou potřeba ve velkém prostředí SAP krajiny, umožňuje služba SAP v konfiguraci clusteru s podporou převzetí služeb při selhání spouštět instance služby SAP Central Services s několika různými systémy SAP. Představte si případy, kdy máte 30 nebo více produkčních systémů NetWeaver nebo S/4HANA. Bez clusteringu s více SID by tyto konfigurace vyžadovaly 60 nebo více virtuálních počítačů v konfiguraci clusteru s podporou převzetí služeb při selhání s Windows nebo Pacemaker. Kromě nezbytných clusterů systému DBMS převzetí služeb při selhání. Nasazení několika centrálních služeb SAP ve dvou uzlech v konfiguraci clusteru s podporou převzetí služeb při selhání může výrazně snížit počet virtuálních počítačů. Nasazení více instancí služby SAP Central Services je ale také v jedné konfiguraci clusteru se dvěma uzly. Problémy s jedním virtuálním počítačem v konfiguraci clusteru se vztahují na více systémů SAP. Údržba hostovaného operačního systému běžícího v konfiguraci clusteru vyžaduje větší koordinaci, protože je ovlivněno více systémů SAP v produkčním prostředí. Nástroje, jako SAP LaMa, nepodporují Clustering s více identifikátory SID v procesu klonování systému.

V Azure je konfigurace clusteru s více identifikátory SID podporovaná pro operační systém Windows s ENSA1 a ENSA2. Doporučením není kombinace starší architektury ENSA1 (Service Replication Service) s novou architekturou (ENSA2) v jednom clusteru s více identifikátory SID. Podrobnosti o této architektuře jsou popsány v článcích

- [Vysoká dostupnost ASCS/SCS instance SAP pomocí clusteringu s podporou převzetí služeb při selhání Windows serveru a sdíleného disku v Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Vysoká dostupnost ASCS/SCS instance SAP s Clustering s podporou převzetí služeb při selhání Windows serveru a sdílenou složkou v Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

V případě SUSE se podporuje i cluster s více identifikátory SID založených na Pacemaker. Zatím je konfigurace podporovaná pro:

- Maximálně pět instancí SAP ASCS/SCS
- Stará architektura Ice serveru pro replikaci ve frontě (ENSA1)
- Konfigurace clusteru se dvěma uzly Pacemaker

Konfigurace je popsána ve [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro Průvodce pro více SID aplikací SAP](./high-availability-guide-suse-multi-sid.md) .

Cluster s více identifikátory SID se schématem replikačního serveru pro zařazování do fronty vypadá nějak takto:

![Diagram, který zobrazuje cluster s více identifikátory SID se serverem replikace ve frontě.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA scénáře škálování na více instancí
SAP HANA scénáře škálování na více instancí se podporují pro podmnožinu certifikovaných virtuálních počítačů Azure HANA, jak je uvedeno v [adresáři SAP HANA hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Všechny virtuální počítače označené "Yes" ve sloupci "Clustering" lze použít pro možnost škálování OLAP nebo S/4HANA. Konfigurace bez pohotovostního režimu se podporují s Azure Storage typy: 

- Azure Premium Storage, včetně akcelerátoru zápisu Azure pro svazek/Hana/log
- [Disky Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA konfigurace škálování na více instancí pro OLAP nebo S/4HANA s pohotovostními uzly se podporuje výhradně se systémem souborů NFS Shared hosted on Azure NetApp Files.

Další informace o přesných konfiguracích úložiště s pohotovostním uzlem nebo bez něj najdete v článcích:

- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](./hana-vm-operations-storage.md) 
- [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Nasazení SAP HANA systému se škálováním na více systémů s pohotovostním uzlem na virtuálních počítačích Azure pomocí Azure NetApp Files v Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Poznámka k podpoře SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Podrobnosti o velkých instancích HANA podporovaných konfiguracích s více instancemi Hana najdete v následující dokumentaci:

- [Podporované scénáře pro škálování velkých instancí HANA s pohotovostním režimem](./hana-supported-scenario.md#scale-out-with-standby)
- [Podporované scénáře pro horizontální navýšení kapacity velkých instancí HANA bez úsporného režimu](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scénář zotavení po havárii
K dispozici je řada scénářů zotavení po havárii, které jsou podporovány. Architektury po havárii definujeme jako architektury, které by měly kompenzovat kompletní oblasti Azure, které se docházejí na mřížku. To znamená, že potřebujeme, aby cíl zotavení po havárii byl jinou oblastí Azure jako cíl, aby se spouštěla vaše technologie SAP na šířku. Metody a konfigurace oddělíme ve vrstvě DBMS a v jiné vrstvě než DBMS. 

### <a name="dbms-layer"></a>Vrstva DBMS
Pro vrstvu DBMS jsou podporované konfigurace, které používají mechanismy nativní replikace systému DBMS, jako je například Always On, Oracle data Guard, Db2 HADR, SAP pomocného programu SAP a replikace systému HANA. Je povinné, aby datový proud replikace v takových případech byl asynchronní, nikoli synchronní jako v typických scénářích s vysokou dostupností, které jsou nasazeny v rámci jedné oblasti Azure. Typický příklad takové podporované konfigurace systému DBMS pro zotavení po havárii je popsán v článku [SAP HANA dostupnosti napříč oblastmi Azure](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). Druhý obrázek v této části popisuje scénář s HANA jako příkladem. Hlavní databáze podporované pro aplikace SAP jsou v takovém scénáři možné nasadit.

V oblasti zotavení po havárii se podporuje použití menšího virtuálního počítače jako cílové instance, protože tento virtuální počítač nefunguje s úplným provozem úloh. K tomu je potřeba vzít v úvahu následující skutečnosti:

- Menší typy virtuálních počítačů neumožňují, aby k nim bylo připojeno mnoho disků než menší virtuální počítače.
- Menší virtuální počítače mají méně propustnost sítě a úložiště.
- Změna velikosti napříč rodinou virtuálních počítačů může představovat problém, když se různé virtuální počítače shromažďují v jedné skupině dostupnosti Azure nebo když by se měla změnit Změna velikosti mezi řadou M-Series a Mv2 řadou virtuálních počítačů.
- Využití CPU a paměti pro instanci databáze schopné získat datový proud se změnami s minimálním zpožděním a dostatečným množstvím PROCESORů a paměti, aby se tyto změny projevily s minimálním zpožděním na data  

Další podrobnosti o omezeních různých velikostí virtuálních počítačů najdete [tady](../../sizes.md) . 

Další podporovanou metodou nasazení cíle DR je nainstalovaná druhá instance systému DBMS na virtuálním počítači, na kterém běží neprodukční instance systému DBMS instance, která není produkčním prostředím SAP. To může být trochu náročnější, protože potřebujete zjistit, jaké jsou požadavky na paměť, prostředky procesoru, šířku pásma sítě a šířku pásma úložiště, pro konkrétní cílové instance, které by měly fungovat jako hlavní instance ve scénáři zotavení po havárii. Obzvláště v HANA se důrazně doporučuje nakonfigurovat instanci, která funguje jako cíl DR na sdíleném hostiteli, aby se data do cílové instance DR předem nenačítala.

Ve scénářích zotavení po havárii pro velké instance služby HANA si Projděte tyto dokumenty:

- [Jeden uzel se systémem DR pomocí replikace úložiště](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Vysoká dostupnost pomocí HSR a zotavení po havárii s využitím replikace úložiště](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Horizontální navýšení kapacity pomocí replikace úložiště v systému DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Jeden uzel s DR pomocí HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR jednoho uzlu do DR (náklady optimalizované)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Vysoká dostupnost a zotavení po havárii pomocí HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Vysoká dostupnost a zotavení po havárii s HSR (náklady optimalizované)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Horizontální navýšení kapacity pomocí HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> Použití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nebylo testováno pro nasazení DBMS v rámci úlohy SAP. V důsledku toho není v této chvíli v tomto okamžiku podporována pro vrstvu DBMS systémů SAP. Jiné metody replikace od společnosti Microsoft a SAP, které nejsou uvedeny, nejsou podporovány. Používání softwaru třetích stran pro replikaci vrstvy DBMS systémů SAP mezi různými oblastmi Azure musí být podporované dodavatelem softwaru a nebude podporované prostřednictvím kanálů podpory Microsoft a SAP. 
 
## <a name="non-dbms-layer"></a>Vrstva bez DBMS
Pro aplikační vrstvu SAP a konečné sdílené složky nebo umístění úložiště, které jsou potřeba, můžou zákazníci využívat dva hlavní scénáře:

- Cíle zotavení po havárii ve druhé oblasti Azure se nepoužívají pro žádné produkční nebo neprodukční účely. V tomto scénáři jsou virtuální počítače, které fungují jako cíl zotavení po havárii, ideálním způsobem nasazené a image a změny imagí produkční vrstvy produktu SAP se replikují do oblasti zotavení po havárii. K [disAzure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)funkce, které mohou provádět tyto úlohy. Azure Site Recovery podporují scénáře replikace z Azure do Azure, jako je to. 
- Cíle zotavení po havárii jsou virtuální počítače, které jsou ve skutečnosti používány neprodukčními systémy. Celá technologie SAP na šířku je rozdělená do dvou různých oblastí Azure s produkčními systémy obvykle v jedné oblasti a v jiných než produkčních systémech v jiné oblasti. V případě velkého počtu zákaznických nasazení má zákazník neprodukční systém, který je ekvivalentní produkčnímu systému. Zákazník má instance produkční aplikace předem nainstalované v neprodukčních systémech aplikační vrstvy. V případě převzetí služeb při selhání by se instance neprodukčního prostředí vypnuly, virtuální názvy produkčních virtuálních počítačů přešly do neprodukčních virtuálních počítačů (po přiřazení nových IP adres v DNS) a Začínáme s předinstalovanými provozními instancemi.

### <a name="sap-central-services-clusters"></a>Clustery služby SAP Central Services
Clustery služby SAP Central Services, které používají sdílené disky (Windows), sdílené složky SMB (Windows) nebo složky NFS jsou obtížnější pro replikaci. Na straně Windows je možné řešení replikace úložiště Windows. V systému Linux rsync je životaschopné řešení.



## <a name="non-supported-scenario"></a>Nepodporovaný scénář
Existuje seznam scénářů, které nejsou podporované pro úlohy SAP v architekturách Azure. **Nepodporováno** znamená, že SAP a Microsoft nebudou moci podporovat tyto konfigurace a musí se odložit na případnou dotčenou třetí stranu, která poskytla software k vytvoření takových architektur. Dvě kategorie jsou:

- Softwarová zařízení pro úložiště: na Azure Marketplace je k dispozici několik softwarových zařízení pro úložiště. Někteří dodavatelé nabízejí vlastní dokumentaci, jak používat tato softwarová zařízení pro úložiště v Azure s využitím softwaru SAP. Podpora konfigurací nebo nasazení, která zahrnují taková zařízení pro dočasné úložiště, musí poskytnout dodavatel těchto softwarových zařízení úložiště. Tento fakt je také manifestovaná v [poznámce SAP support #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Architektury s vysokou dostupností: pro úlohy SAP v Azure jsou podporované jenom Pacemaker a clustery s podporou převzetí služeb při selhání ve Windows serveru. Jak bylo zmíněno dříve, řešení systému s `Datakeeper` je popsané a dokumentováno společností Microsoft. Nicméně součásti nástroje s `Datakeeper` musí být podporovány prostřednictvím s jako dodavatel, který poskytuje tyto komponenty. SAP také uvedené v různých poznámkách SAP k dispozici pro certifikované architektury s vysokou dostupností. Některé z nich byly certifikovány od dodavatele třetí strany i pro Azure. Nicméně podpora konfigurací, které tyto produkty využívají, musí poskytnout dodavatel produktu. Různí dodavatelé mají jinou integraci do procesů podpory SAP. Než se rozhodnete použít produkt v konfiguracích SAP nasazených v Azure, měli byste objasnit, který proces podpory pro konkrétní dodavatele funguje nejlépe.
- Clustery sdílených disků, na kterých jsou umístěny soubory databáze na sdílených discích, nejsou podporovány výjimkou maxDB. Pro všechny ostatní databáze je podporované řešení samostatné umístění úložiště místo sdílené složky SMB nebo NFS nebo sdíleného disku ke konfiguraci scénářů s vysokou dostupností.

Další scénáře, které nejsou podporované, jsou scénáře jako:

- Scénáře nasazení, které představují větší latenci sítě mezi aplikační vrstvou SAP a vrstvou SAP DBMS v rámci běžné architektury SAP, jak je znázorněno v NetWeaver, S/4HANA a `Hybris` např. Sem patří:
    - Nasazení jedné z místních vrstev, zatímco druhá vrstva je nasazená v Azure
    - Nasazení aplikační vrstvy SAP systému v jiné oblasti Azure, než je vrstva DBMS
    - Nasazení jedné úrovně v datových centrech, která jsou společně umístěná v Azure, a na druhé úrovni v Azure, s výjimkou toho, že tyto vzory architektury poskytuje nativní služba Azure
    - Nasazení síťových virtuálních zařízení mezi aplikační vrstvou SAP a vrstvou DBMS
    - Využití úložiště hostovaného v datových centrech, která jsou umístěná v datacentrech, do Azure datacentra pro vrstvu SAP DBMS nebo pro globální transportní adresář SAP
    - Nasazení dvou vrstev pomocí dvou různých dodavatelů cloudu. Například nasazení vrstvy DBMS do cloudové infrastruktury Oracle a aplikační vrstvy v Azure
- Konfigurace clusteru pro více instancí HANA Pacemaker
- Konfigurace clusterů Windows se sdílenými disky prostřednictvím SOFS nebo SMB v ANF pro databáze SAP podporované v systému Windows. Místo toho doporučujeme použití nativní replikace s vysokou dostupností pro konkrétní databáze a používání samostatných zásobníků úložiště.
- Nasazení databází SAP podporovaných v systému Linux s databázovými soubory umístěnými ve sdílených složkách NFS nad ANF s výjimkou SAP HANA
- Nasazení Oracle DBMS na jakýkoli jiný hostovaný operační systém než Windows a Oracle Linux. Viz také [SAP Support – poznámka #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scénáře, které jsme neotestovali, a proto nemají žádné zkušenosti se seznamem jako:

- Azure Site Recovery replikace virtuálních počítačů vrstvy DBMS. V důsledku toho doporučujeme využívat funkci nativní asynchronní replikace databáze pro možnou konfiguraci zotavení po havárii.
 

## <a name="next-steps"></a>Další kroky
Přečtěte si další kroky v tématu [plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](./planning-guide.md)




  