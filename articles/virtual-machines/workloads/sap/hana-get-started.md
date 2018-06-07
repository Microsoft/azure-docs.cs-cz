---
title: 'Rychlý úvod: Ruční instalace jedné instance SAP HANA ve virtuálních počítačích Azure | Microsoft Docs'
description: Průvodce rychlým zahájením pro ruční instalaci jedné instance SAP HANA ve virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 14cdb2d3e433da38913ffa29b3b150bdb264278b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658702"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Rychlý úvod: Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure
## <a name="introduction"></a>Úvod
Tento průvodce vám pomůže nastavit jedné instance SAP HANA na virtuálních počítačích Azure (VM) při instalaci SAP NetWeaver 7.5 a SAP HANA 1.0 SP12 ručně. Cílem tohoto průvodce je k nasazení SAP HANA v Azure. Nenahrazuje SAP dokumentaci. 

>[!Note]
>Tato příručka popisuje nasazení SAP HANA do virtuálních počítačů Azure. Informace o nasazení SAP HANA do instance velké HANA najdete v tématu [pomocí SAP na virtuálních počítačích Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Požadavky
Tato příručka předpokládá, že jste obeznámeni s takovou infrastrukturu jako službu (IaaS) základy jako:
 * Postup nasazení virtuálního počítače nebo virtuální sítě prostřednictvím portálu Azure nebo prostředí PowerShell.
 * Azure napříč platformami rozhraní příkazového řádku (CLI), včetně možnosti použití šablon JavaScript Object Notation (JSON).

Tento průvodce také předpokládá, že jste obeznámeni s:
* SAP HANA a SAP NetWeaver a jak nainstalovat je na místě.
* Instalaci a provozování SAP HANA a SAP instance aplikace na platformě Azure.
* Následující koncepty a procedury:
   * Plánování nasazení SAP na Azure, včetně Azure Virtual Network plánování a využívání Azure Storage. V tématu [SAP NetWeaver ve virtuálních počítačích Azure (VM) – Příručka plánování a implementace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Nasazení zásad a způsobů pro nasazení virtuálních počítačů v Azure. V tématu [nasazení virtuálních počítačů Azure pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Vysoká dostupnost pro SAP NetWeaver ASC (ABAP SAP centrální služby), SCS (SAP centrální služby) a YBRAT (vyhodnotit vyrovnání příjmu) v Azure. V tématu [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Podrobnosti o tom, jak zvýšit efektivitu v využití a více SID instalace ASC nebo SCS v Azure. V tématu [vytvořit konfiguraci více SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principy spuštěných SAP NetWeaver založené na základě Linux virtuálních počítačů v Azure. V tématu [systémem SAP NetWeaver na virtuálních počítačích Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Tato příručka obsahuje nastavení specifická pro Linux ve virtuálních počítačích Azure a podrobnosti o tom, jak správně připojte disky úložiště Azure pro virtuální počítače s Linuxem.

V tomto okamžiku virtuálních počítačích Azure certifikovány SAP pro SAP HANA škálování pouze konfigurace. Konfigurace Škálováním na více systémů s úlohami SAP HANA ještě nejsou podporovány. SAP HANA vysoké dostupnosti v případech škálování konfigurace, najdete v části [vysokou dostupnost SAP HANA na virtuálních počítačích Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Pokud požadujete k získání SAP HANA instance nebo S nebo 4HANA nebo BW/4HANA systému nasazené v čase velmi rychlé, měli byste zvážit použití [knihovny zařízení cloudu SAP](http://cal.sap.com). Můžete najít dokumentaci o nasazení, například S nebo 4HANA systému prostřednictvím SAP CAL na platformě Azure v [Tato příručka](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Je všechno, co je potřeba mít předplatné Azure a SAP uživatele, který lze registrovat pomocí knihovny zařízení SAP cloudu.

## <a name="additional-resources"></a>Další zdroje informací:
### <a name="sap-hana-backup"></a>Zálohování SAP HANA
Informace o zálohování databáze SAP HANA na virtuálních počítačích Azure najdete v tématu:
* [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [SAP HANA zálohování podle úložiště snímků](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Knihovna zařízení cloudu SAP
Informace o používání knihovny zařízení cloudu SAP nasazení S nebo 4HANA nebo BW/4HANA najdete v tématu [nasazení SAP S nebo 4HANA nebo BW/4HANA v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA podporované operační systémy
Informace o SAP HANA podporované operační systémy, v tématu [SAP podporu Poznámka #2235581 - SAP HANA: podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuální počítače Azure podporují jenom podmnožinu těchto operačních systémů. K nasazení SAP HANA v Azure jsou podporovány následující operační systémy: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Další dokumentaci SAP o SAP HANA a různých operačních systémů Linux najdete v tématu:

* [Podpora Poznámka SAP #171356 - SAP softwaru v systému Linux: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787)
* [Poznámka: podpora #1944799 - SAP HANA pokyny pro instalaci operačního systému SLES SAP](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Podpora Poznámka SAP #2205917 - SAP HANA DB doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Podpory Poznámka SAP #1984787 - SUSE Linux Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
* [Podpora Poznámka SAP #1391070 - Linux UUID řešení](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP podporu Poznámka #2009879 - SAP HANA pokyny pro operační systém Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: OS doporučená nastavení pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorování v Azure
Informace o SAP monitorování v Azure najdete v tématu:

* [Poznámka SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Tato poznámka se věnuje SAP "rozšířené monitorování" s virtuální počítače s Linuxem v Azure. 
* [Poznámka SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Tato poznámka popisuje informace o SAPOSCOL v systému Linux. 
* [Poznámka SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Tato poznámka se věnuje monitorování klíčové metriky pro SAP v Microsoft Azure.

### <a name="azure-vm-types"></a>Azure typy virtuálních počítačů
Azure typy virtuálních počítačů a scénáře podporované SAP zatížení použít s SAP HANA jsou dokumentovány v článku [SAP certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure typy virtuálních počítačů, které certifikovány SAP pro SAP NetWeaver nebo aplikační vrstvu S nebo 4HANA jsou dokumentovány v článku [1928533 Poznámka SAP - SAP aplikace v Azure: typy podporovaných produktů a virtuální počítač Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integrace se službou SAP. Linux Azure je podporována pouze na Azure Resource Manager a modelu nasazení classic. 

## <a name="manual-installation-of-sap-hana"></a>Ruční instalace SAP HANA
Tato příručka popisuje, jak ručně instalovat SAP HANA na virtuálních počítačích Azure dvěma způsoby:

* Pomocí SAP softwaru zřizování Manager (SWPM) jako součást distribuované instalace NetWeaver v kroku "instance databáze instalaci"
* Databáze nástroje Správce životního cyklu, HDBLCM a následnou instalaci NetWeaver pomocí SAP HANA

Můžete také použít SWPM pro všechny součásti (SAP HANA, SAP aplikační server a instanci ASC) nainstalovat na jeden virtuální počítač, jak je popsáno v tomto [SAP HANA blog oznámení](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Tato možnost není popsané v tomto průvodci rychlé spuštění, ale problémy, které musí vzít v úvahu jsou stejné.

Před zahájením instalace, doporučujeme, abyste si přečetli "Příprava Azure virtuálních počítačů pro ruční instalaci sady SAP HANA" dál v této příručce. Díky tomu můžete předejít několik základní chyb, které můžou nastat, když používáte pouze výchozí konfigurace virtuálního počítače Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Klíčové kroky pro instalaci SAP HANA při použití SAP SWPM
Tato část uvádí klíčové kroky pro ruční instalaci SAP HANA jedné instance při použití SAP SWPM provádět distribuované instalaci SAP NetWeaver 7.5. Jednotlivé kroky jsou podrobně popsány na snímcích obrazovky v této příručce.

1. Vytvoření virtuální sítě Azure, která zahrnuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy (v našem příkladu SUSE Linux Enterprise Server (SLES) a SLES pro SAP aplikace 12 SP1), podle modelu Azure Resource Manager.
3. Připojte k aplikačnímu serveru virtuálního počítače dva Azure standard nebo premium disky úložiště (například disky 75 GB nebo 500 GB).
4. Disky úložiště premium se připojte k serveru HANA DB virtuálních počítačů. Podrobnosti najdete v tématu v části "Instalace disku" dál v této příručce.
5. V závislosti na požadavcích velikost nebo propustnost připojení více disků a pak vytvořte prokládané svazky s využitím správu logické svazku nebo nástroj pro správu více zařízení (MDADM) na úrovni operačního systému ve virtuálním počítači.
6. Vytvořte XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Použijte jeden systému souborů pro veškerý software SAP. Používejte jiné systému souborů k adresáři /sapmnt a zálohování, například. Na serveru SAP HANA DB připojte na discích úložiště premium jako /hana a /usr/sap systémy souborů XFS. Tento proces je nezbytné pro systém souborů kořenové, který není na virtuálních počítačích Azure Linux velká, zabránit naplňování.
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** v souboru/etc/fstab parametr.
10. Nastavte parametry jádra Linux podle verze operačního systému Linux, kterou používáte. Další informace najdete v tématu příslušné poznámky k SAP, které popisují HANA a v části "Jádra parametry" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické plochy na testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stáhněte SAP software z Marketplace služby SAP.
14. Nainstalujte instanci SAP ASC na aplikačním serveru virtuálního počítače.
15. Pomocí systému souborů NFS sdílejte adresáři /sapmnt mezi testovací virtuální počítače. Aplikační server virtuálního počítače je na serveru NFS.
16. Nainstalujte instanci databáze, včetně HANA, pomocí SWPM na serveru databáze virtuálních počítačů.
17. Instalace serveru primární aplikace (Pa adresy) na aplikačním serveru, virtuálních počítačů.
18. Spusťte konzolu pro správu SAP (SAP MC). Propojit s grafickým uživatelským rozhraním SAP nebo HANA Studio, například.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Klíčové kroky pro instalaci SAP HANA při použití HDBLCM
Tato část uvádí klíčové kroky pro ruční instalaci SAP HANA jedné instance při použití SAP HDBLCM provádět distribuované instalaci SAP NetWeaver 7.5. Jednotlivé kroky jsou podrobně popsány na snímcích obrazovky v této příručce.

1. Vytvoření virtuální sítě Azure, která zahrnuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy (v našem příkladu SLES a SLES pro SAP aplikace 12 SP1) podle modelu Azure Resource Manager.
3. Připojení dvě Azure standard nebo premium disky úložiště (například disky 75 GB nebo 500 GB) na aplikační server virtuálního počítače.
4. Disky úložiště premium se připojte k serveru HANA DB virtuálních počítačů. Podrobnosti najdete v tématu v části "Instalace disku" dál v této příručce.
5. V závislosti na požadavcích na velikost nebo propustnost připojte více disků a vytvořit prokládané svazky s využitím správu logické svazku nebo nástroj pro správu více zařízení (MDADM) na úrovni operačního systému ve virtuálním počítači.
6. Vytvořte XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Použijte jeden systému souborů pro veškerý software SAP a použít jiné jednu pro adresář /sapmnt a zálohování, například. Na serveru SAP HANA DB připojte na discích úložiště premium jako /hana a /usr/sap systémy souborů XFS. Tento proces je potřeba můžou pomoct zabránit kořenové systém souborů, která není velký na virtuálních počítačích Azure Linux, naplňování.
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** v souboru/etc/fstab parametr.
10. Nastavit parametry jádra podle verze operačního systému Linux, kterou používáte. Další informace najdete v tématu příslušné poznámky k SAP, které popisují HANA a v části "Jádra parametry" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické plochy na testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stáhněte SAP software z Marketplace služby SAP.
14. Vytvořte skupinu, sapsys, se skupinou ID 1001 na serveru HANA DB virtuálních počítačů.
15. Nainstalujte SAP HANA na serveru databáze virtuálních počítačů pomocí HANA databáze Lifecycle Manager (HDBLCM).
16. Nainstalujte instanci SAP ASC na aplikačním serveru virtuálního počítače.
17. Pomocí systému souborů NFS sdílejte adresáři /sapmnt mezi testovací virtuální počítače. Aplikační server virtuálního počítače je na serveru NFS.
18. Nainstalujte instanci databáze, včetně HANA, pomocí SWPM na serveru HANA DB virtuálních počítačů.
19. Instalace serveru primární aplikace (Pa adresy) na aplikačním serveru, virtuálních počítačů.
20. Spusťte SAP MC. Připojení přes grafické uživatelské rozhraní SAP nebo HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Příprava virtuálních počítačích Azure pro ruční instalaci sady SAP HANA
Tato část obsahuje následující témata:

* Aktualizace operačního systému
* Instalační program disku
* Parametry jádra
* systémy souborů
* Soubor/etc/hosts
* Soubor/etc/fstab

### <a name="os-updates"></a>Aktualizace operačního systému
Zkontrolujte operační systém Linux aktualizace a opravy před instalací další software. Instalací opravy, může být k tomu volání číslo technické podpory.

Ujistěte se, že používáte:
* SUSE Linux Enterprise Server pro aplikace SAP.
* Red Hat Enterprise Linux pro aplikace SAP nebo Red Hat Enterprise Linux pro SAP HANA. 

Pokud jste to ještě neudělali, zaregistrujte se na vaše předplatné Linux od dodavatele Linux nasazení operačního systému. Všimněte si, že má SUSE bitové kopie operačního systému pro SAP aplikace, které již obsahují služby a které jsou registrovány automaticky.

Tady je příklad Kontrola dostupných oprav pro SUSE Linux pomocí **zypper** příkaz:

 `sudo zypper list-patches`

V závislosti na druhu problém jsou opravy klasifikovány podle kategorie a závažnost. Kategorie běžně používané hodnoty jsou: **zabezpečení**, **doporučená**, **volitelné**, **funkce**, **dokumentu**, nebo **yast**.
Běžně používané hodnoty závažnosti jsou: **kritické**, **důležité**, **střední**, **nízkou**, nebo **neurčené**.

**Zypper** příkaz vyhledá jenom aktualizace, které vyžadují nainstalované balíčky. Můžete například použít tento příkaz:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Můžete přidat parametr `--dry-run` otestovat aktualizaci bez ve skutečnosti aktualizace systému.


### <a name="disk-setup"></a>Instalační program disku
Systém souborů kořenové ve virtuální počítač s Linuxem v Azure má omezenou velikost. Proto je potřeba připojit další místo na disku na virtuální počítač Azure pro spuštění SAP. Pro aplikační server SAP virtuálních počítačích Azure může být dostatečná používat úložiště Azure standardní disky. Pro SAP HANA databázového systému virtuální počítače Azure, je však povinné použití Azure Premium Storage disky pro produkční a mimo produkční implementace.

Na základě [požadavky na úložiště SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), navržený následující konfigurace Azure Premium Storage: 

| VIRTUÁLNÍ POČÍTAČ SKU | Paměť RAM |  / hana/protokolu a/hana/data <br /> rozdělená s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

V konfiguraci navrhované disku HANA datový svazek a svazek protokolu jsou umístěny na stejnou sadu Azure prémiové disky úložiště, které jsou rozložené s LVM nebo MDADM. Není nutné definovat všechny úrovně redundance diskového pole RAID, protože Azure Premium Storage udržuje tři bitové kopie disků pro redundanci. Abyste měli jistotu, že nakonfigurujete dostatečně velké úložiště, najdete [požadavky na úložiště SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) a [aktualizace příručce k instalaci serveru SAP HANA a](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Svazky propustnost jiný virtuální pevný disk (VHD) disků různých Azure premium storage taky zvážit, jak je uvedeno v [vysoce výkonné úložiště Premium a spravované disky pro virtuální počítače](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

K virtuálním počítačům HANA databázového systému pro ukládání záloh databáze nebo transakčního protokolu můžete přidat další disky úložiště premium.

Další informace o dva hlavní nástroje použít ke konfiguraci proložení najdete v následujících článcích:

* [Konfigurace softwaru diskového pole RAID v systému Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurace LVM na virtuální počítač s Linuxem v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o připojení disky pro virtuální počítače Azure se systémem Linux jako hostovaný operační systém najdete v tématu [přidejte disk do virtuálního počítače s Linuxem](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Premium Storage umožňuje definovat disku ukládání do mezipaměti režimy. Pro prokládané sadu, která uchovává /hana/data a /hana/log by mělo být zakázáno ukládání do mezipaměti na disku. Pro další svazky (disky), je třeba nastavit režim ukládání do mezipaměti na **jen pro čtení**.

Další informace najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../windows/premium-storage.md).

Ukázka šablony JSON pro vytvoření virtuálních počítačů, najdete [šablon Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates).
Šablona virtuálního počítače. jednoduchý sles je základní šablonu. Úložiště v tématu, s diskem další 100 GB dat zahrnuje. Tuto šablonu můžete použít jako základ. Konkrétní konfigurace však můžete upravit šablonu.

>[!Note]
>Je důležité připojit disk úložiště Azure pomocí UUID, jak je uvedeno v [systémem SAP NetWeaver na virtuálních počítačích Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V testovacím prostředí byly dva úložiště Azure standardní disky připojené k serveru aplikace SAP virtuálních počítačů, jak je znázorněno na následujícím snímku obrazovky. Jeden disk ukládají veškerý software SAP (včetně NetWeaver 7.5, SAP grafickým uživatelským rozhraním a SAP HANA) pro instalaci. Druhý disk zajistit, že budou k dispozici pro další požadavky (například zálohování a testovací data) a pro /sapmnt adresář (tj, SAP profily), který má být sdílen všechny virtuální počítače, které patří do stejné šířku SAP dost volného místa.

![Aplikace serveru SAP HANA disky okno zobrazování dvě datové disky a jejich velikosti](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jádra
SAP HANA vyžaduje konkrétní nastavení jádra systému Linux, které nejsou součástí standardní Azure Galerie obrázků a musí být nastavena ručně. V závislosti na tom, jestli používáte SUSE nebo Red Hat může být různé parametry. V poznámkách k SAP uvedena i výše poskytnout informace o těchto parametrů. Na snímcích obrazovky ukazuje byl použit SUSE Linux 12 SP1. 

SLES pro SAP aplikace 12 GA a SLES pro SP1 12 SAP aplikací mít nový nástroj, **přizpůsobená adm**, který nahrazuje starý **sapconf** nástroj. Je k dispozici pro speciální profil SAP HANA **přizpůsobená adm**. Chcete-li ladit systému pro SAP HANA, zadejte následující jako kořenové uživatele:

   `tuned-adm profile sap-hana`

Další informace o **přizpůsobená adm**, najdete v článku [SUSE dokumentaci o přizpůsobená adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na následujícím snímku obrazovky, uvidíte jak **přizpůsobená adm** změnit `transparent_hugepage` a `numa_balancing` hodnoty, podle požadované nastavení SAP HANA.

![Nástroj přizpůsobená adm změní hodnoty podle požadované nastavení SAP HANA](./media/hana-get-started/image005.jpg)

Chcete-li nastavení jádra SAP HANA trvalé, použijte **grub2** na SLES 12. Další informace o **grub2**, přejděte na [Struktura konfiguračního souboru](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) části dokumentace SUSE.

Následující snímek obrazovky ukazuje, jak byly nastavení jádra změnit v konfiguračním souboru a poté zkompilovat pomocí **grub2 mkconfig**:

![Nastavení jádra změnit v konfiguračním souboru a zkompilovat pomocí grub2 mkconfig](./media/hana-get-started/image006.jpg)

Další možností je nastavení změnit pomocí YaST a **spouštěcí zavaděč** > **jádra parametry** nastavení:

![Karta nastavení jádra parametry v YaST spouštěcí zavaděč](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>systémy souborů
Následující snímek obrazovky ukazuje dva systémy souborů, které byly vytvořeny na aplikačním serveru SAP virtuálních počítačů nad dva disky připojené úložiště Azure úrovně standard. Oba systémy souborů typu XFS a jsou připojené k /sapdata a /sapsoftware.

Není to povinné do struktury vaší systémy souborů tímto způsobem. Máte další možnosti pro vytvoření struktury místo na disku. Většina důležitý faktor je zabránit nedostatku volného místa v kořenovém souboru systému.

![Dva systémy souborů vytvořena na serveru aplikace SAP virtuálních počítačů](./media/hana-get-started/image008.jpg)

Při použití SAPinst (SWPM) ohledně SAP HANA DB virtuálních počítačů, během instalace databáze a **typické** možnost instalace, všechny součásti nainstalovány v rámci /hana a /usr/sap. V části /usr/sap je výchozím umístěním pro zálohu protokolu SAP HANA. Znovu protože je důležité systém souborů kořenové zabránit vyčerpání volného místa, ujistěte se, že není dostatek volného místa v rámci /hana a /usr/sap před instalací SAP HANA pomocí SWPM.

Popis standardní systém souborů rozložení SAP HANA najdete v tématu [aktualizace příručce k instalaci serveru SAP HANA a](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Další souborové systémy vytvořena na serveru aplikace SAP virtuálních počítačů](./media/hana-get-started/image009.jpg)

Když instalujete SAP NetWeaver na standardní SLES/SLES pro bitovou kopii Galerie Azure 12 aplikace SAP, zobrazí se zpráva s informacemi o tom, že neexistuje žádná odkládacího souboru, jak je znázorněno na následujícím snímku obrazovky. Zavřete tuto zprávu, můžete ručně přidat odkládací soubor pomocí **dd**, **mkswap**, a **swapon**. Další informace jak, vyhledejte "Přidání odkládací soubor ručně" v [pomocí dělicí metody YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) části dokumentace SUSE.

Další možností je konfigurovat odkládacího souboru pomocí agenta virtuálního počítače s Linuxem. Další informace najdete v tématu [Azure Linux Agent uživatelská příručka](../../extensions/agent-linux.md).

![Místní zpráva radí, že je nedostatečné velikosti odkládacího souboru](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Soubor/etc/hosts
Než začnete instalovat SAP, ujistěte se, že obsahuje názvy hostitelů a IP adresy virtuálních počítačů SAP soubor/etc/hosts. Nasadit všechny SAP virtuálních počítačů v rámci jednu virtuální síť Azure a pak použít interní IP adresy, jak je vidět tady:

![Názvy hostitelů a IP adresy virtuálních počítačů SAP uvedené v souboru/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Soubor/etc/fstab

Je vhodné přidat **nofail** parametr fstab souboru. Tímto způsobem, pokud se operace nezdaří s disky, virtuální počítač není přečnívat v procesu spouštění. Ale mějte na paměti, že nemusí být k dispozici další místo na disku a procesů může zaplnit kořenovém souboru systému. Pokud chybí /hana, SAP HANA se nespustí.

![Přidejte parametr nofail do souboru fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafické GNOME plocha u SLES 12/SLES 12 aplikace SAP
Tato část obsahuje následující témata:

* Instalace GNOME plochy a xrdp na SLES 12/SLES pro SAP aplikace 12
* Spuštěné MC SAP založené na jazyce Java pomocí prohlížeče Firefox na SLES 12/SLES 12 aplikace SAP

Můžete taky alternativy například Xterminal nebo VNC (nejsou popsané v tomto průvodci).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalace GNOME plochy a xrdp na SLES 12/SLES pro SAP aplikace 12
Pokud máte pozadí systému Windows, můžete použít grafické plochy přímo v rámci virtuálních počítačů Linux SAP snadno spustit Firefox, SAPinst, SAP grafickým uživatelským rozhraním, SAP MC nebo HANA Studio a připojit k virtuálnímu počítači pomocí protokol RDP (Remote Desktop) z počítače Windows. Závisí na zásady vaší společnosti o přidání grafické uživatelské rozhraní do produkčního prostředí a Linux nevýrobní prostředí na základě systémů, můžete chtít nainstalovat GNOME na vašem serveru. Instalace GNOME plochy na 12/SLES Azure SLES pro virtuální počítač 12 SAP aplikace:

1. Nainstalujte plochy GNOME zadáním následujícího příkazu (například v okně PuTTY):

   `zypper in -t pattern gnome-basic`

2. Nainstalujte xrdp umožňující připojení k virtuálnímu počítači prostřednictvím protokolu RDP:

   `zypper in xrdp`

3. Upravte /etc/sysconfig/windowmanager a nastavte výchozí okno správce na GNOME:

   `DEFAULT_WM="gnome"`

4. Spustit **chkconfig** a ujistěte se, že xrdp spustí automaticky po restartování systému:

   `chkconfig -level 3 xrdp on`

5. Pokud máte potíže s připojením RDP, pokuste se službu restartovat (z PuTTY okna, např.):

   `/etc/xrdp/xrdp.sh restart`

6. Pokud restartování xrdp uvedeno v předchozím kroku nefunguje, můžete zkontrolujte soubor .pid:

   `check /var/run` 

   Vyhledejte `xrdp.pid`. Pokud vám hledání, odeberte ji a zkuste to znovu restartovat.

### <a name="starting-sap-mc"></a>Počáteční MC SAP
Po instalaci plochy GNOME od grafické MC SAP založené na jazyce Java Firefox při spuštění služby Azure SLES 12/SLES pro virtuální počítač 12 aplikace SAP může zobrazit k chybě z důvodu chybějící Java prohlížeče modulu plug-in.

Adresa URL zahájíte SAP MC je `<server>:5<instance_number>13`.

Další informace najdete v tématu [spouštění konzoly pro správu SAP webové](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Následující snímek obrazovky ukazuje chybovou zprávu, která se zobrazí v případě, že chybí modul plug-in prohlížeče Java:

![Chybová zpráva označující chybějící Java – modul plug-in prohlížeče](./media/hana-get-started/image013.jpg)

Jeden způsob, jak vyřešit problém se má nainstalovat chybějící modul plug-in pomocí YaST, jak je znázorněno na následujícím snímku obrazovky:

![Použití YaST k instalaci chybí modulu plug-in](./media/hana-get-started/image014.jpg)

Když znovu zadáte adresu URL konzoly správy SAP, zobrazí se zpráva s žádostí o aktivaci modul plug-in:

![Dialogové okno modul plug-in aktivace](./media/hana-get-started/image015.jpg)

Může také zobrazit chybovou zprávu o soubor chybějící javafx.properties. To se týká požadavek Oracle Java 1.8 pro SAP 7.4 grafickým uživatelským rozhraním. (Viz [Poznámka SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Verzi Javy IBM ani balíček openjdk doručeny s SLES/SLES pro SAP aplikace 12 obsahuje potřebné javafx.properties souboru. Řešení je ke stažení a instalaci Java SE 8 z databáze Oracle.

Informace o podobném problému s openjdk na openSUSE najdete v tématu vlákno diskuse [SAPGui 7.4 Java pro openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Ruční instalace SAP HANA: SWPM
Řadu snímky obrazovky v této části jsou uvedeny klíče kroky pro instalaci SAP NetWeaver 7.5 a SAP HANA SP12 při použití SWPM (SAPinst). Jako součást instalace NetWeaver 7.5 můžete SWPM také nainstalovat databázi HANA jako jedna instance.

V prostředí testovací ukázka jsme nainstalovali jenom jeden rozšířená obchodní aplikace programování (ABAP) aplikačního serveru. Jak je znázorněno na následujícím snímku obrazovky, jsme použili **systému DFS** možnost nainstalovat ASC a instance serveru primární aplikace jeden virtuální počítač Azure a SAP HANA jako systém databázi na jiný virtuální počítač Azure.

![ASC a instance serveru primární aplikace nainstalovat pomocí možnosti systému DFS](./media/hana-get-started/image012.jpg)

Po nainstalování ASC instance na serveru aplikace virtuálních počítačů a je nastaven na "green" v konzole pro správu SAP (zobrazené na následujícím snímku obrazovky), musí být sdílená adresáři /sapmnt (včetně adresáři profil SAP) se serverem databáze SAP HANA virtuálních počítačů. Krok instalace DB potřebuje přístup k těmto informacím. Nejlepší způsob, jak poskytnout přístup se má používat systém souborů NFS, kterého lze nakonfigurovat pomocí YaST.

![Konzola pro správu SAP zobrazující instance ASC nainstalovaný na serveru aplikace virtuálních počítačů a nastavte na "green"](./media/hana-get-started/image016.jpg)

Na serveru aplikace virtuální počítač by měl adresáři /sapmnt sdíleny prostřednictvím systému souborů NFS pomocí **rw** a **no_root_squash** možnosti. Výchozí hodnoty jsou **ro** a **root_squash**, což může vést k problémům při instalaci instanci databáze.

![Pomocí možnosti rw nebo no_root_squash sdílení adresáři /sapmnt prostřednictvím systému souborů NFS](./media/hana-get-started/image017b.jpg)

Jak ukazuje následující snímek obrazovky, musí být /sapmnt sdílenou složku z virtuálního počítače na aplikační server nakonfigurovanému na serveru SAP HANA DB virtuálních počítačů pomocí **klient NFS** (a YaST).

![Sdílenou složku /sapmnt nakonfigurovaný pomocí systému souborů NFS klienta](./media/hana-get-started/image018b.jpg)

K provedení distribuované instalace NetWeaver 7.5 (**Instance databáze**), jak je znázorněno na následujícím snímku obrazovky, přihlaste se k serveru SAP HANA DB virtuálního počítače a spustit SWPM.

![Přihlášení k serveru SAP HANA DB virtuálního počítače a spustit SWPM nainstalovat instanci databáze](./media/hana-get-started/image019.jpg)

Po výběru **typické** instalace a cestu k instalačním médiu, zadejte DB SID, název hostitele, číslo instance a databáze heslo správce systému.

![SAP HANA databáze systému správce přihlašovací stránce](./media/hana-get-started/image035b.jpg)

Zadejte heslo pro DBACOCKPIT schématu:

![Pole zadávání hesel pro schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Zadejte otázku hesla SAPABAP1 schématu:

![Zadejte otázku hesla SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení každé úlohy, zobrazí se vedle jednotlivé fáze procesu instalace DB zelená značka zaškrtnutí. Zpráva "spuštění služby... Zobrazí se dokončil Instance databáze".

![Úloha dokončena okno se zprávou potvrzení](./media/hana-get-started/image023.jpg)

Po úspěšné instalaci by měla konzole pro správu SAP také zobrazit instance databáze jako "green" a zobrazit úplný seznam SAP HANA procesy (hdbindexserver, hdbcompileserver a tak dále).

![Okno konzoly pro správu SAP s seznam procesů, SAP HANA](./media/hana-get-started/image024.jpg)

Následující snímek obrazovky ukazuje části strukturu souborů v adresáři /hana/shared SWPM vytvořený během instalace HANA. Protože neexistuje žádná možnost zadejte jinou cestu, je potřeba připojit další místo na disku v adresáři /hana před instalací SAP HANA pomocí SWPM. To brání systému souborů kořenové nedostatku volného místa.

![/Hana/shared strukturu adresáře soubor, který je vytvořen během instalace HANA](./media/hana-get-started/image025.jpg)

Tento snímek obrazovky ukazuje soubor strukturu adresáře /usr/sap:

![/ Usr/sap souboru strukturu adresáře](./media/hana-get-started/image026.jpg)

Poslední krok distribuovanou instalaci ABAP je instalace primární aplikace instance serveru:

![Instance serveru ABAP instalace zobrazující primární aplikace jako poslední krok](./media/hana-get-started/image027b.jpg)

Po instalaci instance serveru primární aplikace a SAP grafického uživatelského rozhraní pomocí **řídící panel DBA** transakce potvrďte, že byla správně dokončena instalace SAP HANA:

![Řídící panel DBA okno potvrzení úspěšné instalace](./media/hana-get-started/image028b.jpg)

V posledním kroku můžete chcete nejdřív nainstalovali HANA Studio na serveru aplikace SAP virtuálních počítačů a potom se připojte k instanci serveru SAP HANA, která běží na serveru databáze virtuálních počítačů:

![Instalace SAP HANA Studio na serveru aplikace SAP virtuálních počítačů](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Ruční instalace SAP HANA: HDBLCM
Kromě instalace SAP HANA jako součást distribuované instalaci pomocí SWPM, můžete nainstalovat samostatnou HANA nejdřív pomocí HDBLCM. Můžete nainstalovat SAP NetWeaver 7.5, např. Snímky obrazovky v této části ukazují, jak tento proces funguje.

Další informace o nástroji HANA HDBLCM najdete v tématu:

* [Výběr správný SAP HANA HDBLCM pro úlohu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Nástrojích správy životního cyklu SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [Průvodce aktualizací a instalaci serveru SAP HANA](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Aby se zabránilo problémům s ID skupiny výchozí nastavení pro `\<HANA SID\>adm user` (vytvořený pomocí nástroje HDBLCM), zadejte novou skupinu s názvem `sapsys` pomocí ID skupiny `1001` před instalací SAP HANA prostřednictvím HDBLCM:

![Nové skupiny "sapsys" definované za použití skupiny ID 1001](./media/hana-get-started/image030.jpg)

Při prvním spuštění HDBLCM, zobrazí se nabídka jednoduché start. Vyberte položku 1, **nainstalovat nový systém**, jak je znázorněno na následujícím snímku obrazovky:

![Možnost "Nainstalovat nový systém" v okně HDBLCM start](./media/hana-get-started/image031.jpg)

Následující snímek obrazovky se zobrazí všechny klíčové parametry, které jste vybrali dříve.

> [!IMPORTANT]
> Adresáře, které jsou s názvem pro HANA protokolu a datových svazků, jakož i cesta instalace (/ hana/sdílené v této ukázce) a /usr/sap, by neměl být součástí kořenové systému souborů. Tyto adresáře patřit do Azure datových disků, které byly připojené k virtuálnímu počítači (popsaný v části "Instalace Disk"). Tento přístup pomáhá zabránit systému souborů kořenové z nedostatku místa. Na následujícím snímku obrazovky, uvidíte, že správce systému HANA má ID uživatele `1005` a je součástí `sapsys` skupiny (ID `1001`), byl definován před instalací.

![Seznam všech klíčové komponenty SAP HANA vybrali dříve](./media/hana-get-started/image032.jpg)

Můžete zkontrolovat `\<HANA SID\>adm user` (`azdadm` na následujícím snímku obrazovky) podrobností v adresáři/etc/hesel:

![HANA \<HANA SID\>adm uživatele podrobnosti uvedené v adresáři/etc/hesel](./media/hana-get-started/image033.jpg)

Po instalaci SAP HANA pomocí HDBLCM, uvidíte strukturu souborů v sadě SAP HANA Studio, jak je znázorněno na následujícím snímku obrazovky. SAPABAP1 schématu, která obsahuje všechny tabulky SAP NetWeaver, ještě není k dispozici.

![Struktura souborů SAP HANA v SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po instalaci SAP HANA, můžete nainstalovat SAP NetWeaver nad ho. Jak je znázorněno na následujícím snímku obrazovky, instalace byla provedena jako distribuovaná instalace pomocí SWPM (jak je popsáno v předchozí části). Když nainstalujete instanci databáze pomocí SWPM, je třeba zadat stejná data pomocí HDBLCM (například název hostitele, HANA SID a číslo instance). SWPM pak používá stávající instalaci HANA a přidá další schémat.

![Distribuované instalaci provést pomocí SWPM](./media/hana-get-started/image035b.jpg)

Následující snímek obrazovky ukazuje kroku instalace SWPM, kde můžete zadat data o DBACOCKPIT schématu:

![Krok instalace SWPM, kde je zadán DBACOCKPIT schémat dat](./media/hana-get-started/image036b.jpg)

Zadejte data o SAPABAP1 schématu:

![Zadávání dat o SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení instalace instance databáze SWPM, můžete zjistit na schéma SAPABAP1 v SAP HANA Studio:

![Schéma SAPABAP1 v SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Nakonec po dokončení SAP aplikační server a instalací s grafickým uživatelským rozhraním SAP, můžete ověřit HANA DB instance pomocí **řídící panel DBA** transakce:

![Instance databáze HANA ověření u řídící panel DBA transakce](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Stahování softwaru SAP
Software můžete stáhnout z webu Marketplace služby SAP, jak je vidět na následujících snímcích obrazovky.

Stáhněte si NetWeaver 7.5 pro Linux/HANA:

 ![Instalace služby SAP a Upgrade okna pro stahování NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Stáhněte si HANA SP12 platformy edice:

 ![Instalace služby SAP a Upgrade okna pro stahování HANA SP12 platformy Edition](./media/hana-get-started/image002.jpg)

