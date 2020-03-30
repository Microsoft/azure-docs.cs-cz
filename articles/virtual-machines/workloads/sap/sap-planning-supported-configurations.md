---
title: 'SAP v Azure: Podporované scénáře s virtuálními počítači Azure'
description: Podporované scénáře Virtuálních počítačů Azure s úlohami SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137624"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Úlohy SAP ve scénářích podporovaných virtuálním počítačem Azure
Navrhování architektury systémů SAP NetWeaver, `Hybris` Business one nebo S/4HANA v Azure otevírá mnoho různých příležitostí pro různé architektury a nástroje, které se můžete dostat k škálovatelnému, efektivnímu a vysoce dostupnému nasazení. Ačkoli závislé na operačním systému nebo DBMS používá, existují omezení. Také ne všechny scénáře, které jsou podporované místní jsou podporovány stejným způsobem v Azure. Tento dokument povede prostřednictvím podporovaných konfigurací bez vysoké dostupnosti a konfigurace s vysokou dostupností a architektury pomocí virtuálních počítačů Azure výhradně. Pro scénáře podporované [velkými instancemi HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)naleznete v článku [Podporované scénáře pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>Dvouvrstvá konfigurace
Konfigurace SAP 2 vrstvy se považuje za vytvořenou z kombinované vrstvy SAP DBMS a aplikační vrstvy, které běží na stejném serveru nebo jednotce virtuálního počítače. Druhá vrstva je považována za vrstvu uživatelského rozhraní. V případě dvouvrstvé konfigurace vrstvy aplikace DBMS a SAP sdílejí prostředky virtuálního počítače Azure. V důsledku toho je třeba nakonfigurovat různé součásti tak, aby tyto nesoutěží o prostředky. Musíte také být opatrní, abyste se nepřehlásili k odběru prostředků virtuálního soudu. Taková konfigurace neposkytuje žádné vysoké dostupnosti, mimo [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/) různých součástí Azure zapojených.

Grafické znázornění takové konfigurace může vypadat takto:

![Jednoduchá dvouvrstvá konfigurace](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Tyto konfigurace jsou podporovány pomocí windows, red hat, suse a oracle linux pro dbms systémy SQL Server, Oracle, Db2, maxDB a SAP ASE pro produkční a neprodukční případy. Pro SAP HANA jako DBMS je tento typ konfigurace podporován pouze pro neprodukční případy. To zahrnuje také případ nasazení [velkých instancí Azure HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
Pro všechny kombinace Operačního systému a DBMS podporované v Azure je tento typ konfigurace podporován. Je však povinné nastavit konfiguraci dbms a součásti SAP tak, aby součásti DBMS a SAP nesoutěží o paměť a prostředky procesoru a tím překročily fyzické dostupné prostředky. To je třeba provést omezením paměti DBMS je povoleno přidělit. Je také nutné omezit rozšířené paměti SAP na instancí aplikace. Je také potřeba sledovat spotřebu procesoru virtuálního počítače celkově a ujistěte se, že součásti nejsou maximalizaci prostředků procesoru. 

> [!NOTE]
> Pro produkční systémy SAP doporučujeme další konfigurace s vysokou dostupností a případným zotavením po havárii, jak je popsáno dále v tomto dokumentu


## <a name="3-tier-configuration"></a>Třívrstvá konfigurace
V takových konfiguracích oddělíte aplikační vrstvu SAP a vrstvu DBMS do různých virtuálních počítačů. Obvykle to děláte pro větší systémy a z důvodů, proč jsou flexibilnější na prostředky aplikační vrstvy SAP. V nejjednodušším nastavení neexistuje žádná vysoká dostupnost nad rámec [smluv o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/) různých součástí Azure. 

Grafické znázornění vypadá takto:

![Jednoduchá dvouvrstvá konfigurace](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Tento typ konfigurace je podporován v systémech Windows, Red Hat, SUSE a Oracle Linux pro systémy DBMS SQL Server, Oracle, Db2, SAP HANA, maxDB a SAP ASE pro produkční a neprodukční případy. Toto je výchozí konfigurace nasazení pro [velké instance Azure HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Pro zjednodušení jsme nerozlišují mezi SAP Central Services a SAP dialog instance v aplikační vrstvě SAP. V této jednoduché 3vrstvé konfiguraci by neexistovala žádná vysoká ochrana dostupnosti pro centrální služby SAP.

> [!NOTE]
> Pro produkční systémy SAP doporučujeme další konfigurace s vysokou dostupností a případným zotavením po havárii, jak je popsáno dále v tomto dokumentu


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Více instancí DBMS na jednotku velké instance virtuálního počítači nebo HANA
V tomto typu konfigurace hostujete více instancí DBMS na jednotku Azure VM nebo HANA Large Instance. Motivací může být mít méně operačních systémů pro údržbu a s tím snížení nákladů. Další motivací může být mít větší flexibilitu a efektivitu sdílením prostředků větší jednotky velké instance virtuálního počítači nebo HANA mezi více instancí DBMS. Zatím se tyto konfigurace zobrazovali převážně pro neprodukční systémy.

Taková konfigurace by mohla vypadat takto:

![Více instancí DBMS v jedné jednotce](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Tento typ nasazení systému DBMS je podporován pro:

- SQL Server v systému Windows
- IBM Db2. Podrobnosti naleznete v článku [Více instancí (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Pro Oracle. Podrobnosti viz [poznámka podpory SAP #1778431](https://launchpad.support.sap.com/#/notes/1778431) a související poznámky SAP
- Pro SAP HANA je podporováno více instancí na jednom virtuálním počítači, SAP volá tuto metodu nasazení MCOS. Podrobnosti naleznete v článku SAP [Multiple SAP HANA Systems on One Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Spuštění více instancí databáze na jednom hostiteli, musíte se ujistit, že různé instance nejsou konkurenční pro prostředky a tím překročit omezení fyzického prostředku virtuálního počítači. To platí zejména pro paměť, kde je potřeba završit paměti, kterou může přidělit kdokoli z instancí, které virtuální hod může přidělit. To také může platit pro prostředky procesoru různé instance databáze můžete využít. Všechny uvedené DBMS mají konfigurace, které umožňují omezení přidělení paměti a prostředků procesoru na úrovni instance.
Chcete-li mít podporu pro takovou konfiguraci pro virtuální počítače Azure, očekává se, že disky nebo svazky, které se používají pro data a soubory protokolu/opakování protokolu databází spravovaných různými instancemi jsou samostatné. Nebo jinými slovy, data nebo log/redo soubory protokolu databází spravovaných jinou instancí DBMS nemají sdílet stejné disky nebo svazky. 

Konfigurace disku pro velké instance HANA je nakonfigurována a je podrobně popsána v [podporovaných scénářích pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> Pro produkční systémy SAP doporučujeme další vysoké dostupnosti a případné konfigurace zotavení po havárii, jak je popsáno dále v tomto dokumentu. Virtuální počítače s více instancemi DBMS nejsou podporovány s vysokou dostupnostkonfigurace popsané dále v tomto dokumentu.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Více instancí dialogového okna SAP v jednom virtuálním počítači
V mnoha případech se nasazené více instancí dialogového okna na serverech holé metal nebo dokonce ve virtuálních počítačích spuštěných v privátních cloudech. Důvodem pro takové konfigurace bylo přizpůsobit určité instance dialogu SAP určitým pracovním vytížením, obchodním funkcím nebo typům úloh. Důvodem pro neizolace těchto instancí do samostatných virtuálních virtuálních měn byla snaha o údržbu a provoz operačního systému. Nebo v mnoha případech náklady v případě, že hoster nebo provozovatel vm žádá o měsíční poplatek za vod provozovaný a spravovat. V Azure je to scénář hostování více instancí dialogových oken SAP v rámci jednoho virtuálního počítače, který podporujeme pro produkční i neprodukční účely v operačních systémech Windows, Red Hat, SUSE a Oracle Linux. Parametr jádra SAP PHYS_MEMSIZE, který je k dispozici v systému Windows a moderních jádrech Linuxu, by měl být nastaven, pokud je na jednom virtuálním počítači spuštěno více instancí aplikačního serveru SAP. Doporučuje se také omezit rozšíření rozšířené paměti SAP v operačních systémech, jako je Systém Windows, kde je implementován automatický růst rozšířené paměti SAP. To lze provést pomocí parametru `em/max_size_MB`profilu SAP .

Při třívrstvé konfiguraci, kde se v azure virtuálních počítačích spouštějí více instancí oken SAP, může vypadat:

![Více instancí DBMS v jedné jednotce](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Pro zjednodušení jsme nerozlišují mezi SAP Central Services a SAP dialog instance v aplikační vrstvě SAP. V této jednoduché 3vrstvé konfiguraci by neexistovala žádná vysoká ochrana dostupnosti pro centrální služby SAP. U produkčních systémů se nedoporučuje ponechat centrální služby SAP bez ochrany. Specifika týkající se takzvaných konfigurací s více SID kolem centrálních instancí SAP a vysoké dostupnosti takových konfigurací více SID naleznete v dalších částech tohoto dokumentu.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Vysoká dostupnost ochrany pro vrstvu SAP DBMS
Při nasazování produkčních systémů SAP je třeba zvážit typ konfigurace s vysokou dostupností v pohotovostním režimu. Zejména s SAP HANA, kde data musí být načtena do paměti, než budete moci získat plný výkon a škálovatelnost zpět, azure service healing není ideálním měřítkem pro vysokou dostupnost. 

Obecně platí, že společnost Microsoft podporuje pouze konfigurace s vysokou dostupností a softwarové balíčky, které jsou popsány v části úlohy SAP v docs.microsoft.com. Stejný příkaz si můžete přečíst v [poznámce](https://launchpad.support.sap.com/#/notes/1928533)SAP #1928533 . Společnost Microsoft nebude poskytovat podporu pro jiné softwarové architektury jiných výrobců s vysokou dostupností, které nejsou společností Microsoft dokumentovány ve spojení s úlohou SAP. V takových případech je dodavatel architektury s vysokou dostupností, který je dodavatelem vysoké dostupnosti, podpůrnou stranou pro konfiguraci s vysokou dostupností, která musí být vámi jako zákazníkem zapojena do procesu podpory. Výjimky budou uvedeny v tomto článku. 

Obecně platí, že Microsoft podporuje omezenou sadu konfigurací s vysokou dostupností na virtuálních počítačích Azure nebo jednotkách velkých instancí HANA. Podporované scénáře velkých instancí HANA načtěte v dokumentu [Podporované scénáře pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Pro virtuální počítače Azure jsou na úrovni DBMS podporované následující konfigurace s vysokou dostupností:

- Sap HANA System Replication založený na Linux u kardiostimulátoru na SUSE a Red Hat. Podívejte se na podrobné články:
    - [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA horizontální navýšení kapacity n + m konfigurace pomocí [souborů Azure NetApp](https://azure.microsoft.com/services/netapp/) na SUSE a Red Hat. Podrobnosti jsou uvedeny v těchto článcích:
    - [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- Cluster sql serveru s podporou převzetí služeb při selhání založený na souborových službách Windows Scale-Out. Ačkoli doporučení pro produkční systémy je použití SQL Server vždy zapnuto namísto clustering. SQL Server Always On poskytuje lepší dostupnost pomocí samostatného úložiště. Podrobnosti jsou popsány v tomto článku: 
    - [Konfigurace instance clusteru s podporou převzetí služeb při selhání serveru SQL Server na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- SQL Server Always On je podporován operačním systémem Windows pro SQL Server v Azure. Toto je výchozí doporučení pro produkční instance SQL Serveru v Azure. Podrobnosti jsou popsány v těchto článcích:
    - [Představujeme skupiny dostupnosti SQL Server always on na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
    - [Nakonfigurujte skupinu dostupnosti vždy na virtuálních počítačích Azure v různých oblastech](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Konfigurace provyrovnávání zatížení pro skupinu dostupnosti always on v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Oracle Data Guard pro Windows a Oracle Linux. Podrobnosti o oracle linuxu naleznete v tomto článku:
    - [Implementace služby Oracle Data Guard na virtuálním počítači Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- ZDE je uvedena ibm Db2 HADR on SUSE a RHEL Podrobná dokumentace pro SUSE a RHEL pomocí kardiostimulátoru:
    - [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s kardiostimulátorem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Vysoká dostupnost IBM DB2 LUW ve virtuálních počítačích Azure na linuxovém serveru Red Hat Enterprise](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Konfigurace SAP ASE a SAP maxDB, jak je podrobně popsáno v těchto dokumentech:
    - [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Nasazení SAP MaxDB, liveCache a Content Server na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Scénáře vysoké dostupnosti HANA jsou podrobně popsány v:
    - [Podporované scénáře pro velké instance HANA- HSR s STONITH pro vysokou dostupnost](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Podporované scénáře pro velké instance HANA – automatické převzetí služeb při selhání hostitele (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Pro žádný z výše popsaných scénářů podporujeme konfigurace více instancí DBMS v jednom virtuálním počítači. Prostředky v každém z případů pouze jedna instance databáze lze nasadit na jeden virtuální počítač a chráněné popsané metody vysoké dostupnosti. Ochrana více instancí DBMS v rámci stejného clusteru s podporou převzetí služeb při selhání systému Windows nebo pacemakeru **není** v tomto okamžiku podporována. Oracle Data Guard je také podporována pro jednu instanci pouze pro případy nasazení virtuálních počítače. 

Různé databázové systémy umožňují hostovat více databází pod jednou instancí DBMS. Stejně jako v případě SAP HANA, více databází může být hostována ve více databázových kontejnerech (MDC). V případech, kdy tyto konfigurace s více databázemi pracují v rámci jednoho prostředku clusteru s podporou převzetí služeb při selhání, jsou tyto konfigurace podporovány. Konfigurace, které nejsou podporovány, jsou případy, kdy by bylo vyžadováno více prostředků clusteru. Pokud jde o konfigurace, kde byste definovat více skupin dostupnosti serveru SQL Server, pod jednu instanci serveru SQL Server.


![Konfigurace DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

V závislosti na DBMS nebo operačních systémech, součásti, jako je Azure vyrovnávání zatížení může nebo nemusí být požadováno jako součást architektury řešení. 

Konkrétně pro maxDB, konfigurace úložiště musí být různé. V maxDB data a soubory protokolu musí být umístěny na sdílenéúložiště pro konfiguraci s vysokou dostupností. Pouze v případě maxDB je sdílené úložiště podporováno pro vysokou dostupnost. Pro všechny ostatní zásobníky samostatného úložiště DBMS na uzel jsou pouze podporované konfigurace disků.

Je známo, že existují další architektury s vysokou dostupností, o kterých je známo, že běží také v Microsoft Azure. Společnost Microsoft však tyto architektury netestovala. Pokud chcete vytvořit konfiguraci s vysokou dostupností s těmito rámci, budete muset spolupracovat s poskytovatelem tohoto softwaru:

- Vývoj architektury nasazení
- Nasazení architektury
- Podpora architektury

> [!IMPORTANT]
> Microsoft Azure Marketplace nabízí celou řadu softwarových zařízení, která poskytují řešení úložiště nad nativním úložištěm Azure. Tyto měkké spotřebiče lze použít k vytvoření sdílené složky systému NFS také, že teoreticky by mohly být použity v nasazení sap hana horizontální navýšení kapacity, kde je vyžadován pohotovostní uzel. Z různých důvodů není žádný z těchto softwarových zařízení úložiště podporován pro žádné nasazení DBMS společností Microsoft a SAP v Azure. Nasazení DBMS na sdílené složky SMB není podporována vůbec v tomto okamžiku. Nasazení dbms na sdílené složky systému souborů NFS je omezena na sdílené složky nfs 4.1 na [soubory Azure NetApp](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Vysoká dostupnost pro centrální službu SAP
SAP Central Services je druhý jediný bod selhání konfigurace SAP. V důsledku toho budete muset chránit i tyto procesy centrálních služeb. Nabídka podporovaná a zdokumentovaná pro úlohy SAP zní jako:

- Clusterový server Windows s podporou převzetí služeb při selhání využívající souborové služby Windows SAFNT pro sapmnt a globální přenos. Podrobnosti jsou popsány v článku:
    - [Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Příprava infrastruktury Azure pro vysokou dostupnost SAP pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Clusterový server Windows s podporou převzetí služeb při selhání pomocí sdílené složky SMB založené na [souborech Azure NetApp](https://azure.microsoft.com/services/netapp/) pro sapmnt a globální adresář přenosu. Podrobnosti jsou uvedeny v článku:
    - [Vysoká dostupnost pro SAP NetWeaver ve virtuálních počítačích Azure ve Windows se soubory Azure NetApp (SMB) pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Clusterový server windows s podporou `Datakeeper`převzetí služeb při selhání založený na systému SIOS . Ačkoli dokumentováno společností Microsoft, potřebujete vztah podpory se SIOS, takže můžete při použití tohoto řešení zapojit podporu SIOS. Podrobnosti jsou popsány v článku:
    - [Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Kardiostimulátor na operačním systému SUSE s vytvořením vysoce dostupné sdílené `drdb` složky systému souborů NFS pomocí dvou virtuálních počítačů SUSE a pro replikaci souborů. Podrobnosti jsou popsány v článku
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Operační systém Pacemaker SUSE s využitím sdílených složek systému souborů [NFS poskytovaných soubory Azure NetApp](https://azure.microsoft.com/services/netapp/). Podrobnosti jsou zdokumentovány v
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server se soubory Azure NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Kardiostimulátor na operačním systému Red Hat se `glusterfs` sdílenou službou NFS hostovocí v clusteru. Podrobnosti naleznete v článcích
    - [Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Kardiostimulátor na operačním systému Red Hat s sdílenou službou NFS hostovohouji v [souborech Azure NetApp](https://azure.microsoft.com/services/netapp/). Podrobnosti jsou popsány v článku
    - [Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux u souborů Azure NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Z uvedených řešení, budete potřebovat podporu vztah se `Datakeeper` SIOS na podporu produktu a komunikovat s SIOS přímo v případě problémů. V závislosti na způsobu, jakým jste licencovali systém Windows, Red Hat a/nebo suse os, můžete také požadovat, abyste měli se svým poskytovatelem operačního systému smlouvu o podpoře, abyste měli plnou podporu uvedených konfigurací s vysokou dostupností.

Konfigurace může být také zobrazena jako:

![Konfigurace DBMS a ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Na pravé straně grafiky jsou zobrazeny vysoce dostupné služby SAP Central Services. Kromě toho, že služby SAP Central jsou chráněny pomocí clusteru s podporou převzetí služeb při selhání, který může v případě problému přepojit převzetí služeb při selhání, je nutné pro vysoce dostupnou sdílenou složku systému souborů NFS nebo SMB nebo sdílený disk systému Windows, abyste se ujistili, že je adresář sapmnt a globální přenos k dispozici nezávisle na existenci jednoho virtuálního soudu. Další některá řešení, jako je Clusterový server s podporou převzetí služeb při selhání Windows a kardiostimulátor, budou vyžadovat nástroj pro vyrovnávání zatížení Azure k přímému nebo opětovnému přesměrování provozu do uzlu v pořádku.

V zobrazeném seznamu není žádná zmínka o operačním systému Oracle Linux. Oracle Linux nepodporuje Pacemaker jako rámec clusteru. Pokud chcete nasadit systém SAP na Oracle Linux a potřebujete rámec vysoké dostupnosti pro Oracle Linux, musíte pracovat s dodavateli třetích stran. Jedním z dodavatelů je SIOS s jejich Protection Suite pro Linux, který je podporován SAP v Azure. Další informace naleznete v poznámce SAP [#1662610 - Podrobnosti o podpoře pro SIOS Protection Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610) pro více informací.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Podporované úložiště s výše uvedenými scénáři sap centrálních služeb
Vzhledem k tomu, že pouze podmnožina typů úložišť Azure poskytuje vysoce dostupné sdílené složky nfs nebo SMB, které kvalita pro použití v našich scénářích clusteru SAP Central Services seznam podporovaných typů úložišť

- Clusterový server Windows s podporou převzetí služeb při selhání se souborovým serverem Windows lze nasadit na všechny nativní typy úložišť Azure s výjimkou souborů Azure NetApp. Doporučení je však využít úložiště Premium kvůli vynikajícím smlouvám o úrovni služeb v propustnosti a vstupně-sadech.
- Clusterový server Windows s podporou převzetí služeb při selhání s smb v souborech Azure NetApp je podporovaný v souborech Azure NetApp. Sdílené složky SMB ve službách Souborů Azure **nejsou** v tomto okamžiku podporovány.
- Clusterový server Windows s podporou převzetí služeb `Datakeeper` při selhání se sdíleným diskem windows založenýna siosem lze nasadit na všechny nativní typy úložišť Azure s výjimkou souborů Azure NetApp. Doporučení je však využít úložiště Premium kvůli vynikajícím smlouvám o úrovni služeb v propustnosti a vstupně-sadech.
- SUSE nebo Red Hat Pacemaker pomocí sdílených složek nfs na soubory Azure NetApp je podporována na Soubory Azure NetApp. 
- SUSE Pacemaker `drdb` pomocí konfigurace mezi dvěma virtuálními počítači se podporuje pomocí nativních typů úložiště Azure, s výjimkou souborů Azure NetApp. Doporučení je však využít úložiště Premium kvůli vynikajícím smlouvám o úrovni služeb v propustnosti a vstupně-sadech.
- Red Hat Pacemaker, který používá `glusterfs` pro poskytování sdílené složky NFS, se podporuje pomocí nativních typů úložišť Azure, s výjimkou souborů Azure NetApp. Doporučení je však využít úložiště Premium kvůli vynikajícím smlouvám o úrovni služeb v propustnosti a vstupně-sadech.

> [!IMPORTANT]
> Microsoft Azure Marketplace nabízí celou řadu softwarových zařízení, která poskytují řešení úložiště nad nativním úložištěm Azure. Tyto měkké spotřebiče lze použít k vytvoření sdílené složky nfs nebo smb, které teoreticky mohou být použity také v clusterech sap centrálních služeb s podporou převzetí služeb při selhání. Tato řešení nejsou přímo podporována pro zatížení SAP společností Microsoft. Pokud se rozhodnete použít takové řešení k vytvoření sdílené složky systému nfs nebo smb, musí podporu konfigurace centrální služby SAP poskytovat třetí strana, která software vlastní v měkkém zařízení úložiště.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Clustery s podporou převzetí služeb SAP s více sidmi centrálních služeb převzetí služeb převzetí služeb
Chcete-li snížit počet virtuálních počítačů, které jsou potřeba ve velkých prostředích SAP, SAP umožňuje spustit SAP Central Services instance více různých systémů SAP v konfiguraci clusteru s podporou převzetí služeb při selhání. Představte si případy, kdy máte 30 nebo více netweaver nebo S/4HANA produkčních systémů. Bez clusteringu s více SID by tyto konfigurace vyžadovaly 60 nebo více virtuálních počítačů v konfiguracích clusteru s 30 nebo více windows nebo pacemakerem s podporou převzetí služeb při selhání. Kromě clusterů s podporou převzetí služeb při selhání DBMS je nutné. Nasazení více centrálních služeb SAP ve dvou uzlech v konfiguraci clusteru s podporou převzetí služeb při selhání může výrazně snížit počet virtuálních počítačů. Nasazení více instancí služeb SAP Central v konfiguraci clusteru s jedním dvěma uzly má však také některé nevýhody. Problémy kolem jednoho virtuálního počítače v konfiguraci clusteru platí pro více systémů SAP. Údržba hostovaného operačního systému spuštěnév konfiguraci clusteru vyžaduje větší koordinaci, protože jsou ovlivněny více produkčních systémů SAP. Nástroje jako SAP LaMa nepodporují clustering s více SID v procesu klonování systému.

V Azure je pro operační systém Windows s ENSA1 a ENSA2 podporována konfigurace clusteru s více SID. Doporučení není kombinovat starší architekturu služby Replikace fronty (ENSA1) s novou architekturou (ENSA2) v jednom clusteru s více SID. Podrobnosti o takové architektuře jsou popsány v článcích

- [Sap ASCS/SCS instance multi-SID vysoká dostupnost s Windows Server převzetí služeb při selhání clustering a sdílený disk v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Sap ASCS/SCS instance multi-SID vysoká dostupnost s Windows Server převzetí služeb při selhání clustering a sdílení souborů v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Pro SUSE je podporován také cluster s více SID založenýna kardiostimulátorem. Zatím je konfigurace podporována pro:

- Maximálně pět instancí SAP ASCS/SCS
- Stará architektura serveru pro replikaci fronty (ENSA1)
- Konfigurace clusteru Kardiostimulátor u dvou uzlů

Konfigurace je zdokumentována v [dokumentu Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro SAP aplikace multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Cluster s více sidy se serverem fronty replikace schematicky vypadá

![Konfigurace DBMS a ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Sap HANA scénáře horizontálního navýšení kapacity
Sap HANA scénáře horizontálního navýšení kapacity jsou podporovány pro podmnožinu virtuálních počítačů Azure s certifikací HANA, jak je uvedeno v [hardwarovém adresáři SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Všechny virtuální chody označené "Ano" ve sloupci Clustering lze použít pro horizontální navýšení kapacity OLAP nebo S/4HANA. Konfigurace bez pohotovostního režimu jsou podporované s typy úložiště Azure: 

- Azure Premium Storage, včetně akcelerátoru zápisu Azure pro svazek /hana/log
- [Disky Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Sap HANA horizontální navýšení kapacity konfigurace pro OLAP nebo S/4HANA s pohotovostními uzly jsou podporovány výhradně s nfs sdílené hostované na Soubory Azure NetApp.

Další informace o přesných konfiguracích úložiště s pohotovostním uzlinou nebo bez ní naleznete v článcích:

- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Nasazení horizontálního navýšení kapacity systému SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure pomocí souborů Azure NetApp na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Poznámka podpory SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Podrobnosti o podporovaných konfiguracích hana horizontálního navýšení kapacity HANA platí následující dokumentace:

- [Podporované scénáře pro horizontální navýšení kapacity velkých instancí HANA s pohotovostním režimem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Podporované scénáře pro horizontální navýšení kapacity velkých instancí HANA bez pohotovostního režimu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scénář zotavení po havárii
Existuje celá řada scénářů zotavení po havárii, které jsou podporovány. Architektury disaster definujeme jako architektury, které by měly kompenzovat dokončení oblastí Azure, které se vymyká z mřížky. To znamená, že potřebujeme cíl zotavení po havárii, aby se jiný Azure oblasti jako cíl pro spuštění prostředí SAP. Oddělujeme metody a konfigurace ve vrstvě DBMS a bez DBMS. 

### <a name="dbms-layer"></a>Vrstva DBMS
Pro vrstvu DBMS jsou podporovány konfigurace pomocí nativních replikačních mechanismů DBMS, jako jsou Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On nebo HANA System Replication. Je povinné, že tok replikace v takových případech je asynchronní, namísto synchronní jako v typické scénáře s vysokou dostupností, které jsou nasazeny v rámci jedné oblasti Azure. Typický příklad takové podporované konfigurace zotavení po havárii dbms je popsán v článku [dostupnost SAP HANA napříč oblastmi Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions). Druhá grafika v této části popisuje scénář s HANA jako příklad. Hlavní databáze podporované pro aplikace SAP lze v takovém scénáři nasadit.

Je podporováno použití menšího virtuálního virtuálního serveru jako cílové instance v oblasti zotavení po havárii, protože tento virtuální modul nedochází k plnému provozu pracovního vytížení. Přitom je třeba mít na paměti následující skutečnosti:

- Menší typy virtuálních počítačů neumožňují, že mnoho disků připojených než menší virtuální počítače
- Menší virtuální ms mají menší propustnost sítě a úložiště
- Změna velikosti mezi rodinami virtuálních počítačů může být problém, když se různé virtuální počítače shromažďují v jedné sadě dostupnosti Azure nebo když by se mělo dojít k přenastavení velikosti mezi rodinou M-Series a mv2 řady virtuálních počítače
- Využití procesoru a paměti pro instanci databáze je schopen přijímat datový proud změn s minimálním zpožděním a dostatek prostředků procesoru a paměti použít tyto změny s minimálním zpožděním na data  

Další podrobnosti o omezenírůzných velikostech virtuálních počítačů naleznete [zde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Další podporovanou metodou nasazení cíle zotavení po Havárii je mít na virtuálním počítači nainstalovanou druhou instanci DBMS, která spouští neprodukční instanci DBMS neprodukční instance SAP. To může být trochu náročnější, protože je třeba zjistit, co na paměti, prostředky procesoru, šířku pásma sítě a šířku pásma úložiště je potřeba pro konkrétní cílové instance, které by měly fungovat jako hlavní instance ve scénáři zotavení po havárii. Zejména v HANA je důrazně doporučeno, abyste konfigurovali instanci, která funguje jako cíl zotavení po havárii na sdíleném hostiteli tak, aby data nebyla předem načtena do cílové instance zotavení po Havárii.

Pro scénáře HANA Velké instance zotavení po havárii zkontrolujte tyto dokumenty:

- [Jeden uzel s zotavenípo uhlazenou pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Jeden uzel s DR (víceúčelový) pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Jeden uzel s DR (víceúčelový) pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Vysoká dostupnost s HSR a DR s replikací úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Horizontální navýšení kapacity pomocí zotavení po havárii pomocí replikace úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Jeden uzel s DR pomocí HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Jeden uzel HSR do DR (optimalizované pro náklady)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Vysoká dostupnost a zotavení po havárii pomocí hsr](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Vysoká dostupnost a zotavení po havárii pomocí hsr (optimalizované pro náklady)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Horizontální navýšení kapacity pomocí zotavení po havárii pomocí hsr](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Využití [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nebyltestován na nasazení DBMS v rámci úlohy SAP. V důsledku toho není podporována pro vrstvu DBMS systémů SAP v tomto okamžiku. Jiné metody replikace společností Microsoft a SAP, které nejsou uvedeny, nejsou podporovány. Použití softwaru třetích stran pro replikaci vrstvy DBMS systémů SAP mezi různými oblastmi Azure musí být podporováno dodavatelem softwaru a nebude podporováno prostřednictvím podpůrných kanálů Microsoftu a SAP. 
 
## <a name="non-dbms-layer"></a>Vrstva bez DBMS
Pro aplikační vrstvu SAP a případná sdílení nebo umístění úložišť, které jsou potřeba, jsou zákazníci využívány dvěma hlavními scénáři:

- Cíle zotavení po havárii v druhé oblasti Azure se nepoužívají pro žádné produkční nebo neprodukční účely. V tomto scénáři virtuální počítače, které fungují jako cíl zotavení po havárii nejsou v ideálním případě nenasazené a image a změny bitové kopie produkční aplikační vrstvy SAP je replikována do oblasti zotavení po havárii. Funkce, která může provádět takové úkoly, je [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). Azure Site Recovery podporuje scénáře replikace Azure-Azure, jako je tento. 
- Cíle zotavení po havárii jsou virtuální počítače, které jsou ve skutečnosti používány neprodukčními systémy. Celé prostředí SAP se rozprostírá ve dvou různých oblastech Azure s produkčními systémy obvykle v jedné oblasti a neprodukčními systémy v jiné oblasti. V mnoha nasazeních zákazníků má zákazník neprodukční systém, který je ekvivalentní produkčnímu systému. Zákazník má instancí produkčních aplikací předinstalovaných v neprodukčních systémech aplikační vrstvy. V případě převzetí služeb při selhání by se neprodukční instance vypnuly, virtuální názvy produkčních virtuálních počítačů se přesunuly do neprodukčních virtuálních počítačů (po přiřazení nových IP adres ve službě DNS) a předinstalované produkční instance začínají

### <a name="sap-central-services-clusters"></a>Clustery centrálních služeb SAP
Clustery centrálních služeb SAP, které používají sdílené disky (Windows), sdílené složky SMB (Windows) nebo sdílené složky systému NFS, jsou o něco obtížněji replikovány. Na straně Windows je možné řešení replikace úložiště systému Windows. Na Linuxu je rsync životaschopné řešení.



## <a name="non-supported-scenario"></a>Nepodporovaný scénář
Existuje seznam scénářů, které nejsou podporovány pro úlohy SAP na architekturách Azure. **Není podporováno** znamená, že společnosti SAP a Microsoft nebudou moci podporovat tyto konfigurace a musí se podřídit případné zúčastněné třetí straně, která poskytla software k vytvoření takových architektur. Dvě z těchto kategorií jsou:

- Měkké spotřebiče úložiště: Na azure marketplace je nabízena řada softwarových zařízení úložiště. Někteří dodavatelé nabízejí vlastní dokumentaci o tom, jak používat tyto měkké zařízení úložiště v Azure související se softwarem SAP. Dodavatel těchto měkkých spotřebičů pro skladování musí poskytovat podporu konfigurací nebo nasazení zahrnujících tyto měkké spotřebiče pro ukládání. Tato skutečnost se projevuje také v [poznámce podpory SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Rámce s vysokou dostupností: Podporované jsou pouze pacemaker a cluster windows server s podporou převzetí služeb při selhání pro úlohy SAP v Azure. Jak již bylo zmíněno dříve, `Datakeeper` řešení SIOS je popsáno a zdokumentováno společností Microsoft. Nicméně součásti SIOS `Datakeeper` musí být podporovány prostřednictvím SIOS jako dodavatele, který tyto komponenty poskytuje. SAP také v různých poznámkách SAP uvedl další certifikované rámce vysoké dostupnosti. Některé z nich byly certifikovány také dodavatelem třetí strany pro Azure. Nicméně podpora pro konfigurace pomocí těchto produktů musí být poskytována dodavatelem produktu. Různí dodavatelé mají různé integrace do procesů podpory SAP. Měli byste objasnit, jaký proces podpory funguje nejlépe pro konkrétního dodavatele, než se rozhodnete použít produkt v konfiguracích SAP nasazených v Azure.
- Sdílené diskové clustery, ve kterých jsou na sdílených discích soubory databáze, nejsou podporovány s výjimkou technologie maxDB. Pro všechny ostatní databáze je podporované řešení mít samostatná umístění úložišť namísto sdílené složky SMB nebo NFS nebo sdíleného disku pro konfiguraci scénářů s vysokou dostupností

Další scénáře, které nejsou podporovány, jsou scénáře jako:

- Scénáře nasazení, které zavádějí větší latenci sítě mezi aplikační vrstvou SAP a vrstvou SAP DBMS ve společné `Hybris`architektuře SAP, jak je znázorněno v NetWeaver, S/4HANA a např. To zahrnuje:
    - Nasazení jedné z úrovní v místním prostředí, zatímco druhá vrstva se nasazuje v Azure
    - Nasazení aplikační vrstvy SAP systému v jiné oblasti Azure než úroveň DBMS
    - Nasazení jedné vrstvy v datových centrech, které jsou umístěny společně do Azure a další vrstvy v Azure, s výjimkou případů, kdy takové vzory architektury jsou poskytovány nativní službou Azure
    - Nasazení síťových virtuálních zařízení mezi aplikační vrstvou SAP a vrstvou DBMS
    - Využití úložiště, které je hostované v datových centrech umístěných společně v datovém centru Azure pro globální přenosový adresář úrovně SAP DBMS nebo SAP
    - Nasazení dvou vrstev se dvěma různými dodavateli cloudu. Například nasazení úrovně DBMS v infrastruktuře Oracle Cloud infrastructure a aplikační vrstvy v Azure
- Konfigurace clusteru HANA Pacemaker s více instancemi
- Konfigurace clusteru Windows se sdílenými disky prostřednictvím sofs nebo SMB v databázích ANF pro SAP podporovaných v systému Windows. Místo toho doporučujeme použití nativní replikace s vysokou dostupností jednotlivých databází a použití samostatných zásobníků úložiště.
- Nasazení databází SAP podporovaných na Linuxu s databázovými soubory umístěnými ve sdílených složkách NFS nad ANF s výjimkou SAP HANA
- Nasazení oracle DBMS na jakémkoli jiném hostovaném operačním systému než Windows a Oracle Linux. Viz také [poznámka podpory SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scénář (y), které jsme netestovali, a proto nemají žádné zkušenosti se seznamem, jako je:

- Azure Site Recovery replikuje virtuální počítače vrstvy DBMS. V důsledku toho doporučujeme využít nativní funkce replikace databáze pro potenciální konfiguraci zotavení po havárii
 

## <a name="next-steps"></a>Další kroky
Přečtěte si další kroky v [plánování a implementaci virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



