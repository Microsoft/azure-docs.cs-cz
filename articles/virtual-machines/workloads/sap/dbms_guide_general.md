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
ms.openlocfilehash: 5e514f35567f4be0932c7bcc591cbd0f05cd9814
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606754"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP
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

Tato příručka je součástí sady dokumentace na implementaci a nasazení softwaru SAP v Microsoft Azure. Před čtením této příručky, přečtěte si [implementaci Průvodce plánováním a][planning-guide]. Tento dokument popisuje obecné nasazení aspekty související s SAP DBMS systémů na Microsoft Azure Virtual Machines (VM) pomocí Azure infrastruktury jako služby (IaaS) možnosti.

Dokument doplňuje dokumentaci k instalaci SAP a SAP poznámky, které představují primární zdroje pro instalaci a nasazení softwaru SAP v dané platformy.

V tomto dokumentu jsou zavedeny aspekty provozování systémů souvisejících s SAP DBMS na virtuálních počítačích Azure. Existuje několik odkazů na konkrétní systémy DBMS v této kapitole. Místo toho konkrétní systémy DBMS jsou zpracovány v tomto dokumentu po tomto dokumentu.

## <a name="definitions-upfront"></a>Definice předem
V celém dokumentu se používají následující termíny:

* IaaS: Infrastruktura jako služba.
* PaaS: Platforma jako služba.
* SaaS: Software jako služba.
* SAP komponenty: Jednotlivé aplikace SAP ECC, BW, správci řešení nebo podnikovém portálu. Komponenty SAP může být založen na tradičních technologií ABAP a Java nebo jiných NetWeaver na základě aplikace, například pro obchodní objekty.
* Prostředí SAP: jedna nebo více součástí SAP logicky seskupeny provádět obchodní funkce jako je vývoj, QAS, školení, zotavení po Havárii nebo produkčního prostředí.
* Prostředí SAP: Tento termín se vztahuje na celý majetek SAP v zákazníka IT na šířku. Prostředí SAP zahrnuje všechny produkčního prostředí a neprodukční prostředí.
* Systém SAP: Kombinace DBMS vrstvy a aplikační vrstvu služby, například vývojový systém SAP ERP, SAP BW testovacího systému, SAP CRM produkční systém atd. V nasazení Azure není možné rozdělit tyto dvě vrstvy mezi místními a Azure. V důsledku toho systém SAP je nasazená místně nebo je nasazené v Azure. Však můžete nasadit různé systémy prostředí SAP v Azure nebo místně. Můžete například nasazení SAP CRM vývoj a testování v Azure, ale SAP CRM produkční systém místní systémy.
* Mezi různými místy: Popisuje scénář, ve které jsou nasazené virtuální počítače s předplatným Azure, který má site-to-site a multi-Site, připojení ExpressRoute mezi místní nápomocni a Azure. Dokumentace v běžných Azure a tyto druhy nasazení jsou také popsány jako scénáře mezi různými místy. Důvod pro připojení je rozšířit místní domény, v místní službě Active Directory a DNS v místním prostředí do Azure. V místním prostředí je rozšířit na prostředky předplatného Azure. S touto příponou, virtuální počítače můžou být součástí místní domény. Domény uživatele z místní domény můžete přistupovat k serverům a spuštěním služby na těchto virtuálních počítačů (například systému DBMS služby). Místně nasadili komunikace a název řešení mezi virtuálními počítači a je možné virtuální počítače nasazené v Azure. Tento scénář je nejběžnější scénáře pro nasazení SAP prostředků v Azure. Další informace najdete v tématu [plánování a návrh pro VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Nasazení mezi místními systémy SAP, kde Azure Virtual Machines systémy SAP jsou členy domény služby v místním jsou podporovány pro produkční systémy SAP. Konfigurace mezi různými místy jsou podporovány pro nasazení částí nebo dokončení prostředí SAP do Azure. Dokonce i kompletní prostředí SAP běžící v Azure vyžaduje s tyto virtuální počítače se zapojil místní domény a AD/LDAP. V předchozí verze dokumentace IT hybridní scénáře zmínili, ve kterém termín *hybridní* spočívají ve skutečnosti, že existuje připojení mezi místními sítěmi mezi místními a Azure. V tomto případě *hybridní* také znamená, že virtuální počítače v Azure jsou součástí v místní službě Active Directory.
>
>

Některé dokumentaci Microsoftu popisuje scénáře, mezi různými místy trochu jinak, především pro DBMS HA konfigurace. V případě SAP související dokumenty, scénáře mezi různými místy boils s tím, že site-to-site nebo privátní [ExpressRoute](https://azure.microsoft.com/services/expressroute/) připojení a na skutečnost, že se prostředí SAP distribuuje mezi místními a Azure.

## <a name="resources"></a>Zdroje a prostředky
Jsou různé články týkající se úloh SAP v Azure všeobecně dostupné. Doporučuje se spuštění v [úloh SAP v Azure – Začínáme](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) a potom si vyberte oblasti zájmu

Následující poznámky SAP jsou související s řešením SAP v Azure týkající se oblasti zahrnuté v tomto dokumentu:

| Poznámka: číslo | Titul |
| --- | --- |
| [1928533] |Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure |
| [2015553] |SAP v Microsoft Azure: Požadavky pro podporu |
| [1999351] |Řešení potíží s rozšířené monitorování Azure pro SAP |
| [2178632] |Klíč monitorování metrik pro SAP v Microsoft Azure |
| [1409604] |Virtualizace ve Windows: Rozšířené monitorování |
| [2191498] |SAP v Linuxu se službou Azure: Rozšířené monitorování |
| [2039619] |Aplikace SAP v Microsoft Azure s využitím databáze Oracle: Podporované produkty a verze |
| [2233094] |DB6: Aplikace SAP v Azure s využitím IBM DB2 pro Linux, UNIX a Windows – Další informace |
| [2243692] |Linux v Microsoft Azure (IaaS) virtuálního počítače: Problémy licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Poznámky k instalaci |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalace a Upgrade |
| [2069760] |Oracle Linux 7.x SAP instalace a Upgrade |
| [1597355] |Doporučení odkládacího prostoru pro Linux |
| [2171857] |Oracle Database 12c – podpora systému souborů v Linuxu |
| [1114181] |Databáze Oracle 11g - podpora systému souborů v Linuxu |


Přečíst také [oznámení změny stavu Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , která obsahuje všechny poznámky SAP pro Linux.

Měli byste mít praktické znalosti o Architektura Microsoft Azure a jak jsou nasazené a provozované Microsoft Azure Virtual Machines. Další informace najdete [dokumentaci k Azure](https://docs.microsoft.com/azure/).

I když diskuze o IaaS, obecně Windows, Linux a DBMS instalace a konfigurace jsou v podstatě stejné jako libovolný virtuální počítač nebo úplné systému počítač, nainstalujete místní. Existují však některé architektury a systém správy implementace rozhodnutí, která se liší, při použití Azure IaaS. Účelem tohoto dokumentu je vysvětlit konkrétní architektury a rozdíly systému správy, které vám musí být připravené při používání Azure IaaS.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktury úložiště virtuálního počítače pro nasazení relační databázový systém
Pokud chcete postupovat podle této kapitole, je potřeba pochopit, co se zobrazí v [to] [ deployment-guide-3] kapitoly [Průvodce nasazením][deployment-guide]. Poznatky o různých řadu virtuálních počítačů a jejich rozdíly a rozdíly v Azure na úrovni Standard a [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) by měl porozuměl jsem jim a známé před čtením této kapitole.

Z hlediska úložiště Azure pro virtuální počítače Azure měli byste se seznámit s články:

- [O diskové úložiště pro virtuální počítače Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [O diskové úložiště pro virtuální počítače Azure s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds)

V základní konfiguraci doporučujeme obvykle strukturu nasazení, kde jsou oddělené od databázové soubory operačního systému, DBMS a konečný výsledek SAP binární soubory. Proto doporučujeme, abyste ve službě Azure Virtual Machines na základní virtuální pevný disk (nebo disk) nainstalované s operačním systémem, spustitelné soubory systému správy databáze a SAP spustitelné soubory se systémy SAP. Soubory protokolu a data DBMS jsou uložené ve službě Azure Storage (Standard nebo Premium Storage) v samostatných disků a připojit k původní bitové kopie operačního systému Azure virtuální počítač jako logické disky. Zejména v nasazení Linuxu může být jiný doporučení popsané. Zejména SAP HANA.

Při plánování rozložení disku, je potřeba najít nejlepší poměr mezi následující položky:

* Počet datových souborů.
* Počet disků, které obsahují soubory.
* Kvóty vstupně-výstupních operací z jednoho disku.
* Propustnost dat na disk.
* Počet dalších datových disků je to možné, velikosti virtuálních počítačů.
* Celkovou propustnost úložiště může poskytnout virtuálního počítače.
* Můžete zadat různé typy latence v Azure Storage.
* Smlouvy o úrovni služeb virtuálního počítače

Azure vynucuje kvóty vstupně-výstupních operací na datový disk. Tyto kvóty se liší pro disky hostované na Azure Storage úrovně Standard a Premium Storage. Latenci vstupu/výstupu se také liší mezi dvěma typy úložišť. Díky službě Premium Storage poskytuje faktory lepší latenci vstupu/výstupu. Každý z různých typů virtuálních počítačů má omezený počet datových disků, které budete moct připojit. Dalším omezením je skutečnost, že pouze určité typy virtuálních počítačů můžete využít Azure Premium Storage. V důsledku toho rozhodnutí pro určitého typu virtuálních počítačů nemusí pouze bude týkat procesoru a paměti požadavky, ale také podle IOPS, latenci a disk požadavkům na propustnost, které jsou obvykle škálovat počet disků nebo typ disků Premium Storage. Zejména s Premium Storage velikosti disku také může být určený počet IOPS a propustnost, kterou je potřeba se dá dosáhnout každého disku.

> [!NOTE]
> Pro nasazení DBMS důrazně doporučujeme použití služby Premium Storage u dat, protokol transakcí nebo soubory znovu. Tím nezáleží, jestli chcete nasazovat produkční nebo neprodukční systémy.

> [!NOTE]
> Aby bylo možné využívat Azure je jedinečné [jednotné SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) všechny disky připojené musí být typu Azure Premium Storage, včetně základní virtuální pevný disk.
>

Umístění databázových souborů a soubory protokolů a znovu a typu úložiště Azure, musí být definován podle požadavků na vstupně-výstupních operací, latenci a propustnost. Pokud chcete mít dostatek vstupně-výstupních operací, mohlo by být vynuceno využívat více disků nebo použít větší disk Storage úrovně Premium. V případě používání více disků, postavíte softwaru zapisují prokládaně na discích, které obsahují datové soubory nebo soubory protokolů a znovu. V takových případech jsou kumulativní pro výslednou sadu stripe IOPS a propustnost disku smlouvy SLA základní disky Premium Storage nebo maximální dosažitelný disků vstupně-výstupních operací z Azure Storage úrovně Standard.

Jak již bylo uvedeno Pokud požadavek na vstupně-výstupních operací překročí, co můžete zadat jeden virtuální pevný disk, musíte vyvážit počet vstupně-výstupních operací, které jsou potřeba pro soubory databáze a počtem virtuálních pevných disků. Nejjednodušší způsob, jak distribuovat zatížení vstupně-výstupních operací na discích je sestavení software stripe v různých discích. Pak umístíte počet datových souborů k SAP DBMS na jednotky LUN carved z platformy stripe softwaru. počet disků v zapisují prokládaně doprovází požadavky na vstupně-výstupních operací, nárokům na propustnost disku a svazku požadavky.


- - -
> ![Windows][Logo_Windows] Windows
>
> Doporučujeme použít prostory úložiště ve Windows vytvořit takové prokládané sady v rámci více virtuálních pevných disků Azure. Doporučuje se použít minimálně Windows Server 2012 R2 nebo Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> K vytvoření softwarového pole RAID v Linuxu jsou podporovány pouze MDADM a LVM (Správce logických svazků). Další informace najdete v následujících článcích:
>
> - [Konfigurace softwaru diskového pole RAID v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) pomocí MDADM
> - [Konfigurace LVM na virtuální počítač s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) pomocí LVM
>
>

- - -

> [!NOTE]
> Protože Azure Storage je udržovat tři Image virtuální pevné disky, nedává smysl ke konfiguraci redundance, když prokládání. Potřebujete jenom ke konfiguraci prokládání, tedy, že vstupně-výstupních operací získání rozloženy v různých virtuálních pevných disků.
>

### <a name="managed-or-non-managed-disks"></a>Spravované nebo nespravované disky
Účet úložiště Azure se pouze administrativní konstrukce, ale také předmětem omezení. Omezení se liší mezi standardní účet úložiště Azure a účtům úložiště Azure úrovně Premium. Přesné možnosti a omezení jsou uvedená v tomto článku [Azure Storage škálovatelnost a cíle výkonnosti](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Pro službu Azure Standard Storage, je důležité, abyste si možná Vzpomínáte, že platí omezení na vstupně-výstupních operací na účet úložiště (řádek, který obsahuje **celková míra žádosti** v článku [Azure Storage škálovatelnost a cíle výkonnosti](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). Kromě toho je počáteční limit počtu účtů úložiště na předplatné Azure. Proto budete muset využít při vyrovnávání virtuálních pevných disků pro větší prostředí SAP jiný účet úložiště, aby dosažení omezení těchto účtů úložiště. Tedious práce, když mluvíme o několik stovek virtuálních počítačů s více než tisíce virtuálních pevných disků.

Protože se nedoporučuje používat Azure Storage úrovně Standard pro nasazení DBMS ve spojení s úlohami SAP, odkazy a doporučení pro Azure storage úrovně Standard jsou omezené na tomto krátký [článku](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Pokud se chcete vyhnout administrativy pro plánování a nasazení virtuálních pevných disků mezi různými účty úložiště Azure, Microsoft zavedl službu co se nazývá [Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017. Spravované disky jsou dostupné pro Azure Standard Storage, stejně jako Azure Premium Storage. Hlavní výhody služby Managed Disks ve srovnání s seznamu nespravované disky jako:

- U služby Managed Disks Azure distribuuje různých virtuálních pevných disků v různých účtů úložiště automaticky v době nasazení a tím brání dosažení omezení z hlediska objem dat, vstupně-výstupní propustnost a IOPS účtu služby Azure Storage.
- Využití služby Managed Disks, Azure Storage je dodržením koncepty skupin dostupnosti Azure a s, která nasadí základní virtuální pevný disk a připojený disk virtuálního počítače do různých domén selhání a aktualizačními Pokud virtuální počítač je součástí sady dostupnosti Azure.


> [!IMPORTANT]
> Zadaný výhody Azure Managed Disks, důrazně doporučujeme použít Azure Managed Disks pro nasazení DBMS a nasazení SAP obecně.
>

Převod z nespravovaných do spravovaných disků, naleznete v článcích:

- [Převod virtuálního počítače s Windows z nespravovaných disků na spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Převod virtuálního počítače s Linuxem z nespravovaných disků na managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Ukládání do mezipaměti pro virtuální počítače a datové disky
Když připojíte disky pro virtuální počítače, můžete nastavit, zda jsou ukládány do mezipaměti vstupně-výstupní provoz mezi virtuálním počítači a tyto disky umístěné ve službě Azure storage. Azure Standard a Premium Storage pro tento typ mezipaměti použít dvě různé technologie.

Následující doporučení jsou za předpokladu, že tyto charakteristiky vstupně-výstupních operací pro standardní systém DBMS:

- Je většinou pro čtení úlohy proti datové soubory databáze. Tyto operace čtení jsou důležité pro systém DBMS výkonu
- Zápis proti datových souborů dochází v nárůsty zatížení na základě kontrolních bodů nebo nepřetržitý datový proud. Nicméně zprůměrované za den, zápisy jsou menší než čtení. V opačném čtení ze souborů dat těchto zápisy jsou asynchronní a nejsou pojme jakékoli uživatelské transakce.
- Soubory protokolu nebo znovu existují téměř všechny operace čtení z transakce. Výjimky jsou velkými vstupy a výstupy při provádění zálohy transakčního protokolu.
- Hlavní zátěž transakce nebo znovu soubory protokolu jsou zápisy. Závislé na povaze úloh, můžete mít vstupně-výstupních operací malá jako 4 KB nebo v jiných případech vstupně-výstupní operace velikosti nejméně 1 MB.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem

Pro službu Azure Standard Storage jsou tyto typy možných mezipaměti:

* Žádný
* Čtení
* Čtení/zápis

Aby bylo možné získat konzistentní vzhledem k aplikacím a deterministický výkon, byste měli nastavit ukládání do mezipaměti v Azure Storage úrovně Standard pro všechny disky obsahující **DBMS související datové soubory, soubory protokolů a znovu a tabulky místa pro 'NONE'**. Ukládání do mezipaměti základní virtuální pevný disk může zůstat výchozí.

Azure Premium Storage existují tyto možnosti ukládání do mezipaměti:

* Žádný
* Čtení
* Čtení/zápisu
* Žádné a akcelerátor zápisu (pouze pro virtuální počítače Azure řady M-Series)
* Čtení a akcelerátor zápisu (pouze pro virtuální počítače Azure řady M-Series)

Doporučení pro službu Azure Premium Storage je možné využít **čtení ukládání do mezipaměti pro datové soubory** z databáze SAP a vyberte možnost **neexistující ukládání do mezipaměti pro disky souborů protokolu**.

Pro nasazení řady M-Series důrazně doporučujeme použít akcelerátor zápisu Azure pro nasazení DBMS. Pro podrobnosti, omezení a nasazení akcelerátor zápisu Azure najdete dokumentu [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-non-persistent-disks"></a>Dočasné disky Azure
Virtuální počítače Azure nabízejí dočasné disky po nasazení virtuálního počítače. V případě restartování virtuálního počítače vymaže veškerý obsah na těchto jednotkách. Jedná se tedy vzhledem k tomu, že datové soubory a soubory protokolů a znovu databází za žádných okolností musíte umístit na těchto jednotkách netrvalé. Můžou nastat výjimky pro některé databáze, kde tyto disky netrvalé může být vhodný pro databázi tempdb a tabulkové temp prostory. Ale Vyhněte se použití těchto jednotek pro virtuální počítače řady A-Series, od těchto jednotek netrvalé mají omezenou propustnost s rodiny virtuálních počítačů. Další podrobnosti najdete v článku [pochopení dočasné jednotky na virtuálních počítačích s Windows v Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
>
> Jednotce D:\ ve Virtuálním počítači Azure je jednotka netrvalé, která je založená na některé místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že dojde ke ztrátě všechny změny provedené v obsahu na jednotku D:\, po restartování virtuálního počítače. "Všechny změny", jako jsou soubory uložené, vytvoření adresáře aplikace nainstalované, atd.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuální počítače Azure s Linuxem automaticky připojit jednotku na /mnt/resource, který je jednotka netrvalé se opírá o místní disky na Azure výpočetním uzlu. Protože je dočasný, to znamená, že všechny změny obsahu v /mnt/resource ztraceny po restartování virtuálního počítače. Všechny změny, jako jsou soubory uložené, vytvoření adresáře aplikace nainstalované, atd.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odolnost proti chybám služby Microsoft Azure Storage
Microsoft Azure Storage ukládá základní virtuální pevný disk (s operačním systémem) a připojených disků nebo objekty BLOB na alespoň tři samostatné uzly úložiště. Tato skutečnost se nazývá místní redundantní úložiště (LRS). LRS se výchozí nastavení pro všechny typy úložišť v Azure.

Několika způsoby další redundance, všechny popsané v článku [replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Od Azure Premium Storage, což je doporučený typ úložiště pro virtuální počítače DBMS a disky, které ukládají soubory databáze a protokolu/znovu, je k dispozici pouze metody LRS. V důsledku toho musíte nakonfigurovat databázi metody, jako je SQL serveru Always On, Oracle Data Guard nebo systémové replikace HANA povolíte replikaci dat databáze do jiné oblasti Azure nebo jiné zóny dostupnosti Azure.


> [!NOTE]
> Pro nasazení DBMS využití geograficky redundantní úložiště jako dostupné úložiště Azure úrovně Standard se nedoporučuje, protože to ovlivňuje výkon a nastavení nejsou respektovat pořadí zápisu v různých virtuálních pevných disků, které jsou připojeny k virtuálnímu počítači. Skutečnost, že není v různých virtuálních pevných disků příslušných pořadí zápisu nese vysoké riziko ukládaly do nekonzistentní databáze na straně cíle replikace, pokud soubory databáze a protokolu/znovu jsou rozděleny mezi více virtuálních pevných disků (jako většinou případ) na zdrojovém virtuálním počítači na straně.



## <a name="vm-node-resiliency"></a>Uzel odolnost virtuálních počítačů
Platforma Azure nabízí několik různých smlouvy o úrovni služeb pro virtuální počítače. Podrobnosti o najdete v nejnovější verzi [SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Protože DBMS vrstvy je obvykle součástí kritické dostupnost systému SAP, budete muset provést sami dobře známé koncepty skupiny dostupnosti, zóny dostupnosti a události údržby. Články, které popisují tyto koncepty je [Správa dostupnosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) a [Správa dostupnosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Minimální doporučení pro produkční scénáře DBMS s úlohami SAP je:

- Nasaďte dva virtuální počítače do samostatné sady dostupnosti ve stejné oblasti Azure.
- Tyto dva virtuální počítače běžet ve stejné virtuální síti Azure a by ze stejné podsítě připojené síťové karty.
- Databáze metody sloužit k udržení aktivní pohotovostní režim s druhý virtuální počítač. Metody může být SQL serveru Always On, Oracle Data Guard nebo systémové replikace HANA.

Kromě toho můžete nasadit třetí virtuální počítač v jiné oblasti Azure a postupujte stejně jako databáze slouží k poskytování asynchronní repliky v jiné oblasti Azure.

Způsob nastavení skupin dostupnosti Azure je ukázáno v tomto [kurzu](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Důležité informace o Azure síťové
V rozsáhlých nasazení SAP, doporučujeme, že používáte podrobného plánu z [virtuální datové centrum Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) pro své virtuální sítě konfigurace a oprávnění a role přiřazení do různých částí organizace.

Existuje několik osvědčených postupů, které je mimo stovky zákaznická nasazení:

- Aplikace SAP virtuální sítě se nasazuje do, nemá přístup k Internetu.
- Databáze, virtuální počítače jsou spuštěné ve stejné virtuální síti jako aplikační vrstvu.
- Virtuální počítače ve virtuální síti mají statického přidělování privátní IP adresy. Přečtěte si článek [typy IP adres a metody přidělování v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) jako odkaz.
- Směrování omezení do a z virtuálních počítačů DBMS jsou **není** nastavit s branami firewall nainstalované na místních virtuálních počítačích DBMS. Směrování provozu je definován s [skupiny zabezpečení sítě Azure (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Pro účely oddělení a izolace provozu do virtuálních počítačů databázového systému, přiřadit různé síťové adaptéry k virtuálnímu počítači. Kde každé síťové rozhraní má jinou IP adresu a každé síťové rozhraní je přiřazeným k jiné podsíti virtuální sítě, která znovu má jiná pravidla skupiny zabezpečení sítě. Mějte na paměti, že izolace nebo oddělení provozu v síti je právě opatření pro směrování a neumožňuje nastavení kvót pro propustnost sítě.

> [!NOTE]
> Byste měli přiřadit statické IP adresy pomocí Azure znamená, že na jednotlivé virtuální síťové adaptéry. Pro virtuální síťový adaptér by neměl přiřadit statické IP adresy v rámci hostovaného operačního systému. Některé služby Azure, jako je služba Azure Backup závisí na skutečnost, který na alespoň primárního virtuálního síťového adaptéru je nastavena, DHCP, ne statické IP adresy. Viz také dokument [Poradce při potížích s Azure se zálohováním virtuálních počítačů](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Pokud je potřeba přiřadit více statických IP adres k virtuálnímu počítači, musíte přiřadit víc karet Vnic k virtuálnímu počítači.
>


> [!IMPORTANT]
> Mimo funkce, ale další důležité z důvodů výkonu, není možné konfigurovat [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační trasa mezi aplikací SAP a úroveň databázového systému SAP NetWeaver Hybris nebo S/4HANA na systému SAP. Komunikace mezi aplikační vrstvě SAP a vrstvu DBMS musí být s přímým přístupem. Omezení nezahrnuje [Azure ASG a skupiny zabezpečení sítě pravidla](https://docs.microsoft.com/azure/virtual-network/security-overview) tak dlouho, dokud tato pravidla ASG a skupiny zabezpečení sítě umožní přímé komunikaci. Další scénáře, ve kterém nejsou podporovány síťová virtuální zařízení jsou v komunikační trasy mezi virtuálními počítači Azure, které představují uzly clusteru Linux Pacemaker SBD zařízení a jak je popsáno v [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Nebo v komunikaci se trasy mezi virtuálními počítači Azure a Windows Server SOFS nastavit až, jak je popsáno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s využitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Síťová virtuální zařízení v komunikaci cesty může snadno dvakrát latence sítě mezi dva partneři komunikaci, můžete omezit propustnost v kritické cesty mezi aplikační vrstvě SAP a vrstvu DBMS. V některých scénářích zjištěnými se zákazníky může způsobit síťová virtuální zařízení Pacemaker Linuxové clustery selhání v případech, kdy je potřeba komunikace mezi uzly clusteru Linux Pacemaker sdělit své zařízení SBD přes síťové virtuální zařízení.
>

> [!IMPORTANT]
> Další návrh, který je **není** zodpovědnosti aplikační vrstvě SAP a vrstvu DBMS do různých virtuálních sítí Azure, které nejsou podporovány je [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi sebou. Doporučujeme oddělit aplikační vrstvě SAP a DBMS vrstvy pomocí podsítí v rámci virtuální sítě Azure, namísto použití různých virtuálních sítích Azure. Pokud se rozhodnete postupujte podle doporučení a místo toho oddělit dvě vrstvy do jiné virtuální sítě, dvě virtuální sítě musí být [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Mějte na paměti, které síťový provoz mezi dvěma [v partnerském vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuálním sítím Azure jsou předmětem náklady na přenos. S velkou datový svazek v po mnoho terabajtů, které se vyměňují mezi aplikační vrstvě SAP a DBMS vrstvu můžete sbírají značné náklady, pokud aplikační vrstvě SAP a DBMS vrstvy je rozdělen mezi dvěma partnerskými virtuálními sítěmi Azure.

Pro nasazení DBMS ve skupině dostupnosti Azure a navíc samostatné směrování pro aplikační vrstvě SAP a operace a správy provozu na dva virtuální počítače DBMS v produkčním prostředí pomocí dvou virtuálních počítačů, přibližnou diagram vypadat nějak takto:

![Diagram dva virtuální počítače ve dvou podsítí](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Nástroje pro vyrovnávání zatížení Azure pro přesměrování přenosů
Použití privátních virtuálních IP adres používaných pro funkce, jako například SQL serveru Always On nebo HANA System replication vyžaduje konfiguraci služby Azure Load Balancer. Azure Load Balancer je možné prostřednictvím portů testu určit aktivní uzel databázového systému a směrovat provoz výhradně k tomuto uzlu aktivní databáze. V případě selhání uzlu databáze není nutné pro aplikace SAP překonfigurovat. Nejběžnější architektury aplikací SAP bude místo toho připojit proti privátní virtuální IP adresu. Mezitím Azure load balancer reagovalo na převzetí služeb při selhání uzlu pomocí přesměrování přenosů proti privátní virtuální IP adresu na druhém uzlu.

Azure nabízí dva různé [SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Základní SKU a standardních SKU. Pokud chcete nasazení napříč zónami dostupnosti Azure, nástroje pro vyrovnávání zatížení základní SKU se bez problémů.

Je provoz mezi virtuálními počítači DBMS a aplikační vrstvě SAP prostřednictvím služby Azure load balancer vždy směrovat neustále? Odpověď závisí na tom, jak nakonfigurujete nástroj pro vyrovnávání zatížení. Prostřednictvím služby Azure load balancer v tomto okamžiku v okamžiku, bude vždy směrovat příchozí provoz na virtuální počítač DBMS. Odchozí provoz trasy z virtuálního počítače DBMS na aplikační vrstvu virtuálního počítače, na kterém závisí konfiguraci nástroje pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení nabízí možnost DirectServerReturn. Pokud je tato možnost nakonfigurovaná, budou přenášená z virtuálního počítače DBMS na aplikační vrstvě SAP **není** ho směrovat pomocí nástroje pro vyrovnávání zatížení Azure. Místo toho se přímo přejít na aplikační vrstvu. Pokud není nakonfigurováno DirectServerReturn, zpětný provoz na aplikační vrstvě SAP je směrován přes nástroj pro vyrovnávání zatížení Azure

Doporučuje konfiguraci DirectServerReturn v kombinaci s nástroje pro vyrovnávání zatížení Azure, které jsou umístěny mezi aplikační vrstvě SAP a vrstvu DBMS tak snížit latenci sítě mezi těmito dvěma vrstvami

Příklad nastavení taková konfigurace se publikuje kolem SQL server vždy na [v tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Pokud se rozhodnete používat publikované šablony JSON Githubu jako odkaz pro vaše nasazení infrastruktury SAP v Azure, by měla tato studie [šablony 3vrstvé systému SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). V této šabloně také můžete zkoumat správná nastavení nástroje pro vyrovnávání zatížení Azure.

### <a name="azure-accelerated-networking"></a>Akcelerovanými síťovými službami Azure
Pokud chcete dál snížit latenci sítě mezi virtuálními počítači Azure, důrazně doporučujeme zvolit možnost [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) při nasazování virtuálních počítačů Azure pro úlohy SAP. Zejména pro aplikační vrstvě SAP a SAP DBMS vrstvy.

> [!NOTE]
> Ne všechny typy virtuálních počítačů podporují Akcelerovanými síťovými službami. Vybraný článek je seznam typy virtuálních počítačů, které podporují Akcelerovanými síťovými službami.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Pro Windows, projděte si článek [vytvořit virtuální počítač Windows s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) vám pomohou pochopit koncepty a postupy způsob, jak nasadit virtuální počítače s Akcelerovanými síťovými službami
>
> ![Linux][Logo_Linux] Linux
>
> Pro Linux najdete v článku [vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Pokud chcete získat podrobnosti pro Linuxovou distribuci.
>
>

- - -

> [!NOTE]
> V případě SUSE, Red Hat a Oracle Linux Akcelerovanými síťovými službami podporuje nejnovější verze. Starší verze jako SLES 12 SP2 nebo RHEL 7.2 nejsou podpůrné akcelerovaných síťových služeb Azure
>


## <a name="deployment-of-host-monitoring"></a>Nasazení hostitele monitorování
Pro produkční účely aplikací SAP v Azure Virtual Machines SAP vyžaduje možnost využívat hostitele data monitorování z fyzických hostitelů, které jsou spuštěné virtuální počítače Azure. Vyžaduje se konkrétní úroveň opravy agenta hostitele SAP, která umožňuje tato funkce v SAPOSCOL a Agent hostitele SAP. Úroveň oprav přesně je popsána v Poznámka SAP [1409604].

Podrobné informace o nasazení komponenty, které doručují data hostitele SAPOSCOL a Agent hostitele SAP a správa životního cyklu těchto komponent, najdete [Průvodce nasazením][deployment-guide]


## <a name="next-steps"></a>Další kroky
Dokumentaci o konkrétní DBMS naleznete v těchto článcích:

- [Nasazení DBMS v počítačích Azure Virtual Machines s SQL Serverem pro úlohy SAP](dbms_guide_sqlserver.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](dbms_guide_oracle.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](dbms_guide_ibm.md)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](dbms_guide_sapase.md)
- [SAP maxDB, Live mezipaměti a nasazení obsahu serveru v Azure](dbms_guide_maxdb.md)
- [Průvodce provozem SAP HANA v Azure](hana-vm-operations.md)
- [Vysoká dostupnost SAP HANA pro virtuální počítače Azure](sap-hana-availability-overview.md)
- [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md)

