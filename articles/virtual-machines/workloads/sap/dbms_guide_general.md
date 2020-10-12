---
title: Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP | Microsoft Docs
description: Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, DBMS, úložiště, Ultra disk, Premium Storage
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ac3a43776ee71716e618d7a1698aa1915d3d1b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331348"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP
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



Tato příručka je součástí dokumentace k implementaci a nasazení softwaru SAP na Microsoft Azure. Před čtením tohoto průvodce si přečtěte článek [Průvodce plánováním a implementací][planning-guide] a články, na které vás Průvodce plánováním odkazuje. Tento dokument popisuje obecné aspekty nasazení systémů DBMS souvisejících s SAP na Microsoft Azure virtuálních počítačích s využitím možností infrastruktury Azure jako služby (IaaS).

Papír doplňuje dokumentaci k instalaci SAP a poznámky SAP, které reprezentují primární prostředky pro instalace a nasazení softwaru SAP na daných platformách.

V tomto dokumentu jsou představeny požadavky na spouštění systémů DBMS souvisejících s SAP ve virtuálních počítačích Azure. V této kapitole je několik odkazů na konkrétní systémy DBMS. Místo toho se v tomto dokumentu po tomto dokumentu zpracují konkrétní systémy DBMS.

## <a name="definitions"></a>Definice
V celém dokumentu se používají tyto výrazy:

* **IaaS**: infrastruktura jako služba.
* **PaaS**: platforma jako služba.
* **SaaS**: software jako služba.
* **Součást SAP**: samostatná aplikace SAP, jako je například součást ERP Central (ECC), Business Warehouse (černobílý), správce řešení nebo Enterprise Portal (EP). Komponenty SAP můžou být založené na tradičních technologiích ABAP a Java nebo v aplikacích, které nejsou založené na NetWeaver, jako jsou třeba obchodní objekty.
* **Prostředí SAP**: jedna nebo víc komponent SAP se logicky seskupují tak, aby prováděly obchodní funkce, jako je vývoj, zabezpečování kvality, školení, zotavení po havárii nebo produkce.
* **SAP na šířku**: Tento pojem odkazuje na celé prostředky SAP v zákaznickém oddělení IT. SAP na úrovni zahrnuje všechna produkční a nevýrobní prostředí.
* **Systém SAP**: kombinace vrstvy DBMS a aplikační vrstvy, například vývojového systému SAP ERP, testovacího systému SAP Business Warehouse nebo produkčního systému SAP CRM. V nasazeních Azure se nepodporují vydělení těchto dvou vrstev mezi místními a Azure. V důsledku toho je systém SAP buď nasazený místně, nebo je nasazený v Azure. Můžete nasadit různé systémy SAP na šířku v Azure i v místním prostředí. Můžete například nasadit vývojové a testovací systémy SAP CRM do Azure, ale nasadit produkční systém SAP CRM do místního prostředí.
* **Mezi různými**místy: popisuje situaci, kdy se virtuální počítače nasazují do předplatného Azure, které má připojení typu Site-to-site nebo Azure ExpressRoute mezi místními datovými centry a Azure. V běžné dokumentaci k Azure jsou tyto typy nasazení popsány také jako mezi místními scénáři. 

    Důvodem připojení je rozšiřování místních domén, místní služby Active Directory a místní DNS do Azure. Místní orientace se rozšiřuje na prostředky v rámci předplatného Azure. S tímto rozšířením můžou být virtuální počítače součástí místní domény. Uživatelé domény v místní doméně mají přístup k serverům a spouštějí služby na těchto virtuálních počítačích, jako jsou například služby DBMS. Je možné komunikovat a překlad názvů mezi virtuálními počítači nasazenými místně a virtuálními počítači nasazenými v Azure. Tento scénář je nejběžnějším scénářem, který se používá k nasazení prostředků SAP v Azure. Další informace najdete v tématu [plánování a návrh pro bránu VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md).

> [!NOTE]
> Mezi místní nasazení systémů SAP patří virtuální počítače Azure, které spouštějí systémy SAP, jsou členy místní domény a jsou podporované pro produkční systémy SAP. Konfigurace mezi různými místy se podporují pro nasazování částí nebo kompletního SAP krajiny do Azure. I když v Azure běží kompletní prostředí SAP na pracovišti, musí být tyto virtuální počítače součástí místní domény a služby Active Directory/LDAP. 
>
> V předchozích verzích dokumentace byly zmíněny hybridní scénáře IT. Pojem *Hybrid* je ve skutečnosti rootem, protože mezi místními prostředími a Azure je připojení mezi různými místy. V takovém případě Hybrid také znamená, že virtuální počítače v Azure jsou součástí místní služby Active Directory.
>
>

