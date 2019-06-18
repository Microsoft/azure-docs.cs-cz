---
title: Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP | Dokumentace Microsoftu
description: Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836110"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Tato příručka je součástí sady dokumentace o tom, jak implementovat a nasazení softwaru SAP v Microsoft Azure. Předtím, než se pustíte do čtení této příručky, přečtěte si [Příručka pro plánování a implementaci][planning-guide]. Tento dokument popisuje aspekty obecný nasazení systémů souvisejících s SAP DBMS na virtuálních počítačích Microsoft Azure (VM) s využitím Azure infrastruktury jako služby (IaaS) funkce.

Dokument doplňuje dokumentaci k instalaci SAP a SAP poznámky, které představují primární zdroje pro instalaci a nasazení softwaru SAP v dané platformy.

V tomto dokumentu jsou zavedeny aspekty provozování systémů souvisejících s SAP DBMS na virtuálních počítačích Azure. Existuje několik odkazů na konkrétní systémy DBMS v této kapitole. Místo toho konkrétní systémy DBMS jsou zpracovány v tomto dokumentu po tomto dokumentu.

## <a name="definitions"></a>Definice
V celém dokumentu se používají tyto podmínky:

* **IaaS**: Infrastruktura jako služba.
* **PaaS**: Platforma jako služba.
* **SaaS**: Software jako služba.
* **Komponenta SAP**: Jednotlivé aplikace SAP ERP ústřední součást (ECC), Business Warehouse (BW), správci řešení nebo Enterprise Portal (RP). Komponenty SAP může být založeno na tradičních technologií ABAP a Java nebo aplikace nezaložené NetWeaver například pro obchodní objekty.
* **Prostředí SAP**: Jeden nebo více komponent SAP logicky seskupeny k provedení obchodní funkce, jako je vývoj, kontrola kvality, školení, zotavení po havárii nebo produkčního prostředí.
* **Prostředí SAP**: Tento termín se vztahuje na celý majetek SAP v zákazníka IT na šířku. Prostředí SAP zahrnuje všechna produkční a nevýrobní prostředí.
* **Systém SAP**: Kombinace DBMS vrstvy a aplikační vrstva systému, například SAP ERP vývoj, testování SAP Business Warehouse systému, nebo produkční systém SAP CRM. Dělení tyto dvě vrstvy mezi místními a Azure se nepodporuje v nasazení Azure. V důsledku toho systém SAP je nasazená místně nebo je nasazené v Azure. Můžete nasadit různé systémy prostředí SAP v Azure nebo místně. Například může nasazení SAP CRM. vývojové a testovací systémy v Azure ale nasazení SAP CRM produkční systém místní.
* **Mezi různými místy**: Popisuje scénář, kde jsou nasazené virtuální počítače s předplatným Azure, který má site-to-site, nasazení ve více lokalitách nebo připojení Azure ExpressRoute mezi místní data Center a Azure. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. 

    Důvod pro připojení je rozšířit místní domény, v místní službě Active Directory a DNS v místním prostředí do Azure. V místním prostředí je rozšířit na prostředky předplatného Azure. U tohoto rozšíření virtuální počítač může být součástí místní domény. Uživatelé domény z místní domény můžete přístup k serverům a spustit služby na těchto virtuálních počítačů, jako jsou služby DBMS. Místně nasadili komunikace a název řešení mezi virtuálními počítači a je možné virtuální počítače nasazené v Azure. Tento scénář je nejběžnější scénář používá k nasazení prostředků SAP v Azure. Další informace najdete v tématu [plánování a návrh pro VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Nasazení mezi místními systémy SAP jsou, kde Azure virtuální počítače, na kterých běží systémy SAP jsou členy domény služby v místním a jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení prostředí SAP do Azure. Dokonce i kompletní prostředí SAP běžící v Azure vyžaduje tyto virtuální počítače jako součást místní domény a Active Directory nebo LDAP. 
>
> V předchozích verzích nástroje v dokumentaci byly zmíněny IT hybridní scénáře. Termín *hybridní* spočívají ve skutečnosti, že existuje připojení mezi místními sítěmi mezi místními a Azure. V takovém případě hybridní také znamená, že virtuální počítače v Azure jsou součástí služby v místním Active Directory.
>
>

Některé dokumentaci Microsoftu popisuje scénáře, mezi různými místy trochu jinak, především pro DBMS Konfigurace vysoké dostupnosti. V případě SAP související dokumenty, scénáře mezi různými místy boils na site-to-site nebo privátní [ExpressRoute](https://azure.microsoft.com/services/expressroute/) připojení a SAP na šířku, která se distribuuje mezi místními a Azure.

## <a name="resources"></a>Zdroje a prostředky
Existují další články k dispozici na úloh SAP v Azure. Začněte s [úloh SAP v Azure: Začínáme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) a pak zvolte oblast zájmu.

Následující poznámky SAP jsou související s řešením SAP v Azure ve vztahu oblasti zahrnuté v tomto dokumentu.

| Poznámka: číslo | Titul |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure |
| [2015553] |SAP v Microsoft Azure: Požadavky pro podporu |
| [1999351] |Řešení potíží s rozšířené monitorování Azure, které pro SAP |
| [2178632] |Klíč monitorování metrik pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: Rozšířené monitorování |
| [2191498] |SAP v Linuxu se službou Azure: Rozšířené monitorování |
| [2039619] |Aplikace SAP v Microsoft Azure s využitím databáze Oracle: Podporované produkty a verze |
| [2233094] |DB6: Aplikace SAP v Azure s využitím IBM DB2 pro Windows, Linux a UNIX: Další informace |
| [2243692] |Linux v Microsoft Azure (IaaS) virtuálního počítače: Problémy licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalace a upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |
| [2171857] |Oracle Database 12c: Podpora systému souborů v Linuxu |
| [1114181] |Databáze Oracle 11g: Podpora systému souborů v Linuxu |


Informace o všech poznámky SAP pro Linux, najdete v článku [SAP komunity wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Potřebujete praktické znalosti architektury Microsoft Azure a jak se virtuální počítače Microsoft Azure nasazují a provozují. Další informace najdete v tématu [dokumentace ke službě Azure](https://docs.microsoft.com/azure/).

Obecně platí Windows, Linux a DBMS instalace a konfigurace jsou v podstatě stejné jako libovolný virtuální počítač nebo úplné systému počítač nainstalujete místně. Existují některé architektury a systém implementace rozhodnutích týkajících se správy, které se liší, když použijete Azure IaaS. Tento dokument popisuje specifické pro architekturu a systém správy rozdíly, se připravit při použití Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktury úložiště virtuálního počítače pro nasazení relační databázový systém
Pokud chcete postupovat podle této kapitole, čtení a seznamte se s informacemi v [této kapitole] [ deployment-guide-3] z [Průvodce nasazením][deployment-guide]. Musíte pochopit a o různých řadu virtuálních počítačů a jaký je rozdíl mezi storage úrovně standard a premium před čtením této kapitole. 

Další informace o službě Azure Storage pro virtuální počítače Azure, najdete v tématech:

- [Úvod do spravované disky pro virtuální počítače Windows Azure](../../windows/managed-disks-overview.md).
- [Úvod do spravované disky pro virtuální počítače Azure s Linuxem](../../linux/managed-disks-overview.md).

V základní konfiguraci doporučujeme obvykle strukturu nasazení, kde jsou oddělené od databázové soubory operačního systému, DBMS a konečný výsledek SAP binární soubory. Doporučujeme, aby systémy SAP v Azure virtual machines základního virtuálního pevného disku nebo disku, nainstalovány s operačním systémem, spustitelné soubory systému správy databáze a SAP spustitelné soubory. 

Soubory protokolu a data DBMS jsou uloženy v storage úrovně standard nebo premium storage. Jsou uložené v samostatných disků a připojit k původní bitové kopie operačního systému Azure virtuální počítač jako logické disky. Pro nasazení systému Linux jsou popsané různé doporučení speciálně pro SAP HANA.

Při plánování rozložení disku najdete nejlepší poměr mezi těmito položkami:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty vstupně-výstupních operací z jednoho disku.
* Propustnost dat na disk.
* Počet dalších datových disků je to možné, velikosti virtuálních počítačů.
* Celkovou propustnost úložiště může poskytnout virtuálního počítače.
* Můžete zadat různé typy latence v Azure Storage.
* VM SLAs.

Azure vynucuje kvóty vstupně-výstupních operací na datový disk. Tyto kvóty se liší pro disky hostované na úložiště úrovně standard a premium storage. Latenci vstupu/výstupu se také liší mezi dvěma typy úložišť. Premium storage poskytuje lepší latenci vstupu/výstupu. 

Každý z různých typů virtuálních počítačů má omezený počet datových disků, které můžete připojit. Dalším omezením je skutečnost, že můžete použít pouze určité typy virtuálních počítačů služby premium storage. Obvykle se rozhodnete použít určitého typu virtuálních počítačů podle požadavků na CPU a paměti. Můžete také zvážit vstupně-výstupních operací, latence a propustnosti disku, které obvykle se upraví počet disků nebo typ disky storage úrovně premium. Počet IOPS a propustnost se dá dosáhnout každého disku může určovat velikost disku, zejména s premium storage.

> [!NOTE]
> Pro nasazení DBMS doporučujeme použít storage úrovně premium pro jakákoli data, protokol transakcí, nebo znovu soubory. Nezáleží, jestli chcete nasazovat produkční nebo neprovozním systémy.

> [!NOTE]
> Abyste využili výhod Azure je jedinečné [jednotné SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), všechny disky, které jsou připojeny musí být typu úložiště úrovně premium, která obsahuje základní virtuální pevný disk.

> [!NOTE]
> Hostování souborů hlavní databáze, jako jsou například soubory protokolu a data databází SAP na hardwaru úložiště, který se nachází v centrech společně umístěných datových třetích stran vedle datových center Azure se nepodporuje. Pro úlohy SAP se podporuje jenom úložiště, který je reprezentován jako nativní služby Azure pro data a transakce soubory protokolu databází SAP.

Umístění souborů databáze a soubory protokolů a znovu a typu pomocí služby Azure Storage je definována požadavků na vstupně-výstupních operací, latenci a propustnost. Aby bylo dost vstupně-výstupních operací, mohlo by být vynuceno na použití více disků nebo větší disk úložiště úrovně premium. Pokud používáte víc disků, vytvářejte software stripe disky, které obsahují datové soubory nebo v protokolu a znovu soubory. V takových případech, IOPS a propustnost disku smlouvy SLA pro použité úložiště úrovně premium jsou kumulativní pro výslednou sadu stripe disky nebo maximální IOPS dosažitelný disků storage úrovně standard.

Jak již bylo uvedeno Pokud požadavek na vstupně-výstupních operací překročí, co můžete zadat jeden virtuální pevný disk, vyvážení počtu vstupně-výstupních operací, které jsou potřeba pro soubory databáze a počtem virtuálních pevných disků. Nejjednodušší způsob, jak distribuovat zatížení vstupně-výstupních operací na discích je sestavení software stripe v různých discích. Pak umístíte počet datových souborů k SAP DBMS na jednotky LUN carved z platformy stripe softwaru. počet disků v zapisují prokládaně doprovází požadavky na vstupně-výstupních operací, nárokům na propustnost disku a svazku požadavky.


- - -
> ![Windows][Logo_Windows] Windows
>
> Doporučujeme použít prostory úložiště ve Windows vytvořit prokládané sady v rámci více virtuálních pevných disků Azure. Použijte aspoň Windows Server 2012 R2 nebo Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> K vytvoření softwarového pole RAID v Linuxu jsou podporovány pouze MDADM a Správce logických svazků (LVM). Další informace naleznete v tématu:
>
> - [Konfigurace softwarového pole RAID v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) pomocí MDADM
> - [Konfigurace LVM na virtuální počítač s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) pomocí LVM
>
>

- - -

> [!NOTE]
> Protože Azure Storage uchovává tři Image virtuálních pevných disků, to nemá smysl nakonfigurovat redundance, když jste prokládané. Potřebujete nakonfigurovat prokládáním tak, aby vstupně-výstupních operací se distribuují prostřednictvím různých virtuálních pevných disků.
>

### <a name="managed-or-nonmanaged-disks"></a>Spravovaná nebo nespravovaná disků
Účet úložiště Azure je administrativní konstrukce a také předmětem omezení. Omezení se liší mezi účty úložiště úrovně standard a účty služby premium storage. Informace o možnosti a omezení, najdete v části [škálovatelnost a výkonnostní cíle Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Pro úložiště úrovně standard mějte na paměti, že platí omezení na vstupně-výstupních operací na účet úložiště. Zobrazit řádek, který obsahuje **celková míra žádosti** v článku [škálovatelnost a výkonnostní cíle Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Je také počáteční limit pro počet účtů úložiště na předplatné Azure. Vyrovnávání virtuálních pevných disků pro větší prostředí SAP napříč různých účtů úložiště vyhnout se dosažení omezení těchto účtů úložiště. To je pracné, když mluvíme o několik stovek virtuálních počítačů s více než tisíce virtuálních pevných disků.

Protože není doporučeno používat úložiště úrovně standard pro nasazení DBMS ve spojení s úlohami SAP, odkazy a doporučení pro úložiště úrovně standard jsou omezené na tomto krátký [článku](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Chcete-li předejít správce pro plánování a nasazení virtuálních pevných disků mezi jiný účet úložiště Azure, společnost Microsoft zavedla [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017. Spravované disky jsou k dispozici pro úložiště úrovně standard a premium storage. Hlavní výhody spravovaných disků ve srovnání s nespravovaná disků jsou:

- Za spravované disky Azure distribuuje různé virtuální pevné disky přes jiný účet úložiště automaticky v době nasazení. Tímto způsobem limity účtu úložiště pro objem dat, propustnost vstupně-výstupních operací, a nejsou přístupů vstupně-výstupních operací.
- Použití spravovaných disků, Azure Storage respektuje koncepty skupinami dostupnosti Azure. Pokud virtuální počítač je součástí skupiny dostupnosti Azure, základní virtuální pevný disk a připojený disk virtuálního počítače se nasadí do různých doménami selhání a aktualizačními doménami.


> [!IMPORTANT]
> Zadaný výhody Azure Managed Disks, doporučujeme použít Azure Managed Disks pro nasazení DBMS a nasazení SAP obecně.
>

Převod z nespravovaných do spravovaných disků, naleznete v tématu:

- [Převod virtuálního počítače s Windows z nespravovaných disků na managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Převod virtuálního počítače s Linuxem z nespravovaných disků na managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Když připojíte disky pro virtuální počítače, můžete nastavit, zda je do mezipaměti vstupně-výstupní provoz mezi virtuálním počítači a tyto disky umístěné ve službě Azure storage. Storage úrovně Standard a premium použít dvě různé technologie pro tento typ mezipaměti.

Následující doporučení předpokládají tyto charakteristiky vstupně-výstupních operací pro standardní systém DBMS:

- Je většinou čtení úlohy proti datové soubory databáze. Tyto operace čtení jsou důležité pro systém DBMS výkonu.
- Vyvolá se v nárůsty zatížení na základě kontrolních bodů nebo nepřetržitý datový proud zápis proti datových souborů. Průměr za den, jsou menší počet zápisů než čtení. Než čtení z datových souborů tyto zápisy jsou asynchronní a není zdržet jakékoli uživatelské transakce.
- Soubory protokolu nebo znovu existují téměř všechny operace čtení z transakce. Výjimky jsou velkými vstupy a výstupy při provádění zálohy transakčního protokolu.
- Hlavní zátěž transakce nebo znovu soubory protokolu jsou zápisy. Závislé na povaze zatížení, můžete mít vstupně-výstupních operací malá jako 4 KB, nebo v jiných případech vstupně-výstupní operace velikosti nejméně 1 MB.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem.

Pro úložiště úrovně standard je to možné mezipaměti typy jsou:

* Žádný
* Čtení
* Čtení/zápis

Získejte konzistentní vzhledem k aplikacím a deterministický, nastavte ukládání do mezipaměti na úložiště úrovně standard pro všechny disky, které obsahují soubory související DBMS data protokolů a znovu soubory a tabulka prostoru pro **NONE**. Ukládání do mezipaměti základní virtuální pevný disk může zůstat výchozí.

Pro premium storage existují tyto možnosti ukládání do mezipaměti:

* Žádný
* Čtení
* Čtení/zápisu
* Žádné a akcelerátor zápisu, která je jenom pro virtuální počítače Azure řady M-Series
* Čtení a akcelerátor zápisu, která je jenom pro virtuální počítače Azure řady M-Series

Pro premium storage, doporučujeme použít **čtení ukládání do mezipaměti pro datové soubory** SAP databázi a zvolte **neexistující ukládání do mezipaměti pro disky souborů protokolu**.

Pro nasazení řady M-Series doporučujeme použít akcelerátor zápisu Azure pro nasazení DBMS. Podrobnosti, omezení a nasazení akcelerátor zápisu Azure najdete v tématu [povolení akcelerátoru zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Zajišťováno disků v Azure
Virtuální počítače Azure nabízejí nonpersistent disky po nasazení virtuálního počítače. V případě restartování virtuálního počítače veškerý obsah na těchto jednotkách dojde k vymazání. Jde vzhledem k tomu, že datové soubory a soubory protokolu a znovu databází za žádných okolností musíte umístit na těchto jednotkách netrvalý. Můžou nastat výjimky pro některé databáze, kde může být vhodný pro databázi tempdb a dočasné tabulky kvůli tyto netrvalý jednotky. Vyhněte se použití těchto jednotek pro virtuální počítače řady A-Series, protože tyto netrvalý jednotky mají omezenou propustnost s rodiny virtuálních počítačů. 

Další informace najdete v tématu [pochopení dočasné jednotky na virtuálních počítačích s Windows v Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> Jednotky D ve Virtuálním počítači Azure je netrvalý jednotku, která je založená na některé místní disky na Azure výpočetním uzlu. Protože je netrvalý, budou ztraceny všechny změny provedené obsah na jednotce D, po restartování virtuálního počítače. Změny zahrnují soubory, které byly uloženy, adresářů, které byly vytvořeny a aplikace, které byly nainstalovány.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuální počítače Azure s Linuxem automaticky připojit jednotku na /mnt/resource, který je netrvalý jednotka se opírá o místní disky na Azure výpočetním uzlu. Protože je netrvalý, po restartování virtuálního počítače budou ztraceny všechny změny obsahu v/mnt/prostředku. Změny zahrnují soubory, které byly uloženy, adresářů, které byly vytvořeny a aplikace, které byly nainstalovány.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odolnost proti chybám služby Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální pevný disk, s operačním systémem a připojených disků nebo objekty BLOB, do alespoň tři samostatné uzly úložiště. Tento typ úložiště se nazývá místně redundantní úložiště (LRS). LRS se výchozí nastavení pro všechny typy úložišť v Azure.

Existují jiné způsoby redundance. Další informace najdete v tématu [replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Premium storage je doporučený typ úložiště pro DBMS virtuální počítače a disky, které ukládají databáze a protokolu a znovu soubory. Metoda redundance k dispozici pouze pro premium storage je LRS. V důsledku toho musíte nakonfigurovat databázi metody povolující data replikace databáze do jiné Azure oblasti a dostupnost zóny. Metody databáze patří SQL serveru Always On, Oracle Data Guard a HANA System Replication.


> [!NOTE]
> Pro nasazení DBMS použijte geograficky redundantní úložiště (GRS) se nedoporučuje pro úložiště úrovně standard. GRS vážně ovlivňuje výkon a nebude respektovat pořadí zápisu v různých virtuálních pevných disků, které jsou připojeny k virtuálnímu počítači. Není dodržením pořadí zápisu v různých virtuálních pevných disků potenciálně vede k nekonzistentní databáze na straně cíle replikace. K této situaci dochází, pokud soubory databáze a protokolu a znovu jsou rozděleny mezi více virtuálních pevných disků, protože se obvykle stává, na zdrojovém virtuálním počítači na straně.



## <a name="vm-node-resiliency"></a>Uzel odolnost virtuálních počítačů
Azure nabízí několik různých smlouvy SLA pro virtuální počítače. Další informace najdete v tématu nejnovější verzi [SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Protože vrstva DBMS, je obvykle důležité pro dostupnost v systému SAP, budete muset porozumět skupiny dostupnosti, zóny dostupnosti a události údržby. Další informace o těchto konceptech najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a [Správa dostupnosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minimální doporučení pro produkční scénáře DBMS se SAP funkcí je:

- Nasaďte dva virtuální počítače v samostatné skupiny dostupnosti ve stejné oblasti Azure.
- Spusťte tyto dva virtuální počítače ve stejné virtuální síti Azure a síťovými kartami připojenými ze stejné podsítě.
- Databáze metody sloužit k udržení aktivní pohotovostní režim s druhý virtuální počítač. Metody může být SQL serveru Always On, Oracle Data Guard nebo systémové replikace HANA.

Také můžete nasadit třetí virtuální počítač v jiné oblasti Azure a postupujte stejně jako databáze slouží k poskytování asynchronní repliky v jiné oblasti Azure.

Informace o tom, jak vytvořit skupinu dostupnosti Azure najdete v tématu [v tomto kurzu](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Důležité informace o síti Azure
V rozsáhlých nasazení SAP použít podrobný plán z [virtuální datové centrum Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Použijte pro virtuální síť konfigurace a oprávnění a role přiřazení k různým částem vaší organizace.

Tyto osvědčené postupy jsou výsledkem stovky zákaznická nasazení:

- Virtuální sítě, které aplikace SAP se nasazuje do nemají přístup k Internetu.
- Databáze, virtuální počítače, spusťte ve stejné virtuální síti jako aplikační vrstvu.
- Virtuální počítače ve virtuální síti mají statického přidělování privátní IP adresy. Další informace najdete v tématu [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Směrování omezení do a z virtuálních počítačů DBMS jsou *není* nastavit s branami firewall nainstalované na místních virtuálních počítačích DBMS. Místo toho směrování provozu je definována s [skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Oddělení a izolaci provozu do virtuálních počítačů databázového systému, přiřadíte různé síťové adaptéry k virtuálnímu počítači. Každé síťové rozhraní získá jinou IP adresu a každé síťové rozhraní je přiřazen k jiné virtuální sítě podsíť. Každou podsíť, má jiná pravidla skupiny zabezpečení sítě. Izolace nebo oddělení provozu sítě je míra pro směrování. Chcete-li nastavit kvóty pro propustnost sítě není použit.

> [!NOTE]
> Přiřazení statických IP adres prostřednictvím Azure znamená, že je přiřadit k jednotlivým virtuální síťové karty. Přiřadit statické IP adresy v rámci hostovaného operačního systému pro virtuální síťový adaptér. Některé služby Azure, jako je Azure Backup závisí na skutečnost, který na nejnižší primární virtuální síťové karty nastavená DHCP, ne statické IP adresy. Další informace najdete v tématu [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). K virtuálnímu počítači přiřadit více statických IP adres, virtuálnímu počítači přiřadíte víc virtuálních síťových karet.
>


> [!IMPORTANT]
> Konfigurace [síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v cestě komunikaci mezi aplikací SAP a DBMS vrstvy aplikace SAP NetWeaver - Hybris – nebo systém SAP S/4HANA – na základě není podporováno. Toto omezení je důvodů funkčnosti a výkonu. Komunikační trasa mezi aplikační vrstvě SAP a DBMS vrstva musí být přímá. Omezení neobsahuje [skupiny zabezpečení aplikací (ASG) a pravidla NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) Pokud pravidla ASG a skupině NSG povolit cestu přímá komunikace. 
>
> Další scénáře, ve kterém nejsou podporovány síťová virtuální zařízení jsou v:
>
> * Komunikační trasy mezi virtuálními počítači Azure, které představují Linux Pacemaker clusteru uzly a SBD zařízení, jak je popsáno v [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Komunikační trasy mezi virtuálními počítači Azure a Windows Server horizontální navýšení kapacity souborový Server (SOFS) nastavit až, jak je popsáno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Síťová virtuální zařízení v komunikaci cesty můžete snadno double latence sítě mezi dvěma komunikace partnery. Také můžete omezují propustnost v kritické cesty mezi aplikační vrstvě SAP a vrstvu DBMS. V některých scénářích zákazníka může způsobit síťová virtuální zařízení Pacemaker Linuxové clustery selhání. Toto jsou případy, kdy komunikace mezi uzly clusteru Linux Pacemaker sdělit SBD zařízení přes síťové virtuální zařízení.
>

> [!IMPORTANT]
> Další návrh, který vaší *není* zodpovědnosti aplikační vrstvě SAP a vrstvu DBMS do různých virtuálních sítí Azure, které nejsou podporovány je [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi sebou. Doporučujeme oddělit SAP aplikační vrstva a vrstva DBMS pomocí podsítí v rámci virtuální sítě Azure místo použití různých virtuálních sítích Azure. 
>
> Pokud se rozhodnete postupujte podle doporučení a místo toho oddělit dvě vrstvy do různých virtuálních sítí, musí být dvě virtuální sítě [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Mějte na paměti, které síťový provoz mezi dvěma [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuálním sítím Azure je v souladu s náklady na přenos. Obrovská datový svazek, který se skládá z po mnoho terabajtů se vyměňují mezi aplikační vrstvě SAP a vrstvu DBMS. Pokud SAP aplikační vrstva a vrstva DBMS jsou odděleny mezi dvěma partnerskými virtuálními sítěmi Azure, lze nashromáždit značné náklady.

Nastavit dva virtuální počítače použít pro nasazení DBMS v Azure dostupné v produkčním prostředí. Také použijte samostatný směrování pro aplikační vrstvě SAP a operace a správy provozu na dva virtuální počítače DBMS. Viz následující obrázek:

![Diagram dva virtuální počítače ve dvou podsítí](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Použití Azure Load Balancer pro přesměrování přenosu dat
Použití privátních virtuálních IP adres používaných pro funkce, jako například SQL serveru Always On nebo systémové replikace HANA vyžaduje konfiguraci služby Azure load balancer. Nástroje pro vyrovnávání zatížení používá test porty určit aktivní uzel databázového systému a směrovat provoz výhradně k tomuto uzlu aktivní databáze. 

Pokud dojde k převzetí služeb při selhání uzlu databáze, není nutné pro aplikace SAP překonfigurovat. Místo toho se znovu připojit většiny běžných aplikačních architektur SAP proti privátní virtuální IP adresu. Mezitím nástroje pro vyrovnávání zatížení reaguje na převzetí služeb při selhání uzlu pomocí přesměrování přenosů proti privátní virtuální IP adresu na druhém uzlu.

Azure nabízí dva různé [SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): na základních a standardních SKU. Pokud chcete nasazení napříč zónami dostupnosti Azure, nástroje pro vyrovnávání zatížení základní SKU se bez problémů.

Je provoz mezi virtuálními počítači DBMS a aplikační vrstvě SAP vždy směrovat pomocí nástroje pro vyrovnávání zatížení neustále? Odpověď závisí na tom, jak nakonfigurujete nástroj pro vyrovnávání zatížení. 

V tuto chvíli je příchozí provoz na virtuální počítač DBMS vždy směrován přes nástroj pro vyrovnávání zatížení. Odchozí provoz trasy z virtuálního počítače DBMS na aplikační vrstvu virtuálního počítače, na kterém závisí konfiguraci nástroje pro vyrovnávání zatížení. 

Nástroje pro vyrovnávání zatížení nabízí možnost DirectServerReturn. Pokud je tato možnost nakonfigurovaná, je přenášená z virtuálního počítače DBMS na aplikační vrstvě SAP *není* směrován přes nástroj pro vyrovnávání zatížení. Místo toho přejde přímo na aplikační vrstvu. Pokud není nakonfigurováno DirectServerReturn, návratový provoz na aplikační vrstvě SAP je směrován přes nástroj pro vyrovnávání zatížení.

Doporučujeme nakonfigurovat DirectServerReturn v kombinaci s nástroji Vyrovnávání zatížení, které jsou umístěny mezi aplikační vrstvě SAP a vrstvu DBMS. Tato konfigurace snižuje latence sítě mezi těmito dvěma vrstvami.

Příklad toho, jak nastavit tuto konfiguraci pomocí SQL serveru Always On, naleznete v tématu [konfigurace naslouchacího procesu ILB pro skupiny dostupnosti Always On v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Pokud používáte publikované šablony JSON Githubu jako referenci pro vaše nasazení infrastruktury SAP v Azure, zkoumání to [šablony 3vrstvé systému SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). V této šabloně také uvidíte správná nastavení nástroje pro vyrovnávání zatížení.

### <a name="azure-accelerated-networking"></a>Akcelerovanými síťovými službami Azure
Pokud chcete dál snížit latenci sítě mezi virtuálními počítači Azure, doporučujeme, abyste zvolili [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Používejte při nasazování virtuálních počítačů Azure úloh SAP, zejména pro aplikační vrstvě SAP a SAP DBMS vrstvy.

> [!NOTE]
> Ne všechny typy virtuálních počítačů podporují Akcelerovanými síťovými službami. V předchozím článku jsou uvedeny typy virtuálních počítačů, které podporují Akcelerovanými síťovými službami.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Zjistěte, jak nasadit virtuální počítače s Accelerated Networking pro Windows, najdete v článku [vytvořit virtuální počítač Windows s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Další informace o distribuci Linuxu najdete v tématu [vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> V případě SUSE, Red Hat a Oracle Linux Akcelerovanými síťovými službami podporuje nejnovější verze. Starší verze jako SLES 12 SP2 nebo RHEL 7.2 nepodporují akcelerovaných síťových služeb Azure.
>


## <a name="deployment-of-host-monitoring"></a>Nasazení hostitele monitorování
SAP pro použití v produkčním prostředí aplikací SAP na virtuálních počítačích Azure vyžaduje možnost využívat hostitele data monitorování z fyzických hostitelů, na kterých běží virtuální počítače Azure. Vyžaduje se konkrétní úroveň opravy agenta hostitele SAP, která umožňuje tato funkce v SAPOSCOL a Agent hostitele SAP. Úroveň oprav přesně je popsána v Poznámka SAP [1409604].

Další informace o nasazení komponenty, které doručují data hostitele SAPOSCOL a Agent hostitele SAP a správa životního cyklu těchto komponent, najdete v článku [Průvodce nasazením][deployment-guide].


## <a name="next-steps"></a>Další postup
Další informace o konkrétní DBMS naleznete v tématu:

- [Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP](dbms_guide_sqlserver.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](dbms_guide_oracle.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](dbms_guide_ibm.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](dbms_guide_sapase.md)
- [SAP maxDB, Live mezipaměti a nasazení obsahu serveru v Azure](dbms_guide_maxdb.md)
- [Průvodce provozem SAP HANA v Azure](hana-vm-operations.md)
- [Vysoká dostupnost SAP HANA pro virtuální počítače Azure](sap-hana-availability-overview.md)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](sap-hana-backup-guide.md)

