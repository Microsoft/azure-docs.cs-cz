---
title: SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server | Microsoft Docs
description: Průvodce vysokou dostupností pro SAP NetWeaver v SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934167"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný systém HANA v konfiguraci se škálováním na více systémů pomocí úsporného režimu na virtuálních počítačích Azure s [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) pro sdílené svazky úložiště.  
V ukázkových konfiguracích, instalačních příkazech atd. instance HANA je **03** a ID systému Hana je **HN1**. Příklady jsou založené na HANA 2,0 SP4 a SUSE Linux Enterprise Server pro SAP 12 SP4. 

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
* Poznámka [1928533]pro SAP obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro aplikace SAP Poznámka [2205917] se doporučuje nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP Poznámka [1944799] obsahuje pokyny sap pro SUSE Linux Enterprise Server pro aplikace SAP
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* Poznámka SAP poznámky [1900823] obsahuje informace o požadavcích SAP HANA Storage.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Průvodce osvědčenými postupy pro SUSE SAP ha][suse-ha-guide] Příručky obsahují všechny požadované informace pro nastavení NetWeaver HA a SAP HANA místní replikace systému. Použijte tyto příručky jako obecné standardní hodnoty. Poskytují mnohem podrobnější informace.
* [Zpráva k vydání verze SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]
* [SAP HANA v systémech NetApp se systémem souborů NFS](https://www.netapp.com/us/media/tr-4435.pdf). Průvodce konfigurací obsahuje informace o tom, jak nastavit SAP HANA pomocí systému souborů NFS poskytovaného Azure NetApp Files.


## <a name="overview"></a>Přehled

Jednou z metod, jak dosáhnout vysoké dostupnosti pro HANA, je automatické převzetí služeb při selhání hostitele. Pokud chcete konfigurovat automatické převzetí služeb při selhání hostitele, jeden nebo více virtuálních počítačů se přidá do systému HANA a nakonfiguruje se jako pohotovostní uzly. Když aktivní uzel neproběhne úspěšně, převezme pohotovostní uzel automaticky. V předkládaných konfiguracích s virtuálními počítači Azure vychází [systém souborů NFS na Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Pohotovostní uzel musí mít přístup ke všem svazkům databáze. Svazky HANA musí být připojené jako názvů NFSv4 svazky. Vylepšený mechanizmus zapůjčení na základě zapůjčení souborů v protokolu názvů NFSv4 se používá pro `I/O` oplocení. 

> [!IMPORTANT]
> Aby byla podporovaná konfigurace, je nutné nasadit svazky dat a protokolů HANA jako svazky NFSv 4.1 a připojit je pomocí protokolu NFSv 4.1. Konfigurace automatického převzetí služeb při selhání hostitele HANA s pohotovostním uzlem není v NFSv3 podporována.

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Po SAP HANA doporučení k síti se v jedné virtuální síti Azure vytvořily tři podsítě: pro komunikaci se systémem úložiště pro interní komunikaci mezi uzly HANA a pro komunikaci klientů. Svazky NetApp Azure jsou v samostatné podsíti, [delegované na Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

V tomto příkladu konfigurace jsou podsítě:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Azure NetApp Files 

Než budete pokračovat s nastavením infrastruktury souborů Azure NetApp, Seznamte se s [dokumentaci Azure NetApp Files][anf-azure-doc]. Soubory Azure NetApp jsou k dispozici v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Ověřte, jestli vybraná oblast Azure nabízí Azure NetApp Files.  

Následující odkaz ukazuje dostupnost Azure NetApp Files podle oblasti Azure: [Azure NetApp Files dostupnost podle oblasti Azure][anf-avail-matrix].  
Před nasazením Azure NetApp Files požádat o registraci do Azure NetApp Files podle [pokynů pro soubory služby Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure NetApp Files  

V následujících krocích se předpokládá, že jste už nasadili [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Prostředky Azure NetApp Files a virtuální počítače, kde budou připojené prostředky Azure NetApp Files, musí být nasazené ve stejné službě Azure Virtual Network nebo ve virtuálních sítích Azure s partnerským vztahem.  

1. Pokud jste to ještě neudělali, požádejte o [registraci Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Vytvořte účet NetApp ve vybrané oblasti Azure a postupujte podle [pokynů k vytvoření účtu NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Nastavte Azure NetApp Files fond kapacit podle [pokynů, jak nastavit fond kapacit Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura HANA uvedená v tomto článku využívá fond kapacit s jedním Azure NetApp Files a úrovní služeb úrovně Ultra. Pro úlohy HANA v Azure doporučujeme Azure NetApp Files [úrovně služeb](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) v režimu Ultra nebo Premium.  

4. Přenesete podsíť do souborů Azure NetApp, jak je popsáno v [pokynech delegování podsítě na Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Nasaďte Azure NetApp Files svazky podle [pokynů pro vytvoření svazku pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Při nasazování svazků nezapomeňte vybrat verzi **nfsv 4.1** . V současné době přístup k NFSv 4.1 vyžaduje další seznam povolených. Nasaďte svazky v určené Azure NetApp Files [podsíti](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Mějte na paměti, že Azure NetApp Files prostředky a virtuální počítače Azure musí být ve stejném Virtual Network Azure nebo ve virtuálních sítích Azure s partnerským vztahem. Například <b>HN1</b>-data-Mnt00001, <b>HN1</b>-log-mnt00001 atd. jsou názvy svazků<b>a NFS://10.23.1.5/HN1-data</b>-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001 atd. jsou cesty k souborům pro Azure NetApp Files svazky.  

   1. Volume <b>HN1</b>-data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. Volume <b>HN1</b>-data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. Volume <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. Volume <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. svazek <b>HN1</b>– Shared (NFS://10.23.1.4/<b>HN1</b>– Shared)
   
   V tomto příkladu jsme pro každý datový svazek HANA a svazky protokolů používali samostatné Azure NetApp Files. Pro zvýšení nákladů optimalizované pro menší nebo neproduktivní systémy je možné umístit všechna připojení dat a všechny protokoly se připojí na jeden svazek.  

### <a name="important-considerations"></a>Důležité informace

Při zvažování Azure NetApp Files pro SAP NetWeaver v architektuře SUSE pro vysokou dostupnost mějte na paměti následující důležité informace:

- Minimální fond kapacit je 4 TiB.  
- Minimální velikost svazku je 100 GiB
- Azure NetApp Files a všech virtuálních počítačů, kde se Azure NetApp Files svazky připojí, musí být ve stejné oblasti jako Azure Virtual Network nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .  
- Vybraná virtuální síť musí mít podsíť, delegovanou na Azure NetApp Files.
- Propustnost svazku Azure NetApp je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Při změně velikosti svazků NetApp HANA Azure se ujistěte, že výsledná propustnost splňuje požadavky na systém HANA.  
- Azure NetApp Files nabízí [zásady exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): můžete řídit povolené klienty, typ přístupu (čtení & zápisu, jen pro čtení atd.). 
- Azure NetApp Files funkce zatím nereaguje na zóny. Aktuálně Azure NetApp Files funkce není nasazená ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.  
- Je důležité mít virtuální počítače nasazené v těsné blízkosti úložiště Azure NetApp pro nízkou latenci. Pro úlohy SAP HANA nízká latence je kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  
- ID uživatele <b>pro ADM a ID skupiny pro `sapsys`</b>na virtuálních počítačích se musí shodovat s konfigurací v Azure NetApp Files. 

> [!IMPORTANT]
> Pro úlohy SAP HANA nízká latence je kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  

> [!IMPORTANT]
> Pokud dojde k neshodě mezi ID uživatele pro <b>ADM a ID skupiny pro `sapsys`</b>mezi virtuálním počítačem a konfigurací Azure NetApp, zobrazí se jako `nobody` oprávnění pro soubory na svazcích Azure NetApp připojené k virtuálním počítačům. Nezapomeňte zadat správné ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys` při [připojování nového systému](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) k Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Určení velikosti databáze HANA v Azure NetApp Files

Propustnost svazku Azure NetApp je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Při návrhu infrastruktury pro SAP v Azure byste měli znát minimální požadavky na úložiště, které SAP podporuje, a překládat je na minimální propustnost:

- Povolit čtení a zápis v/Hana/logu s 250 MB/s a velikosti 1 MB vstupu/výstupu  
- Povolit aktivitu čtení minimálně 400 MB/s pro/Hana/data pro velikost I/O 16 MB a 64 MB  
- Povolit aktivitu zápisu alespoň 250 MB/s pro/Hana/data s 16 MB a 64 MB I/O velikosti  

[Omezení propustnosti Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TIB kvót:
- Premium Storage vrstva – 64 MiB/s  
- Úroveň úložiště úrovně Ultra – 128 MiB/s  

Aby splňovala požadavky na minimální propustnost SAP pro data a protokol a podle pokynů pro `/hana/shared`, Doporučené velikosti by vypadaly takto:

| Svazek | Velikost<br /> Premium Storage úroveň | Velikost<br /> Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) na 4 pracovní uzly | Max (512 GB, 1xRAM) na 4 pracovní uzly | V3 nebo v 4.1 |

Konfigurace SAP HANA pro rozložení prezentovaná v tomto článku by mohla pomocí Azure NetApp Files úrovně Ultra Storage vypadat takto:

| Svazek | Velikost<br /> Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | V3 nebo v 4.1 |

> [!NOTE]
> Tady uvedená doporučení pro změny velikosti Azure NetApp Files cílí na to, aby splňovala minimální požadavky SAP na jejich poskytovatele infrastruktury. V reálných scénářích nasazení zákazníků a úloh, které nemusí být dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.  

> [!TIP]
> Velikost svazků Azure NetApp Files lze dynamicky měnit bez nutnosti `unmount` svazků, zastavení virtuálních počítačů nebo zastavení SAP HANA. To umožňuje flexibilitu v závislosti na očekávané i nepředvídatelné propustnosti požadavků.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Nasazení virtuálních počítačů se systémem Linux prostřednictvím Azure Portal

Nejprve je třeba vytvořit svazky Azure NetApp Files. Vytvořte [podsítě virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) v [Virtual Network Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Nasaďte virtuální počítače. Vytvořte další síťová rozhraní a připojte síťová rozhraní k odpovídajícím virtuálním počítačům. Každý virtuální počítač má tři síťová rozhraní odpovídající třem podsítím virtuální sítě Azure (`storage`, `hana` a `client`).  Podívejte [se, jak vytvořit virtuální počítač se systémem Linux v Azure s několika síťovými kartami](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Pro úlohy SAP HANA nízká latence je kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti, aby se dosáhlo nízké latence. Odešlete potřebné informace při [připojování nového SAP HANA systému](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), který používá SAP HANA Azure NetApp Files.  
> 
V dalších krocích se předpokládá, že jste už vytvořili skupinu prostředků, virtuální síť Azure a tři podsítě Azure Virtual Network: `storage`, `hana` a `client`.  Při nasazování virtuálních počítačů vyberte podsíť úložiště, aby bylo síťové rozhraní úložiště primárním rozhraním virtuálních počítačů. Pokud to není možné, musí být nakonfigurovaná explicitní trasa k delegované podsíti Azure NetApp přes bránu podsítě úložiště. 

> [!IMPORTANT]
> Ujistěte se, že operační systém, který vyberete, je SAP Certified for SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačních systémů pro tyto typy můžete vyhledat v [SAP HANA certifikovaných IaaS platformách](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ujistěte se, že kliknete na podrobnosti o typu virtuálního počítače a získáte úplný seznam SAP HANA podporovaných verzí operačního systému pro konkrétní typ virtuálního počítače.  

1. Vytvořte skupinu dostupnosti pro SAP HANA. Ujistěte se, že jste nastavili maximální doménu aktualizace.  

2. Vytvořit tři Virtual Machines (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Použijte SLES4SAP image v galerii Azure, která je podporovaná pro SAP HANA. V tomto příkladu jsme použili bitovou kopii SLES4SAP 12 SP4.  
   - Vyberte skupinu dostupnosti vytvořenou dříve pro SAP HANA.  
   - Vyberte podsíť úložiště Azure Virtual Network. Vyberte možnost [akcelerovaná síť](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Když nasadíte virtuální počítače, název síťového rozhraní se vygeneruje automaticky. Budeme odkazovat na síťová rozhraní připojená k podsíti úložiště Azure Virtual Network jako **úložiště hanadb1**, **hanadb2-Storage**a **hanadb3-Storage**. 
3. Vytvořte tři síťová rozhraní – jednu pro každý virtuální počítač pro podsíť `hana` virtuální sítě. V tomto příkladu: **hanadb1-Hana**, **hanadb2-Hana**a **hanadb3-Hana**.  
4. Vytvořte tři síťová rozhraní – jednu pro každý virtuální počítač pro podsíť **klientské** virtuální sítě. V tomto příkladu: **hanadb1-Client**, **hanadb2-** Client a **hanadb3-Client**.  
5. Připojit nově vytvořená rozhraní virtuální sítě k odpovídajícím virtuálním počítačům  

    1. Přejděte k virtuálnímu počítači v [Azure Portal](https://portal.azure.com/#home).  
    2. V levém navigačním podokně vyberte Virtual Machines. Filtr na název virtuálního počítače, například **hanadb1**. Klikněte na virtuální počítač.  
    3. V části Přehled vyberte zastavit, čímž zrušíte přidělení virtuálního počítače.  
    4. Vyberte sítě, připojit síťové rozhraní. V rozevíracím seznamu v části připojit síťové rozhraní vyberte již vytvořená síťová rozhraní pro **`hana`** a **klientské** podsítě.  Uložte. 
    5. Opakujte u zbývajících virtuálních počítačů. V našem příkladu – **hanadb2** a **hanadb3**.
    6. Virtuální počítače teď ponechte v zastaveném stavu. Dále umožníte [urychlenou síť](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pro všechna nově připojená síťová rozhraní.  

6. Povolte akcelerované sítě pro další síťová rozhraní pro **`hana`** a **`client`** podsítí.  

    1. Otevřete [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v [Azure Portal](https://portal.azure.com/#home)  
    2. Spusťte následující příkazy, aby bylo možné zrychlit síťové rozhraní pro další síťová rozhraní připojená k **`hana`** a **`client`** podsítí.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Spuštění virtuálních počítačů  

    1. V levém navigačním podokně vyberte Virtual Machines. Filtr na název virtuálního počítače, například **hanadb1**. Klikněte na virtuální počítač.  
    2. V přehledu vyberte spustit.  

## <a name="operating-system-configuration-and-preparation"></a>Konfigurace a příprava operačního systému

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1, **[2]** , které platí jenom pro uzel 2 nebo **[3]** – platí jenom pro uzel 3.

1. **[A]** Udržujte soubory hostitelů na virtuálních počítačích. Zahrnout položky pro všechny podsítě V tomto příkladu byly přidány následující položky `/etc/hosts`.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** změňte nastavení DHCP a konfigurace cloudu tak, aby se zabránilo nezamýšleným změnám názvu hostitele.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** Připravte operační systém na spouštění SAP HANA v systémech NetApp se systémem souborů NFS, jak je popsáno v [SAP HANA v systémech NetApp AFF s příručkou konfigurace systému souborů NFS](https://www.netapp.com/us/media/tr-4435.pdf). Vytvořit konfigurační soubor pro nastavení konfigurace NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** vytvořit konfigurační soubor s nastaveními konfigurace Microsoft pro Azure: `/etc/sysctl.d/ms-az.conf`.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** upravte nastavení sunRPC podle doporučení v [SAP HANA v systémech NetApp AFF s Průvodcem konfigurací systému souborů NFS](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Připojení Azure NetApp Filesch svazků

1. **[A]** vytvoření přípojných bodů pro databázové svazky Hana  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** vytvořit adresáře pro `/usr/sap` pro sdílenou uzel v **HN1**.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** ověřte nastavení domény NFS. Ujistěte se, že je doména nakonfigurovaná jako **`localdomain`** a že mapování je nastavené na **nikdo**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** ZAKÁŽE mapování ID názvů NFSv4. Spusťte příkaz Mount a vytvořte tak adresářovou strukturu, kde se nachází `nfs4_disable_idmapping`.  V části/sys/modules nebudete moct ručně vytvořit adresář, protože pro jádro nebo ovladače se rezervuje přístup.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** vytvořte skupinu SAP Hana a uživatele ručně. ID pro skupiny sapsys a User **HN1**ADM musí být nastavená na stejná ID, která jste zadali během připojování. V tomto příkladu jsou ID nastavená na **1001**. V opačném případě nebude možné získat přístup ke svazkům.  ID skupinových sapsys a uživatelských účtů **HN1**ADM a sapadm musí být na všech virtuálních počítačích stejné.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** připojte sdílené svazky Azure NetApp Files.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** připojte svazky specifické pro uzel v **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** připojte svazky specifické pro uzel v **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** připojte svazky specifické pro uzel v **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** ověřte, že jsou všechny svazky Hana připojené k protokolu NFS verze **názvů NFSv4**.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalace  

V tomto příkladu pro nasazení SAP HANA v konfiguraci škálování na více instancí s využitím pohotovostního uzlu s Azure jsme použili HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Příprava instalace HANA

1. **[A]** před instalací Hana nastavte kořenové heslo (kořenové heslo můžete po konkurenční instalaci zakázat). Příkaz EXECUTE AS `root` `passwd`.  

2. **[1]** ověřte, že můžete SSH na **hanadb2** a **hanadb3**, aniž by se zobrazila výzva k zadání hesla.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** nainstalujte další balíčky požadované pro Hana 2,0 SP4. Podrobnosti najdete v tématu SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Změňte vlastnictví SAP HANA `data` a `log` adresáře na **HN1**ADM.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalace HANA

1. **[1]** nainstalujte SAP HANA podle pokynů v [Průvodci instalací a aktualizací SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). V tomto příkladu nainstalujeme SAP HANA horizontálního navýšení kapacity s hlavním, jedním pracovním a jedním pohotovostním uzlem.  
   Spusťte program **hdblcm** z adresáře instalačního softwaru Hana. Použijte parametr `internal_network` a předejte adresní prostor pro podsíť, který se používá pro interní komunikaci mezi uzly v HANA.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Na příkazovém řádku zadejte následující hodnoty.

     * Vyberte akci: zadejte **1** (pro instalaci).
     * Vyberte další součásti pro instalaci: zadejte **2, 3** .
     * Zadejte instalační cestu: stiskněte klávesu ENTER (výchozí nastavení je/Hana/Shared).
     * Zadejte název místního hostitele: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Chcete přidat hostitele do systému? Zadat **y**
     * Zadejte Coma – oddělené názvy hostitelů, které chcete přidat: **hanadb2, hanadb3**
     * Zadání kořenového uživatelského jména [root]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte heslo uživatele root: zadejte heslo ke kořenu.
     * Vyberte role pro hostitele hanadb2: zadejte **1** (pro pracovního procesu).
     * Zadejte skupinu převzetí služeb při selhání hostitele pro hanadb2 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte číslo oddílu úložiště pro hanadb2 hostitele [<<assign automatically>>]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte skupinu pracovních procesů pro hanadb2 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Vyberte role pro hostitele hanadb3: zadejte **2** (pro pohotovostní režim).
     * Zadejte skupinu převzetí služeb při selhání hostitele pro hanadb3 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte skupinu pracovních procesů pro hanadb3 hostitele [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte ID SAP HANA systému: zadejte **HN1** .
     * Zadejte číslo instance [00]: zadejte **03**
     * Zadejte místní hostitelský pracovní skupinu [výchozí]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Vyberte využití systému/zadejte index [4]: zadejte **4** (pro vlastní).
     * Zadejte umístění datových svazků [/hana/data/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte umístění svazků protokolu [/hana/log/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Omezit maximální přidělení paměti? [n]: zadejte **n**
     * Zadejte název hostitele certifikátu pro hostitele hanadb1 [hanadb1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte název hostitele certifikátu pro hostitele hanadb2 [hanadb2]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte název hostitele certifikátu pro hostitele hanadb3 [hanadb3]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Zadejte heslo správce systému (hn1adm): zadejte heslo.
     * Zadejte heslo uživatele systémové databáze (systém): zadejte heslo systému.
     * Potvrďte heslo uživatele systémové databáze (systém): zadejte heslo systému.
     * Restartovat systém po restartování počítače? [n]: zadejte **n** 
     * Chcete pokračovat (a/n): ověřit souhrn a pokud vše vypadá dobře, zadejte **y** .


2. **[1]** ověřit Global. ini  

   Zobrazte Global. ini a ujistěte se, že je nastavená konfigurace pro interní SAP HANA komunikace mezi uzly. Ověřte **komunikaci**oddílu. Měl by mít adresní prostor pro **`hana`** podsíť a `listeninterface` by měl být nastaven na `.internal`. Ověřte oddíl **internal_hostname_resolution**. Měl by mít IP adresy pro virtuální počítače HANA, které patří do podsítě **`hana`** .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** přidejte mapování hostitele, aby se zajistilo, že se IP adresy klientů použijí pro komunikaci klientů. Přidejte část `public_host_resolution` a přidejte odpovídající IP adresy z klientské podsítě.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** restartujte SAP HANA, aby se změny aktivovaly.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** ověřte, zda klientské rozhraní bude používat IP adresy z **klientské** podsítě pro komunikaci.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Podrobnosti o tom, jak ověřit konfiguraci, najdete v tématu SAP Note [2183363 – konfigurace SAP HANA interní sítě](https://launchpad.support.sap.com/#/notes/2183363) .  

6. Pokud chcete optimalizovat SAP HANA pro základní úložiště souborů Azure NetApp, nastavte následující parametry SAP HANA:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **na**
   - `async_write_submit_active` **na**
   - `async_write_submit_blocks` **vše**

   Podrobnosti najdete v tématu [SAP HANA NetAppch AFF systémů s nastavením systému souborů NFS](https://www.netapp.com/us/media/tr-4435.pdf). 

   Počínaje systémy SAP HANA 2,0 můžete nastavit parametry v `global.ini`. Viz SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   V případě systémů SAP HANA 1,0 verze až do SPS12 lze tyto parametry nastavit během instalace, jak je popsáno v tématu SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Úložiště, které používá Azure NetApp Files, má omezení velikosti souboru nebo 16 TB. SAP HANA se implicitně neomezuje na omezení úložiště a při dosažení limitu velikosti souboru 16 TB nebude automaticky vytvářet nové datové soubory. Jak SAP HANA se pokusí zvětšit soubor nad rámec 16 TB, bude mít za následek chyby a nakonec dojde k chybě serveru index. 

   > [!IMPORTANT]
   > Chcete-li zabránit SAP HANA pokusu o zvětšení datových souborů nad rámec [16TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) úložiště sybsystem, nastavte v `global.ini` následující parametry.  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 podrobnosti najdete v tématu SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Pozor na SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test SAP HANA převzetí služeb při selhání 

1. Simulace havárie uzlu na SAP HANA pracovním uzlu  

   Spusťte následující příkazy jako **HN1**ADM pro zachycení stavu prostředí před simulací havárie uzlu.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   Spusťte následující příkaz jako kořen v pracovním uzlu, který je v tomto případě **hanadb2** pro simulaci selhání uzlu.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Sledujte systém pro dokončení převzetí služeb při selhání. Po dokončení převzetí služeb při selhání se zachytí stav, který by měl vypadat jako na následujícím obrázku.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Abyste se vyhnuli prodlevám při převzetí služeb při selhání v SAP HANA při nečinnosti uzlu, nastavte `kernel.panic` na 20 sekund na **všech** virtuálních počítačích Hana. Konfigurace se provádí v `/etc/sysctl`. Změny aktivujete restartováním virtuálních počítačů. Převzetí služeb při selhání v případě, že dojde k selhání uzlu, může trvat 10 nebo více minut, pokud tato změna neproběhne.  

2. Ukončí názvový server.  
   Spusťte následující příkazy jako **HN1**ADM, abyste zkontrolovali stav prostředí před testem:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Spusťte následující příkazy jako **HN1**ADM na aktivním hlavním uzlu, což je v tomto případě **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Pohotovostní uzel **hanadb3** převezme jako hlavní uzel. Stav prostředku po dokončení testu převzetí služeb při selhání:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Znovu spusťte instanci HANA na **hanadb1**, která je na stejném virtuálním počítači, kde byl názvový server ukončen. Uzel **hanadb1** se znovu připojí k prostředí a zachová se jeho pohotovostní role.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po spuštění SAP HANA v **hanadb1**očekávat následující stav:  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Teď znovu zapněte názvový server na aktuálně aktivním hlavním uzlu, který je na hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   **Hanadb1** uzlu obnoví roli hlavního uzlu. Stav, po dokončení testu převzetí služeb při selhání bude vypadat takto:
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   Spustit SAP HANA v **hanadb3** – bude připravená sloužit jako pohotovostní uzel.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Stav po spuštění SAP HANA v **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP 
* HANA v Azure (velké instance), přečtěte si téma [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
