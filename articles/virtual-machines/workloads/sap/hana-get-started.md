---
title: 'Rychlý start: Ruční instalace SAP HANA s jednou instancí v Azure Virtual Machines | Microsoft Docs'
description: Průvodce rychlým startem pro ruční instalaci SAP HANA s jednou instancí v Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 8d4e7b7056f4d5e53785366818fad05e24cfc605
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100054"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Rychlý start: Ruční instalace SAP HANA s jednou instancí v Azure Virtual Machines
## <a name="introduction"></a>Úvod
Tato příručka vám pomůže nastavit SAP HANA s jednou instancí v Azure Virtual Machines při ruční instalaci SAP NetWeaver 7,5 a SAP HANA 1,0 SP12. Tato příručka se zaměřuje na nasazení SAP HANA v Azure. Nenahrazuje dokumentaci SAP. 

> [!NOTE]
> Tato příručka popisuje nasazení SAP HANA do virtuálních počítačů Azure. Informace o tom, jak nasadit SAP HANA do velkých instancí HANA, najdete v tématu [použití SAP v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Požadavky
V tomto průvodci se předpokládá, že jste obeznámeni s takovými základními principy infrastruktury jako služby (IaaS):
 * Jak nasadit virtuální počítače (VM) nebo virtuální sítě prostřednictvím Azure Portal nebo PowerShellu
 * Rozhraní příkazového řádku (CLI) Azure pro různé platformy, které obsahuje možnost použít šablony JavaScript Object Notation (JSON).

Tato příručka také předpokládá, že jste obeznámeni s:
* SAP HANA a SAP NetWeaver a jak je nainstalovat místně.
* Jak nainstalovat a provozovat instance aplikací SAP HANA a SAP v Azure
* Následující koncepty a postupy:
   * Plánování nasazení SAP v Azure, které zahrnuje plánování a Azure Storage využití Azure Virtual Network. Přečtěte si článek [SAP NetWeaver on Azure Virtual Machines – Průvodce plánováním a implementací](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Zásady nasazení a způsoby nasazení virtuálních počítačů v Azure. Viz [nasazení služby Azure Virtual Machines pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Vysoká dostupnost pro SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) a Replication Server (OLAJÍCÍCH) v Azure. Další informace najdete v tématu [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Podrobnosti o tom, jak zvýšit efektivitu v instalaci ASCS/SCS s více identifikátory SID v Azure. Viz [Vytvoření konfigurace s více identifikátory SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principy spuštění SAP NetWeaver založené na virtuálních počítačích založených na platformě Linux v Azure. Viz [spuštění SAP NetWeaver v Microsoft Azure virtuálních počítačích s SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Tato příručka poskytuje konkrétní nastavení pro Linux ve virtuálních počítačích Azure. Obsahuje také informace o tom, jak správně připojit disky úložiště Azure k virtuálním počítačům se systémem Linux.

Typy virtuálních počítačů Azure, které se dají použít v produkčních scénářích, najdete v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). V případě neproduktivních scénářů je k dispozici širší spektrum nativních typů virtuálních počítačů Azure.
Další informace o konfiguraci a operacích virtuálních počítačů najdete v tématu [SAP HANA konfiguracích a operacích infrastruktury v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
SAP HANA vysoké dostupnosti najdete v tématu [SAP HANA vysoká dostupnost pro Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Pokud chcete rychle nasadit instanci SAP HANA nebo S/4HANA nebo ČERNOBÍLý nebo 4HANA systém, zvažte použití [knihovny cloudových zařízení SAP](https://cal.sap.com). Dokumentaci k nasazení systému S/4HANA prostřednictvím knihovny cloudového zařízení SAP v Azure najdete v dokumentaci, například v [této příručce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Potřebujete jenom předplatné Azure a uživatele SAP, který je možné zaregistrovat pomocí knihovny cloudových zařízení SAP.

## <a name="additional-resources"></a>Další zdroje
### <a name="sap-hana-backup"></a>Zálohování SAP HANA
Informace o tom, jak zálohovat SAP HANA databází na virtuálních počítačích Azure, najdete v těchto tématech:
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup na úrovni souboru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA zálohování na základě snímků úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Knihovna cloudových zařízení SAP
Informace o tom, jak pomocí knihovny cloudových zařízení SAP nasadit S/4HANA nebo ČERNOBÍLé/4HANA, najdete v tématu [nasazení SAP S/4HANA nebo černobílé/4HANA na Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA – podporované operační systémy
Informace o podporovaných operačních systémech SAP HANA najdete v tématu [SAP Note 2235581-SAP HANA: Podporované operační systémy](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuální počítače Azure podporují pouze podmnožinu těchto operačních systémů. Nasazení SAP HANA v Azure podporuje následující operační systémy: 

* SUSE Linux Enterprise Server 12. x
* Red Hat Enterprise Linux 7,2

Další dokumentaci ke službě SAP týkající se SAP HANA a různých operačních systémů Linux najdete v těchto tématech:

* [Poznámka SAP ke 171356: Software SAP na platformě Linux: Obecné informace](https://launchpad.support.sap.com/#/notes/1984787).
* [Poznámka SAP ke 1944799: SAP HANA pokyny pro instalaci](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)operačního systému SLES.
* [Poznámka SAP ke 2205917: SAP HANA DB doporučuje nastavení operačního systému pro aplikace](https://launchpad.support.sap.com/#/notes/2205917/E)SAP SLES 12.
* [Poznámka SAP ke 1391070: Řešení](https://launchpad.support.sap.com/#/notes/1391070)UUID pro Linux.
* [Poznámka SAP ke 2009879: Pokyny pro SAP HANA pro operační systém](https://launchpad.support.sap.com/#/notes/2009879)Red Hat Enterprise Linux (RHEL).
* [Poznámka SAP ke 2292690: SAP HANA DB: Doporučené nastavení operačního systému pro RHEL](https://launchpad.support.sap.com/#/notes/2292690/E)7.

### <a name="sap-monitoring-in-azure"></a>Monitorování SAP v Azure
Informace o monitorování SAP v Azure:

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) popisuje rozšířené monitorování SAP s virtuálními počítači se systémem Linux v Azure. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) popisuje informace o SAPOSCOL v systému Linux. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) popisuje klíčové metriky monitorování pro SAP v Microsoft Azure.

### <a name="azure-vm-types"></a>Typy virtuálních počítačů Azure
Typy virtuálních počítačů Azure a scénáře úloh podporované SAP, které se používají s SAP HANA, jsou popsané v [SAP Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Typy virtuálních počítačů Azure, které jsou certifikovány SAP pro SAP pro SAP NetWeaver nebo aplikační vrstva s/4HANA [, jsou zdokumentovány v části SAP Note 1928533: Aplikace SAP v Azure: Podporované produkty a typy](https://launchpad.support.sap.com/#/notes/1928533/E)virtuálních počítačů Azure.

> [!NOTE]
> SAP-Linux – Integrace Azure je podporovaná jenom v Azure Resource Manager a ne v modelu nasazení Classic. 

## <a name="manual-installation-of-sap-hana"></a>Ruční instalace SAP HANA

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je SAP Certified for SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačního systému pro tyto typy virtuálních počítačů se dá vyhledat v [SAP HANA certifikovaných IaaS platformách](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ujistěte se, že kliknete na podrobnosti o typu virtuálního počítače, který se zobrazí, a získejte úplný seznam SAP HANA podporovaných verzí operačního systému pro konkrétní typ virtuálního počítače. V příkladu v tomto dokumentu jsme použili verzi operačního systému SUSE Linux Enterprise Server (SLES), kterou SAP nepodporuje pro SAP HANA na virtuálních počítačích řady M-Series.
>

Tato příručka popisuje, jak ručně nainstalovat SAP HANA na virtuální počítače Azure dvěma různými způsoby:

* Použijte nástroj SAP software Provisioning Manager (SWPM) jako součást instalace distribuované NetWeaver v kroku "instalace instance databáze".
* Použijte nástroj SAP HANA Database Lifecycle Manager (HDBLCM) a pak nainstalujte NetWeaver.

SWPM můžete použít také k instalaci všech součástí, například SAP HANA, aplikačního serveru SAP a instance ASCS v jednom jednom virtuálním počítači. Postup je popsaný v tomto [SAP HANA oznámení blogu](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Tato možnost není popsaná v tomto průvodci rychlým startem, ale problémy, které je potřeba vzít v úvahu, jsou stejné.

Než začnete s instalací, doporučujeme Přečtěte si část Příprava virtuálních počítačů Azure na ruční instalaci SAP HANA dále v této příručce. Můžete tak zabránit několika základním chybám, které se můžou vyskytnout, když použijete jenom výchozí konfiguraci virtuálního počítače Azure.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Klíčové kroky pro instalaci SAP HANA, když používáte SAP SWPM
V této části jsou uvedené klíčové kroky pro ruční instalaci SAP HANA jedné instance při použití SAP SWPM k provedení distribuované instalace SAP NetWeaver 7,5. Jednotlivé kroky jsou podrobněji vysvětleny na snímcích obrazovky později v této příručce.

1. Vytvořte virtuální síť Azure, která obsahuje dva testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy podle modelu Azure Resource Manager. V tomto příkladu se používá SUSE Linux Enterprise Server a SLES pro SAP Applications 12 SP1. 
3. Připojte k virtuálnímu počítači aplikačního serveru dva disky úložiště Azure Standard nebo Premium, například 75-GB nebo 500-GB disků.
4. Připojte disky Premium Storage k virtuálnímu počítači serveru HANA DB. Další informace najdete v části "nastavení disku" dále v této příručce.
5. Podle požadavků na velikost nebo propustnost Připojte více disků. Pak vytvořte prokládané svazky. Pomocí nástroje Správa logických svazků (LVM) nebo správy více zařízení (mdadm) na úrovni operačního systému uvnitř virtuálního počítače.
6. Na připojených discích nebo na logických svazcích vytvořte XFS souborové systémy.
7. Připojte nové systémy souborů XFS na úrovni operačního systému. Pro veškerý software SAP použijte jeden systém souborů. Pro adresář/sapmnt a zálohování použijte jiný systém souborů, například. Na serveru SAP HANA DB Připojte systémy souborů XFS na discích úložiště úrovně Premium jako/Hana a/usr/SAP. Tento proces je nutný k tomu, aby se zabránilo naplnění kořenového systému souborů. Kořenový systém souborů není ve virtuálních počítačích se systémem Linux Azure velký. 
8. Zadejte místní IP adresy testovacích virtuálních počítačů v souboru/etc/hosts.
9. V souboru /etc/fstab zadejte parametr neúspěšná.
10. V závislosti na verzi operačního systému Linux, kterou používáte, nastavte parametry jádra systému Linux. Další informace najdete v poznámkách SAP, které se týkají HANA a v části "parametry jádra" v této příručce.
11. Přidejte místo odkládacího souboru.
12. Volitelně můžete nainstalovat grafickou plochu na testovacích virtuálních počítačích. V opačném případě použijte vzdálenou instalaci SAPinst.
13. Stáhněte si software SAP z webu SAP Service Marketplace.
14. Nainstalujte instanci SAP ASCS na virtuálním počítači aplikačního serveru.
15. Sdílejte adresář/sapmnt mezi testovacími virtuálními počítači pomocí systému souborů NFS. Virtuální počítač aplikačního serveru je server NFS.
16. Nainstalujte instanci databáze, která zahrnuje HANA, pomocí SWPM na virtuálním počítači DATABÁZOVÉho serveru.
17. Nainstalujte primární aplikační server (PAS) na virtuální počítač aplikačního serveru.
18. Spusťte konzolu pro správu SAP (SAP MC). Připojte se například pomocí grafického uživatelského rozhraní SAP nebo HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Klíčové kroky pro instalaci SAP HANA, když používáte HDBLCM
V této části jsou uvedené klíčové kroky pro ruční instalaci SAP HANA jedné instance při použití SAP HDBLCM k provedení distribuované instalace SAP NetWeaver 7,5. Jednotlivé kroky jsou podrobněji vysvětleny na snímcích obrazovky v rámci této příručky.

1. Vytvořte virtuální síť Azure, která obsahuje dva testovací virtuální počítače.
2. Nasaďte dva virtuální počítače Azure s operačními systémy podle modelu Azure Resource Manager. V tomto příkladu se používají SLES a SLES pro SAP Applications 12 SP1.
3. Připojte dva disky úložiště Azure Standard nebo Premium Storage, například 75-GB nebo 500-GB disků, na virtuální počítač aplikačního serveru.
4. Připojte disky Premium Storage k virtuálnímu počítači serveru HANA DB. Další informace najdete v části "nastavení disku" dále v této příručce.
5. Podle požadavků na velikost nebo propustnost Připojte více disků. Prokládané svazky můžete vytvořit pomocí nástroje Správa logických svazků nebo mdadm na úrovni operačního systému v rámci virtuálního počítače.
6. Na připojených discích nebo na logických svazcích vytvořte XFS souborové systémy.
7. Připojte nové systémy souborů XFS na úrovni operačního systému. Pro veškerý software SAP použijte jeden systém souborů. Pro adresář/sapmnt a zálohování použijte jiný systém souborů, například. Na serveru SAP HANA DB Připojte systémy souborů XFS na discích úložiště úrovně Premium jako/Hana a/usr/SAP. Tento proces je nutný k tomu, aby se zabránilo naplnění kořenového systému souborů. Kořenový systém souborů není ve virtuálních počítačích se systémem Linux Azure velký.
8. Zadejte místní IP adresy testovacích virtuálních počítačů v souboru/etc/hosts.
9. V souboru /etc/fstab zadejte parametr neúspěšná.
10. Nastavte parametry jádra podle používané verze operačního systému Linux. Další informace najdete v poznámkách SAP, které se týkají HANA a v části "parametry jádra" v této příručce.
11. Přidejte místo odkládacího souboru.
12. Volitelně můžete nainstalovat grafickou plochu na testovacích virtuálních počítačích. V opačném případě použijte vzdálenou instalaci SAPinst.
13. Stáhněte si software SAP z webu SAP Service Marketplace.
14. Na virtuálním počítači serveru HANA DB vytvořte skupinu sapsys s ID skupiny 1001.
15. Nainstalujte SAP HANA na virtuálním počítači s databázovým serverem pomocí Správce životního cyklu databáze HANA.
16. Nainstalujte instanci SAP ASCS na virtuálním počítači aplikačního serveru.
17. Sdílejte adresář/sapmnt mezi testovacími virtuálními počítači pomocí systému souborů NFS. Virtuální počítač aplikačního serveru je server NFS.
18. Nainstalujte instanci databáze, která zahrnuje HANA, pomocí SWPM na virtuálním počítači serveru HANA DB.
19. Nainstalujte primární aplikační server na virtuální počítač aplikačního serveru.
20. Spusťte SAP MC. Připojte se prostřednictvím grafického uživatelského rozhraní SAP nebo HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Příprava virtuálních počítačů Azure na ruční instalaci SAP HANA
Tato část obsahuje následující témata:

* Aktualizace operačního systému
* Nastavení disku
* Parametry jádra
* Systémy souborů
* Soubor/etc/hosts
* Soubor/etc/fstab

### <a name="os-updates"></a>Aktualizace operačního systému
Před instalací dalšího softwaru se podívejte na aktualizace a opravy operačního systému Linux. Instalací opravy se můžete vyhnout volání oddělení podpory.

Ujistěte se, že používáte:
* SUSE Linux Enterprise Server pro aplikace SAP.
* Red Hat Enterprise Linux pro aplikace SAP nebo Red Hat Enterprise Linux pro SAP HANA. 

Pokud jste to ještě neudělali, zaregistrujte nasazení operačního systému u svého předplatného pro Linux od dodavatele systému Linux. SUSE má image operačních systémů pro aplikace SAP, které už obsahují služby a které jsou zaregistrované automaticky.

Tady je příklad, jak vyhledat dostupné opravy pro SUSE Linux pomocí příkazu **zypperu** :

 `sudo zypper list-patches`

V závislosti na druhu problému jsou opravy klasifikované podle kategorie a závažnosti. Běžně používané hodnoty pro kategorii jsou: 
- Zabezpečení
- Doporučené
- volitelná,
- Funkce
- Dokument
- Yast

Běžně používané hodnoty pro závažnost jsou:

- Kritická
- Důležité
- Pokročilé
- Nízká
- Neurčeno

Příkaz **zypperu** vyhledá jenom aktualizace, které vaše nainstalované balíčky potřebují. Například můžete použít tento příkaz:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Můžete přidat parametr `--dry-run` pro otestování aktualizace bez toho, aby systém skutečně aktualizoval systém.


### <a name="disk-setup"></a>Nastavení disku
Kořenový systém souborů na virtuálním počítači se systémem Linux v Azure má omezení velikosti. K virtuálnímu počítači Azure proto budete muset ke spuštění SAP připojit další místo na disku. V případě virtuálních počítačů Azure aplikačního serveru SAP může stačit použití disků Azure Storage úrovně Standard. U SAP HANA DBMS virtuálních počítačů Azure je použití disků Azure Premium Storage pro produkční a nevýrobní implementace povinné.

V závislosti na [SAP HANA požadavky na úložiště TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)je doporučena následující konfigurace služby Azure Premium Storage: 

| SKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM |  /Hana/data a/Hana/log <br /> prokládaný pomocí LVM nebo mdadm | /hana/shared | Rozsah/root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

V navrhovaných konfiguracích disků se svazek dat HANA a svazek protokolu nacházejí na stejné sadě disků služby Azure Premium Storage, které jsou vykládané pomocí LVM nebo mdadm. Není nutné definovat žádnou redundanci RAID, protože Azure Premium Storage udržuje tři bitové kopie disků pro zajištění redundance. 

Abyste měli jistotu, že nakonfigurujete dostatečné úložiště, přečtěte si téma [SAP HANA požadavky na úložiště TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) a [Průvodce instalací a aktualizací serveru SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Vezměte v úvahu také různé svazky propustnosti virtuálních pevných disků (VHD) různých disků služby Azure Premium Storage, jak je uvedeno ve [vysoce výkonném úložišti Premium a na spravovaných discích pro virtuální počítače](../../windows/disks-types.md). 

Do virtuálních počítačů HANA DBMS můžete přidat další disky úložiště úrovně Premium, které budou ukládat zálohy databáze nebo protokolu transakcí.

Další informace o dvou hlavních nástrojích, které se používají ke konfiguraci proložení, najdete v těchto tématech:

* [Konfigurace softwarového pole RAID v systému Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [NAKONFIGURUJTE LVM na virtuálním počítači Linux v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Další informace o tom, jak připojit disky k virtuálním počítačům Azure, na kterých běží Linux, jako hostovaný operační systém, najdete v tématu [Přidání disku do virtuálního počítače se systémem Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

S Azure Premium SSD můžete definovat režimy ukládání disku do mezipaměti. Pro prokládanou sadu, která obsahuje/Hana/data a/Hana/log, zakažte ukládání disku do mezipaměti. U ostatních svazků, to znamená disků, nastavte režim ukládání do mezipaměti na **jen pro čtení**.

Ukázkové šablony JSON, které se mají použít k vytvoření virtuálních počítačů, najdete v tématu [šablony pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates).
Šablona VM-Simple-SLES je základní šablonou. Obsahuje oddíl úložiště s dalšími datovými disky 100-GB. Tuto šablonu použijte jako základnou. Šablonu můžete přizpůsobit pro konkrétní konfiguraci.

> [!NOTE]
> Je důležité připojit disk služby Azure Storage pomocí identifikátoru UUID, jak je popsáno v části [spuštění SAP NetWeaver v Microsoft Azure virtuálních počítačích s SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

V testovacím prostředí jsou dva disky úložiště Azure Standard připojené k virtuálnímu počítači aplikačního serveru SAP, jak je znázorněno na následujícím snímku obrazovky. Jeden disk ukládá veškerý software SAP, například NetWeaver 7,5, grafické rozhraní SAP a SAP HANA pro instalaci. Druhý disk zajišťuje, aby bylo k dispozici dostatek volného místa pro další požadavky. Například data zálohování a testování a adresář/sapmnt, to znamená, že profily SAP, je nutné sdílet mezi všemi virtuálními počítači, které patří do stejné technologie SAP na šířku.

![Okno SAP HANA disků aplikačního serveru zobrazující dva datové disky a jejich velikosti](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parametry jádra
SAP HANA vyžaduje specifická nastavení jádra pro Linux. Tato nastavení jádra nejsou součástí standardních imagí Galerie Azure a musí být nastavená ručně. Parametry se můžou lišit v závislosti na tom, jestli používáte SUSE nebo Red Hat. Poznámky SAP uvedené dříve obsahují informace o těchto parametrech. V zobrazených snímcích obrazovky se použil systém SUSE Linux 12 SP1. 

SLES for SAP Applications 12 General availability a SLES for SAP Applications 12 SP1 mají novýnástroj, který je vyladěný, který nahrazuje starý nástroj **sapconf** . K dispozici je speciální profil SAP HANA pro vyladěné **-ADM**. Pro optimalizaci systému pro SAP HANA zadejte následující profil jako kořenového uživatele:

   `tuned-adm profile sap-hana`

Další informace o vyladěných **admch**najdete v [dokumentaci k SUSE o laděném-ADM](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na následujícím snímku obrazovky vidíte, jak vyladěná funkce **ADM** změnila `transparent_hugepage` hodnoty `numa_balancing` a podle požadovaných nastavení SAP HANA:

![Nástroj pro vyladěné a ADM mění hodnoty podle požadovaného nastavení SAP HANA](./media/hana-get-started/image005.jpg)

Chcete-li nastavit SAP HANA nastavení jádra jako trvalé, použijte **grub2** na SLES 12. Další informace o **grub2**najdete v části [Struktura konfiguračního souboru](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) v dokumentaci SUSE.

Následující snímek obrazovky ukazuje, jak se v konfiguračním souboru změnila nastavení jádra a potom se zkompiluje pomocí **grub2-mkconfig**:

![Nastavení jádra se v konfiguračním souboru změnilo a zkompiluje pomocí grub2-mkconfig.](./media/hana-get-started/image006.jpg)

Další možností je změnit nastavení pomocí YaST a nastavení**parametrů jádra jádra** **spouštěcího zavaděče** > :

![Karta nastavení parametrů jádra v zavaděči YaST Boot](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systémy souborů
Následující snímek obrazovky ukazuje dva systémy souborů, které byly vytvořeny na virtuálním počítači s aplikačním serverem SAP, nad dvěma připojenými disky Azure Standard Storage. Oba systémy souborů jsou typu XFS a jsou připojeny k/sapdata a/sapsoftware.

Tímto způsobem není nutné strukturovat systémy souborů. Máte další možnosti, jak strukturovat místo na disku. Nejdůležitějším aspektem je zabránit tomu, aby na kořenovém systému souborů běželo volné místo.

![Dva souborové systémy vytvořené na virtuálním počítači aplikačního serveru SAP](./media/hana-get-started/image008.jpg)

Pro virtuální počítač SAP HANA DB během instalace databáze při použití SAPinst s SWPM a **typickou** možností instalace je vše nainstalované v oblasti/Hana a/usr/SAP. Výchozím umístěním pro zálohu protokolu SAP HANA je/usr/SAP. Znovu je důležité zabránit v nedostatku místa v úložišti kořenového systému souborů. Před instalací SAP HANA pomocí SWPM se ujistěte, že je v části/Hana a/usr/SAP k dispozici dostatek volného místa.

Popis standardního rozložení souborového systému SAP HANA najdete v [Průvodci instalací a aktualizací SAP HANA serveru](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Další systémy souborů vytvořené na virtuálním počítači s aplikačním serverem SAP](./media/hana-get-started/image009.jpg)

Když nainstalujete SAP NetWeaver na standardní SLES/SLES pro aplikace SAP – obrázek Galerie Azure, zobrazí se zpráva, že neexistuje žádné odkládací místo, jak je znázorněno na následujícím snímku obrazovky. Tuto zprávu můžete zavřít ručním přidáním odkládacího souboru pomocí direktiv **DD**, **mkswap**a **swapon**. Pokud se chcete dozvědět, jak, vyhledejte téma "Přidání odkládacího souboru ručně" v části [použití oddílu YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) v dokumentaci SUSE.

Další možností je nakonfigurovat odkládací prostor pomocí agenta virtuálního počítače se systémem Linux. Další informace najdete v [uživatelské příručce agenta Azure Linux](../../extensions/agent-linux.md).

![Automaticky otevíraná zpráva, která doporučuje, aby nedostatek odkládacího prostoru](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Soubor/etc/hosts
Než začnete instalovat SAP, ujistěte se, že jste do souboru/etc/hosts zahrnuli názvy hostitelů a IP adresy virtuálních počítačů SAP. Nasazení všech virtuálních počítačů SAP v rámci jedné virtuální sítě Azure Pak použijte interní IP adresy, jak je znázorněno zde:

![Názvy hostitelů a IP adresy pro virtuální počítače SAP uvedené v souboru/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Soubor/etc/fstab

Je užitečné přidat do souboru fstab parametr neúspěch. V případě, že dojde k nějakému problému s disky, virtuální počítač přestane v procesu spouštění reagovat. Mějte ale na paměti, že další místo na disku nemusí být k dispozici a procesy mohou zaplnit kořenový systém souborů. Pokud/Hana chybí, SAP HANA se nespustí.

![Přidejte do souboru fstab parametr neúspěch.](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafický GNOME Desktop na SLES 12/SLES pro SAP Applications 12
V této části se dozvíte, jak:

* Nainstalujte desktop GNOME a xrdp na SLES 12/SLES pro SAP Applications 12.
* Spouštějte modul SAP MC založený na jazyce Java pomocí prohlížeče Firefox v SLES 12/SLES pro SAP Applications 12.

Můžete také použít alternativy, jako je například Xterminal nebo VNC, které nejsou popsány v tomto průvodci.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalace desktopu GNOME a xrdp na SLES 12/SLES pro SAP Applications 12
Pokud máte pozadí Windows, můžete snadno použít grafickou plochu přímo v rámci virtuálních počítačů SAP Linux ke spouštění aplikací Firefox, SAPinst, SAP GUI, SAP MC nebo HANA Studio. Pak se můžete připojit k VIRTUÁLNÍmu počítači přes protokol RDP (Remote Desktop Protocol) (RDP) z počítače s Windows. V závislosti na zásadách vaší společnosti, které se týkají přidávání GUI do produkčních a neproduktivních systémů na platformě Linux, můžete na server nainstalovat GNOME. Pomocí těchto kroků nainstalujete desktop GNOME na Azure SLES 12/SLES pro SAP Applications 12 VM.

1. Nainstalujte GNOME plochu tak, že zadáte následující příkaz, například v okně výstupu:

   `zypper in -t pattern gnome-basic`

2. Nainstalujte xrdp, aby se povolilo připojení k virtuálnímu počítači přes RDP:

   `zypper in xrdp`

3. Upravte/etc/sysconfig/WindowManager a nastavte výchozího správce oken na GNOME:

   `DEFAULT_WM="gnome"`

4. Spusťte **chkconfig** a ujistěte se, že se xrdp po restartování spustí automaticky:

   `chkconfig -level 3 xrdp on`

5. Pokud máte problém s připojením RDP, zkuste restartovat například z okna pro výstup:

   `/etc/xrdp/xrdp.sh restart`

6. Pokud xrdp restart zmíněný v předchozím kroku nefunguje, vyhledejte soubor. PID:

   `check /var/run` 

   `xrdp.pid`Hledat. Pokud ho najdete, odeberte ho a zkuste ho znovu spustit.

### <a name="start-sap-mc"></a>Spustit SAP MC
Po instalaci desktopu GNOME můžete z Firefox spustit grafický software SAP založený na jazyce Java. Pokud běží v Azure SLES 12/SLES pro SAP Applications 12 VM, může se zobrazit chyba. K této chybě dochází z důvodu chybějícího modulu plug-in prohlížeče Java.

Adresa URL pro spuštění SAP MC je `<server>:5<instance_number>13`.

Další informace najdete v tématu [spuštění webové konzoly pro správu SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Následující snímek obrazovky ukazuje chybovou zprávu, která se zobrazí, když chybí modul plug-in Java-Browser:

![Chybová zpráva oznamující chybějící modul plug-in Java-Browser](./media/hana-get-started/image013.jpg)

Jedním ze způsobů, jak problém vyřešit, je instalace chybějícího modulu plug-in pomocí YaST, jak je znázorněno na následujícím snímku obrazovky:

![Instalace chybějícího modulu plug-in pomocí YaST](./media/hana-get-started/image014.jpg)

Po opětovném zadání adresy URL konzoly pro správu SAP budete požádáni o aktivaci modulu plug-in:

![Dialogové okno požadující aktivaci modulu plug-in](./media/hana-get-started/image015.jpg)

Může se také zobrazit chybová zpráva o chybějícím souboru, JavaFX. Properties. Vztahuje se na požadavky Oracle Java 1,8 pro rozhraní SAP GUI 7,4. Další informace najdete v tématu [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Verze IBM Java a balíček OpenJDK, který se dodává s SLES/SLES pro aplikace SAP, 12 nezahrnuje potřebný soubor JavaFX. Properties. Řešením je stáhnout a nainstalovat Java SE 8 od Oracle.

Informace o podobném problému s OpenJDK v openSUSE najdete v tématu diskuze [SAPGui 7,4 Java for openSUSE 42,1](https://scn.sap.com/thread/3908306)přestupnější.

## <a name="manual-installation-of-sap-hana-swpm"></a>Ruční instalace SAP HANA: SWPM
Série obrazovek v této části ukazuje klíčové kroky pro instalaci SAP NetWeaver 7,5 a SAP HANA SP12 při použití SWPM s SAPinst. V rámci instalace NetWeaver 7,5 může SWPM také nainstalovat databázi HANA jako jednu instanci.

V ukázkovém testovacím prostředí jsme nainstalovali jeden aplikační server ABAP (Advanced Business Application Programming). Jak je znázorněno na následujícím snímku obrazovky, použili jsme možnost **distribuovaného systému** k instalaci instancí ASCS a primárního aplikačního serveru v jednom virtuálním počítači Azure. Používali jsme SAP HANA jako databázový systém na jiném virtuálním počítači Azure.

![Instance ASCS a primárního aplikačního serveru nainstalované pomocí možnosti distribuovaného systému](./media/hana-get-started/image012.jpg)

Po instalaci instance ASCS na virtuálním počítači aplikačního serveru se tato ikona identifikuje zelenou ikonou v konzole pro správu SAP. Adresář/sapmnt, který zahrnuje adresář profilu SAP, musí být sdílen s virtuálním počítačem serveru SAP HANA DB. Tento krok instalace databáze potřebuje přístup k těmto informacím. Nejlepším způsobem, jak zajistit přístup, je použít systém souborů NFS, který se dá nakonfigurovat pomocí YaST.

![Konzola pro správu SAP zobrazující instanci ASCS nainstalovanou na virtuálním počítači s aplikačním serverem pomocí zelené ikony](./media/hana-get-started/image016.jpg)

Na virtuálním počítači s aplikačním serverem se adresář/sapmnt sdílí přes systém souborů NFS pomocí možností **RW** a **no_root_squash** . Výchozí hodnoty jsou **ro** a **root_squash**, což může vést k problémům při instalaci instance databáze.

![Sdílení adresáře/sapmnt přes systém souborů NFS pomocí možností RW a no_root_squash](./media/hana-get-started/image017b.jpg)

Jak ukazuje následující snímek obrazovky, musí být sdílená složka/sapmnt z virtuálního počítače aplikačního serveru nakonfigurovaná na virtuálním počítači serveru SAP HANA DB pomocí **klienta NFS** a YaST:

![Sdílená složka/sapmnt nakonfigurovaná pomocí klienta NFS](./media/hana-get-started/image018b.jpg)

K provedení distribuované instalace NetWeaver 7,5, tedy **instance databáze**, se přihlaste k virtuálnímu počítači serveru SAP HANA DB a spusťte SWPM:

![Instalace instance databáze přihlášením k virtuálnímu počítači serveru SAP HANA DB a spuštěním SWPM](./media/hana-get-started/image019.jpg)

Až vyberete **typickou** instalaci a cestu k instalačnímu médiu, zadejte SID databáze, název hostitele, číslo instance a heslo správce systému databáze:

![Přihlašovací stránka Správce databázového systému SAP HANA](./media/hana-get-started/image035b.jpg)

Zadejte heslo pro schéma DBACOCKPIT:

![Pole pro zadání hesla pro schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Zadejte otázku pro SAPABAP1 heslo schématu:

![Zadejte otázku pro heslo schématu SAPABAP1.](./media/hana-get-started/image037b.jpg)

Po dokončení jednotlivých úloh se vedle každé fáze procesu instalace databáze zobrazí zelená značka zaškrtnutí. Zpráva "provedení... Je zobrazená instance databáze, která je dokončená.

![Okno úlohy – dokončeno s potvrzovací zprávou](./media/hana-get-started/image023.jpg)

Po úspěšné instalaci se v konzole pro správu SAP zobrazuje také instance databáze se zelenou ikonou. Zobrazí se úplný seznam SAP HANAch procesů, například hdbindexserver a hdbcompileserver.

![Okno konzoly pro správu SAP se seznamem procesů SAP HANA](./media/hana-get-started/image024.jpg)

Následující snímek obrazovky ukazuje části struktury souborů v adresáři/Hana/Shared, který SWPM vytvořil během instalace HANA. Vzhledem k tomu, že není k dispozici možnost zadat jinou cestu, je důležité připojit další místo na disku v adresáři/Hana před instalací SAP HANA pomocí SWPM. Tento krok zabrání kořenovému systému souborů v chodu volného místa.

![Struktura souborů adresáře/Hana/Shared vytvořená během instalace HANA](./media/hana-get-started/image025.jpg)

Na tomto snímku obrazovky se zobrazuje struktura souborů adresáře/usr/SAP:

![Struktura souborů adresáře/usr/SAP](./media/hana-get-started/image026.jpg)

Posledním krokem instalace distribuované ABAP je instalace primární instance aplikačního serveru:

![Instalace ABAP znázorňující instanci primárního aplikačního serveru jako poslední krok](./media/hana-get-started/image027b.jpg)

Po instalaci primární instance aplikačního serveru a grafického uživatelského rozhraní SAP použijte **řídicí panel DBA** a potvrďte, že se instalace SAP HANA správně dokončila:

![Okno řídicího panelu DBA potvrzující úspěšnou instalaci](./media/hana-get-started/image028b.jpg)

V posledním kroku budete možná chtít nejdřív nainstalovat HANA Studio na virtuální počítač aplikačního serveru SAP. Pak se připojte k instanci SAP HANA, která je spuštěná na virtuálním počítači DATABÁZOVÉho serveru.

![Instalace SAP HANA studia na virtuálním počítači s aplikačním serverem SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Ruční instalace SAP HANA: HDBLCM
Kromě instalace SAP HANA v rámci distribuované instalace pomocí SWPM můžete nejdřív nainstalovat samostatnou instalaci HANA, a to pomocí HDBLCM. Pak můžete nainstalovat SAP NetWeaver 7,5 například. Snímky obrazovky v této části ukazují, jak tento proces funguje.

Další informace o nástroji HANA HDBLCM najdete v těchto tématech:

* [Vyberte správné SAP HANA HDBLCM pro úlohu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Nástroje pro správu životního cyklu SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Instalace a Průvodce aktualizací serveru SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Chcete se vyhnout problémům s výchozím nastavením ID skupiny pro `\<HANA SID\>adm user`, který je vytvořen nástrojem HDBLCM Tool. Před instalací SAP HANA přes HDBLCM Definujte novou skupinu s názvem `sapsys` ID `1001`skupiny:

![Nová skupina "sapsys" definovaná pomocí ID skupiny 1001](./media/hana-get-started/image030.jpg)

Při prvním spuštění HDBLCM se zobrazí jednoduchá nabídka Start. Vyberte položku 1, **nainstalovat nový systém**:

![Možnost "nainstalovat nový systém" v okně Start HDBLCM](./media/hana-get-started/image031.jpg)

Na následujícím snímku obrazovky se zobrazí všechny klíčové možnosti, které jste vybrali dříve.

> [!IMPORTANT]
> Adresáře s názvem pro protokoly a datové svazky HANA a instalační cestu, která je v této ukázce/Hana/Shared, a/usr/SAP nesmí být součástí kořenového systému souborů. Tyto adresáře patří k datovým diskům Azure, které byly připojeny k virtuálnímu počítači. Další informace najdete v části "nastavení disku". 

Tento přístup pomáhá zabránit v nedostatku místa v kořenovém systému souborů. Všimněte si, že správce systému Hana má ID `1005` uživatele a je součástí `sapsys` skupiny s ID `1001`, které bylo definováno před instalací.

![Seznam všech dříve vybraných součástí Key SAP HANA](./media/hana-get-started/image032.jpg)

Projděte `\<HANA SID\>adm user` si podrobnosti v adresáři/etc/passwd. `azdadm`Vyhledejte, jak je znázorněno na následujícím snímku obrazovky:

![Podrobnosti \<o uživatelích\>ADM v Hana Hana, které jsou uvedené v adresáři/etc/passwd](./media/hana-get-started/image033.jpg)

Po instalaci SAP HANA pomocí HDBLCM můžete zobrazit strukturu souborů v aplikaci SAP HANA Studio, jak je znázorněno na následujícím snímku obrazovky. Schéma SAPABAP1, které zahrnuje všechny tabulky SAP NetWeaver, ještě nejsou k dispozici.

![SAP HANA struktura souborů v SAP HANA studiu](./media/hana-get-started/image034.jpg)

Po instalaci SAP HANA můžete nainstalovat SAP NetWeaver nad ním. Jak je znázorněno na následujícím snímku obrazovky, instalace byla provedena jako distribuovaná instalace pomocí SWPM. Tento postup je popsaný v předchozí části. Při instalaci instance databáze pomocí SWPM zadáváte stejná data pomocí HDBLCM. Například zadáte název hostitele, identifikátor SID HANA a číslo instance. SWPM pak použije existující instalaci HANA a přidá další schémata.

![Distribuovaná instalace prováděná pomocí SWPM](./media/hana-get-started/image035b.jpg)

Následující snímek obrazovky ukazuje krok instalace SWPM, kde zadáváte data o schématu DBACOCKPIT:

![Krok instalace SWPM, ve kterém se zadají data schématu DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Zadejte data o schématu SAPABAP1:

![Zadávání dat o schématu SAPABAP1](./media/hana-get-started/image037b.jpg)

Po dokončení instalace instance databáze SWPM můžete v SAP HANA studiu Zobrazit schéma SAPABAP1:

![Schéma SAPABAP1 v SAP HANA studiu](./media/hana-get-started/image038b.jpg)

Nakonec po dokončení instalace aplikace SAP App Server a grafického uživatelského rozhraní SAP ověřte instanci databáze HANA pomocí **řídicího panelu DBA** :

![Instance databáze HANA byla ověřena pomocí řídící transakce DBA.](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Stažení softwaru SAP
Software si můžete stáhnout z webu SAP Service Marketplace, jak je znázorněno na následujících snímcích obrazovky.

Stáhněte si NetWeaver 7,5 pro Linux/HANA:

 ![Okno instalace a upgrade služby SAP pro stažení NetWeaver 7,5](./media/hana-get-started/image001.jpg)

Stáhnout verzi HANA SP12 Platform:

 ![Okno instalace a upgrade služby SAP pro stažení verze HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

