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
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60204629"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Rychlý start: Ruční instalace jedné instance SAP HANA v Azure Virtual Machines
## <a name="introduction"></a>Úvod
Tento průvodce vám pomůže nastavit jednou instancí SAP HANA v Azure Virtual Machines při instalaci SAP NetWeaver 7.5 a SAP HANA 1.0 SP12 ručně. Sada je zaměřena tak tato příručka pro nasazení SAP HANA v Azure. Nenahrazuje dokumentaci k SAPU. 

> [!NOTE]
> Tato příručka popisuje nasazení SAP HANA na virtuálních počítačích Azure. Informace o tom, jak nasadit SAP HANA na velkých instancích HANA, naleznete v tématu [použití SAP na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Požadavky
Tento průvodce to předpokládá, že jste obeznámeni s takovou infrastrukturu jako službu (IaaS) základy jako:
 * Jak nasadit virtuální počítače (VM) nebo virtuální sítě pomocí webu Azure portal nebo Powershellu.
 * Multiplatformní rozhraní příkazového řádku Azure (CLI), která zahrnuje možnost používat šablony JavaScript Object Notation (JSON).

Tento průvodce to předpokládá také, že už znáte:
* SAP HANA a SAP NetWeaver a způsobu jejich instalace v místním prostředí.
* Jak nainstalovat a provozovat SAP HANA a instancemi aplikací SAP v Azure.
* Následující koncepty a postupy:
   * Plánování pro nasazení SAP v Azure, která zahrnuje plánování virtuální sítě Azure a použití služby Azure Storage. Zobrazit [SAP NetWeaver na virtuálních počítačích Azure – Příručka pro plánování a implementaci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Nasazení zásad a způsoby, jak nasadit virtuální počítače v Azure. Zobrazit [nasazení Azure Virtual Machines pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Vysoká dostupnost pro SAP NetWeaver ABAP SAP Central Services ASCS, SAP Central Services (SCS) a zařazení do fronty replikace serveru (Lajících) v Azure. Zobrazit [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Podrobnosti o tom, jak zvýšit efektivitu při instalaci několika identifikátorů SID ASCS/SCS v Azure. Zobrazit [vytvoření konfigurace několika identifikátorů SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principy používání systému SAP NetWeaver založené na Linuxu řízené virtuální počítače v Azure. Zobrazit [běží SAP NetWeaver na virtuálních počítačích Microsoft Azure operačním systémem SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Tato příručka obsahuje konkrétní nastavení pro Linux ve virtuálních počítačích Azure. Poskytuje informace o tom, jak správně připojení služby Azure storage disků pro virtuální počítače s Linuxem.

Typy virtuálních počítačů Azure, které lze použít pro produkční scénáře jsou uvedeny v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). U scénářů s neprovozním širší nativní typy virtuálních počítačů Azure je k dispozici.
Další informace o konfiguraci virtuálních počítačů a operací, najdete v části [konfigurace infrastruktury SAP HANA a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Pro zajištění vysoké dostupnosti SAP HANA, naleznete v tématu [vysoké dostupnosti SAP HANA pro Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Pokud chcete získat instance SAP HANA nebo S/4HANA nebo BW/4HANA systému rychle nasadit, zvažte použití [SAP Cloud Appliance Library](https://cal.sap.com). Můžete najít dokumentaci o tom, jak nasadit systém S/4HANA přes SAP Cloud Appliance Library v Azure, například v [Tato příručka](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Je vše, co potřebujete předplatné Azure a SAP uživatele, který lze dokument zaregistrovat u SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Další materiály
### <a name="sap-hana-backup"></a>Zálohování SAP HANA
Informace o tom, jak zálohovat databáze SAP HANA na virtuálních počítačích Azure najdete tady:
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Zálohování SAP HANA založené na snímcích úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Informace o tom, jak použít k nasazení S/4HANA nebo BW/4HANA, SAP Cloud Appliance Library najdete v tématu [nasadit řešení SAP S/4HANA nebo BW/4HANA v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA podporované operační systémy
Informace o SAP HANA podporované operační systémy, najdete v části [2235581 Poznámka SAP - SAP HANA: Podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuální počítače Azure podporují pouze podmnožinu těchto operačních systémů. Následující operační systémy jsou podporovány pro nasazení SAP HANA v Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Další dokumentaci k SAP o SAP HANA a různých operačních systémech Linux najdete v článku:

* [Poznámka SAP 171356: Softwaru SAP v Linuxu: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787).
* [Poznámka SAP 1944799: SAP HANA pokyny pro instalaci operačního systému SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [Poznámka SAP 2205917: Databáze SAP HANA doporučené nastavení operačního systému pro SLES 12 pro aplikace SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [Poznámka SAP 1391070: Řešení pro Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
* [Poznámka SAP 2009879: Pokyny pro SAP HANA pro operační systém Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [Poznámka SAP 2292690: DATABÁZE SAP HANA: Doporučené nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP monitorování v Azure
Další informace o monitorování SAP v Azure:

* [SAP Poznámka 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) popisuje SAP enhanced monitoring s Linuxovými virtuálními počítači v Azure. 
* [SAP Poznámka 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) popisuje informace o SAPOSCOL v Linuxu. 
* [SAP Poznámka 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) popisuje klíčové metriky pro monitorování pro SAP v Microsoft Azure.

### <a name="azure-vm-types"></a>Azure typy virtuálních počítačů
Azure typy virtuálních počítačů a scénáře podporované SAP úloh používá se SAP HANA, které jsou popsány v [SAP certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure typy virtuálních počítačů, které jsou certifikované SAP pro SAP NetWeaver a S/4HANA aplikační vrstvy jsou dokumentovány v článku [SAP 1928533 Poznámka: Aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> Integrace se službou SAP-Linux-Azure je podporována pouze na Azure Resource Manageru a modelu nasazení classic. 

## <a name="manual-installation-of-sap-hana"></a>Ruční instalace SAP HANA

> [!IMPORTANT]
> Ujistěte se, že je operační systém, vyberete na konkrétní typy virtuálních počítačů, které můžete použít pro SAP HANA s certifikací SAP. Seznam SAP HANA certified typy virtuálních počítačů a operačního systému, aktualizací pro tyto typy virtuálních počítačů lze vyhledávat [platformách IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Nezapomeňte kliknout na podrobné informace o typu virtuálního počítače uvedené získat úplný seznam všech verzí operačního systému SAP HANA nepodporuje pro konkrétní typ virtuálního počítače. Například v tomto dokumentu jsme použili verze operačního systému SUSE Linux Enterprise Server (SLES) operačního systému, který není podporován SAP pro SAP HANA na virtuálních počítačích řady M-series.
>

Tato příručka popisuje, jak ručně nainstalovat SAP HANA na virtuálních počítačích Azure dvěma různými způsoby:

* Použití SAP Software zřizování správce (SWPM) jako součást distribuované instalaci NetWeaver v kroku "instance instalace databáze".
* Pomocí nástroje Správce (HDBLCM) životního cyklu databáze SAP HANA a pak nainstalujte NetWeaver.

Také vám pomůže SWPM nainstalujte všechny komponenty, jako je SAP HANA, aplikační server SAP a ASCS instance v jedné jeden virtuální počítač. Kroky jsou popsané v tomto [oznámení na blogu SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Tato možnost není popsané v této úvodní příručky, ale problémy, které musíte zvážit, jsou stejné.

Než začnete instalací, doporučujeme, abyste si přečetli "Příprava virtuálních počítačů Azure pro ruční instalaci sady SAP HANA" dále v tomto průvodci. To pomáhá zabránit několika základní chyby, které mohou nastat při použití pouze výchozí konfigurace virtuálního počítače Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Základní kroky pro instalaci SAP HANA při použití SAP SWPM
Tato část uvádí základní kroky pro ruční instalace SAP HANA jednou instancí, použijete-li provést distribuované instalaci SAP NetWeaver 7.5 SAP SWPM. Jednotlivé kroky jsou vysvětlené podrobněji na snímcích obrazovky dál v této příručce.

1. Vytvořte virtuální síť Azure, která obsahuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy podle modelu Azure Resource Manageru. Tento příklad používá operačním systémem SUSE Linux Enterprise Server a SLES pro SAP aplikace 12 SP1. 
3. Připojte dva Azure standard nebo disky storage úrovně premium, například 75 GB a 500 GB disky, k aplikačnímu serveru virtuálního počítače.
4. Disky premium storage se připojte k serveru databáze HANA virtuálního počítače. Další informace najdete v části "Nastavení disku" dál v této příručce.
5. V závislosti na požadavcích velikost nebo propustnost připojte několik disků. Pak vytvořte prokládané svazky. Správa logických svazků (LVM) nebo nástroje pro správu (mdadm) více zařízení pomocí na úrovni operačního systému ve virtuálním počítači.
6. Vytvoření XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Pomocí jednoho systému souborů pro veškerého softwaru SAP. Používejte jiné systém souborů k adresáři /sapmnt a zálohy, třeba. Na serveru databáze SAP HANA připojte na disky premium storage jako /hana a /usr/sap XFS systémy souborů. Tento proces je nutné zabránit nezaplňuje kořenové systému souborů. Systém souborů root není velké na virtuálních počítačích Azure s Linuxem. 
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** parametru v souboru/etc/fstab.
10. Nastavte parametry jádro Linuxu podle verze operačního systému Linux, které používáte. Další informace najdete v tématu poznámky SAP, která popisují HANA a v části "Jádra parametrů" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické desktopu testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stahování softwaru SAP SAP Service Marketplace.
14. Nainstalujte instanci SAP ASCS na serveru aplikace virtuálního počítače.
15. Sdílení adresář /sapmnt mezi testovací virtuální počítače s použitím systému souborů NFS. Aplikační server virtuálního počítače je na serveru NFS.
16. Nainstalujte instanci databáze, která zahrnuje HANA, a to s využitím SWPM na serveru databáze virtuálního počítače.
17. Instalace serveru primární aplikace (Pa adresy) na aplikačním serveru virtuálního počítače.
18. Spusťte konzolu pro správu SAP (SAP MC). Spojte se s grafickým uživatelským rozhraním SAP nebo HANA Studio, například.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Základní kroky pro instalaci SAP HANA při použití HDBLCM
Tato část uvádí základní kroky pro ruční instalace SAP HANA jednou instancí, použijete-li provést distribuované instalaci SAP NetWeaver 7.5 SAP HDBLCM. Jednotlivé kroky jsou vysvětlené podrobněji snímky obrazovky v této příručce.

1. Vytvořte virtuální síť Azure, která obsahuje dvě testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy podle modelu Azure Resource Manageru. Tento příklad používá SLES a SLES pro SAP aplikace 12 SP1.
3. Připojte dva Azure standard nebo disky storage úrovně premium, například 75 GB a 500 GB disky, virtuální počítač na aplikační server.
4. Disky premium storage se připojte k serveru databáze HANA virtuálního počítače. Další informace najdete v části "Nastavení disku" dál v této příručce.
5. V závislosti na požadavcích velikost nebo propustnost připojte několik disků. Vytvořte prokládané svazky s využitím Správa logických svazků nebo mdadm nástroj na úrovni operačního systému ve virtuálním počítači.
6. Vytvoření XFS souborové systémy připojené disky nebo logické svazky.
7. Připojte nový systémy souborů XFS na úrovni operačního systému. Pomocí jednoho systému souborů pro veškerého softwaru SAP. Používejte jiné systém souborů k adresáři /sapmnt a zálohy, třeba. Na serveru databáze SAP HANA připojte na disky premium storage jako /hana a /usr/sap XFS systémy souborů. Tento proces je nutné zabránit nezaplňuje kořenové systému souborů. Systém souborů root není velké na virtuálních počítačích Azure s Linuxem.
8. Zadejte místní IP adresy testovací virtuální počítače v souboru/etc/hosts.
9. Zadejte **nofail** parametru v souboru/etc/fstab.
10. Nastavit parametry jádra podle verze operačního systému Linux, které používáte. Další informace najdete v tématu poznámky SAP, která popisují HANA a v části "Jádra parametrů" v této příručce.
11. Zvětšete odkládací soubor.
12. Volitelně můžete nainstalujte grafické desktopu testovací virtuální počítače. Jinak použijte vzdálenou instalaci SAPinst.
13. Stahování softwaru SAP SAP Service Marketplace.
14. Vytvořte skupinu sapsys, s ID 1001 skupiny na serveru databáze HANA VM.
15. Instalace SAP HANA na serveru databáze virtuálního počítače pomocí Správce životního cyklu databáze HANA.
16. Nainstalujte instanci SAP ASCS na serveru aplikace virtuálního počítače.
17. Sdílení adresář /sapmnt mezi testovací virtuální počítače s použitím systému souborů NFS. Aplikační server virtuálního počítače je na serveru NFS.
18. Nainstalujte instanci databáze, která zahrnuje HANA, a to s využitím SWPM na serveru databáze HANA virtuálního počítače.
19. Nainstalujte primárním aplikačním serveru na aplikační server virtuálního počítače.
20. Spuštění SAP MC. Připojení přes grafické uživatelské rozhraní SAP nebo HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Příprava virtuálních počítačů Azure pro ruční instalaci sady SAP HANA
Tato část obsahuje následující témata:

* Aktualizace operačního systému
* Nastavení disku
* Parametry jádra
* Systémy souborů
* Soubor/etc/hosts
* Soubor/etc/fstab

### <a name="os-updates"></a>Aktualizace operačního systému
Zkontrolujte operační systém Linux aktualizace a opravy, před instalací dalšího softwaru. Instalací opravy, se můžete vyhnout volání číslo technické podpory.

Ujistěte se, že používáte:
* SUSE Linux Enterprise Server pro aplikace SAP.
* Red Hat Enterprise Linux pro SAP aplikace nebo systému Red Hat Enterprise Linux for SAP HANA. 

Pokud jste to ještě neudělali, zaregistrujte nasazení operačního systému ve vašem předplatném Linux od dodavatele Linux. SUSE má Image operačních systémů pro aplikace SAP, které už zahrnují služby a které jsou registrovány automaticky.

Tady je příklad toho, jak vyhledat dostupné opravy operačního systému SUSE Linux s použitím **zypperu** příkaz:

 `sudo zypper list-patches`

V závislosti na druhu problém opravy dělí podle kategorie a závažnost. Kategorie běžně používané hodnoty jsou: 
- Zabezpečení
- Doporučené
- Nepovinné
- Funkce
- Dokument
- yast

Běžně používané hodnoty závažnosti jsou:

- Kritická
- Důležité
- Střední
- Nízká
- Tento parametr zadán

**Zypperu** příkaz by mohl vypadat pouze pro aktualizace, které je třeba nainstalované balíčky. Například můžete použít tento příkaz:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Můžete přidat parametr `--dry-run` otestovat aktualizaci bez skutečně aktualizace systému.


### <a name="disk-setup"></a>Nastavení disku
Kořenové systému souborů do virtuálního počítače s Linuxem v Azure má omezení velikosti. Ano budete muset připojit další místo na disku k virtuálnímu počítači Azure pro spuštění SAP. Pro aplikační server SAP virtuální počítače Azure, disky Azure standard storage je možné použít dostačující. Pro SAP HANA DBMS virtuální počítače Azure použijte disky Azure premium storage pro produkční a úspoře implementace je povinný.

Na základě [požadavky na úložiště SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), navrhne se následující konfigurace Azure premium storage: 

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM |  / hana/dat a/hana/log <br /> prokládané s LVM nebo mdadm | / hana/sdílené | / root svazku | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

V konfiguraci navrhované disku objemu dat HANA a svazek s protokolem umísťují na stejnou sadu disky Azure premium storage, které jsou rozložené s LVM nebo mdadm. Není nutné definovat všechny úrovně redundance diskového pole RAID, protože úložiště Azure premium storage udržuje tři imagí disků pro zajištění redundance. 

Chcete-li mít jistotu, že nakonfigurujete dostatečně velké úložiště, přečtěte si téma [požadavky na úložiště SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) a [Průvodce instalace a aktualizace serveru SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Zvažte také jiný virtuální pevný disk (VHD) propustnost objem disky různých Azure premium storage, jak je uvedeno v [vysoce výkonné úložiště úrovně premium a spravovaným diskům pro virtuální počítače](../../windows/disks-types.md). 

Můžete přidat další disky storage úrovně premium pro virtuální počítače HANA DBMS pro ukládání záloh databáze nebo transakčního protokolu.

Další informace o dva hlavní nástroje použít ke konfiguraci prokládání naleznete v tématu:

* [Konfigurace softwarového pole RAID v Linuxu](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Konfigurace LVM na virtuální počítač s Linuxem v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Další informace o tom, jak připojit disků na virtuální počítače Azure, který běží Linux jako hostovaný operační systém najdete v tématu [přidání disku do virtuálního počítače s Linuxem](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

S SSD disky Azure úrovně premium můžete definovat režimy používání mezipaměti disku. Prokládané sady, která obsahuje /hana/data a /hana/log Zakázání používání mezipaměti disku. Pro jiné svazky, to znamená, disky, nastavte režim ukládání do mezipaměti na **jen pro čtení**.

Ukázkové šablony JSON použít k vytvoření virtuálních počítačů najdete v tématu [šablony rychlý start Azure](https://github.com/Azure/azure-quickstart-templates).
Šablona virtuálního počítače. jednoduchý sles je základní šablony. Oddíl úložiště s diskem dalších 100 GB dat zahrnuje. Tuto šablonu použijte jako základ. Šablony můžete přizpůsobit na konkrétní konfiguraci.

> [!NOTE]
> Je potřeba připojit disk úložiště Azure s použitím UUID, jak je uvedeno v [spuštění SAP NetWeaver na virtuálních počítačích Microsoft Azure operačním systémem SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V testovacím prostředí jsou připojeny disky dvou úložiště Azure úrovně standard na aplikační server SAP virtuálního počítače, jak je znázorněno na následujícím snímku obrazovky. Jeden disk ukládá všechny SAP software, jako je například NetWeaver 7.5, grafickým uživatelským rozhraním SAP a SAP HANA, pro instalaci. Druhý disk se zajistí, že je dostatek volného místa k dispozici pro další požadavky. Například zálohování a testovací data a adresáři /sapmnt tj, SAP profily, potřeba sdílet mezi všechny virtuální počítače, které patří do stejného prostředí SAP.

![Aplikační server SAP HANA disky okno zobrazující dva datové disky a jejich velikosti](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jádra
SAP HANA vyžaduje konkrétní nastavení jádro Linuxu. Tato nastavení jádra nejsou součástí standardní Azure Galerie imagí a musí být nastaven na ručně. V závislosti na tom, ať už používáte, SUSE nebo Red Hat může být jiné parametry. Poznámky SAP uvedené dříve poskytnout informace o těchto parametrů. Na snímcích obrazovky ukazuje byl použit operačním systémem SUSE Linux 12 SP1. 

SLES pro obecnou dostupnost 12 aplikací SAP a SLES pro SAP aplikace 12 SP1 mají nový nástroj **vyladěný adm**, který nahrazuje původní **sapconf** nástroj. Speciální profil SAP HANA je k dispozici pro **vyladěný adm**. Pokud chcete optimalizovat systém pro SAP HANA, zadejte jako uživatel root následující profil:

   `tuned-adm profile sap-hana`

Další informace o **vyladěný adm**, najdete v článku [SUSE dokumentaci k vyladění adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na následujícím snímku obrazovky vidíte jak **vyladěný adm** změnit `transparent_hugepage` a `numa_balancing` hodnoty, podle toho, požadovaná nastavení SAP HANA:

![Nástroj vyladěný adm změní hodnoty podle požadované nastavení SAP HANA](./media/hana-get-started/image005.jpg)

Pokud chcete trvalé nastavení jádra SAP HANA, použijte **grub2** na SLES 12. Další informace o **grub2**, najdete v článku [struktura konfiguračních souborů](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) část dokumentace SUSE.

Následující snímek obrazovky ukazuje, jak nastavení jádra byly změněny v konfiguračním souboru a poté zkompilován s použitím **grub2 mkconfig**:

![Nastavení jádra změnil v konfiguračním souboru a zkompilován s použitím grub2 mkconfig](./media/hana-get-started/image006.jpg)

Další možností je toto nastavení změnit pomocí YaST a **spouštěcí zavaděč** > **jádra parametry** nastavení:

![Na kartě Nastavení parametrů jádra v YaST spouštěcí zavaděč](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systémy souborů
Následující snímek obrazovky ukazuje dva systémy souborů, které byly vytvořeny na aplikační server SAP virtuálního počítače na dvou discích připojených úložiště Azure úrovně standard. Oba systémy souborů jsou typu XFS a jsou připojené k /sapdata a /sapsoftware.

Není nutné strukturovat vaše systémy souborů tímto způsobem. Máte další možnosti pro Jak strukturovat místo na disku. Nejdůležitější aspektem je kořenový soubor systém zabránit nedostatku volného místa.

![Dva systémy souborů, které jsou vytvořené na aplikační server SAP virtuálního počítače](./media/hana-get-started/image008.jpg)

Pro SAP HANA DB VM během instalace databáze, pokud používáte SAPinst se SWPM a **typické** možnost instalace, všechno, co je nainstalované v /hana a /usr/sap. Výchozím umístěním pro zálohu protokolu SAP HANA je pod /usr/sap. Znovu je důležité zabránit nedostatku místa v úložišti kořenové systému souborů. Ujistěte se, že má dostatek volného místa v rámci /hana a /usr/sap před instalací SAP HANA s využitím SWPM.

Popis rozložení standardní systém souborů systému SAP HANA, najdete v článku [Průvodce instalace a aktualizace serveru SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Další souborové systémy vytvořené na aplikační server SAP virtuálního počítače](./media/hana-get-started/image009.jpg)

Když nainstalujete SAP NetWeaver na standard SLES/SLES pro image z Galerie Azure 12 aplikací SAP, zobrazí se zpráva s upozorněním, že není žádná odkládacího souboru, jak je znázorněno na následujícím snímku obrazovky. Chcete zavřít tuto zprávu, můžete ručně přidat odkládací soubor pomocí **dd**, **mkswap**, a **swapon**. Další informace, jak vyhledat "Odkládacího souboru ruční přidávání" v [pomocí rozdělovače YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) část dokumentace SUSE.

Další možností je konfigurace velikosti odkládacího souboru pomocí agenta virtuálního počítače s Linuxem. Další informace najdete v [uživatelské příručce agenta Azure Linux](../../extensions/agent-linux.md).

![Místní zpráva s informacemi, že je nedostatečné velikosti odkládacího souboru](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Soubor/etc/hosts
Před zahájením instalace SAP, ujistěte se, že obsahují názvy hostitele a IP adresy virtuálních počítačů SAP v souboru/etc/hosts. Nasaďte všechny virtuální počítače pro SAP v rámci jedné virtuální sítě Azure. Potom použijte interních IP adres, jak je znázorněno zde:

![Názvy hostitele a IP adresy virtuálních počítačů SAP uvedené v souboru/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Soubor/etc/fstab

Je vhodné přidat **nofail** parametr souboru fstab. Tímto způsobem, pokud dojde k nějaké chybě s disky, virtuální počítač nebude přestane reagovat v procesu spouštění. Ale nezapomeňte, že nemusí být k dispozici další místo na disku a procesů může zaplnit kořenové systému souborů. Pokud chybí /hana, nespustí se SAP HANA.

![Přidání parametru nofail do souboru fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Na SLES 12/SLES pro SAP aplikace 12 grafický desktop GNOME
Tato část vysvětluje, jak:

* Instalace GNOME desktop a xrdp na SLES 12/SLES 12 aplikací SAP.
* Spuštění SAP MC založené na jazyce Java pomocí Firefoxu na SLES 12/SLES 12 aplikací SAP.

Můžete použít také alternativní databáze, třeba Xterminal nebo VNC, které nebyly popsány v této příručce.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalace GNOME desktop a xrdp na SLES 12/SLES pro SAP aplikace 12
Pokud máte Windows na pozadí, snadno můžete grafické desktopu přímo v rámci SAP virtuálních počítačů s Linuxem ke spouštění Firefox, SAPinst, grafické uživatelské rozhraní SAP, SAP MC nebo HANA Studio. Pak můžete připojit k virtuálnímu počítači prostřednictvím protokol RDP (Remote Desktop) z počítače Windows. V závislosti na zásady vaší společnosti o přidání GUI do produkčního prostředí a úspoře systémů založených na Linuxu můžete chtít nainstalovat GNOME na vašem serveru. Postupujte podle těchto kroků nainstalujte GNOME desktop na Azure SLES 12/SLES pro SAP aplikace 12 virtuálních počítačů.

1. GNOME desktop nainstalujte tak, že zadáte následující příkaz, například PuTTY okno:

   `zypper in -t pattern gnome-basic`

2. Nainstalujte xrdp umožňující připojení k virtuálnímu počítači přes protokol RDP:

   `zypper in xrdp`

3. Upravit /etc/sysconfig/windowmanager a nastavit výchozí Správce oken GNOME:

   `DEFAULT_WM="gnome"`

4. Spustit **chkconfig** abyste měli jistotu, že xrdp automaticky spustí po restartování:

   `chkconfig -level 3 xrdp on`

5. Pokud máte potíže s připojením RDP, pokuste se restartovat, například PuTTY okno:

   `/etc/xrdp/xrdp.sh restart`

6. Pokud restartování xrdp uvedené v předchozím kroku nefunguje, zkontrolujte soubor .pid:

   `check /var/run` 

   Vyhledejte `xrdp.pid`. Pokud pro vás, odeberte ji a zkuste restartovat znovu.

### <a name="start-sap-mc"></a>Spuštění SAP MC
Po instalaci GNOME desktop spuštění grafického MC SAP založené na jazyce Java z Firefox. Pokud poběží v Azure SLES 12/SLES pro SAP aplikace 12 virtuálních počítačů, může se zobrazit chyba. Z důvodu chybějící modul plug-in prohlížeče Java dojde k chybě.

Adresa URL pro spuštění SAP MC je `<server>:5<instance_number>13`.

Další informace najdete v tématu [spuštění webové konzoly správy SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Na následujícím snímku obrazovky se zobrazí chybová zpráva, která se zobrazí, když se modul plug-in prohlížeče Java chybí:

![Chybová zpráva indikující chybějící Java – modul plug-in prohlížeče](./media/hana-get-started/image013.jpg)

Jedním ze způsobů řešení problému je instalace chybějící modul plug-in pomocí YaST, jak je znázorněno na následujícím snímku obrazovky:

![Použití YaST k instalaci chybějícím zásuvným modulem](./media/hana-get-started/image014.jpg)

Pokud jste znovu zadat adresu URL konzoly správy SAP, budete vyzváni k aktivaci modulu plug-in:

![Dialogové okno požadující aktivace modulu plug-in](./media/hana-get-started/image015.jpg)

Může také zobrazí chybová zpráva o chybí soubor javafx.properties. Souvisí s požadavkem Oracle Java 1.8 pro SAP 7.4 grafického uživatelského rozhraní. Další informace najdete v tématu [2059429 Poznámka SAP](https://launchpad.support.sap.com/#/notes/2059424).
IBM Java verze a openjdk balíček, který se dodává se SLES nebo SLES 12 aplikací SAP neobsahují potřebné javafx.properties souboru. Řešením je stáhnout a nainstalovat z Oracle Java SE 8.

Informace o k podobnému problému s openjdk na openSUSE najdete v tématu vlákno diskuse [SAPGui 7.4 Java pro openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Ruční instalace SAP Hana: SWPM
Řadu snímky obrazovky v této části ukazuje klíčové kroky pro instalaci SAP NetWeaver 7.5 a SAP HANA SP12 používání SWPM s SAPinst. Jako součást instalace NetWeaver 7.5 SWPM také můžete nainstalovat databázi HANA jako jedna instance.

V testovacím prostředí vzorku jsme nainstalovali jeden aplikační server Advanced Business Application programování (ABAP). Jak je znázorněno na následujícím snímku obrazovky jsme použili **distribuovaného systému** možnost instalace ASCS a instancí primární aplikačních serverů v jeden virtuální počítač Azure. SAP HANA jsme použili jako databázový systém na jiném virtuálním počítači Azure.

![ASCS a nainstalovali pomocí systému DFS instancí primární aplikačních serverů](./media/hana-get-started/image012.jpg)

Po instalaci ASCS instance na serveru aplikace virtuálního počítače je identifikován zelená ikona v konzole pro správu SAP. /Sapmnt directory, která obsahuje adresář profilu SAP, je potřeba sdílet s server databáze SAP HANA virtuálního počítače. Krok instalace DB potřebuje přístup k těmto informacím. Nejlepší způsob, jak poskytnout přístup se má používat systém souborů NFS, kterého lze nakonfigurovat pomocí YaST.

![Konzola pro správu SAP ASCS instance nainstalována na serveru aplikace virtuálního počítače pomocí zelená ikona znázorňující](./media/hana-get-started/image016.jpg)

Na virtuální počítač serveru aplikace adresář /sapmnt sdílené úložiště přes systém souborů NFS pomocí **rw** a **no_root_squash** možnosti. Výchozí hodnoty jsou **ro** a **root_squash**, což může vést k problémům při instalaci instance databáze.

![Sdílení adresář /sapmnt prostřednictvím systému souborů NFS pomocí možnosti rw a no_root_squash](./media/hana-get-started/image017b.jpg)

Jak ukazuje následující snímek obrazovky, musí být nakonfigurovaná sdílená /sapmnt z aplikačního serveru virtuálního počítače na serveru databáze SAP HANA virtuálního počítače pomocí **klienta systému souborů NFS** a YaST:

![Pomocí systému souborů NFS klienta byla nakonfigurována sdílená složka /sapmnt](./media/hana-get-started/image018b.jpg)

Instalace distribuované NetWeaver 7.5, to znamená, **Instance databáze**, přihlaste se k serveru databáze SAP HANA virtuální počítač a spusťte SWPM:

![Přihlášení k serveru databáze SAP HANA VM a potom spustit SWPM nejprve nainstalovat instanci databáze](./media/hana-get-started/image019.jpg)

Po výběru **typické** instalace a cestu k instalačním médiu, zadejte DB SID, název hostitele, číslo instance a heslo správce databáze systému:

![SAP HANA database správce přihlašovací stránce systému](./media/hana-get-started/image035b.jpg)

Zadejte heslo pro schéma DBACOCKPIT:

![Do pole zadání hesla pro schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Zadejte otázku hesla SAPABAP1 schématu:

![Zadejte otázku hesla SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení každé úlohy se zobrazí zelená značka zaškrtnutí vedle jednotlivé fáze procesu instalace DB. Zpráva "spuštění aplikace... Zobrazí se dokončil v Instance databáze".

![Úloha dokončena okno s potvrzovací zpráva](./media/hana-get-started/image023.jpg)

Po úspěšné instalaci konzoly pro správu SAP také ukazuje instanci databáze s ikonou zelené. Zobrazí seznam všech procesů SAP HANA, jako je například hdbindexserver a hdbcompileserver.

![Okno konzoly pro správu SAP s seznam procesů SAP HANA](./media/hana-get-started/image024.jpg)

Následující snímek obrazovky ukazuje části Struktura souborů v adresáři /hana/shared SWPM vytvořený během instalace HANA. Protože neexistuje žádná možnost zadejte jinou cestu, je potřeba připojit další místo na disku v adresáři /hana před instalací SAP HANA s využitím SWPM. Tento krok zabrání systému souborů kořenové nedostatku volného místa.

![Struktura souborů directory /hana/shared vytvoří během instalace HANA](./media/hana-get-started/image025.jpg)

Tento snímek obrazovky znázorňuje strukturu souborů /usr/sap adresáře:

![/ Usr/sap souboru strukturu adresářů](./media/hana-get-started/image026.jpg)

Posledním krokem distribuované ABAP instalace je instalace primární aplikace instance serveru:

![Instance serveru ABAP instalace zobrazuje primární aplikace jako poslední krok](./media/hana-get-started/image027b.jpg)

Po instalaci instance primární aplikační server a SAP grafického uživatelského rozhraní, použijte **řídicí panel DBA** transakce na potvrzení správně dokončení instalace SAP HANA:

![Řídicí panel DBA okno potvrzení úspěšné instalace](./media/hana-get-started/image028b.jpg)

V posledním kroku může být vhodné, abyste nejdřív nainstalovali HANA Studio v aplikační server SAP virtuálního počítače. Připojte se k instanci SAP HANA, která běží na serveru databáze virtuálního počítače.

![Instalace SAP HANA Studio aplikační server SAP virtuálního počítače](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Ruční instalace SAP Hana: HDBLCM
Kromě instalace SAP HANA jako součást distribuované instalaci s použitím SWPM, můžete nainstalovat samostatnou HANA nejprve pomocí HDBLCM. Můžete nainstalovat SAP NetWeaver 7.5, například. Snímky obrazovky v této části ukazují, jak tento proces funguje.

Další informace o nástroji pro HANA HDBLCM najdete v tématu:

* [Zvolte správný HDBLCM SAP HANA pro úkol](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Nástroje pro správu životního cyklu SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Příručka pro instalaci a aktualizace serveru SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Chcete se vyhnout problémům s výchozí nastavení ID skupiny pro `\<HANA SID\>adm user`, který je vytvořen nástrojem HDBLCM. Před instalací SAP HANA přes HDBLCM definovat novou skupinu s názvem `sapsys` s použitím ID skupiny `1001`:

![Nová skupina "sapsys" definované pomocí skupiny ID 1001](./media/hana-get-started/image030.jpg)

Při prvním spuštění HDBLCM spustit jednoduchý nabídky zobrazí. Vyberte položku 1, **instalace nového systému**:

![Možnost "Nainstalovat nový systém" v okně HDBLCM start](./media/hana-get-started/image031.jpg)

Na následujícím snímku obrazovky se zobrazí všechny klíčové možnosti, které jste vybrali dříve.

> [!IMPORTANT]
> Adresáře, které jsou pojmenovány protokolu HANA a datové svazky a instalační cestu, která je v této ukázkové a /usr/sap /hana/shared nesmí být součástí systému kořenový soubor. Tyto adresáře patřit do Azure datové disky připojené k virtuálnímu počítači. Další informace najdete v části "Nastavení disku". 

Tento přístup pomáhá systém souborů kořenové Zabraňte jejich spuštění volné místo. Všimněte si, že správce systému HANA má ID uživatele `1005` a je součástí `sapsys` skupina s ID `1001`, která byla definována před instalací.

![Seznam všechny klíčové komponenty SAP HANA vybrali dříve](./media/hana-get-started/image032.jpg)

Zkontrolujte, `\<HANA SID\>adm user` podrobnosti v adresáři/etc/hesel. Vyhledejte `azdadm`, jak je znázorněno na následujícím snímku obrazovky:

![HANA \<HANA SID\>ADM – podrobnosti o uživateli na seznam v adresáři/etc/hesel](./media/hana-get-started/image033.jpg)

Po instalaci SAP HANA s využitím HDBLCM uvidíte struktura souborů v sadě Studio SAP HANA, jak je znázorněno na následujícím snímku obrazovky. SAPABAP1 schématu, která obsahuje všechny tabulky SAP NetWeaver, ještě není k dispozici.

![Struktura souborů SAP HANA v SAP HANA Studio](./media/hana-get-started/image034.jpg)

Po instalaci SAP HANA, můžete nainstalovat SAP NetWeaver dojde k jeho zvýraznění. Jak je znázorněno na následujícím snímku obrazovky, instalace byla provedena jako distribuované instalaci s použitím SWPM. Tento proces je popsaný v předchozí části. Při instalaci instance databáze s použitím SWPM zadejte pomocí HDBLCM stejná data. Například zadejte název hostitele, identifikátor SID HANA a číslo instance. SWPM pak používá existující instalace HANA a přidá další schémata.

![Distribuované instalaci provést s použitím SWPM](./media/hana-get-started/image035b.jpg)

Následující snímek obrazovky ukazuje krok instalace SWPM, kde zadáte informace o schématu DBACOCKPIT:

![Krok instalace SWPM, pokud se zadá DBACOCKPIT schéma dat](./media/hana-get-started/image036b.jpg)

Zadejte informace o schématu SAPABAP1:

![Zadávání dat o SAPABAP1 schématu](./media/hana-get-started/image037b.jpg)

Po dokončení instalace SWPM databáze instance, můžete zobrazit schéma SAPABAP1 v SAP HANA Studio:

![Schéma SAPABAP1 v SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Nakonec po dokončení aplikační server SAP a SAP grafickým uživatelským rozhraním instalace ověřit pomocí instance databáze HANA **řídicí panel DBA** transakce:

![Instance databáze HANA ověřit s transakcí DBA řídicí panel](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Stahování softwaru SAP
Software můžete stáhnout z SAP Service Marketplace, jak je znázorněno na následujících snímcích obrazovky.

Stáhněte si NetWeaver 7.5 pro Linux/HANA:

 ![Okno instalace a upgrade služby SAP pro stahování NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Stáhněte edici platformy HANA SP12:

 ![Okno instalace a upgrade služby SAP pro stahování HANA SP12 platformy Edition](./media/hana-get-started/image002.jpg)

