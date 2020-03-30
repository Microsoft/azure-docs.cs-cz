---
title: Důležité informace o nasazení dbms virtuálních počítačů Azure pro úlohy SAP | Dokumenty společnosti Microsoft
description: Důležité informace o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101366"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Důležité informace o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Tato příručka je součástí dokumentace o tom, jak implementovat a nasadit software SAP v Microsoft Azure. Než si přečtete tuto příručku, přečtěte si [průvodce plánováním a implementací][planning-guide]. Tento dokument popisuje obecné aspekty nasazení systémů DBMS souvisejících se systémem SAP na virtuálních počítačích Microsoft Azure pomocí možností infrastruktury Azure jako služby (IaaS).

Článek doplňuje instalační dokumentaci SAP a poznámky SAP, které představují primární zdroje pro instalace a nasazení softwaru SAP na daných platformách.

V tomto dokumentu jsou zavedeny důležité informace o spuštění systémů DBMS souvisejících s SAP ve virtuálních počítačích Azure. V této kapitole je několik odkazů na konkrétní systémy DBMS. Místo toho jsou konkrétní dbms systémy zpracovány v rámci tohoto dokumentu, po tomto dokumentu.

## <a name="definitions"></a>Definice
V celém dokumentu se používají tyto termíny:

* **IaaS**: Infrastruktura jako služba.
* **PaaS**: Platforma jako služba.
* **SaaS**: Software jako služba.
* **Komponenta SAP**: Samostatná aplikace SAP, například ERP Central Component (ECC), Business Warehouse (BW), Solution Manager nebo Enterprise Portal (EP). Komponenty SAP mohou být založeny na tradičních technologiích ABAP nebo Java nebo na aplikaci, která není založena na aplikaci netweaveru, jako jsou obchodní objekty.
* **Prostředí SAP**: Jedna nebo více součástí SAP logicky seskupených k provádění obchodních funkcí, jako je vývoj, zajištění kvality, školení, zotavení po havárii nebo výroba.
* **Sap krajina**: Tento termín odkazuje na celý majetek SAP v it prostředí zákazníka. Prostředí SAP zahrnuje všechna výrobní a neprodukční prostředí.
* **Systém SAP**: Kombinace vrstvy DBMS a aplikační vrstvy například vývojového systému SAP ERP, testovacího systému SAP Business Warehouse nebo výrobního systému SAP CRM. V nasazení Azure není podporováno rozdělení těchto dvou vrstev mezi místní a Azure. V důsledku toho se systém SAP nasadí místně nebo se nasadí v Azure. Můžete nasadit různé systémy prostředí SAP v Azure nebo místně. Můžete například nasadit vývojové a testovací systémy SAP CRM v Azure, ale nasadit produkční systém SAP CRM místně.
* **Mezi místními**: Popisuje scénář, kde se virtuální počítače nasazují do předplatného Azure, které má připojení site-to-site, multisite nebo Azure ExpressRoute mezi místními datovými centry a Azure. V běžné dokumentaci Azure tyto druhy nasazení jsou také popsány jako scénáře mezi místními. 

    Důvodem připojení je rozšíření místních domén, místní služby Active Directory a místního DNS do Azure. Místní prostředí se rozšiřuje na prostředky Azure předplatného. S tímto rozšířením virtuálních počítačích může být součástí místní domény. Uživatelé domény místní domény mohou přistupovat k serverům a spouštět služby na těchto virtuálních počítačích, jako jsou služby DBMS. Komunikace a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure je možné. Tento scénář je nejběžnější scénář, který se používá k nasazení prostředků SAP v Azure. Další informace naleznete v [tématu Plánování a návrh brány VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Mezimístní nasazení systémů SAP jsou místa, kde virtuální počítače Azure, které používají systémy SAP, jsou členy místní domény a jsou podporované pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporované pro nasazení částí nebo dokončení prostředí SAP do Azure. I spuštění celého prostředí SAP v Azure vyžaduje, aby tyto virtuální počítače byly součástí místní domény a služby Active Directory/LDAP. 
>
> V předchozích verzích dokumentace byly zmíněny scénáře hybridního IT. Termín *hybrid* je zakořeněný v tom, že existuje mezimístní připojení mezi místním a Azure. V tomto případě hybridní také znamená, že virtuální počítače v Azure jsou součástí místní služby Active Directory.
>
>

Některá dokumentace společnosti Microsoft popisuje scénáře mezi místními prostory trochu jinak, zejména pro konfigurace dbms s vysokou dostupností. V případě dokumentů souvisejících s SAP se scénář mezi místními prostory scvrkává na připojení služby Site-to-site nebo private [ExpressRoute](https://azure.microsoft.com/services/expressroute/) a na prostředí SAP, které je distribuováno mezi místní mi s Azure.

## <a name="resources"></a>Prostředky
Existují další články dostupné o zatížení SAP v Azure. Začněte s [úlohami SAP v Azure: Začněte](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) a pak si vyberte oblast zájmu.

Následující poznámky SAP se vztahují k SAP v Azure s ohledem na oblast, na kterou se vztahuje tento dokument.

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure |
| [2015553] |SAP v Microsoft Azure: Požadavky podpory |
| [1999351] |Poradce při potížích s rozšířeným monitorováním Azure pro SAP |
| [2178632] |Klíčové metriky monitorování sapu v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: Rozšířené monitorování |
| [2191498] |SAP na Linuxu s Azure: Vylepšené monitorování |
| [2039619] |Aplikace SAP v Microsoft Azure pomocí databáze Oracle: Podporované produkty a verze |
| [2233094] |DB6: Aplikace SAP v Azure pomocí IBM DB2 pro Linux, UNIX a Windows: Další informace |
| [2243692] |Linux na virtuálním počítači Microsoft Azure (IaaS): Problémy s licencí SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalace a upgrade |
| [2069760] |Instalace a upgrade systému Oracle Linux 7.x SAP |
| [1597355] |Doporučení pro swap-space pro Linux |
| [2171857] |Oracle Database 12c: Podpora souborového systému na Linuxu |
| [1114181] |Oracle Database 11g: Podpora souborového systému na Linuxu |


Informace o všech sap notes pro Linux najdete na [wiki komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Potřebujete pracovní znalosti architektury Microsoft Azure a o tom, jak se virtuální počítače Microsoft Azure nasazují a provozují. Další informace naleznete v [dokumentaci k Azure](https://docs.microsoft.com/azure/).

Obecně platí, že instalace a konfigurace systému Windows, Linux a DBMS jsou v podstatě stejné jako jakýkoli virtuální počítač nebo holý kovový počítač, který nainstalujete místně. Existují některé architektury a rozhodnutí implementace správy systému, které se liší při použití Azure IaaS. Tento dokument vysvětluje konkrétní rozdíly v architektuře a správě systému, na které se mají připravit při použití Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura úložiště virtuálního počítače pro nasazení RDBMS
Chcete-li postupovat podle této kapitoly, přečtěte si informace uvedené v [této kapitole][deployment-guide-3] [průvodce nasazením][deployment-guide]. Než si přečtete tuto kapitolu, musíte o různých řadách virtuálních zařízení a rozdílech mezi standardním a prémiovým úložištěm porozumět různým řadám virtuálních zařízení a vědět je. 

Další informace o Azure Storage pro virtuální počítače Azure najdete v tématu:

- [Úvod ke spravovaným diskům pro virtuální počítače Azure windows](../../windows/managed-disks-overview.md).
- [Úvod ke spravovaným diskům pro virtuální počítače Azure Linux](../../linux/managed-disks-overview.md).

V základní konfiguraci obvykle doporučujeme strukturu nasazení, kde jsou operační systém, DBMS a případné binární soubory SAP oddělené od databázových souborů. Doporučujeme, aby systémy SAP, které běží ve virtuálních počítačích Azure, měly základní virtuální pevný disk nebo disk, nainstalovaný s operačním systémem, spustitelnými soubory systému pro správu databází a spustitelnými soubory SAP. 

Data a soubory protokolu DBMS jsou uloženy ve standardním úložišti nebo ve standardním úložišti. Jsou uloženy na samostatných discích a připojené jako logické disky k původnímu virtuálnímu počítači image operačního systému Azure. Pro nasazení Linuxu jsou zdokumentována různá doporučení, zejména pro SAP HANA.

Při plánování rozložení disku najděte nejlepší rovnováhu mezi těmito položkami:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty VOPS jednoho disku.
* Propustnost dat na disk.
* Počet dalších datových disků možný na velikost virtuálního počítače.
* Celková propustnost úložiště, kterou může virtuální hod poskytnout.
* Latence, kterou můžou poskytnout různé typy Azure Storage.
* VM SLA.

Azure vynucuje kvótu VOPS na datový disk. Tyto kvóty se liší pro disky hostované ve standardním úložišti a úložiště premium. Latence vstupně-výstupních vstupně-výstupních objektů se také liší mezi dvěma typy úložiště. Prémiové úložiště poskytuje lepší latenci vstupně-výstupních prostor. 

Každý z různých typů virtuálních počítačů má omezený počet datových disků, které můžete připojit. Dalším omezením je, že pouze určité typy virtuálních počítačů můžete použít úložiště premium. Obvykle se rozhodnete použít určitý typ virtuálního počítače na základě požadavků na procesor a paměť. Můžete také zvážit požadavky na vstupně-výstupní operace, latence a propustnost disku, které se obvykle škálují podle počtu disků nebo typu disků úložiště premium. Počet viops a propustnost, které mají být dosaženy každý disk může diktovat velikost disku, zejména s úložištěm premium.

> [!NOTE]
> Pro nasazení DBMS doporučujeme použít úložiště premium pro všechna data, transakční protokol nebo znovu soubory. Nezáleží na tom, zda chcete nasadit produkční nebo neprodukční systémy.

> [!NOTE]
> Chcete-li využívat jedinečnou [jednotnou sla virtuálního počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)Azure , musí být všechny připojené disky typ úložiště premium, který zahrnuje základní virtuální pevný disk.

> [!NOTE]
> Hostování hlavních databázových souborů, jako jsou data a soubory protokolu, databází SAP na hardwaru úložiště, který se nachází v datových centrech třetích stran umístěných společně umístěnými k datovým centrům Azure, není podporováno. Pro úlohy SAP je podporováno jenom úložiště, které je reprezentované jako nativní služba Azure pro soubory protokolu dat a transakcí databází SAP.

Umístění databázových souborů a souborů protokolu a opakování a typ služby Azure Storage, který používáte, je definován požadavky na viopy, latenci a propustnost. Chcete-li mít dostatek videa videa videa, můžete být nuceni používat více disků nebo použít větší disk úložiště premium. Používáte-li více disků, vytvořte softwarový proužek na discích, které obsahují datové soubory nebo soubory protokolu a opakování. V takových případech jsou viposlužby a propustnost disku sla podkladových disků úložiště premium nebo maximální dosažitelné vipony standardních paměťových disků akumulační pro výslednou sadu proužků.

Jak již bylo uvedeno, pokud váš požadavek na vops překročí, co může poskytnout jeden virtuální pevný disk, vyvažte počet vod videa, které jsou potřebné pro databázové soubory, napříč několika virtuálními pevnými disky. Nejjednodušší způsob, jak distribuovat zatížení vops mezi disky je vytvořit softwarový proužek přes různé disky. Pak umístěte několik datových souborů SAP DBMS na luny vytesané ze softwarového proužek. Počet disků v pruhu je řízen požadavky iOP, požadavky na propustnost disku a požadavky na svazek.


---
> ![Windows][Logo_Windows] Windows
>
> Doporučujeme použít prostory úložiště Windows k vytvoření prokládaných sad napříč několika virtuálními počítači Azure. Používejte alespoň Windows Server 2012 R2 nebo Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> K vytvoření softwarového raidu na Linuxu jsou podporovány pouze MDADM a Logical Volume Manager (LVM). Další informace naleznete v tématu:
>
> - [Konfigurace softwarového raidu na Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) pomocí MDADM
> - [Konfigurace LVM na virtuálním počítači s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) pomocí LVM
>
>

---

> [!NOTE]
> Protože Azure Storage udržuje tři image virtuálních discích, nemá smysl konfigurovat redundanci při prokládání. Je třeba pouze nakonfigurovat prokládání tak, aby vstupně-pásma jsou distribuovány přes různé Virtuální počítače.
>

### <a name="managed-or-nonmanaged-disks"></a>Spravované nebo nespravované disky
Účet úložiště Azure je konstrukce správy a také předmětem omezení. Omezení se liší mezi účty standardního úložiště a účty úložiště premium. Informace o možnostech a omezeních najdete v tématu [Škálovatelnost úložiště Azure a cíle výkonu](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Pro standardní úložiště nezapomeňte, že je limit na viops na účet úložiště. Podívejte se na řádek, který obsahuje **celkovou míru požadavků** v článku [Škálovatelnost úložiště Azure a cíle výkonu](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). K dispozici je také počáteční limit na počet účtů úložiště na předplatné Azure. Vyvažte virtuální žena pro větší prostředí SAP napříč různými účty úložiště, abyste se vyhnuli omezením těchto účtů úložiště. To je zdlouhavá práce, když mluvíte o několika stovkách virtuálních počítačů s více než tisíci virtuálními počítači.

Vzhledem k tomu, že použití standardního úložiště pro nasazení DBMS ve spojení s úlohou SAP se nedoporučuje, odkazy a doporučení pro standardní úložiště jsou omezeny na tento krátký [článek](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Aby se microsoft vyhnul administrativní práci při plánování a nasazování virtuálních pevných disků napříč různými účty úložiště Azure, představil [azure spravované disky](https://azure.microsoft.com/services/managed-disks/) v roce 2017. Spravované disky jsou k dispozici pro standardní úložiště a úložiště premium. Hlavní výhody spravovaných disků ve srovnání s nespravovanými disky jsou:

- U spravovaných disků Azure automaticky distribuuje různé virtuální disky mezi různé účty úložiště v době nasazení. Tímto způsobem nejsou přístupů k přístupům k účtu úložiště pro svazek dat, vstupně-to-v.i. propustnost a vstupně-operace.
- Pomocí spravovaných disků Azure Storage respektuje koncepty sad dostupnosti Azure. Pokud je virtuální počítač součástí sady dostupnosti Azure, základní virtuální pevný disk a připojený disk virtuálního počítače se nasazují do různých domén selhání a aktualizací.


> [!IMPORTANT]
> Vzhledem k výhodám spravovaných disků Azure doporučujeme používat spravované disky Azure pro nasazení DBMS a nasazení SAP obecně.
>

Pokud chcete převést z nespravovaných na spravované disky, přečtěte si:

- [Převod virtuálního počítače se systémem Windows z nespravovaných disků na spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Převod virtuálního počítače Linux z nespravovaných disků na spravované disky](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Při připojení disků na virtuální počítače, můžete zvolit, zda vstupně-v., pokud je vstupně-v., jako uvaděč mezi virtuálním počítačem a těmi disky umístěnými v úložišti Azure v mezipaměti. Standardní a prémiové úložiště používá pro tento typ mezipaměti dvě různé technologie.

Následující doporučení předpokládají tyto charakteristiky vstupně-in/o pro standardní DBMS:

- Je to většinou čtení zatížení proti datovým souborům databáze. Tato čtení jsou důležité pro výkon systému DBMS.
- Zápis proti datovým souborům dochází v shluky na základě kontrolních bodů nebo konstantní datový proud. Zprůměrováno za den, je méně zápisů než čtení. Na rozdíl od čtení z datových souborů jsou tyto zápisy asynchronní a nezadržují žádné uživatelské transakce.
- Neexistují téměř žádné čtení z transakční protokol nebo znovu soubory. Výjimky jsou velké vstupně-va při provádění záloh protokolu transakcí.
- Hlavní zatížení proti transakce nebo znovu soubory protokolu je zápisy. V závislosti na povaze pracovního vytížení můžete mít vstupně-blou tak malé, jak 4 KB nebo v ostatních případech vstupně-va velikosti 1 MB nebo více.
- Všechny zápisy musí být trvalé na disku spolehlivým způsobem.

Pro standardní úložiště jsou možné typy mezipaměti:

* Žádný
* Čtení
* Čtení/zápis

Chcete-li dosáhnout konzistentního a deterministického výkonu, nastavte ukládání do mezipaměti na standardním úložišti pro všechny disky, které obsahují datové soubory související s dbms, soubory protokolu a opakování a místo v tabulce na **NONE**. Ukládání základního virtuálního pevného disku do mezipaměti může zůstat ve výchozím nastavení.

Pro prémiové úložiště existují následující možnosti ukládání do mezipaměti:

* Žádný
* Čtení
* Čtení/zápis
* Žádný + akcelerátor zápisu, který je jenom pro virtuální počítače Azure řady M
* Akcelerátor čtení + zápisu, který je jenom pro virtuální počítače Azure řady M

Pro úložiště premium doporučujeme použít **ukládání do mezipaměti pro datové soubory** databáze SAP a zvolit možnost Žádné ukládání do **mezipaměti pro disky souborů protokolu**.

Pro nasazení řady M doporučujeme použít Azure Write Accelerator pro nasazení DBMS. Podrobnosti, omezení a nasazení akcelerátoru zápisu Azure najdete v tématu [Povolení akcelerátoru zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Netrvalé disky Azure
Virtuální počítače Azure nabízejí netrvalé disky po nasazení virtuálního počítače. V případě restartování virtuálního počítače se vymaže veškerý obsah na těchto jednotkách. Je to dané, že datové soubory a protokol a znovu soubory databází by za žádných okolností být umístěny na těchto netrvalých jednotek. Mohou existovat výjimky pro některé databáze, kde tyto netrvalé jednotky mohou být vhodné pro tempdb a temp tablespaces. Nepoužívejte tyto jednotky pro virtuální aplikace řady A, protože tyto netrvalé jednotky mají omezenou propustnost s rodinou virtuálních společností. 

Další informace najdete [v tématu Principy dočasné jednotky na virtuálních počítačích s Windows v Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> Jednotka D v virtuálním počítači Azure je netrvalá jednotka, která je zálohována některými místními disky na výpočetním uzlu Azure. Vzhledem k tomu, že není trvalé, všechny změny provedené v obsahu na jednotce D jsou ztraceny při restartování virtuálního počítače. Změny zahrnují soubory, které byly uloženy, adresáře, které byly vytvořeny, a aplikace, které byly nainstalovány.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuální počítače Linux Azure automaticky připojují jednotku na /mnt/resource, která je netrvalá jednotka podporovaná místními disky na výpočetním uzlu Azure. Vzhledem k tomu, že není trvalé, všechny změny provedené v obsahu v /mnt/resource jsou ztraceny při restartování virtuálního počítače. Změny zahrnují soubory, které byly uloženy, adresáře, které byly vytvořeny, a aplikace, které byly nainstalovány.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odolnost úložiště Microsoft Azure
Úložiště Microsoft Azure storage ukládá základní virtuální pevný disk s os a připojenými disky nebo objekty BLOB na nejméně třech samostatných uzlech úložiště. Tento typ úložiště se nazývá místně redundantní úložiště (LRS). LRS je výchozí pro všechny typy úložiště v Azure.

Existují i jiné metody redundance. Další informace najdete v [tématu replikace služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Úložiště Premium je doporučený typ úložiště pro virtuální počítače DBMS a disky, které ukládají soubory databáze a protokoly a opakování. Jedinou dostupnou metodou redundance pro úložiště premium je LRS. V důsledku toho je třeba nakonfigurovat metody databáze povolit replikaci dat databáze do jiné oblasti Azure nebo zóny dostupnosti. Metody databáze zahrnují SQL Server Always On, Oracle Data Guard a HANA System Replication.


> [!NOTE]
> Pro nasazení DBMS se pro standardní úložiště nedoporučuje použití geograficky redundantního úložiště (GRS). GRS vážně ovlivňuje výkon a nerespektuje pořadí zápisu napříč různými virtuálními disponiály, které jsou připojeny k virtuálnímu virtuálnímu virtuálnímu virtuálnímu soudu. Nectít pořadí zápisu napříč různými virtuálními počítači potenciálně vede k nekonzistentní databáze na straně cíle replikace. K této situaci dochází, pokud databáze a protokol a znovu soubory jsou rozloženy mezi více Virtuální počítač, jako je tomu obecně, na straně zdrojového virtuálního počítači.



## <a name="vm-node-resiliency"></a>Odolnost uzlu virtuálního virtuálního jazyka
Azure nabízí několik různých služeb SLA pro virtuální počítače. Další informace naleznete v nejnovější verzi [sla pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Vzhledem k tomu, že vrstva DBMS je obvykle důležitá pro dostupnost v systému SAP, musíte pochopit, že skupiny dostupnosti, zóny dostupnosti a události údržby. Další informace o těchto konceptech najdete [v tématu Správa dostupnosti virtuálních počítačů SAP v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a Správa [dostupnosti virtuálních počítačů Linuxu v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minimální doporučení pro produkční scénáře DBMS s úlohou SAP je:

- Nasaďte dva virtuální počítače v samostatné sadě dostupnosti ve stejné oblasti Azure.
- Spusťte tyto dva virtuální počítače ve stejné virtuální síti Azure a mají síťové karty připojené ze stejných podsítí.
- Pomocí databázových metod udržujte aktivní pohotovostní režim s druhým virtuálním virtuálním připojením. Metody mohou být SQL Server Always On, Oracle Data Guard nebo HANA System Replication.

Můžete také nasadit třetí virtuální počítač v jiné oblasti Azure a použít stejné metody databáze k poskytování asynchronní repliky v jiné oblasti Azure.

Informace o tom, jak nastavit sady dostupnosti Azure, najdete [v tomto kurzu](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Důležité informace o síti Azure
Ve velkém měřítku nasazení SAP použijte podrobný plán [Virtuálního datového centra Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Použijte ji pro konfiguraci virtuální sítě a oprávnění a přiřazení rolí do různých částí vaší organizace.

Tyto osvědčené postupy jsou výsledkem stovek nasazení zákazníků:

- Virtuální sítě, do kterých je aplikace SAP nasazená, nemají přístup k internetu.
- Databázové virtuální počítače běží ve stejné virtuální síti jako aplikační vrstva.
- Virtuální počítače ve virtuální síti mají statické přidělení privátní IP adresy. Další informace najdete v tématu [typy IP adres a metody přidělení v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Omezení směrování do a z virtuálních počítačů DBMS *nejsou* nastaveny s firewally nainstalovanými na místních virtuálních počítačích DBMS. Místo toho je směrování provozu definováno [pomocí skupin zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Chcete-li oddělit a izolovat provoz na virtuální mísu DBMS, přiřaďte k virtuálnímu počítači různé síťové karty. Každá síťová karta získá jinou ADRESU IP a každá síťová karta je přiřazena k jiné podsíti virtuální sítě. Každá podsíť má jiná pravidla skupiny síťových sítí. Izolace nebo oddělení síťového provozu je opatření pro směrování. Nepoužívá se k nastavení kvót pro propustnost sítě.

> [!NOTE]
> Přiřazení statických IP adres prostřednictvím Azure znamená jejich přiřazení jednotlivým virtuálním síťovém terminálům. Nepřiřazujte virtuální nic v rámci hostovaného operačního programu statické IP adresy v rámci hostovaného operačního programu. Některé služby Azure, jako je Azure Backup, spoléhají na to, že alespoň primární virtuální rozhraní nove je nastaveno na DHCP a ne na statické IP adresy. Další informace najdete [v tématu Poradce při potížích se zálohováním virtuálních strojů Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud chcete virtuálnímu počítači přiřadit více statických IP adres, přiřaďte virtuální síťové karty k virtuálnímu počítači.
>


> [!IMPORTANT]
> Konfigurace [síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační cestě mezi aplikací SAP a vrstvou DBMS systému SAP NetWeaver-, Hybris-nebo S/4HANA není podporována. Toto omezení je z důvodů funkčnosti a výkonu. Komunikační cesta mezi aplikační vrstvou SAP a vrstvou DBMS musí být přímá. Omezení nezahrnuje [skupinu zabezpečení aplikace (ASG) a pravidla skupiny zabezpečení zabezpečení,](https://docs.microsoft.com/azure/virtual-network/security-overview) pokud tato pravidla ASG a NSG umožňují přímou komunikační cestu. 
>
> Další scénáře, kde nejsou podporována síťová virtuální zařízení, jsou v:
>
> * Komunikační cesty mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker a zařízení SBD, jak je popsáno v [části Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Komunikační cesty mezi virtuálními počítači Azure a souborovým serverem Windows Server s horizontálním navýšením kapacity (SOFS) jsou nastaveny tak, jak je popsáno v [clusteru, instanci SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Síťová virtuální zařízení v komunikačních cestách mohou snadno zdvojnásobit latenci sítě mezi dvěma komunikačními partnery. Mohou také omezit propustnost v kritických cestách mezi aplikační vrstvou SAP a vrstvou DBMS. V některých zákaznických scénářích mohou síťová virtuální zařízení způsobit selhání clusterů Pacemaker Linux. Jedná se o případy, kdy komunikace mezi uzly clusteru Linux Pacemaker komunikovat se svým zařízením SBD prostřednictvím síťového virtuálního zařízení.
>

> [!IMPORTANT]
> Dalším návrhem, který *není* podporován, je oddělení aplikační vrstvy SAP a vrstvy DBMS do různých virtuálních sítí Azure, které nejsou vzájemně [peered.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Doporučujeme oddělit aplikační vrstvu SAP a vrstvu DBMS pomocí podsítí v rámci virtuální sítě Azure namísto použití různých virtuálních sítí Azure. 
>
> Pokud se rozhodnete neřídit se doporučením a místo toho oddělit dvě vrstvy do různých virtuálních sítí, musí být dvě virtuální sítě [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Uvědomte si, že síťový provoz mezi [dvěma partnerskými virtuálními sítěmi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure podléhá nákladům na přenos. Obrovský objem dat, který se skládá z mnoha terabajtů, se vyměňuje mezi aplikační vrstvou SAP a vrstvou DBMS. Můžete akumulovat značné náklady, pokud aplikační vrstva SAP a vrstva DBMS jsou odděleny mezi dvěma partnerskými virtuálními sítěmi Azure.

Použijte dva virtuální počítače pro produkční nasazení DBMS v rámci sady dostupnosti Azure. Použijte také samostatné směrování pro aplikační vrstvu SAP a provoz správy a provozu na dva virtuální servery DBMS. Viz následující obrázek:

![Diagram dvou virtuálních připojení ve dvou podsítích](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Použití Azure Load Balancer k přesměrování provozu
Použití privátních virtuálních IP adres používaných ve funkcích, jako je SQL Server Always On nebo HANA System Replication, vyžaduje konfiguraci nástroje pro vyrovnávání zatížení Azure. Správce zatížení používá porty sondy k určení aktivního uzlu DBMS a směrování provozu výhradně do tohoto aktivního databázového uzlu. 

Pokud je převzetí služeb při selhání uzlu databáze, není nutné pro aplikaci SAP překonfigurovat. Místo toho nejběžnější architektury aplikací SAP znovu připojit proti privátní virtuální IP adresu. Mezitím nástroj pro vyrovnávání zatížení reaguje na převzetí služeb při selhání uzlu přesměrováním provozu proti privátní virtuální IP adresu do druhého uzlu.

Azure nabízí dva různé [skladové položky pro vyrovnávání zatížení:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)základní skladovou položku a standardní skladovou položku. Pokud nechcete nasadit napříč zónami dostupnosti Azure, základní skladová položka pro vyrovnávání zatížení je v pořádku.

Je provoz mezi virtuálními mandy DBMS a aplikační vrstvou SAP vždy směrován přes zařízení pro vyrovnávání zatížení po celou dobu? Odpověď závisí na způsobu konfigurace provyrovnávání zatížení. 

V tomto okamžiku příchozí provoz na virtuální ms DBMS je vždy směrována prostřednictvím správce zatížení. Odchozí trasa provozu z virtuálního počítače DBMS do virtuálního počítače aplikační vrstvy závisí na konfiguraci nástroje pro vyrovnávání zatížení. 

Nástroj pro vyrovnávání zatížení nabízí možnost DirectServerReturn. Pokud je tato možnost nakonfigurována, provoz směrovaný z virtuálního počítače DBMS do aplikační vrstvy SAP *není* směrován přes vyrovnávání zatížení. Místo toho přejde přímo do aplikační vrstvy. Pokud DirectServerReturn není nakonfigurován, je zpětný provoz do aplikační vrstvy SAP směrován přes nástroj pro vyrovnávání zatížení.

Doporučujeme nakonfigurovat DirectServerReturn v kombinaci s nástrojpro vyrovnávání zatížení, které jsou umístěny mezi aplikační vrstvou SAP a vrstvou DBMS. Tato konfigurace snižuje latenci sítě mezi dvěma vrstvami.

Příklad nastavení této konfigurace pomocí sql serveru always on najdete v [tématu Konfigurace naslouchací proces ILB pro skupiny dostupnosti always on v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Pokud používáte publikované šablony GitHub JSON jako referenci pro nasazení infrastruktury SAP v Azure, prostudujte si tuto [šablonu pro 3vrstvý systém SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). V této šabloně můžete také zobrazit správné nastavení pro vyrovnávání zatížení.

### <a name="azure-accelerated-networking"></a>Azure akcelerované sítě
Chcete-li dále snížit latenci sítě mezi virtuálními počítači Azure, doporučujeme zvolit [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Použijte ji při nasazení virtuálních počítačích Azure pro úlohy SAP, zejména pro aplikační vrstvu SAP a vrstvu SAP DBMS.

> [!NOTE]
> Ne všechny typy virtuálních počítačů podporují akcelerované sítě. V předchozím článku jsou uvedeny typy virtuálních počítačů, které podporují akcelerované sítě.
>

---
> ![Windows][Logo_Windows] Windows
>
> Informace o nasazení virtuálních počítačů pomocí akcelerované sítě pro Windows najdete [v tématu Vytvoření virtuálního počítače s Windows s akcelerací sítě](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Další informace o distribuci Linuxu najdete [v tématu Vytvoření virtuálního počítače s Linuxem se zrychlenou sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> V případě SUSE, Red Hat a Oracle Linux, Accelerated Networking je podporován s nedávnými verzemi. Starší verze, jako je SLES 12 SP2 nebo RHEL 7.2, nepodporují Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Nasazení monitorování hostitele
Pro produkční využití aplikací SAP ve virtuálních počítačích Azure sap vyžaduje možnost získat data monitorování hostitele z fyzických hostitelů, kteří spouštějí virtuální počítače Azure. Je vyžadována určitá úroveň opravy sap host agenta, která umožňuje tuto funkci v SAPOSCOL a SAP Host Agent. Přesná úroveň opravy je popsána v poznámce SAP [1409604].

Další informace o nasazení součástí, které doručují data hostitele agentovi SAPOSCOL a SAP Host Agent a správu životního cyklu těchto součástí, naleznete v [příručce nasazení][deployment-guide].


## <a name="next-steps"></a>Další kroky
Další informace o konkrétním systému DBMS naleznete v tématu:

- [Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP](dbms_guide_sqlserver.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](dbms_guide_oracle.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](dbms_guide_ibm.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](dbms_guide_sapase.md)
- [Nasazení SAP maxDB, Live Cache a Content Server v Azure](dbms_guide_maxdb.md)
- [Průvodce provozem SAP HANA v Azure](hana-vm-operations.md)
- [Sap HANA vysoká dostupnost pro virtuální počítače Azure](sap-hana-availability-overview.md)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](sap-hana-backup-guide.md)

