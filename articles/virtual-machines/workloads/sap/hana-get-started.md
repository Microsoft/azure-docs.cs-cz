---
title: 'Rychlý start: Ruční instalace jedné instance SAP HANA v Azure Virtual Machines | Dokumentace Microsoftu'
description: Úvodní příručka k ruční instalaci jedné instance SAP HANA v Azure Virtual Machines
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
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: c1d9047de814b7a80210fe2502d219921f5829a4
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976898"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Rychlý start: Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure
## <a name="introduction"></a>Úvod
Tento průvodce vám pomůže nastavit jednou instancí SAP HANA na Azure virtual machines (VM) při instalaci SAP NetWeaver 7.5 a SAP HANA 1.0 SP12 ručně. Je aktivní tohoto průvodce nasazením SAP HANA v Azure. Nenahrazuje dokumentaci k SAPU. 

>[!Note]
>Tato příručka popisuje nasazení SAP HANA na virtuálních počítačích Azure. Informace o nasazení SAP HANA na velkých instancích HANA, naleznete v tématu [použití SAPU ve virtuálních počítačích Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Požadavky
Tato příručka předpokládá, že máte zkušenosti s takovou infrastrukturu jako službu (IaaS) základy jako:
 * Jak nasadit virtuální počítače nebo virtuální sítě pomocí webu Azure portal nebo Powershellu.
 * Multiplatformní rozhraní příkazového řádku Azure (CLI), včetně možnosti používat šablony JavaScript Object Notation (JSON).

Tato příručka také předpokládá, že máte zkušenosti s:
* SAP HANA a SAP NetWeaver a způsobu jejich instalace v místním prostředí.
* Instalaci a provozování SAP HANA a instancemi aplikací SAP v Azure.
* Následující koncepty a postupy:
   * Plánování pro nasazení SAP v Azure, včetně plánování virtuální sítě Azure a použití služby Azure Storage. Zobrazit [SAP NetWeaver v Azure Virtual Machines (VM) – Příručka pro plánování a implementaci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Nasazení zásad a způsoby, jak nasadit virtuální počítače v Azure. Zobrazit [nasazení Azure Virtual Machines pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Vysoká dostupnost pro SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (centrální služby SAP) a Lajících (zařadit do fronty replikace serveru) v Azure. Zobrazit [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Podrobnosti o tom, jak zvýšit efektivitu ve využívání s instalací s několika SID ASCS/SCS v Azure. Zobrazit [vytvoření konfigurace několika identifikátorů SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principy používání systému SAP NetWeaver založené na Linuxu řízené virtuální počítače v Azure. Zobrazit [používání systému SAP NetWeaver na virtuálních počítačích Microsoft Azure operačním systémem SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Tato příručka obsahuje konkrétní nastavení pro Linux v Azure virtuální počítače a podrobnosti o tom, jak správně připojení služby Azure storage disků pro virtuální počítače s Linuxem.

Typy virtuálních počítačů Azure, které lze použít pro produkční scénáře jsou uvedeny v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). U scénářů s testovacím širší nativní typy virtuálních počítačů Azure je k dispozici.
Podrobné informace o virtuálním počítači operace a konfigurace najdete dokumentu [konfigurace infrastruktury SAP HANA a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Pro zajištění vysoké dostupnosti SAP HANA, naleznete v tématu [vysoké dostupnosti SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Pokud hledáte získejte instance SAP HANA nebo S/4HANA nebo BW/4HANA systému nasazené ve velmi rychlé uvedení, měli byste zvážit použití [SAP Cloud Appliance Library](http://cal.sap.com). Můžete najít dokumentaci k nasazení, například systém S/4HANA přes SAP CAL na Azure v [Tato příručka](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Je všechno, co je potřeba mít předplatné Azure a SAP uživatele, který lze dokument zaregistrovat u SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Další materiály
### <a name="sap-hana-backup"></a>Zálohování SAP HANA
Informace o zálohování databáze SAP HANA na virtuálních počítačích Azure najdete tady:
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Zálohování SAP HANA založené na snímcích úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Informace o používání SAP Cloud Appliance Library S/4HANA nebo BW/4HANA nasazení najdete v tématu [nasadit řešení SAP S/4HANA nebo BW/4HANA v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA podporované operační systémy
Informace o SAP HANA podporované operační systémy, najdete v části [SAP Support Poznámka #2235581 – SAP HANA: Podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuální počítače Azure podporují pouze podmnožinu těchto operačních systémů. Následující operační systémy jsou podporovány pro nasazení SAP HANA v Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Další dokumentaci k SAP o SAP HANA a různých operačních systémech Linux najdete v článku:

* [SAP Support Poznámka 171356 # - softwaru SAP v Linuxu:  Obecné informace](https://launchpad.support.sap.com/#/notes/1984787)
* [Poznámka: podpora #1944799 – SAP HANA pokyny pro instalaci operačního systému SLES pro SAP](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Poznámka: podpora SAP #2205917 – databáze SAP HANA doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Poznámka: podpora SAP 1984787 # - operačním systémem SUSE Linux Enterprise Server 12:  Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
* [Poznámka: podpora 1391070 # – Linux UUID řešení SAP](https://launchpad.support.sap.com/#/notes/1391070)
* [Poznámka: podpora #2009879 – pokyny pro SAP HANA pro operační systém Red Hat Enterprise Linux (RHEL) SAP](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 – DATABÁZE SAP HANA: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorování v Azure
Informace o SAP monitorování v Azure najdete tady:

* [Poznámka SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Tato poznámka popisuje SAP "rozšířené monitorování" s Linuxovými virtuálními počítači v Azure. 
* [Poznámka SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Tato poznámka popisuje informace o SAPOSCOL v Linuxu. 
* [Poznámka SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Tato poznámka popisuje klíčové metriky pro monitorování pro SAP v Microsoft Azure.

### <a name="azure-vm-types"></a>Azure typy virtuálních počítačů
Azure typy virtuálních počítačů a scénáře podporované SAP úloh používá se SAP HANA, které jsou popsány v [SAP certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure typy virtuálních počítačů, které jsou certifikované SAP pro SAP NetWeaver a S/4HANA aplikační vrstvy jsou dokumentovány v článku [SAP Poznámka 1928533 – aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integrace se službou SAP-Linux-Azure je podporována pouze na Azure Resource Manageru a modelu nasazení classic. 

## <a name="manual-installation-of-sap-hana"></a>Ruční instalace SAP HANA

> [!IMPORTANT]
> Ujistěte se, že je operační systém, vyberete na konkrétní typy virtuálních počítačů, které používáte pro SAP HANA s certifikací SAP. Seznam SAP HANA certified typy virtuálních počítačů a operační systém verze pro ty lze vyhledávat [platformách IaaS certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ujistěte se, že klikněte na tlačítko na podrobné informace o typu virtuálního počítače uvedené zobrazíte úplný seznam SAP HANA nepodporuje verzí operačního systému pro konkrétní typ virtuálního počítače. Uvědomte si, že v příkladu v tomto dokumentu jsme použili operačním systémem SLES verze, která není podporována SAP pro SAP HANA na virtuálních počítačích řady M-Series.
>

Tato příručka popisuje, jak ručně nainstalovat SAP HANA na virtuálních počítačích Azure dvěma různými způsoby:

* S použitím distribuované instalaci NetWeaver v kroku "instance databáze instalace" v rámci SAP Software zřizování správce (SWPM)
* Databáze nástroje Správce životního cyklu, HDBLCM a následnou instalaci NetWeaver pomocí SAP HANA

Můžete také použít SWPM pro všechny součásti (SAP HANA, aplikační server SAP a ASCS instance) nainstalovat na jeden virtuální počítač, jak je popsáno v tomto [oznámení na blogu SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Tato možnost není popsané v této úvodní příručky, ale problémy, které musí vzít v úvahu jsou stejné.

Než začnete instalací, doporučujeme, abyste si přečetli "Azure Příprava virtuálních počítačů pro ruční instalace SAP HANA" dále v tomto průvodci. To pomáhá zabránit několika základní chyby, které mohou nastat při použití pouze výchozí konfigurace virtuálního počítače Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Základní kroky pro instalaci SAP HANA při použití SAP SWPM
Tato část uvádí základní kroky pro ruční instalace SAP HANA jednou instancí, použijete-li provést distribuované instalaci SAP NetWeaver 7.5 SAP SWPM. Jednotlivé kroky jsou vysvětlené podrobněji na snímcích obrazovky dál v této příručce.

1. Vytvořte virtuální síť Azure, která obsahuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy (v našem příkladu, operačním systémem SUSE Linux Enterprise Server (SLES) a SLES pro SAP aplikace 12 SP1), podle modelu Azure Resource Manageru.
3. Připojte k aplikačnímu serveru virtuálního počítače dvě Azure standard nebo disky storage úrovně premium (například disky 75 GB a 500 GB).
4. Disky premium storage se připojte k serveru databáze HANA virtuálního počítače. Podrobnosti najdete v části "Nastavení disku" dál v této příručce.
5. V závislosti na požadavcích velikost nebo propustnost připojit několik disků a pak vytvořte prokládané svazky s využitím Správa logických svazků nebo nástroje pro správu více zařízení (MDADM) na úrovni operačního systému ve virtuálním počítači.
6. Vytvoření XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Pomocí jednoho systému souborů pro veškerého softwaru SAP. Používejte jiné systém souborů k adresáři /sapmnt a zálohy, třeba. Na serveru databáze SAP HANA připojte na disky premium storage jako /hana a /usr/sap XFS systémy souborů. Je nutné zabránit nezaplňuje kořenové systém souborů, který není velká na virtuálních počítačích Azure s Linuxem, tento proces.
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** parametru v souboru/etc/fstab.
10. Nastavte parametry jádro Linuxu podle verze operačního systému Linux, který používáte. Další informace najdete v příslušné poznámky SAP, které popisují HANA a v části "Jádra parametrů" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické desktopu testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stahování softwaru SAP SAP Service Marketplace.
14. Nainstalujte instanci SAP ASCS na serveru aplikace virtuálního počítače.
15. Sdílení adresář /sapmnt mezi testovací virtuální počítače s použitím systému souborů NFS. Aplikační server virtuálního počítače je na serveru NFS.
16. Nainstalujte instanci databáze, včetně HANA, a to pomocí SWPM na serveru databáze virtuálního počítače.
17. Instalace serveru primární aplikace (Pa adresy) na aplikačním serveru virtuálního počítače.
18. Spusťte konzolu pro správu SAP (SAP MC). Spojte se s grafickým uživatelským rozhraním SAP nebo HANA Studio, například.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Základní kroky pro instalaci SAP HANA při použití HDBLCM
Tato část uvádí základní kroky pro ruční instalace SAP HANA jednou instancí, použijete-li provést distribuované instalaci SAP NetWeaver 7.5 SAP HDBLCM. Jednotlivé kroky jsou vysvětlené podrobněji snímky obrazovky v této příručce.

1. Vytvořte virtuální síť Azure, která obsahuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy (v našem příkladu SLES a SLES pro SAP SP1 12 aplikací) podle modelu Azure Resource Manageru.
3. Připojte k virtuálnímu počítači serverem aplikace dva Azure standard nebo disky storage úrovně premium (například disky 75 GB a 500 GB).
4. Disky premium storage se připojte k serveru databáze HANA virtuálního počítače. Podrobnosti najdete v části "Nastavení disku" dál v této příručce.
5. V závislosti na požadavcích velikost nebo propustnost připojit několik disků a vytvořte prokládané svazky s využitím Správa logických svazků nebo nástroje pro správu více zařízení (MDADM) na úrovni operačního systému ve virtuálním počítači.
6. Vytvoření XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Pomocí jednoho systému souborů pro veškerého softwaru SAP a použijte druhou k adresáři /sapmnt a zálohy, třeba. Na serveru databáze SAP HANA připojte na disky premium storage jako /hana a /usr/sap XFS systémy souborů. Tento proces je nutné zabránit nezaplňuje kořenové systém souborů, který není velká na virtuálních počítačích Azure s Linuxem.
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** parametru v souboru/etc/fstab.
10. Nastavit parametry jádra podle verze operačního systému Linux, který používáte. Další informace najdete v příslušné poznámky SAP, které popisují HANA a v části "Jádra parametrů" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické desktopu testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stahování softwaru SAP SAP Service Marketplace.
14. Vytvořte skupinu sapsys, s ID 1001 skupiny na serveru databáze HANA VM.
15. Instalace SAP HANA na serveru databáze virtuálního počítače pomocí HANA Database životního cyklu správce (HDBLCM).
16. Nainstalujte instanci SAP ASCS na serveru aplikace virtuálního počítače.
17. Sdílení adresář /sapmnt mezi testovací virtuální počítače s použitím systému souborů NFS. Aplikační server virtuálního počítače je na serveru NFS.
18. Nainstalujte instanci databáze, včetně HANA, a to pomocí SWPM na serveru databáze HANA virtuálního počítače.
19. Instalace serveru primární aplikace (Pa adresy) na aplikačním serveru virtuálního počítače.
20. Spuštění SAP MC. Připojení přes grafické uživatelské rozhraní SAP nebo HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Příprava virtuálních počítačů Azure pro ruční instalaci sady SAP HANA
Tato část obsahuje následující témata:

* Aktualizace operačního systému
* Nastavení disku
* Parametry jádra
* Systémy souborů
* Soubor/etc/hosts
* Soubor/etc/fstab

### <a name="os-updates"></a>Aktualizace operačního systému
Vyhledat operační systém Linux aktualizace a opravy, před instalací dalšího softwaru. Instalací opravy, může být schopni vyhnout volání číslo technické podpory.

Ujistěte se, že používáte:
* SUSE Linux Enterprise Server pro aplikace SAP.
* Red Hat Enterprise Linux pro SAP aplikace nebo systému Red Hat Enterprise Linux for SAP HANA. 

Pokud jste to ještě neudělali, zaregistrujte nasazení operačního systému ve vašem předplatném Linux od dodavatele Linux. Všimněte si, že SUSE má Image operačních systémů pro aplikace SAP, které už zahrnují služby a které jsou registrovány automaticky.

Tady je příklad vyhledání dostupných oprav operačního systému SUSE Linux s použitím **zypperu** příkaz:

 `sudo zypper list-patches`

V závislosti na druhu problém opravy dělí podle kategorie a závažnost. Běžně používané hodnoty pro kategorie: **zabezpečení**, **doporučuje**, **volitelné**, **funkce**, **dokumentu**, nebo **yast**.
Běžně používané hodnoty pro závažnost: **kritické**, **důležité**, **střední**, **nízké**, nebo **tento parametr zadán**.

**Zypperu** příkaz by mohl vypadat pouze pro aktualizace, které je třeba nainstalované balíčky. Například můžete použít tento příkaz:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Můžete přidat parametr `--dry-run` otestovat aktualizaci bez skutečně aktualizace systému.


### <a name="disk-setup"></a>Nastavení disku
Kořenové systému souborů do virtuálního počítače s Linuxem v Azure má omezení velikosti. Proto je potřeba připojit další místo na disku k virtuálnímu počítači Azure pro spuštění SAP. Pro aplikační server SAP virtuální počítače Azure je možné použít disky Azure standard storage dostatečná. Pro SAP HANA DBMS virtuální počítače Azure, použijte disky Azure Premium Storage pro produkční a neprodukční implementace ale povinné.

Na základě [požadavky na úložiště pro SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), navrhne se následující konfigurace služby Azure Premium Storage: 

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM |  / hana/dat a/hana/log <br /> prokládané s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

V konfiguraci navrhované disku objemu dat HANA a svazek s protokolem umísťují na stejnou sadu disky Azure premium storage, které jsou rozložené s LVM nebo MDADM. Není nutné definovat všechny úrovně redundance diskového pole RAID, protože Azure Premium Storage udržuje tři imagí disků pro zajištění redundance. Pokud chcete mít jistotu, že nakonfigurujete dostatečně velké úložiště, najdete [požadavky na úložiště pro SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) a [Průvodce instalací serveru SAP HANA a aktualizací](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Zvažte také jiný virtuální pevný disk (VHD) propustnost objem disky různých Azure premium storage, jak je uvedeno v [vysoce výkonné úložiště úrovně Premium a spravovaným diskům pro virtuální počítače](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Můžete přidat další disky storage úrovně premium pro virtuální počítače HANA DBMS pro ukládání záloh databáze nebo transakčního protokolu.

Další informace o dva hlavní nástroje použít ke konfiguraci prokládání najdete v následujících článcích:

* [Konfigurace softwarového pole RAID v Linuxu](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurace LVM na virtuální počítač s Linuxem v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další informace o připojování disků k virtuálním počítačům Azure s Linuxem jako hostovaný operační systém, najdete v části [přidání disku do virtuálního počítače s Linuxem](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Premium Storage umožňuje definovat režimy používání mezipaměti disku. Prokládané sady /hana/data a /hana/log by mělo být zakázáno ukládání do mezipaměti disku. Pro jiné svazky (disky), je třeba nastavit režim ukládání do mezipaměti na **jen pro čtení**.

Další informace najdete v tématu [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../../windows/premium-storage.md).

Pokud chcete najít ukázkové šablony JSON pro vytvoření virtuálních počítačů, přejděte na [šablony pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates).
Šablona virtuálního počítače. jednoduchý sles je základní šablony. Oddíl úložiště s diskem dalších 100 GB dat zahrnuje. Tuto šablonu můžete použít jako základ. Šablony můžete přizpůsobit na konkrétní konfiguraci.

>[!Note]
>Je potřeba připojit disk úložiště Azure s použitím UUID, jak je uvedeno v [systémem SAP NetWeaver na virtuálních počítačích Microsoft Azure operačním systémem SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V testovacím prostředí byly připojeny disky dvou úložiště Azure úrovně standard na aplikační server SAP virtuálního počítače, jak je znázorněno na následujícím snímku obrazovky. Jeden disk ukládají pro instalaci softwaru SAP (včetně NetWeaver 7.5, grafickým uživatelským rozhraním SAP a SAP HANA). Druhý disk zajistit, že bude k dispozici pro další požadavky (například zálohování a testovací data) a /sapmnt adresář (tj, SAP profily), který chcete sdílet mezi všechny virtuální počítače, které patří do stejného prostředí SAP dostatek volného místa.

![Aplikační server SAP HANA disky okno zobrazující dva datové disky a jejich velikosti](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jádra
SAP HANA vyžaduje konkrétní nastavení jádra systému Linux, které nejsou součástí standardní Azure Galerie imagí a musí být nastaven na ručně. V závislosti na tom, ať už používáte, SUSE nebo Red Hat může být jiné parametry. Poznámky SAP výše uvedených poskytnout informace o těchto parametrů. Na snímcích obrazovky ukazuje byl použit operačním systémem SUSE Linux 12 SP1. 

SLES pro SAP aplikace 12 GA a SLES pro SAP aplikace 12 SP1 mají nový nástroj, **vyladěný adm**, který nahrazuje původní **sapconf** nástroj. Speciální profil SAP HANA je k dispozici pro **vyladěný adm**. Pokud chcete optimalizovat systém pro SAP HANA, zadejte následující jako kořenové uživatele:

   `tuned-adm profile sap-hana`

Další informace o **vyladěný adm**, najdete v článku [SUSE dokumentaci k vyladění adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na následujícím snímku obrazovky vidíte jak **vyladěný adm** změnit `transparent_hugepage` a `numa_balancing` hodnoty, podle toho, požadovaná nastavení SAP HANA.

![Nástroj vyladěný adm změní hodnoty podle požadované nastavení SAP HANA](./media/hana-get-started/image005.jpg)

Pokud chcete trvalé nastavení jádra SAP HANA, použijte **grub2** na SLES 12. Další informace o **grub2**, přejděte [struktura konfiguračních souborů](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) část dokumentace SUSE.

Následující snímek obrazovky ukazuje, jak nastavení jádra byly změněny v konfiguračním souboru a poté zkompilován s použitím **grub2 mkconfig**:

![Nastavení jádra změnil v konfiguračním souboru a zkompilován s použitím grub2 mkconfig](./media/hana-get-started/image006.jpg)

Další možností je toto nastavení změnit pomocí YaST a **spouštěcí zavaděč** > **jádra parametry** nastavení:

![Na kartě Nastavení parametrů jádra v YaST spouštěcí zavaděč](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systémy souborů
Následující snímek obrazovky ukazuje dva systémy souborů, které byly vytvořeny na aplikační server SAP virtuálního počítače na dvou discích připojených úložiště Azure úrovně standard. Oba systémy souborů jsou typu XFS a jsou připojené k /sapdata a /sapsoftware.

Není nutné strukturovat vaše systémy souborů tímto způsobem. Máte další možnosti pro strukturování místo na disku. Nejdůležitější aspektem je kořenový soubor systém zabránit nedostatku volného místa.

![Dva systémy souborů, které jsou vytvořené na aplikační server SAP virtuálního počítače](./media/hana-get-started/image008.jpg)

Během instalace databáze týkající se virtuální počítač SAP HANA DB, při použití SAPinst (SWPM) a **typické** možnost instalace, všechno, co je nainstalované v /hana a /usr/sap. Výchozím umístěním pro zálohu protokolu SAP HANA je pod /usr/sap. Znovu protože je důležité, chcete-li zabránit nedostatku místa v úložišti kořenové systému souborů, ujistěte se, že není dostatek volného místa v rámci /hana a /usr/sap před instalací SAP HANA s využitím SWPM.

Popis rozložení standardní systém souborů systému SAP HANA, najdete v článku [Průvodce instalací serveru SAP HANA a aktualizací](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Další souborové systémy vytvořené na aplikační server SAP virtuálního počítače](./media/hana-get-started/image009.jpg)

Když nainstalujete SAP NetWeaver na standard SLES/SLES pro image z Galerie Azure 12 aplikací SAP, zobrazí se zpráva s upozorněním, že není žádná odkládacího souboru, jak je znázorněno na následujícím snímku obrazovky. Chcete zavřít tuto zprávu, můžete ručně přidat odkládací soubor pomocí **dd**, **mkswap**, a **swapon**. Další informace, jak vyhledat "Odkládacího souboru ruční přidávání" v [pomocí rozdělovače YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) část dokumentace SUSE.

Další možností je konfigurace velikosti odkládacího souboru pomocí agenta virtuálního počítače s Linuxem. Další informace najdete v tématu [uživatelská příručka agenta Azure Linux](../../extensions/agent-linux.md).

![Místní zpráva s informacemi, že je nedostatečné velikosti odkládacího souboru](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Soubor/etc/hosts
Před zahájením instalace SAP, ujistěte se, že obsahují názvy hostitele a IP adresy virtuálních počítačů SAP v souboru/etc/hosts. Nasazení všech virtuálních počítačů pro SAP v rámci jedné virtuální sítě Azure a pak použít interních IP adres, jak je znázorněno zde:

![Názvy hostitele a IP adresy virtuálních počítačů SAP uvedené v souboru/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Soubor/etc/fstab

Je vhodné přidat **nofail** parametr souboru fstab. Tímto způsobem, pokud dojde k nějaké chybě s disky, virtuální počítač není zablokování v procesu spouštění. Ale nezapomeňte, že nemusí být k dispozici další místo na disku a procesů může zaplnit kořenové systému souborů. Pokud chybí /hana, nespustí se SAP HANA.

![Přidání parametru nofail do souboru fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Na SLES 12/SLES pro SAP aplikace 12 grafický desktop GNOME
Tato část obsahuje následující témata:

* Instalace GNOME desktop a xrdp na SLES 12/SLES pro SAP aplikace 12
* Spuštění SAP MC založené na jazyce Java pomocí Firefoxu na SLES 12/SLES pro SAP aplikace 12

Můžete použít také alternativy, třeba Xterminal nebo VNC (není popsané v tomto průvodci).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalace GNOME desktop a xrdp na SLES 12/SLES pro SAP aplikace 12
Pokud máte Windows na pozadí, můžete použít grafické desktopu přímo v rámci virtuálních počítačů s Linuxem SAP snadno ke spuštění Firefox, SAPinst, grafické uživatelské rozhraní SAP, SAP MC nebo HANA Studio a připojit k virtuálnímu počítači prostřednictvím protokol RDP (Remote Desktop) z počítače s Windows. Závislé na zásady vaší společnosti o přidání grafického uživatelského rozhraní do produkčního prostředí a Linux li se o neprodukční systémech, můžete chtít nainstalovat GNOME na vašem serveru. Instalace GNOME desktop na Azure SLES 12/SLES pro SAP aplikace 12 virtuálních počítačů:

1. GNOME desktop nainstalujte tak, že zadáte následující příkaz (například v okně PuTTY):

   `zypper in -t pattern gnome-basic`

2. Nainstalujte xrdp umožňující připojení k virtuálnímu počítači přes protokol RDP:

   `zypper in xrdp`

3. Upravit /etc/sysconfig/windowmanager a nastavit výchozí Správce oken GNOME:

   `DEFAULT_WM="gnome"`

4. Spustit **chkconfig** abyste měli jistotu, že xrdp automaticky spustí po restartování:

   `chkconfig -level 3 xrdp on`

5. Pokud máte potíže s připojením RDP, zkuste restartovat (z PuTTY okna, například):

   `/etc/xrdp/xrdp.sh restart`

6. Pokud restartování xrdp uvedené v předchozím kroku nefunguje, zkontrolujte soubor .pid:

   `check /var/run` 

   Vyhledejte `xrdp.pid`. Pokud pro vás, odeberte ji a zkuste restartovat znovu.

### <a name="starting-sap-mc"></a>Spuštění SAP MC
Po instalaci GNOME desktop grafické MC SAP založené na jazyce Java od Firefox při spuštění do Azure SLES 12/SLES pro SAP aplikace 12 virtuální počítač se může zobrazit chyba z důvodu chybějící Java – prohlížeč modulu plug-in.

Adresa URL pro spuštění SAP MC je `<server>:5<instance_number>13`.

Další informace najdete v tématu [spuštění webové konzoly správy SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Na následujícím snímku obrazovky se zobrazí chybovou zprávu, která se zobrazí, když nebyl nalezen modul plug-in prohlížeče Java:

![Chybová zpráva indikující chybějící Java – modul plug-in prohlížeče](./media/hana-get-started/image013.jpg)

Jedním ze způsobů řešení problému je instalace chybějící modul plug-in pomocí YaST, jak je znázorněno na následujícím snímku obrazovky:

![Použití YaST k instalaci chybějícím zásuvným modulem](./media/hana-get-started/image014.jpg)

Když znovu zadáte adresu URL konzoly správy SAP, zobrazí se zpráva s výzvou k aktivaci modulu plug-in:

![Dialogové okno požadující aktivace modulu plug-in](./media/hana-get-started/image015.jpg)

Může také zobrazí chybová zpráva o chybí soubor javafx.properties. To se týká požadavek Oracle Java 1.8 7.4 grafickým uživatelským rozhraním SAP. (Viz [Poznámka SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) IBM Java verze ani balíček openjdk dodávají s SLES/SLES pro SAP aplikace 12 obsahuje potřebné javafx.properties soubor. Řešením je stáhnout a nainstalovat z Oracle Java SE 8.

Informace o k podobnému problému s openjdk na openSUSE najdete v tématu vlákno diskuse [SAPGui 7.4 Java pro openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Ruční instalace SAP Hana: SWPM
Řadu snímky obrazovky v této části ukazuje klíčové kroky pro instalaci SAP NetWeaver 7.5 a SAP HANA SP12 při použití SWPM (SAPinst). Jako součást instalace NetWeaver 7.5 SWPM také nainstalovat databázi HANA jako jedna instance.

V testovacím prostředí vzorku jsme nainstalovali jedním Advanced Business Application programování (ABAP) aplikačního serveru. Jak je znázorněno na následujícím snímku obrazovky jsme použili **distribuovaného systému** možnost instalace ASCS a instancí primární aplikačních serverů v jednom virtuálním počítači Azure a SAP HANA jako databázový systém na jiném virtuálním počítači Azure.

![ASCS a nainstalovali pomocí systému DFS instancí primární aplikačních serverů](./media/hana-get-started/image012.jpg)

Poté, co je nainstalována ASCS instance na serveru aplikace virtuálního počítače a je nastaven na "green" v konzole pro správu SAP (zobrazené na následujícím snímku obrazovky), musí být serverem databáze SAP HANA VM sdílené /sapmnt adresáři (včetně adresář profilu SAP). Krok instalace DB potřebuje přístup k těmto informacím. Nejlepší způsob, jak poskytnout přístup se má používat systém souborů NFS, kterého lze nakonfigurovat pomocí YaST.

![Konzola pro správu SAP zobrazující ASCS instance nainstalovaná na serveru aplikace virtuálních počítačů a nastavte na "green"](./media/hana-get-started/image016.jpg)

Na serveru aplikace virtuálního počítače musí /sapmnt directory sdílené úložiště přes systému souborů NFS pomocí **rw** a **no_root_squash** možnosti. Výchozí hodnoty jsou **ro** a **root_squash**, což může vést k problémům při instalaci instance databáze.

![Sdílení adresář /sapmnt prostřednictvím systému souborů NFS pomocí možnosti rw a no_root_squash](./media/hana-get-started/image017b.jpg)

Jak ukazuje následující snímek obrazovky, musí být nakonfigurovaná sdílená /sapmnt z aplikačního serveru virtuálního počítače na serveru databáze SAP HANA virtuálního počítače pomocí **klienta systému souborů NFS** (a YaST).

![Pomocí systému souborů NFS klienta byla nakonfigurována sdílená složka /sapmnt](./media/hana-get-started/image018b.jpg)

Instalace distribuované NetWeaver 7.5 (**Instance databáze**), jak je znázorněno na následujícím snímku obrazovky, přihlaste se k virtuálnímu počítači serverem databáze SAP HANA a spusťte SWPM.

![Přihlášení k serveru databáze SAP HANA VM a potom spustit SWPM nejprve nainstalovat instanci databáze](./media/hana-get-started/image019.jpg)

Po výběru **typické** instalace a cestu k instalačním médiu, zadejte DB SID, název hostitele, číslo instance a heslo správce systému databáze.

![SAP HANA database správce přihlašovací stránce systému](./media/hana-get-started/image035b.jpg)

Zadejte heslo pro schéma DBACOCKPIT:

![Do pole zadání hesla pro schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Zadejte otázku hesla SAPABAP1 schématu:

![Zadejte otázku hesla SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení každé úlohy se zobrazí zelená značka zaškrtnutí vedle jednotlivé fáze procesu instalace DB. Zpráva "spuštění aplikace... Zobrazí se dokončil v Instance databáze".

![Úloha dokončena okno s potvrzovací zpráva](./media/hana-get-started/image023.jpg)

Po úspěšné instalaci by měla konzolu pro správu SAP také zobrazit instanci databáze jako "green" a zobrazit seznam všech procesů SAP HANA (hdbindexserver hdbcompileserver a tak dále).

![Okno konzoly pro správu SAP s seznam procesů SAP HANA](./media/hana-get-started/image024.jpg)

Následující snímek obrazovky ukazuje části Struktura souborů v adresáři /hana/shared SWPM vytvořený během instalace HANA. Protože neexistuje žádná možnost zadejte jinou cestu, je potřeba připojit další místo na disku v adresáři /hana před instalací SAP HANA s využitím SWPM. To zabrání systému souborů kořenové vyčerpání volného místa.

![Struktura souborů directory /hana/shared vytvoří během instalace HANA](./media/hana-get-started/image025.jpg)

Tento snímek obrazovky znázorňuje strukturu souborů /usr/sap adresáře:

![/ Usr/sap souboru strukturu adresářů](./media/hana-get-started/image026.jpg)

Posledním krokem distribuované ABAP instalace je instalace primární aplikace instance serveru:

![Instance serveru ABAP instalace zobrazuje primární aplikace jako poslední krok](./media/hana-get-started/image027b.jpg)

Po instalaci instance primární aplikační server a SAP grafického uživatelského rozhraní, použijte **řídicí panel DBA** transakce na potvrzení, že byla správně dokončena instalace SAP HANA:

![Řídicí panel DBA okno potvrzení úspěšné instalace](./media/hana-get-started/image028b.jpg)

V posledním kroku může být chcete nainstalovat první HANA Studio v aplikační server SAP virtuálního počítače a pak se připojte k instanci SAP HANA, která běží na serveru databáze virtuálního počítače:

![Instalace SAP HANA Studio aplikační server SAP virtuálního počítače](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Ruční instalace SAP Hana: HDBLCM
Kromě instalace SAP HANA jako součást distribuované instalaci s použitím SWPM, můžete nainstalovat samostatnou HANA nejprve pomocí HDBLCM. Můžete nainstalovat SAP NetWeaver 7.5, například. Snímky obrazovky v této části ukazují, jak tento proces funguje.

Další informace o nástroji pro HANA HDBLCM najdete v tématu:

* [Výběr HDBLCM správné SAP HANA pro úkol](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Nástroje pro správu životního cyklu SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [Průvodce instalací serveru SAP HANA a aktualizací](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Pokud chcete zabránit potížím s výchozí nastavení ID skupiny pro `\<HANA SID\>adm user` (vytvořené nástrojem HDBLCM), definovat novou skupinu s názvem `sapsys` s použitím ID skupiny `1001` před instalací SAP HANA přes HDBLCM:

![Nová skupina "sapsys" definované pomocí skupiny ID 1001](./media/hana-get-started/image030.jpg)

Při prvním spuštění HDBLCM, zobrazí se nabídka start jednoduché. Vyberte položku 1, **instalace nového systému**, jak je znázorněno na následujícím snímku obrazovky:

![Možnost "Nainstalovat nový systém" v okně HDBLCM start](./media/hana-get-started/image031.jpg)

Na následujícím snímku obrazovky se zobrazí všechny klíčové možnosti, které jste vybrali dříve.

> [!IMPORTANT]
> Adresáře, které jsou pojmenovány pro HANA protokolu a datových svazků, stejně jako cestu instalace (/ hana/sdílené v této ukázce) a /usr/sap, neměl by být součástí kořenové systému souborů. Tyto adresáře patřit do Azure datové disky připojené k virtuálnímu počítači (popsaný v části "Nastavení disku"). Tento přístup pomáhá systém souborů kořenové Zabraňte jejich spuštění volné místo. Na následujícím snímku obrazovky vidíte, že správce systému HANA má ID uživatele `1005` a je součástí `sapsys` skupiny (ID `1001`), která byla definována před instalací.

![Seznam všechny klíčové komponenty SAP HANA vybrali dříve](./media/hana-get-started/image032.jpg)

Můžete zkontrolovat `\<HANA SID\>adm user` (`azdadm` na následujícím snímku obrazovky) podrobnosti v adresáři/etc/hesel:

![HANA \<HANA SID\>ADM – podrobnosti o uživateli na seznam v adresáři/etc/hesel](./media/hana-get-started/image033.jpg)

Po instalaci SAP HANA s využitím HDBLCM uvidíte struktura souborů v sadě Studio SAP HANA, jak je znázorněno na následujícím snímku obrazovky. SAPABAP1 schématu, která obsahuje všechny tabulky SAP NetWeaver, ještě není k dispozici.

![Struktura souborů SAP HANA v SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po instalaci SAP HANA, můžete nainstalovat SAP NetWeaver dojde k jeho zvýraznění. Jak je znázorněno na následujícím snímku obrazovky, instalace byla provedena jako distribuované instalaci s použitím SWPM (jak je popsáno v předchozí části). Při instalaci instance databáze s použitím SWPM zadejte stejná data s využitím HDBLCM (například název hostitele, identifikátor SID HANA a číslo instance). SWPM pak používá existující instalace HANA a přidá další schémata.

![Distribuované instalaci provést s použitím SWPM](./media/hana-get-started/image035b.jpg)

Následující snímek obrazovky ukazuje krok instalace SWPM, kde zadáte informace o schématu DBACOCKPIT:

![Krok instalace SWPM, pokud se zadá DBACOCKPIT schéma dat](./media/hana-get-started/image036b.jpg)

Zadejte informace o schématu SAPABAP1:

![Zadávání dat o SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení instalace SWPM databáze instance, můžete zobrazit schéma SAPABAP1 v SAP HANA Studio:

![Schéma SAPABAP1 v SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Nakonec po aplikační server SAP a SAP grafickým uživatelským rozhraním instalace se nedokončí, můžete ověřit instance databáze HANA pomocí **řídicí panel DBA** transakce:

![Instance databáze HANA ověřit s transakcí DBA řídicí panel](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Stahování softwaru SAP
Software můžete stáhnout z SAP Service Marketplace, jak je znázorněno na následujících snímcích obrazovky.

Stáhněte si NetWeaver 7.5 pro Linux/HANA:

 ![Instalace služby SAP a Upgrade okno pro stahování NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Stáhněte edici platformy HANA SP12:

 ![Instalace služby SAP a Upgrade okno pro stahování HANA SP12 platformy Edition](./media/hana-get-started/image002.jpg)