Některá dokumentace Microsoftu popisuje různé scénáře různých scénářů, a to hlavně v případě konfigurací s vysokou dostupností systému DBMS. V případě dokumentů souvisejících s SAP se scénář mezi místními systémy doloží na připojení typu Site-to-site nebo Private [ExpressRoute](https://azure.microsoft.com/services/expressroute/) a na šířku SAP, která je distribuována mezi místními a Azure.

## <a name="resources"></a>Zdroje a prostředky
V úlohách SAP v Azure jsou k dispozici další články. Začněte s [úlohou SAP v Azure:](./get-started.md) začněte a pak zvolte svou oblast zájmu.

Následující poznámky SAP souvisejí s SAP v Azure v souvislosti s oblastí pokrytou v tomto dokumentu.

| Číslo poznámky | Nadpis |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: podporované produkty a typy virtuálních počítačů Azure |
| [2015553] |SAP v Microsoft Azure: požadavky na podporu |
| [1999351] |Řešení potíží s vylepšeným monitorováním Azure pro SAP |
| [2178632] |Klíčové metriky monitorování pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: rozšířené monitorování |
| [2191498] |SAP v systému Linux s Azure: rozšířené monitorování |
| [2039619] |Aplikace SAP na Microsoft Azure s využitím databáze Oracle: podporované produkty a verze |
| [2233094] |DB6: aplikace SAP v Azure s využitím IBM DB2 pro Linux, UNIX a Windows: Další informace |
| [2243692] |Virtuální počítač se systémem Linux na Microsoft Azure (IaaS): problémy s licencí SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7. x: instalace a upgrade |
| [2069760] |Instalace a upgrade systému Oracle Linux 7. x SAP |
| [1597355] |Doporučení pro zaměněné místo pro Linux |
| [2171857] |Oracle Database 12c: Podpora systému souborů na platformě Linux |
| [1114181] |Oracle Database 11g: Podpora systému souborů na platformě Linux |


Informace o všech poznámkách SAP pro Linux najdete na [wikiwebu komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Potřebujete praktické znalosti architektury Microsoft Azure a jak se nasazují a provozují Microsoft Azure virtuální počítače. Další informace najdete v [dokumentaci k Azure](../../../index.yml).

Obecně platí, že instalace a konfigurace systému Windows, Linux a DBMS jsou v podstatě stejné jako u libovolného virtuálního počítače nebo holého počítače, který instalujete místně. Pokud používáte Azure IaaS, existují určitá rozhodnutí o implementaci architektury a správy systému, která se liší. Tento dokument vysvětluje konkrétní rozdíly v architektuře a správě systémů, které je potřeba připravit při používání Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura úložiště virtuálního počítače pro nasazení RDBMS
Pokud chcete postupovat podle této kapitoly, přečtěte si informace uvedené v části:

- [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](./planning-guide.md)
- [Typy služby Azure Storage pro úlohy SAP](./planning-guide-storage.md)
- [Který software SAP se podporuje pro nasazení Azure](./sap-supported-product-on-azure.md)
- [Podporované scénáře pro úlohy SAP na virtuálních počítačích Azure](./sap-planning-supported-configurations.md) 

Před čtením této kapitoly musíte pochopit a znát různé VM-Series a rozdíly mezi úložištěm Standard a Premium. 

U blokového úložiště Azure se důrazně doporučuje použití Azure Managed disks. Podrobnosti o službě Azure Managed disks najdete v článku [Úvod ke spravovaným diskům pro virtuální počítače Azure](../../managed-disks-overview.md).

V základní konfiguraci obvykle doporučujeme strukturu nasazení, ve které jsou operační systém, systémy DBMS a konečné binární soubory SAP oddělené od databázových souborů. Pokud chcete změnit předchozí doporučení, doporučujeme, abyste měli samostatné disky Azure pro:

- Operační systém (základní virtuální pevný disk nebo virtuální pevný disk s operačním systémem)
- Spustitelné soubory systému správy databáze
- Spustitelné soubory SAP jako/usr/SAP

Konfigurace, která tyto součásti odděluje na třech různých discích Azure, může mít za následek vyšší odolnost, protože nadměrné zápisy protokolů nebo výpisů paměti pomocí DBMS nebo spustitelných souborů SAP nekolidují s diskovými kvótami disku s operačním systémem. 

Data a soubory protokolu transakcí a transakcí DBMS se ukládají v úložišti blokových dat podporovaném Azure nebo v Azure NetApp Files. Ukládají se na samostatné disky a připojují se jako logické disky k původnímu virtuálnímu počítači bitové kopie operačního systému Azure. Pro nasazení systému Linux jsou popsána různá doporučení, zejména pro SAP HANA. Přečtěte si článek [Azure Storage typy pro úlohy SAP](./planning-guide-storage.md) pro funkce a podporu různých typů úložiště pro váš scénář. 

Při plánování rozložení disku Najděte nejlepší rovnováhu mezi těmito položkami:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty IOPS pro jeden disk nebo sdílenou složku NFS.
* Propustnost dat na disk nebo sdílenou složku NFS.
* Počet dalších datových disků možných pro jednotlivé velikosti virtuálních počítačů.
* Celková propustnost úložiště nebo sítě, kterou může virtuální počítač poskytnout.
* Může poskytovat různé typy Azure Storage latence.
* SLA virtuálního počítače.

Azure vynutil kvótu IOPS za sekundu na datový disk nebo sdílenou složku NFS. Tyto kvóty se liší pro disky hostované v různých řešeních a sdílených složkách Azure Block Storage. I/O latence se také liší mezi různými typy úložišť. 

Každý z různých typů virtuálních počítačů má omezený počet datových disků, které můžete připojit. Dalším omezením je, že může používat jenom určitý typ virtuálních počítačů, například Premium Storage. Obvykle se rozhodnete použít určitý typ virtuálního počítače na základě požadavků na procesor a paměť. Můžete také zvážit požadavky na vstupně-výstupní operace IOPS, latence a propustnosti disku, které se obvykle škálují s počtem disků nebo typem prémiových disků úložiště. Počet vstupně-výstupních operací a propustnosti, které má každý disk dosáhnout, může určovat velikost disku, zejména úložiště Premium.

> [!NOTE]
> Pro nasazení DBMS doporučujeme sdílené složky systému souborů NFS služby Azure Premium Storage, Ultra disk nebo Azure NetApp Files na bázi NFS (výhradně pro SAP HANA) pro všechny data, transakční protokol nebo soubory pro opětovné provedení. Nezáleží na tom, jestli chcete nasadit produkční nebo nevýrobní systémy.

> [!NOTE]
> Aby bylo možné využít [smlouvu SLA pro jednotlivé virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)v Azure, musí být všechny připojené disky ve službě Azure Premium Storage nebo v Azure Ultra disk, což zahrnuje základní virtuální pevný disk (Azure Premium Storage).

> [!NOTE]
> Hostování hlavních databázových souborů, jako jsou data a soubory protokolů, pro databáze SAP na hardwaru úložiště, které jsou umístěné ve společně umístěných datových centrech třetích stran sousedících s datovými centry Azure, se nepodporuje. Úložiště, které poskytuje softwarová zařízení hostovaná na virtuálních počítačích Azure, nejsou také podporována pro tento případ použití. Pro úlohy SAP DBMS se podporuje jenom úložiště, které je reprezentované jako nativní služba Azure pro data a soubory protokolů transakcí databáze SAP obecně. Různé systémy DBMS můžou podporovat různé typy úložišť Azure. Další podrobnosti najdete v článku [Azure Storage typech pro úlohu SAP](./planning-guide-storage.md) .

Umístění databázových souborů a souborů protokolů a opakování a používaného typu Azure Storage jsou definovány podle požadavků IOPS, latence a propustnosti. Konkrétně pro zajištění dostatečného počtu IOPS pro Azure Premium Storage je možné, že budete muset použít více disků nebo použít větší disk služby Premium Storage. Pokud používáte více disků, sestavte softwarový proužek na discích, které obsahují datové soubory nebo soubory protokolů a opakování. V takových případech jsou pro výslednou sadu accumulative pro výslednou sadu SLA vstupně-výstupních operací a propustnosti disku pro základní disky úložiště úrovně Premium nebo maximální dosažitelný počet IOPS u standardních disků úložiště.

Pokud požadavek na IOPS překročí, co může jeden virtuální pevný disk poskytnout, vyvážit počet IOPS potřebných pro soubory databáze v rámci řady virtuálních pevných disků. Nejjednodušší způsob, jak distribuovat zatížení IOPS mezi disky, je vytvořit softwarový proužek na různých discích. Pak na logické jednotky (LUN) Carved z softwarového proužku umístěte několik datových souborů systému SAP DBMS. Počet disků v pruzích se řídí požadavky IOPS, požadavky na propustnost disku a požadavky na svazky.


---
> ![Úložiště pro Windows][Logo_Windows] Windows
>
> Pomocí prostorů úložiště Windows doporučujeme vytvářet sady prokládaných svazků napříč několika virtuálními pevnými disky Azure. Používejte aspoň Windows Server 2012 R2 nebo Windows Server 2016.
>
> ![Inproužekování úložiště pro Linux][Logo_Linux] Linux
>
> Pro vytvoření softwarového pole RAID v systému Linux jsou podporovány pouze MDADM a Správce logických svazků (LVM). Další informace naleznete v tématech:
>
> - [Konfigurace softwarového pole RAID v systému Linux](../../linux/configure-raid.md) pomocí MDADM
> - [Konfigurace LVM na virtuálním počítači se systémem Linux v Azure](../../linux/configure-lvm.md) pomocí LVM
>
>

---

Pro Azure Ultra disk není nutné obložení, protože je možné definovat vstupně-výstupní operace za sekundu a propustnosti disku nezávisle na velikosti disku.


> [!NOTE]
> Vzhledem k tomu, že Azure Storage udržuje tři image VHD, neznamená to, že při proužkování nebudete moct konfigurovat redundanci. Je třeba nakonfigurovat prokládání, aby byly vstupně-výstupy na různých virtuálních pevných discích distribuovány.
>

### <a name="managed-or-nonmanaged-disks"></a>Spravované nebo nespravované disky
Účet služby Azure Storage je administrativní konstrukcí a je také předmětem omezení. Mezi standardními účty úložiště a účty úložiště úrovně Premium se liší omezení. Informace o možnostech a omezeních najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](../../../storage/common/scalability-targets-standard-account.md).

U úložiště úrovně Standard si pamatujte, že pro každý účet úložiště je limit IOPS. Podívejte se na řádek, který obsahuje **celkovou míru požadavků** v článku [Azure Storage škálovatelnost a výkonnostní cíle](../../../storage/common/scalability-targets-standard-account.md). U každého předplatného Azure je také počáteční limit počtu účtů úložiště. Vyvážit virtuální pevné disky pro větší kapacitu SAP napříč různými účty úložiště, abyste se vyhnuli překročení limitů těchto účtů úložiště. Jedná se o zdlouhavou práci, když mluvíte o několika stovkách virtuálních počítačů s více než tisíci VHD.

Použití standardního úložiště pro nasazení DBMS ve spojení s úlohou SAP se nedoporučuje. Odkazy a doporučení na standardní úložiště jsou proto omezené na tento krátký [článek](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance) .

Abychom se vyhnuli administrativní práci při plánování a nasazování virtuálních pevných disků napříč různými účty úložiště Azure, společnost Microsoft představila [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) v 2017. Služba Managed disks je dostupná pro úložiště úrovně Standard a Premium. Hlavní výhody spravovaných disků v porovnání s nespravovanými disky jsou:

- U spravovaných disků Azure distribuuje různé virtuální pevné disky v různých účtech úložiště automaticky v době nasazení. Tímto způsobem se omezí omezení účtu úložiště pro datový svazek, propustnost vstupně-výstupních operací a IOPS.
- Pomocí spravovaných disků Azure Storage respektují koncepty skupin dostupnosti Azure. Pokud je virtuální počítač součástí skupiny dostupnosti Azure, základní virtuální pevný disk a připojený disk virtuálního počítače se nasazují do různých domén selhání a aktualizačních domén.


> [!IMPORTANT]
> Vzhledem k výhodám Azure Managed Disks důrazně doporučujeme, abyste používali Azure Managed Disks pro nasazení DBMS a obecné nasazení SAP.
>

Pokud chcete převádět z nespravovaných na Managed disks, přečtěte si:

- [Převeďte virtuální počítač s Windows z nespravovaných disků na Managed disks](../../windows/convert-unmanaged-to-managed-disks.md).
- [Převod virtuálního počítače se systémem Linux z nespravovaných disků na Managed disks](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Když připojíte disky k virtuálním počítačům, můžete zvolit, jestli se vstupně-výstupní přenos mezi virtuálním počítačem a disky umístěnými ve službě Azure Storage ukládá do mezipaměti.

Následující doporučení předpokládají tyto vstupně-výstupní charakteristiky pro standardní systémy DBMS:

- Většinou se jedná o úlohy čtení z datových souborů databáze. Tyto čtení jsou v systému DBMS kritické pro výkon.
- Zápis proti datovým souborům probíhá v shlukech na základě kontrolních bodů nebo konstantního datového proudu. Průměrně za den je méně zápisů než čtení. Proti čtení z datových souborů jsou tyto zápisy asynchronní a neobsahují žádné uživatelské transakce.
- Z protokolu transakcí nebo souborů pro opětovné provedení se špatně žádné čtení. Výjimky jsou velkými vstupně-výstupy při provádění záloh protokolu transakcí.
- Hlavní zatížení proti transakci nebo soubory protokolu opakovaného zápisu jsou zápisy. V závislosti na povaze úlohy můžete mít vstupně-výstupní operace až na 4 KB nebo v jiných případech velikost I/O velikosti 1 MB nebo více.
- Všechny zápisy musí být trvale uchovány na disku spolehlivě.

Pro úložiště úrovně Standard jsou možné typy mezipaměti:

* Žádné
* Číst
* Čtení/zápis

Pro zajištění konzistentního a deterministického výkonu nastavte ukládání do mezipaměti na úrovni Standard pro všechny disky, které obsahují datové soubory související se systémem DBMS, soubory protokolů a opakování, a prostor tabulky do **žádného**. Ukládání základního virtuálního pevného disku do mezipaměti může zůstat ve výchozím nastavení.

V případě služby Azure Premium Storage existují následující možnosti ukládání do mezipaměti:

* Žádné
* Číst
* Čtení/zápis
* None + Akcelerátor zápisu, což je jenom pro virtuální počítače Azure M-Series
* Čtení a Akcelerátor zápisu, který je určený jenom pro virtuální počítače Azure M-Series

Pro prémiové úložiště doporučujeme, abyste pro **datové soubory** databáze SAP používali ukládání pro čtení a nezvolili jste **žádné ukládání do mezipaměti pro disky souborů protokolu**.

Pro nasazení řady M-Series doporučujeme, abyste pro nasazení DBMS používali Azure Akcelerátor zápisu. Podrobnosti, omezení a nasazení služby Azure Akcelerátor zápisu najdete v tématu [povolení akcelerátor zápisu](../../how-to-enable-write-accelerator.md).

Pro ultra disk a Azure NetApp Files nejsou nabízeny žádné možnosti ukládání do mezipaměti.


### <a name="azure-nonpersistent-disks"></a>Netrvalé disky Azure
Virtuální počítače Azure nabízejí po nasazení virtuálního počítače netrvalé disky. V případě restartování virtuálního počítače se vymaže veškerý obsah na těchto jednotkách. Jedná se o to, aby datové soubory a soubory protokolů a opětovného provedení databáze měly být v žádném případě umístěny na těchto netrvalých jednotkách. Pro některé databáze můžou existovat výjimky, které by mohly být vhodné pro tyto netrvalé jednotky pro databáze tempdb a dočasné prostory. Nepoužívejte tyto jednotky pro virtuální počítače řady A-Series, protože tyto netrvalé jednotky jsou v rámci této řady virtuálních počítačů omezené v propustnosti. 

Další informace najdete v tématu [vysvětlení dočasné jednotky na virtuálních počítačích s Windows v Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines).

---
> ![Netrvalý disk se systémem Windows][Logo_Windows] Windows
>
> Jednotka D ve virtuálním počítači Azure je netrvalá jednotka, na kterou se zálohují některé místní disky na výpočetním uzlu Azure. Protože není trvale zachované, všechny změny obsahu na jednotce D se po restartování virtuálního počítače ztratí. Změny zahrnují soubory, které byly uloženy, adresáře, které byly vytvořeny, a aplikace, které byly nainstalovány.
>
> ![Linuxnonpersisted disk][Logo_Linux] Linux
>
> Virtuální počítače se systémem Linux automaticky připojí jednotku na/mnt/Resource, která je netrvalou jednotkou zajištěnou místními disky na výpočetním uzlu Azure. Protože není trvale zachované, všechny změny v obsahu v/mnt/Resource se při restartu virtuálního počítače ztratí. Změny zahrnují soubory, které byly uloženy, adresáře, které byly vytvořeny, a aplikace, které byly nainstalovány.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odolnost Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální pevný disk s operačním systémem a připojenými disky nebo objekty blob na nejméně třech samostatných uzlech úložiště. Tento typ úložiště se nazývá místně redundantní úložiště (LRS). LRS je výchozí hodnota pro všechny typy úložiště v Azure.

Existují další metody redundance. Další informace najdete v tématu [replikace Azure Storage](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
> Azure Premium Storage, Ultra disk a Azure NetApp Files (výhradně pro SAP HANA), jsou doporučeným typem úložiště pro virtuální počítače a disky s protokolem DBMS, na kterých se ukládají databáze a soubory pro opakování. Jediná dostupná metoda redundance pro tyto typy úložišť je LRS. V důsledku toho je potřeba nakonfigurovat metody databáze a povolit tak replikaci dat databáze do jiné oblasti Azure nebo zóny dostupnosti. Mezi metody databáze patří SQL Server vždycky zapnuto, Oracle data Guard a systém HANA pro replikaci.


> [!NOTE]
> Pro nasazení DBMS se použití geograficky redundantního úložiště (GRS) nedoporučuje pro úložiště úrovně Standard. GRS má vážně vliv na výkon a nerespektuje pořadí zápisu napříč různými virtuálními pevnými disky připojenými k virtuálnímu počítači. Nerespektování pořadí zápisu napříč různými virtuálními pevnými disky potenciálně vede k nekonzistentním databázím na straně cíle replikace. K této situaci dochází, pokud jsou databáze a soubory protokolu a opakování rozloženy mezi více virtuálních pevných disků, což je v případě, že je to obvykle případ, na straně zdrojového virtuálního počítače.



## <a name="vm-node-resiliency"></a>Odolnost uzlu virtuálního počítače
Azure nabízí několik různých SLA pro virtuální počítače. Další informace najdete v nejnovější verzi [smlouvy SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Vzhledem k tomu, že je vrstva DBMS důležitá k dostupnosti v systému SAP, potřebujete pochopit skupiny dostupnosti, Zóny dostupnosti a události údržby. Další informace o těchto konceptech najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure](../../windows/manage-availability.md) a [Správa dostupnosti virtuálních počítačů se systémem Linux v Azure](../../linux/manage-availability.md).

Minimální doporučení pro provoz DBMS scénářů s úlohou SAP:

- Nasaďte dva virtuální počítače v samostatné skupině dostupnosti ve stejné oblasti Azure.
- Spusťte tyto dva virtuální počítače ve stejné virtuální síti Azure a použijte síťové karty připojené ze stejných podsítí.
- Použijte metody databáze a udržujte aktivní pohotovostní režim s druhým virtuálním počítačem. Metody mohou být SQL Server Always On, Oracle data Guard nebo HANA pro replikaci systému HANA.

Můžete také nasadit třetí virtuální počítač v jiné oblasti Azure a použít stejné metody databáze k poskytnutí asynchronní repliky v jiné oblasti Azure.

Informace o tom, jak nastavit skupiny dostupnosti Azure, najdete v [tomto kurzu](../../windows/tutorial-availability-sets.md).



## <a name="azure-network-considerations"></a>Požadavky na síť Azure
V rozsáhlých nasazeních SAP použijte podrobný plán [Azure Virtual datacentra](/azure/architecture/vdc/networking-virtual-datacenter). Použijte ji pro konfiguraci virtuální sítě a oprávnění a přiřazení rolí pro různé části vaší organizace.

Tyto osvědčené postupy jsou výsledkem stovek nasazení zákazníků:

- Virtuální sítě, ke kterým je aplikace SAP nasazená, nemají přístup k Internetu.
- Virtuální počítače databáze běží ve stejné virtuální síti jako aplikační vrstva, oddělené v jiné podsíti z aplikační vrstvy SAP.
- Virtuální počítače v rámci virtuální sítě mají statické přidělení privátní IP adresy. Další informace najdete v tématu [typy IP adres a metody přidělování v Azure](../../../virtual-network/public-ip-addresses.md).
- Omezení směrování do a z virtuálních počítačů s DBMS *nejsou nastavená* pomocí bran firewall nainstalovaných na místních virtuálních počítačích DBMS. Místo toho je směrování provozu definované se [skupinami zabezpečení sítě (skupin zabezpečení sítě)](../../../virtual-network/security-overview.md).
- Pokud chcete provoz oddělit a izolovat k virtuálnímu počítači DBMS, přiřaďte k virtuálnímu počítači různé síťové adaptéry. Každé síťové rozhraní získá jinou IP adresu a každá síťová karta je přiřazena k jiné podsíti virtuální sítě. Každá podsíť má odlišná pravidla NSG. Izolaci nebo oddělení síťových přenosů je míra pro směrování. Nepoužívá se k nastavení kvót pro propustnost sítě.

> [!NOTE]
> Přiřazení statických IP adres prostřednictvím Azure znamená, že je přiřadíte jednotlivým virtuálním síťovým kartám. Nepřiřazujte statické IP adresy v rámci hostovaného operačního systému k virtuální síťové kartě. Některé služby Azure, například Azure Backup, spoléhají na skutečnost, že aspoň primární virtuální síťová karta je nastavená na DHCP, a ne na statické IP adresy. Další informace najdete v tématu [řešení potíží se zálohováním virtuálních počítačů Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking). Pokud chcete přiřadit více statických IP adres k virtuálnímu počítači, přiřaďte virtuálnímu počítači několik virtuálních síťových karet.
>


> [!WARNING]
> Konfigurace [síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační cestě mezi aplikací SAP a vrstvou DBMS systému SAP NetWeaver-, Hybris-nebo S/4HANA se nepodporuje. Toto omezení je z hlediska funkčnosti a výkonu. Komunikační cesta mezi aplikační vrstvou SAP a vrstvou DBMS musí být přímá. Omezení nezahrnuje [skupinu zabezpečení aplikací (ASG) a pravidla NSG](../../../virtual-network/security-overview.md) , pokud pravidla ASG a NSG povolují cestu přímé komunikace. 
>
> Další scénáře, ve kterých nejsou síťová virtuální zařízení podporovaná, jsou v:
>
> * Cesty komunikace mezi virtuálními počítači Azure, které reprezentují uzly clusteru Linux Pacemaker a SBD zařízení, jak je popsáno v tématu [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md).
> * Cesty komunikace mezi virtuálními počítači Azure a souborovým serverem Windows Server Scale-Out (SOFS), jak je popsáno v tématu vytvoření [instance SAP ASCS/SCS v clusteru Windows s podporou převzetí služeb při selhání pomocí sdílené složky v Azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Síťová virtuální zařízení v cestách komunikace můžou snadno zdvojnásobit latenci sítě mezi dvěma komunikačními partnery. Můžou taky omezit propustnost v kritických cestách mezi aplikační vrstvou SAP a vrstvou DBMS. V některých scénářích zákazníků může síťová virtuální zařízení způsobit selhání clusterů Pacemaker Linux. Jedná se o případy, kdy komunikace mezi uzly clusteru Pacemaker Linux komunikuje se svým zařízením SBD prostřednictvím síťového virtuálního zařízení.
>

> [!IMPORTANT]
> Jiný návrh, který *není* podporován, je oddělení aplikační vrstvy SAP a vrstvy DBMS do různých virtuálních sítí Azure, které nejsou vzájemně [partnerské](../../../virtual-network/virtual-network-peering-overview.md) . Doporučujeme, abyste vrstvu aplikace SAP a systém DBMS odlišili pomocí podsítí v rámci služby Azure Virtual Network místo pomocí různých virtuálních sítí Azure. 
>
> Pokud se rozhodnete nepostupovat podle doporučení a místo toho rozdělit dvě vrstvy do různých virtuálních sítí, musí být tyto dvě virtuální sítě [partnerské](../../../virtual-network/virtual-network-peering-overview.md). 
>
> Uvědomte si, že síťový provoz mezi dvěma [partnerskými](../../../virtual-network/virtual-network-peering-overview.md) virtuálními sítěmi Azure podléhá přenosům. Velký objem dat, který se skládá z mnoha terabajtů, se vymění mezi aplikační vrstvou SAP a vrstvou DBMS. Pokud je vrstva aplikace SAP a vrstva DBMS oddělená od dvou partnerských virtuálních sítí Azure, můžete nashromáždit značné náklady.

Použijte dva virtuální počítače pro nasazení v produkčním systému DBMS v rámci skupiny dostupnosti Azure nebo mezi dvěma Zóny dostupnosti Azure. Použijte také samostatné směrování pro aplikační vrstvu SAP a provoz správy a provozu na tyto dva virtuální počítače se systémem DBMS. Viz následující obrázek:

![Diagram dvou virtuálních počítačů ve dvou podsítích](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Přesměrování provozu pomocí Azure Load Balancer
Použití privátních virtuálních IP adres, které se používají ve funkcích, jako je SQL Server Always On nebo HANA, vyžaduje konfiguraci nástroje pro vyrovnávání zatížení Azure. Nástroj pro vyrovnávání zatížení využívá porty sondy k určení aktivního uzlu DBMS a směrování provozu výhradně do tohoto uzlu aktivní databáze. 

Pokud dojde k převzetí služeb při selhání uzlu databáze, není nutné znovu konfigurovat aplikaci SAP. Místo toho se nejběžnější architektury aplikací SAP znovu připojovat k privátní virtuální IP adrese. Mezitím Nástroj pro vyrovnávání zatížení po přesměrování uzlů do převzetí služeb při selhání provede přesměrováním provozu proti privátní virtuální IP adrese do druhého uzlu.

Azure nabízí dvě různé [SKU nástroje pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md): základní SKU a standardní SKU. Na základě výhod v nastavení a funkčnosti byste měli použít standardní SKU nástroje pro vyrovnávání zatížení Azure. Jednou z velkých výhod standardní verze nástroje pro vyrovnávání zatížení je, že přenos dat není směrován prostřednictvím samotného nástroje pro vyrovnávání zatížení.

Příklad, jak můžete nakonfigurovat interní nástroj pro vyrovnávání zatížení, najdete v článku [kurz: Konfigurace skupiny dostupnosti SQL Server v Azure Virtual Machines ručně.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial#create-an-azure-load-balancer)

> [!NOTE]
> Existují rozdíly v chování základní a standardní SKU, které souvisí s přístupem k veřejným IP adresám. Způsob, jak obejít omezení standardní SKU pro přístup k veřejným IP adresám, je popsán v dokumentu [připojení veřejných koncových bodů pro Virtual Machines s využitím Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) .


### <a name="azure-accelerated-networking"></a>Urychlené síťové služby Azure
K dalšímu snížení latence sítě mezi virtuálními počítači Azure doporučujeme, abyste vybrali [Azure akcelerované síťové služby](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Použijte ho při nasazení virtuálních počítačů Azure pro úlohu SAP, zvláště pro vrstvu aplikace SAP a vrstvu DBMS systému SAP.

> [!NOTE]
> Ne všechny typy virtuálních počítačů podporují urychlené síťové služby. V předchozím článku jsou uvedené typy virtuálních počítačů, které podporují urychlené síťové služby.
>

---
> ![Akcelerované síťové služby Windows][Logo_Windows] Windows
>
> Informace o tom, jak nasadit virtuální počítače s akcelerovanou sítí pro Windows, najdete v tématu [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](../../../virtual-network/create-vm-accelerated-networking-powershell.md)službami.
>
> ![Linux – akcelerované síťové služby][Logo_Linux] Linux
>
> Další informace o distribuci systému Linux najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](../../../virtual-network/create-vm-accelerated-networking-cli.md)službami.
>
>

---

> [!NOTE]
> V případě SUSE, Red Hat a Oracle Linux se v nejnovějších verzích podporují urychlené síťové služby. Starší verze, jako je SLES 12 SP2 nebo RHEL 7,2, nepodporují urychlené síťové služby Azure.
>


## <a name="deployment-of-host-monitoring"></a>Nasazení monitorování hostitele
Pro produkční použití aplikací SAP na virtuálních počítačích Azure vyžaduje SAP možnost získat data monitorování hostitele z fyzických hostitelů, na kterých běží virtuální počítače Azure. Vyžaduje se konkrétní úroveň opravy agenta hostitele SAP, která umožňuje tuto schopnost v agentovi hostitele SAPOSCOL a SAP. Přesná úroveň opravy je popsána v části SAP Note [1409604].

Další informace o nasazení komponent, které doručují data hostitele do SAPOSCOL a agenta hostitele SAP a správu životního cyklu těchto komponent, najdete v [Průvodci nasazením][deployment-guide].


## <a name="next-steps"></a>Další kroky
Další informace o konkrétním systému DBMS najdete v těchto tématech:

- [Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP](dbms_guide_sqlserver.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](dbms_guide_oracle.md)
- [Nasazení IBM DB2 Azure Virtual Machines DBMS pro úlohy SAP](dbms_guide_ibm.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](dbms_guide_sapase.md)
- [MaxDB SAP, živá mezipaměť a nasazení obsahu serveru v Azure](dbms_guide_maxdb.md)
- [Průvodce provozem SAP HANA v Azure](hana-vm-operations.md)
- [SAP HANA vysoké dostupnosti pro virtuální počítače Azure](sap-hana-availability-overview.md)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](sap-hana-backup-guide.md)